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

For an individual bundle, tooling MUST reject any manifest member name, manifest string, manifest URI, retained-evidence metadata field, or decoded retained-evidence value that contains, embeds, encodes or is deterministically derived from a plaintext government identifier or another low-entropy identity credential.

This includes identifiers placed in fields unrelated to identity, such as `authority_representation`, `provenance.notes`, evidence descriptions, artifact descriptions, URI path/query/fragment components, patent-evidence payloads, PDF metadata, JSON member names, or text bodies.

For reviewed free-form public text, decimal digits and identifier-like alphanumeric runs are not presumed safe. If such a run is not proven to be a public non-personal structured identifier already present in the manifest or its retained public evidence, validation fails. Labels or variants denoting SSN, DNI, passport, national ID, tax ID, social-security number or equivalent government credentials fail regardless of surrounding punctuation or case.

For reviewed public URIs, validation examines both the exact raw URI and each percent-decoded UTF-8 component. Invalid percent encoding, decoder replacement, recursive/double encoding, user-info credentials, or an identifier-bearing component whose public non-personal meaning cannot be established causes rejection. A URI is not safe merely because it uses HTTPS.

### 1.3 Fields copied into the attestation

`patent_licensor.name` is a public display name, not an identifier transport. The root schema rejects digits, control characters, identifier-like delimiters and common government-identifier labels in that field.

`identity_reference.jurisdiction` MUST use exactly `ISO3166-1:<AA>` or `ISO3166-2:<AA-SUBDIVISION>` and MUST be verified against authenticated immutable ISO evidence as defined in section 4. A live lookup against whatever registry state happens to exist at validation time is not sufficient.

Every manifest value copied into the retained identity attestation MUST first pass its schema/profile grammar and the whole-manifest privacy traversal above. No free-form manifest string may be copied into an attestation field unless the profile explicitly identifies that field and its grammar.

These checks are in addition to the existing opaque-token, fixed media-type, content-addressed path and no-`record_uri` requirements.

### 1.4 Retained-evidence traversal

The privacy boundary is the **entire published PatentGrantBundle**, not merely `manifest/patent-grant.json`. Before a bundle for an individual can be marked operative, tooling MUST privacy-review the exact bytes and container metadata of every member in the transitive evidence closure defined in section 4.4.

Accepted decoding rules are fail-closed:

- JSON evidence MUST be strict UTF-8, pass the same duplicate-member and lone-surrogate lexical gate as the manifest, then be recursively traversed over every decoded member name and string/URI value.
- `text/plain` evidence MUST be strict UTF-8 with no replacement decoding; every Unicode scalar value is inspected as one complete text payload.
- JCS identity attestations and ECL-PL ISO snapshot/authentication records are inspected under their fixed schemas plus the same credential-exclusion rule.
- PDF, `application/octet-stream`, images, compressed containers, office formats, or any other format with metadata or embedded payloads are **not privacy-auditable by default**. They are rejected for an individual bundle unless this reviewed security profile later names an exact deterministic decoder/extractor profile that exposes all textual metadata, embedded objects and alternate streams relevant to privacy inspection. No such permissive binary profile exists in this draft.

Container metadata such as archive comments, member extra fields, extended attributes, filenames outside the canonical member path, MIME parameters, alternate streams and implementation-specific metadata is either forbidden by the package profile or must be exposed to the same credential-exclusion traversal. Hidden or uninspectable metadata causes rejection.

The privacy-review record MUST bind the exact PatentGrantBundle identity, exact manifest SHA-256, every reviewed member path and digest, the decoder/profile used for each member, the validator/reviewer identity and the result. A review of different bytes or an incomplete member set is irrelevant.

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

