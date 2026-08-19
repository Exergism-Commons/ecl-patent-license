# ECL-PL Security Profile

> **Status: mandatory architecture-stage release gate. No licensing effect.**

This document adds fail-closed security requirements to the release workflow in `spec/VERSIONING.md`. It does not relax any rule in that specification. Where this document narrows an acceptance rule, the narrower rule controls for a conforming ECL-PL release validator. Where this document explicitly says that it supersedes an exact structure or member-set rule in `spec/VERSIONING.md`, this security profile controls for that narrow case.

## 1. Individual-bundle privacy gate

For `patent_licensor.identity_reference.subject_type: individual`, release validation MUST apply the root-schema individual constraints and this privacy gate before any identity attestation or PatentGrantBundle may be accepted.

### 1.1 Whole-manifest traversal

After the raw-JSON lexical gate and schema validation succeed, tooling MUST recursively visit **every decoded object member name, every string value and every URI value in the complete PatentGrantManifest**, including nested objects, array items, map keys, free-form descriptions, notes, authority text, evidence metadata, provenance text, artifact metadata, ECL references and patent-source URIs.

No JSON path is implicitly exempt merely because its value is not copied into the attestation.

The validator classifies each visited value as exactly one of:

1. **schema-structured public value** — a value whose complete spelling is constrained by an ECL-PL grammar to a non-personal public namespace, such as a SHA-256 digest, PatentGrant ID, patent-publication key, claim number, fixed enum/URN, content-addressed bundle path, timestamp, ISO jurisdiction code, opaque identity token or pinned public key;
2. **reviewed free-form public text** — prose that contains no identity credential or credential derivative and whose privacy review is bound to the exact manifest hash; or
3. **reviewed public URI** — a URI whose raw and decoded components have been checked and whose identifier-bearing components resolve only to public non-personal records relevant to the grant.

Anything that cannot be placed unambiguously into one of those three classes fails closed.

### 1.2 Credential exclusion

For an individual bundle, tooling MUST reject any published member name, decoded member value, member metadata field, manifest string, manifest URI or fixed-license text that contains, embeds, encodes or is deterministically derived from a plaintext government identifier or another low-entropy identity credential.

This includes identifiers placed in fields unrelated to identity, such as `authority_representation`, `provenance.notes`, evidence descriptions, artifact descriptions, URI path/query/fragment components, patent-evidence payloads, JSON member names, fixed licence prose, bundle metadata or text bodies.

For reviewed free-form public text, decimal digits and identifier-like alphanumeric runs are not presumed safe. If such a run is not proven to be a public non-personal structured identifier already present in the manifest or its retained public evidence, validation fails. Labels or variants denoting SSN, DNI, passport, national ID, tax ID, social-security number or equivalent government credentials fail regardless of surrounding punctuation or case.

For reviewed public URIs, validation examines both the exact raw URI and each percent-decoded UTF-8 component. Invalid percent encoding, decoder replacement, recursive/double encoding, user-info credentials, or an identifier-bearing component whose public non-personal meaning cannot be established causes rejection. A URI is not safe merely because it uses HTTPS.

### 1.3 Fields copied into the attestation

`patent_licensor.name` is a public display name, not an identifier transport. The root schema rejects digits, control characters, identifier-like delimiters and common government-identifier labels in that field.

`identity_reference.jurisdiction` MUST use exactly `ISO3166-1:<AA>` or `ISO3166-2:<AA-SUBDIVISION>` and MUST be verified against authenticated immutable ISO evidence as defined in section 4. A live lookup against whatever registry state happens to exist at validation time is not sufficient.

Every manifest value copied into the retained identity attestation MUST first pass its schema/profile grammar and the whole-manifest privacy traversal above. No free-form manifest string may be copied into an attestation field unless the profile explicitly identifies that field and its grammar.

These checks are in addition to the existing opaque-token, fixed media-type, content-addressed path and no-`record_uri` requirements.

### 1.4 Whole-package traversal

The privacy boundary is the **entire physical PatentGrantBundle**, not merely the PatentGrantManifest or transitive evidence closure. Before a bundle for an individual can be marked operative, tooling MUST privacy-review the exact bytes and container metadata of:

