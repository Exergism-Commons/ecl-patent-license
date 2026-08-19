# ECL-PL Physical Container Profile

> **Status: mandatory architecture-stage serialization profile for individual PatentGrantBundles. No licensing effect.**

This document is the complete and exclusive definition of `urn:ecl-pl:container-profile:metadata-free-v1`, the container profile referenced by `spec/SECURITY-PROFILE.md` section 1.4. For that profile, this document narrows the generic archive/container wording in `SECURITY-PROFILE.md` and `VERSIONING.md`. A validator that gives this profile identifier any other outer-container meaning is non-conforming.

The profile binds exactly one decoder/grammar:

- container profile ID: `urn:ecl-pl:container-profile:metadata-free-v1`
- decoder profile ID: `urn:ecl-pl:container-decoder:eclplb1-v1`
- media type: `application/ecl-pl-bundle-v1`
- serialization name: `ECLPLB1`

ZIP, TAR, cpio, filesystem trees, MIME multipart, self-extracting archives, compressed archive wrappers, content sniffing and implementation-selected archive libraries are **not** alternate encodings of this profile.

## 1. Exact byte grammar

An `ECLPLB1` container is exactly the following byte sequence and nothing else. Integer fields are unsigned, fixed-width and big-endian.

```text
magic          = 8 bytes: 45 43 4c 50 4c 42 31 0a   # ASCII "ECLPLB1\n"
member_count   = uint32be
record[0]      = record
...
record[N-1]    = record
EOF            = immediate end of input

record         = path_length || path_bytes || content_length || content_bytes
path_length    = uint16be
path_bytes     = exactly path_length bytes
content_length = uint64be
content_bytes  = exactly content_length bytes
```

There are **no** flags, timestamps, permissions, owner/group values, comments, extra fields, checksums, compression methods, padding bytes, alignment bytes, filename aliases, directory records, trailers, indexes, signatures, optional headers or extension blocks in this outer format.

A conforming decoder MUST parse only by the grammar above. It MUST NOT probe the bytes as ZIP, TAR or another archive before, during or after `ECLPLB1` parsing.

## 2. Header, count and normative resource limits

The following limits are part of the `ECLPLB1` validity grammar and are identical for every conforming validator:

```text
MAX_MEMBER_COUNT          = 4096
MAX_PATH_LENGTH_BYTES     = 1024
MAX_MEMBER_CONTENT_BYTES  = 67108864    # 64 MiB
MAX_CONTAINER_BYTES       = 536870912   # 512 MiB
```

1. The first eight bytes MUST equal the magic exactly. A BOM, preamble or byte before the magic fails validation.
2. `member_count` MUST be at least 3 and at most `MAX_MEMBER_COUNT`.
3. The exact complete outer byte sequence MUST be no larger than `MAX_CONTAINER_BYTES`. A longer input is profile-invalid before member processing.
4. Every `content_length` MUST be at most `MAX_MEMBER_CONTENT_BYTES` as well as no greater than the remaining input. A larger declared member is profile-invalid.
5. The decoder MUST use checked integer arithmetic for every offset and length. Integer overflow, truncation, allocation wraparound or a declared length greater than the remaining input fails validation.
6. A conforming implementation MUST NOT impose a smaller value and call an otherwise profile-valid input invalid. If local memory, storage, CPU quota or another implementation resource prevents completion for an input within the normative limits, the result MUST be reported distinctly as **validation incomplete / resource exhausted**. That condition is neither a successful validation nor evidence that the package is profile-invalid, and the bundle MUST NOT become operative from that incomplete result.
7. Implementations SHOULD stream member payloads and hashing rather than require allocation of `MAX_CONTAINER_BYTES` or an entire member in memory.
8. The byte immediately following the final record payload MUST be EOF. Any suffix, trailer, second archive, concatenated payload or ignored bytes fail validation.

These limits intentionally make the accepted language independent of host-specific allocator, process-memory or library defaults. Two validators may differ in whether they can complete a validation in a constrained environment, but they MUST NOT disagree that an input is profile-invalid merely because one validator exhausted local resources.

## 3. Path decoding and ordering

For every record:

1. `path_length` MUST be in `1..MAX_PATH_LENGTH_BYTES`.
2. `path_bytes` MUST be strict UTF-8 with no BOM, replacement decoding, NUL, control characters or invalid sequence.
3. Decoding and then strict UTF-8 re-encoding MUST reproduce `path_bytes` byte-for-byte. No Unicode normalization, case folding, slash conversion or filesystem normalization is permitted.
4. The decoded path MUST satisfy the canonical PatentGrantBundle member-path rules in `spec/VERSIONING.md` and `spec/SECURITY-PROFILE.md`.
5. Record 0 MUST have path exactly `BUNDLE-INDEX`.
6. Records 1 through `N-1` MUST be in strictly increasing unsigned lexicographic order of their exact `path_bytes`.
7. Duplicate paths, byte-distinct aliases that the canonical path rules reject, or a second `BUNDLE-INDEX` fail validation.