Jurisdiction validation for an individual MUST NOT depend on mutable live ISO registry state or on self-authenticated retained bytes.

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
trust_anchor_fingerprint
media_type
```

Requirements:

- `standard` is exactly `ISO-3166`;
- `edition_id` is a nonempty immutable edition/snapshot identifier and MUST NOT be `latest`, `current`, a mutable URL or another moving label;
- `jurisdiction_code` is exactly the manifest `identity_reference.jurisdiction`;
- every bundle path is content-addressed as `evidence/identity/sha256/<64 lowercase hex>` and its terminal digest equals the corresponding SHA-256 field and recomputed exact member digest;
- `snapshot_bundle_path` identifies the normalized ECL-PL resolution record described in section 4.3;
- `source_artifact_bundle_path` identifies the exact ISO-issued source artifact whose contents support that resolution record;
- `signature_bundle_path` identifies the exact ISO-issued detached signature, signed manifest, or equivalent cryptographic authentication artifact for `source_artifact_bundle_path`;
- `trust_anchor_fingerprint` identifies an exact active anchor in `spec/ISO3166-TRUST-REGISTRY.json`; and
- `media_type` is exactly `application/ecl-pl-iso3166-snapshot+json`.

### 4.2 Source authentication and trust registry

A source artifact is accepted only when its cryptographic authentication verifies to a trust anchor listed in the exact reviewed `spec/ISO3166-TRUST-REGISTRY.json` input for the release validator.

The registry itself is not allowed to bootstrap trust by assertion. An anchor entry may be added only when review evidence establishes that the pinned public key/certificate is issued or controlled by ISO for authenticating the relevant ISO 3166 publication artifact. Each anchor entry must pin exact key/certificate bytes by SHA-256, identify the signature profile, state its validity interval/purpose, and be independently reviewed before use.

**Current fail-closed state:** the architecture-stage registry intentionally contains zero anchors. Therefore no individual PatentGrantBundle can currently satisfy this ISO-authentication gate or become operative. HTTPS delivery from `iso.org`, an unsigned ISO-looking file, a repository maintainer signature, an ECL-PL reviewer statement, or a self-described `edition_id` is not a substitute for ISO-origin cryptographic authentication.

When an anchor is eventually configured, validation MUST verify the exact signature/authentication artifact over the exact retained source-artifact bytes, verify the chain/key against the pinned anchor and purpose, and bind the authenticated source artifact to the signed `edition_id`. A mismatch or unsupported signature profile fails closed.

### 4.3 Exact resolution record

The normalized ECL-PL ISO resolution record at `snapshot_bundle_path` is strict UTF-8 JSON with no BOM and contains exactly:

```text
profile
standard
edition_id
source_artifact_sha256
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
- `jurisdiction_code` equals the signed attestation and manifest jurisdiction;
- `record_kind` is exactly `country` for `ISO3166-1:<AA>` or `subdivision` for `ISO3166-2:<AA-SUBDIVISION>`;
- `assigned` is JSON `true`; withdrawn, reserved, deleted, unassigned or ambiguous records do not validate;
- `canonical_name` is the exact name obtained from the authenticated source artifact under the edition-specific parser profile; and
- `parent_code` is `null` for a country and the exact `ISO3166-1:<AA>` parent for a subdivision.

No additional members are permitted. The release implementation MUST identify the exact parser profile used to derive this record from the authenticated source artifact. If the source artifact format cannot be parsed deterministically under a reviewed profile, validation fails closed rather than accepting a hand-authored mapping.

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

For the current identity profile, the manifest directly references the signed identity attestation; that attestation transitively references the normalized ISO resolution record, the exact ISO-issued source artifact and its cryptographic authentication artifact. All four identity-evidence members therefore participate in the canonical bundle identity.

Validation hashes every exact member and requires equality among path digest, declared digest, index digest and recomputed digest at every edge.

## 5. Release-gate precedence

These checks run before a bundle may be marked operative. Passing JSON Schema validation alone does not satisfy this security profile. Any ambiguity in privacy classification, retained-evidence decoding, percent-decoding, point decoding, package entry type, transitive evidence closure, ISO trust-anchor provenance, signature authentication, registry snapshot identity or jurisdiction resolution fails closed.