- `BUNDLE-INDEX` itself;
- the fixed member `license/PATENT-LICENSE`;
- the fixed member `manifest/patent-grant.json`; and
- every evidence member in the transitive closure defined in section 4.4.

No member is exempt because it is fixed, normative legal text, generated metadata, or outside the evidence namespace.

Accepted decoding rules are fail-closed:

- `BUNDLE-INDEX` MUST use its canonical textual format from `spec/VERSIONING.md`; every path and digest is checked structurally and every other textual value is subject to the credential-exclusion rule.
- `license/PATENT-LICENSE` MUST be strict UTF-8 with no BOM and no replacement decoding. The complete legal-text payload is inspected as public text; a credential-looking sequence that is not provably a public non-personal legal/patent reference fails closed.
- `manifest/patent-grant.json` is reviewed under sections 1.1–1.3 in addition to this package-wide rule.
- JSON evidence MUST be strict UTF-8, pass the same duplicate-member and lone-surrogate lexical gate as the manifest, then be recursively traversed over every decoded member name and string/URI value.
- `text/plain` evidence MUST be strict UTF-8 with no replacement decoding; every Unicode scalar value is inspected as one complete text payload.
- JCS identity attestations and ECL-PL registry/snapshot records are inspected under their fixed profiles plus the same credential-exclusion rule.
- PDF, `application/octet-stream`, images, compressed containers, office formats, or any other format with metadata or embedded payloads are **not privacy-auditable by default**. They are rejected for an individual bundle unless this reviewed security profile names an exact deterministic decoder/extractor profile that exposes all textual metadata, embedded objects and alternate streams relevant to privacy inspection. No such permissive binary profile exists in this draft.

Container metadata such as archive comments, member extra fields, extended attributes, filenames outside the canonical member path, MIME parameters, alternate streams and implementation-specific metadata is either forbidden by the package profile or must be exposed to the same credential-exclusion traversal. Hidden or uninspectable metadata causes rejection.

The privacy-review record MUST bind the exact PatentGrantBundle identity, exact `BUNDLE-INDEX` SHA-256, exact manifest SHA-256, **every physical member path and digest**, the decoder/profile used for each member, the validator/reviewer identity and the result. A review of different bytes or an incomplete physical member set is irrelevant.

## 2. Strict Ed25519 verification profile

The fixed profile `urn:ecl-pl:identity-attestation-profile:ed25519-jcs-v1` uses RFC 8032 Ed25519 with **one strict verification rule**. Permissive/cofactored verification, ZIP-215-style acceptance, or library-specific acceptance of noncanonical/small-order encodings is non-conforming.

The following representations are distinct:

- `public_key_string` is the exact manifest string `ed25519:v1:<payload>`;
- `public_key_payload` is the `<payload>` substring after removing the exact ASCII prefix `ed25519:v1:`;
- `A_encoded` is the 32-byte result of strict canonical base64url decoding of `public_key_payload`;
- the 64-byte signature is split into `R_encoded || S_encoded`, where each half is 32 bytes; and
- `M` is the exact RFC 8785 JCS UTF-8 message defined by the identity-attestation profile.

A conforming validator MUST:

1. Require `public_key_string` to have the exact `ed25519:v1:` prefix and no other prefix or suffix.
2. Strictly base64url-decode `public_key_payload`; require exactly 32 bytes, no padding or non-alphabet characters, then canonically re-encode those bytes and require byte-for-byte equality with `public_key_payload`.
3. Treat those decoded 32 bytes as `A_encoded` and decode them as the canonical RFC 8032 compressed Edwards25519 point. The encoded `y` coordinate MUST be `< p`, where `p = 2^255 - 19`, and the sign bit MUST identify one unique curve point. Noncanonical encodings fail.
4. Reject decoding failure, the identity point, every small-order/torsion point and every point outside the prime-order subgroup. With `L = 2^252 + 27742317777372353535851937790883648493`, require `[L]A = identity` and `A != identity`.
5. Strictly decode `R_encoded` using the same canonical point rules. Reject identity, small-order/torsion and non-prime-subgroup `R`; require `[L]R = identity` and `R != identity`.
6. Parse `S_encoded` as the RFC 8032 little-endian scalar and require the canonical bound `0 <= S < L`.
7. Compute `k = SHA-512(R_encoded || A_encoded || M)` as a little-endian integer reduced modulo `L`.
8. Accept the signature **only** if the exact equation `[S]B = R + [k]A` holds. A validator MUST NOT multiply either side by the cofactor and MUST NOT substitute a relaxed/cofactored equation.
9. Fail closed if its cryptographic library cannot expose or guarantee all canonical-decoding, subgroup and scalar checks above.