Member identity is the exact validated path byte sequence. Extraction to a filesystem is never used to determine identity.

## 4. Payload rules

1. `content_length` names exactly the following number of payload bytes and MUST satisfy the normative limit in section 2.
2. The payload is not compressed, transformed, escaped, checksummed or normalized by the outer container.
3. The exact payload bytes are the bytes supplied to SHA-256 and to the member-specific decoder/privacy gate.
4. The outer decoder MUST NOT inspect a payload to infer an alternate outer-container boundary.
5. Nested/container-like bytes inside a member have no outer-container meaning. Whether such a member format is permitted is decided only by the member rules in `SECURITY-PROFILE.md`.

## 5. Binding to BUNDLE-INDEX

After record 0 has been read and `BUNDLE-INDEX` has passed its own canonical parser:

1. record 0 is the physical `BUNDLE-INDEX` and is **not** required or permitted to list its own digest; `spec/VERSIONING.md` section 4 rule 10 continues to forbid self-listing;
2. the SHA-256 of record 0's exact payload MUST nevertheless be computed and bound separately wherever the PatentGrantBundle identity, release record and privacy-review record require the exact `BUNDLE-INDEX` digest;
3. the set of paths in records 1 through `N-1` MUST equal exactly the set of non-index physical member paths required by that `BUNDLE-INDEX` plus the transitive-evidence rules applicable to the bundle;
4. every indexed path MUST occur exactly once as one record in records 1 through `N-1`;
5. no record in records 1 through `N-1` may exist outside that required set;
6. for **records 1 through `N-1` only**, each payload SHA-256 MUST equal the digest declared for that path by `BUNDLE-INDEX` or the applicable fixed/transitive rule; and
7. `member_count` MUST equal one plus the number of non-index physical members.

A mismatch in count, path set or any required digest fails closed before legal-operativeness processing. The `BUNDLE-INDEX` digest is deliberately verified through the separate bundle-identity/release/privacy bindings rather than by a prohibited self-entry.

## 6. Single-decoder and anti-polyglot rule

For an individual PatentGrantBundle, `ECLPLB1` is a protocol object, not a generic archive. Conformance is determined only by `urn:ecl-pl:container-decoder:eclplb1-v1`.

A validator MUST:

- reject automatic archive-format detection and extension/MIME heuristics;
- reject fallback to another parser after any `ECLPLB1` parsing error;
- reject any implementation mode that ignores unknown, malformed or trailing bytes;
- reject any wrapper that presents different logical members than the exact `ECLPLB1` parse;
- never treat an extracted directory tree as the reviewed package; and
- never accept a second interpretation produced by ZIP/TAR/filesystem APIs as normative ECL-PL state.

Thus two conforming validators given the same outer byte sequence and able to complete validation either derive the same ordered `(path_bytes, content_bytes)` records or both reject it for the same profile rule. A local resource-exhaustion result defined in section 2 is not an alternate validity judgment. Library-specific archive behavior has no authority to expand acceptance.

## 7. Privacy-review binding

For `urn:ecl-pl:container-profile:metadata-free-v1`, the privacy-review record required by `SECURITY-PROFILE.md` MUST identify both:

```text
container_profile_id = urn:ecl-pl:container-profile:metadata-free-v1
container_decoder_profile_id = urn:ecl-pl:container-decoder:eclplb1-v1
container_media_type = application/ecl-pl-bundle-v1
outer_container_sha256 = <sha256 of the exact complete ECLPLB1 byte sequence>
```

The record MUST additionally bind the exact ordered record inventory `(path, content_length, sha256)` produced by the decoder, including the separately computed SHA-256 of record 0 (`BUNDLE-INDEX`). Re-encoding the same logical members creates different outer bytes unless the serialization is byte-for-byte identical and therefore requires a different `outer_container_sha256` and a new physical-package privacy review.

## 8. Fail-closed precedence

Any ambiguity about magic, integer decoding, normative size limits, record boundary, UTF-8 path bytes, canonical path identity, ordering, count, payload extent, EOF, member-set equality, digest binding or decoder selection is a validation failure.

Local resource exhaustion for an otherwise within-limit input is not such an ambiguity: it is an incomplete validation and MUST NOT be converted into either acceptance or profile-invalid status.

No implementation-defined archive behavior may repair, reinterpret or normalize malformed `ECLPLB1` bytes.
