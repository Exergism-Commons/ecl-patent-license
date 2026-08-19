# ECL-PL Security Profile

> **Status: mandatory architecture-stage release gate. No licensing effect.**

This document adds fail-closed security requirements to the release workflow in `spec/VERSIONING.md`. It does not relax any rule in that specification. Where this document narrows an acceptance rule, the narrower rule controls for a conforming ECL-PL release validator. Where this document explicitly says that it supersedes an exact structure or member-set rule in `spec/VERSIONING.md`, this security profile controls for that narrow case.

## 1. Individual-manifest privacy gate

For `patent_licensor.identity_reference.subject_type: individual`, release validation MUST apply the root-schema individual constraints and this whole-manifest privacy gate before any identity attestation or bundle may be accepted.

### 1.1 Whole-manifest traversal

Privacy validation is not limited to fields copied into the identity attestation. After the raw-JSON lexical gate and schema validation succeed, tooling MUST recursively visit **every decoded object member name, every string value and every URI value in the complete PatentGrantManifest**, including nested objects, array items, map keys, free-form descriptions, notes, authority text, evidence metadata, provenance text, artifact metadata, ECL references and patent-source URIs.

No JSON path is implicitly exempt merely because its value is not copied into the attestation.

The validator classifies each visited value as exactly one of:

1. **schema-structured public value** — a value whose complete spelling is constrained by an ECL-PL grammar to a non-personal public namespace, such as a SHA-256 digest, PatentGrant ID, patent-publication key, claim number, fixed enum/URN, content-addressed bundle path, timestamp, ISO jurisdiction code, opaque identity token or pinned public key;
2. **reviewed free-form public text** — prose that contains no identity credential or credential derivative and whose privacy review is bound to the exact manifest hash; or
3. **reviewed public URI** — a URI whose raw and decoded components have been checked and whose identifier-bearing components resolve only to public non-personal records relevant to the grant.

Anything that cannot be placed unambiguously into one of those three classes fails closed.

### 1.2 Credential exclusion

For an individual manifest, tooling MUST reject any manifest member name, string or URI that contains, embeds, encodes or is deterministically derived from a plaintext government identifier or another low-entropy identity credential. This includes identifiers placed in fields unrelated to identity, such as `authority_representation`, `provenance.notes`, evidence descriptions, artifact descriptions or URI path/query/fragment components.

For reviewed free-form public text, decimal digits and identifier-like alphanumeric runs are not presumed safe. If such a run is not proven to be a public non-personal structured identifier already present in the manifest or its retained public evidence, validation fails. Labels or variants denoting SSN, DNI, passport, national ID, tax ID, social-security number or equivalent government credentials fail regardless of surrounding punctuation or case.

For reviewed public URIs, validation examines both the exact raw URI and each percent-decoded UTF-8 component. Invalid percent encoding, decoder replacement, recursive/double encoding, user-info credentials, or an identifier-bearing component whose public non-personal meaning cannot be established causes rejection. A URI is not safe merely because it uses HTTPS.

The privacy-review record MUST identify the exact manifest SHA-256, the validator/reviewer and the result of the complete recursive traversal. A privacy review of different manifest bytes is irrelevant.

### 1.3 Fields copied into the attestation

`patent_licensor.name` is a public display name, not an identifier transport. The root schema rejects digits, control characters, identifier-like delimiters and common government-identifier labels in that field.

`identity_reference.jurisdiction` MUST use exactly `ISO3166-1:<AA>` or `ISO3166-2:<AA-SUBDIVISION>` and MUST be verified against the immutable ISO evidence described in section 4. A live lookup against whatever ISO registry state happens to exist at validation time is not sufficient.

Every manifest value copied into the retained identity attestation MUST first pass its schema/profile grammar and the whole-manifest privacy traversal above. No free-form manifest string may be copied into an attestation field unless the profile explicitly identifies that field and its grammar.

These checks are in addition to the existing opaque-token, fixed media-type, content-addressed path and no-`record_uri` requirements.

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

## 4. Immutable ISO 3166 jurisdiction evidence

Jurisdiction validation for an individual MUST NOT depend on mutable live ISO registry state.

This section supersedes the exact identity-attestation member list in `spec/VERSIONING.md` section 10.2.3 for the fixed `ed25519-jcs-v1` profile by adding exactly one member named `jurisdiction_registry_snapshot`. No other additional attestation member is introduced by this rule.

`jurisdiction_registry_snapshot` is an object containing exactly:

```text
standard
edition_id
jurisdiction_code
bundle_path
sha256
media_type
```

with these requirements:

- `standard` is exactly `ISO-3166`;
- `edition_id` is a nonempty immutable edition/snapshot identifier supplied by the retained source and MUST NOT be `latest`, `current`, a mutable URL or another moving label;
- `jurisdiction_code` is exactly the manifest `identity_reference.jurisdiction`;
- `bundle_path` is exactly `evidence/identity/sha256/<64 lowercase hex>`;
- `sha256` is exactly the lowercase SHA-256 of the retained registry-snapshot bytes and MUST equal the digest embedded in `bundle_path`; and
- `media_type` is exactly `application/ecl-pl-iso3166-snapshot+json`.

The retained snapshot is a deterministic UTF-8 JSON evidence document that contains the edition identifier and the complete ISO country/subdivision record needed to resolve `jurisdiction_code`, including whether that code was assigned/valid in that edition. It is subject to the same strict UTF-8, duplicate-member and lone-surrogate rules as other retained JSON evidence.

This profile also narrowly supersedes `spec/VERSIONING.md` section 4 rules 5 and 11 for this profile-required **transitive identity evidence**: the ISO snapshot member referenced by the signed identity attestation MUST appear exactly once in `BUNDLE-INDEX` and exactly once as a physical regular-file member even though the PatentGrantManifest references it transitively through `attestation_snapshot`. It therefore participates in the canonical bundle identity.

Validation MUST:

1. hash the exact ISO snapshot member bytes and require equality with the attestation `sha256`, path digest and `BUNDLE-INDEX` digest;
2. parse only those retained bytes, never a live replacement, to resolve the jurisdiction;
3. require the retained evidence edition ID to equal the signed attestation `edition_id`;
4. require the retained record to resolve exactly the signed/manifest `jurisdiction_code`; and
5. fail closed if the snapshot, edition metadata or code status is missing, ambiguous or contradictory.

A validator may consult a live ISO service as supplementary information, but live state cannot override or replace the immutable snapshot that is cryptographically bound into the bundle.

## 5. Release-gate precedence

These checks run before a bundle may be marked operative. Passing JSON Schema validation alone does not satisfy this security profile. Any ambiguity in privacy classification, percent-decoding, point decoding, package entry type, registry snapshot identity or jurisdiction resolution fails closed.