The release record MUST identify the Ed25519 implementation and version used. A key/signature pair accepted by one implementation but rejected by this strict profile is invalid for ECL-PL.

The verifier key remains a cryptographic origin pin, not proof of patent ownership or legal authority; grant-specific legal review and Patent Licensor approval remain separate mandatory gates.

## 3. Physical PatentGrantBundle member type

Before hashing or extraction, a conforming package reader MUST classify `BUNDLE-INDEX` and every indexed physical member.

Each MUST be exactly one finite **regular file/data stream** with one deterministic byte sequence. The following are invalid even when the raw member name is otherwise canonical:

- directory entries;
- symlinks or hardlinks;
- FIFOs or named pipes;
- sockets;
- block or character devices;
- sparse/hole-bearing archive entries whose logical bytes depend on archive/filesystem interpretation;
- alternate data streams, forks or extended-stream aliases;
- metadata-only entries or any other non-regular/special entry type.

For an archive format, the reader MUST require that format's regular-file entry type and MUST verify that the declared uncompressed byte length equals the exact byte sequence supplied to SHA-256. Duplicate physical entries with the same canonical name fail before hashing. Extraction is never used to determine member identity or bytes.

If the container format or API does not let the validator distinguish regular data entries from special entries deterministically, the package is non-conforming and validation fails closed.

## 4. Authenticated immutable ISO 3166 jurisdiction evidence

Jurisdiction validation for an individual MUST NOT depend on mutable live ISO registry state, mutable validator configuration, implementation-local parser choice or self-authenticated retained bytes.

### 4.1 Attestation member

This section supersedes the exact identity-attestation member list in `spec/VERSIONING.md` section 10.2.3 for the fixed `ed25519-jcs-v1` profile by adding exactly one member named `jurisdiction_registry_snapshot`. No other additional attestation member is introduced by this rule.

`jurisdiction_registry_snapshot` is an object containing exactly:

```text
standard
edition_id
jurisdiction_code
snapshot_bundle_path
snapshot_sha256
source_artifact_bundle_path
source_artifact_sha256
signature_bundle_path
signature_sha256
trust_registry_version
trust_registry_bundle_path
trust_registry_sha256
parser_registry_version
parser_registry_bundle_path
parser_registry_sha256
parser_profile_id
parser_profile_bundle_path
parser_profile_sha256
trust_anchor_fingerprint
media_type
```

Requirements:

- `standard` is exactly `ISO-3166`;
- `edition_id` is a nonempty immutable edition/snapshot identifier and MUST NOT be `latest`, `current`, a mutable URL or another moving label;
- `jurisdiction_code` is exactly the manifest `identity_reference.jurisdiction`;
- `trust_registry_version` is exactly `ecl-pl-iso3166-trust-registry-v1`;
- `parser_registry_version` is exactly `ecl-pl-iso3166-parser-registry-v1`;
- `parser_profile_id` is the immutable ID of the exact parser profile selected by the retained parser registry and trust-anchor/edition rule;
- every `*_bundle_path` is content-addressed as `evidence/identity/sha256/<64 lowercase hex>` and its terminal digest equals the corresponding SHA-256 field and recomputed exact member digest;
- `snapshot_bundle_path` identifies the normalized ECL-PL resolution record described in section 4.3;
- `source_artifact_bundle_path` identifies the exact ISO-issued source artifact whose contents support that resolution record;
- `signature_bundle_path` identifies the exact ISO-issued detached signature, signed manifest, or equivalent cryptographic authentication artifact for `source_artifact_bundle_path`;
- `trust_registry_bundle_path` identifies the exact trust-registry bytes used for this release;
- `parser_registry_bundle_path` identifies the exact closed parser-registry bytes used for this release;
- `parser_profile_bundle_path` identifies the exact parser-profile bytes used to derive the resolution record;
- `trust_anchor_fingerprint` identifies one exact active anchor in the retained trust-registry bytes; and
- `media_type` is exactly `application/ecl-pl-iso3166-snapshot+json`.

