# ECL-PL Security Profile

> **Status: mandatory architecture-stage release gate. No licensing effect.**

This document adds fail-closed security requirements to the release workflow in `spec/VERSIONING.md`. It does not relax any rule in that specification. Where this document narrows an acceptance rule, the narrower rule controls for a conforming ECL-PL release validator.

## 1. Individual-manifest privacy gate

For `patent_licensor.identity_reference.subject_type: individual`, release validation MUST apply the root-schema individual constraints before any identity attestation is accepted.

- `patent_licensor.name` is a public display name, not an identifier transport. The root schema rejects digits, control characters, identifier-like delimiters and common government-identifier labels in that field.
- `identity_reference.jurisdiction` MUST use exactly `ISO3166-1:<AA>` or `ISO3166-2:<AA-SUBDIVISION>` and the code MUST resolve in the authoritative ISO 3166 registry. A syntactically matching nonexistent code fails closed.
- Every manifest value copied into the retained identity attestation MUST first pass its schema/profile grammar. No free-form manifest string may be copied into an attestation field unless the profile explicitly identifies that field and its grammar.
- Release tooling MUST reject an individual manifest if any copied value contains, embeds or is deterministically derived from a plaintext government identifier or other low-entropy identity credential.

These checks are in addition to the existing opaque-token, fixed media-type, content-addressed path and no-`record_uri` requirements.

## 2. Strict Ed25519 verification profile

The fixed profile `urn:ecl-pl:identity-attestation-profile:ed25519-jcs-v1` uses RFC 8032 Ed25519 with **one strict verification rule**. Permissive/cofactored verification, ZIP-215-style acceptance, or library-specific acceptance of noncanonical/small-order encodings is non-conforming.

Let `A_encoded` be the exact 32 public-key bytes from `attestation_verifier.public_key`, let the 64-byte signature be `R_encoded || S_encoded`, and let `M` be the exact RFC 8785 JCS UTF-8 message defined by the identity-attestation profile.

A conforming validator MUST:

1. Strictly base64url-decode and re-encode `A_encoded`; require exactly 32 bytes and byte-for-byte canonical round-trip.
2. Decode `A_encoded` as the canonical RFC 8032 compressed Edwards25519 point. The encoded `y` coordinate MUST be `< p`, where `p = 2^255 - 19`, and the sign bit MUST identify one unique curve point. Noncanonical encodings fail.
3. Reject decoding failure, the identity point, every small-order/torsion point and every point outside the prime-order subgroup. With `L = 2^252 + 27742317777372353535851937790883648493`, require `[L]A = identity` and `A != identity`.
4. Strictly decode `R_encoded` using the same canonical point rules. Reject identity, small-order/torsion and non-prime-subgroup `R`; require `[L]R = identity` and `R != identity`.
5. Parse `S_encoded` as the RFC 8032 little-endian scalar and require the canonical bound `0 <= S < L`.
6. Compute `k = SHA-512(R_encoded || A_encoded || M)` as a little-endian integer reduced modulo `L`.
7. Accept the signature **only** if the exact equation `[S]B = R + [k]A` holds. A validator MUST NOT multiply either side by the cofactor and MUST NOT substitute a relaxed/cofactored equation.
8. Fail closed if its cryptographic library cannot expose or guarantee all canonical-decoding, subgroup and scalar checks above.

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

## 4. Release-gate precedence

These checks run before a bundle may be marked operative. Passing JSON Schema validation alone does not satisfy this security profile. Any ambiguity in point decoding, package entry type, privacy classification or registry resolution fails closed.