The existing signed identity-attestation `issued_at` timestamp is the **sole trust-anchor validity evaluation instant** for this profile. `valid_from`/`valid_until` in the retained trust registry are evaluated against that exact immutable instant; current wall-clock time, revalidation time and repository modification time MUST NOT affect the result.

### 4.2 Immutable trust-registry input

A source artifact is accepted only when its cryptographic authentication verifies to a trust anchor listed in the **exact retained trust-registry member** identified by `trust_registry_bundle_path`/`trust_registry_sha256`.

At initial release, those retained registry bytes MUST be byte-for-byte identical to the exact reviewed `spec/ISO3166-TRUST-REGISTRY.json` input selected by the release gate. The release record MUST bind the exact registry version, SHA-256 and attestation `issued_at` validity instant. Later revalidation MUST use the retained bytes; a newer repository registry, a mutable `main` copy or ambient validator configuration cannot replace them.

The registry itself is not allowed to bootstrap trust by assertion. An anchor entry may be added only when review evidence establishes that the pinned public key/certificate is issued or controlled by ISO for authenticating the relevant ISO 3166 publication artifact. Each active anchor/edition rule MUST pin exact key/certificate bytes by SHA-256 and bind at least:

- signature profile;
- `valid_from` and `valid_until`;
- authentication purpose;
- source media type;
- immutable edition scope; and
- exact `parser_profile_id` and `parser_profile_sha256`.

Anchor status, purpose, validity, signature profile and parser binding are read only from the retained trust-registry bytes. The signed attestation `issued_at` instant MUST fall within the retained anchor validity interval. Missing, revoked/inactive, ambiguous or multiply matching entries fail closed.

**Current fail-closed state:** the architecture-stage trust registry intentionally contains zero anchors. Therefore no individual PatentGrantBundle can currently satisfy this ISO-authentication gate or become operative. HTTPS delivery from `iso.org`, an unsigned ISO-looking file, a repository maintainer signature, an ECL-PL reviewer statement or self-described edition metadata is not a substitute for ISO-origin cryptographic authentication.

When an anchor is eventually configured, validation MUST verify the exact signature/authentication artifact over the exact retained source-artifact bytes, verify the key/chain against the retained pinned anchor and purpose at the immutable `issued_at` instant, and bind the authenticated source artifact to the signed `edition_id`. A mismatch or unsupported signature profile fails closed.

### 4.3 Closed content-addressed parser input

A validator MUST NOT select a parser by implementation name, local configuration, package version, mutable URL, repository `main`, media-type heuristic or any other ambient mechanism.

The exact retained parser-registry member identified by `parser_registry_bundle_path`/`parser_registry_sha256` MUST be byte-for-byte identical, at initial release, to the exact reviewed `spec/ISO3166-PARSER-REGISTRY.json` input selected by the release gate. The release record MUST bind its exact version and SHA-256. Later validation uses only the retained registry bytes.

The parser registry is a closed allowlist. A parser profile is usable only if:

1. the retained parser registry contains exactly one entry for `parser_profile_id` and `parser_profile_sha256`;
2. the retained `parser_profile_bundle_path` bytes hash exactly to `parser_profile_sha256`;
3. the active retained trust-anchor/edition rule independently names the same parser ID/hash for the authenticated source media type and edition scope; and
4. the parser profile is complete under the mandatory fields/semantics declared by `ecl-pl-iso3166-parser-registry-v1`, including exact input encoding, deterministic record locator, field mapping, normalization order, rejection rules, fixed output profile and `external_lookups: forbidden`.

No prose-only parser description or locally implemented interpretation is sufficient. If the retained parser registry has no matching profile, contains conflicting entries, or the exact profile cannot deterministically parse the authenticated source bytes, validation fails closed.

**Current fail-closed state:** `spec/ISO3166-PARSER-REGISTRY.json` intentionally contains zero parser profiles. Therefore no individual PatentGrantBundle can currently pass jurisdiction resolution, even if a trust anchor were added independently.

The normalized ECL-PL ISO resolution record at `snapshot_bundle_path` is strict UTF-8 JSON with no BOM and contains exactly:

```text
profile
standard
edition_id
source_artifact_sha256
parser_profile_id
parser_profile_sha256
jurisdiction_code
record_kind
assigned
canonical_name
parent_code
```

where:

- `profile` is exactly `urn:ecl-pl:iso3166-resolution-record:v1`;
- `standard` is exactly `ISO-3166`;
- `edition_id` equals the authenticated source edition;
- `source_artifact_sha256` equals the authenticated retained ISO source artifact digest;
- `parser_profile_id` and `parser_profile_sha256` equal the signed attestation values and the selected retained parser-registry/trust-anchor binding;
- `jurisdiction_code` equals the signed attestation and manifest jurisdiction;
- `record_kind` is exactly `country` for `ISO3166-1:<AA>` or `subdivision` for `ISO3166-2:<AA-SUBDIVISION>`;
- `assigned` is JSON `true`; withdrawn, reserved, deleted, unassigned or ambiguous records do not validate;
- `canonical_name` is the exact name produced by the retained content-addressed parser profile; and
- `parent_code` is `null` for a country and the exact `ISO3166-1:<AA>` parent for a subdivision.

No additional members are permitted. The resolution record MUST be recomputed from the exact authenticated source bytes using the exact retained parser profile and must match byte-for-byte after canonical serialization; a hand-authored mapping cannot substitute for deterministic derivation.

### 4.4 Transitive evidence closure

For the fixed individual profile, **“manifest-referenced evidence” means the transitive closure of content-addressed evidence references beginning at the PatentGrantManifest**.

This clause explicitly supersedes both:

- `spec/VERSIONING.md` section 4 rules 5 and 11 to the extent they speak only of evidence directly referenced by the manifest; and
- `spec/VERSIONING.md` section 10.4 clauses stating that every indexed evidence path must be referenced by the manifest and every manifest-referenced path must appear exactly once.

The operative rule is instead:

1. start with every evidence member directly referenced by the manifest;
2. parse each member under its fixed profile and add every content-addressed evidence member that profile normatively references;
3. repeat until no new member is discovered;
4. reject cycles, duplicate logical references with conflicting digests, unsupported reference-bearing formats, or any evidence path outside the canonical content-addressed namespaces;
5. require **every and only** member in that transitive closure to appear exactly once in `BUNDLE-INDEX` and exactly once as a physical regular-file member; and
6. reject any indexed evidence member outside the closure.

For the current individual identity profile, the manifest directly references the signed identity attestation. That attestation transitively references the normalized ISO resolution record, exact ISO-issued source artifact, exact authentication artifact, exact trust-registry bytes, exact parser-registry bytes and exact parser-profile bytes. Thus the identity path consists of **seven content-addressed evidence members including the attestation itself**, and all seven participate in the canonical bundle identity.

Validation hashes every exact member and requires equality among path digest, declared digest, index digest and recomputed digest at every edge.

## 5. Release-gate precedence and immutable validation inputs

These checks run before a bundle may be marked operative. Passing JSON Schema validation alone does not satisfy this security profile.

For an individual bundle, the release record MUST bind at least:

- exact PatentGrantBundle identity;
- exact `BUNDLE-INDEX` and manifest SHA-256 values;
- identity-attestation `issued_at` trust-evaluation instant;
- trust-registry version and SHA-256;
- parser-registry version and SHA-256;
- parser-profile ID and SHA-256;
- trust-anchor fingerprint; and
- validator/cryptographic implementation identities and versions.

The exact trust-registry, parser-registry and parser-profile bytes are retained in the bundle through section 4.4, so revalidation does not depend on a mutable repository or ambient local configuration.

Any ambiguity in privacy classification, fixed-member decoding, retained-evidence decoding, percent-decoding, point decoding, package entry type, transitive evidence closure, trust-registry identity, ISO trust-anchor provenance, validity instant, signature authentication, parser-registry identity, parser-profile identity, source parsing, registry snapshot identity or jurisdiction resolution fails closed.
