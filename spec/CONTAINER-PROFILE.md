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

## 2. Header and count rules

1. The first eight bytes MUST equal the magic exactly. A BOM, preamble or byte before the magic fails validation.
2. `member_count` MUST be at least 3 and at most 4096.
3. The decoder MUST use checked integer arithmetic for every offset and length. Integer overflow, truncation, allocation wraparound or a declared length greater than the remaining input fails validation.
4. An implementation MAY impose a smaller documented resource limit, but exceeding that implementation limit is a validation failure; it MUST NOT truncate, stream-skip or reinterpret the record.
5. The byte immediately following the final record payload MUST be EOF. Any suffix, trailer, second archive, concatenated payload or ignored bytes fail validation.

## 3. Path decoding and ordering

For every record:

1. `path_length` MUST be in `1..1024`.
2. `path_bytes` MUST be strict UTF-8 with no BOM, replacement decoding, NUL, control characters or invalid sequence.
3. Decoding and then strict UTF-8 re-encoding MUST reproduce `path_bytes` byte-for-byte. No Unicode normalization, case folding, slash conversion or filesystem normalization is permitted.
4. The decoded path MUST satisfy the canonical PatentGrantBundle member-path rules in `spec/VERSIONING.md` and `spec/SECURITY-PROFILE.md`.
5. Record 0 MUST have path exactly `BUNDLE-INDEX`.
6. Records 1 through `N-1` MUST be in strictly increasing unsigned lexicographic order of their exact `path_bytes`.
7. Duplicate paths, byte-distinct aliases that the canonical path rules reject, or a second `BUNDLE-INDEX` fail validation.

Member identity is the exact validated path byte sequence. Extraction to a filesystem is never used to determine identity.

## 4. Payload rules

1. `content_length` names exactly the following number of payload bytes.
2. The payload is not compressed, transformed, escaped, checksummed or normalized by the outer container.
3. The exact payload bytes are the bytes supplied to SHA-256 and to the member-specific decoder/privacy gate.
4. The outer decoder MUST NOT inspect a payload to infer an alternate outer-container boundary.
5. Nested/container-like bytes inside a member have no outer-container meaning. Whether such a member format is permitted is decided only by the member rules in `SECURITY-PROFILE.md`.

## 5. Binding to BUNDLE-INDEX

After record 0 has been read and `BUNDLE-INDEX` has passed its own canonical parser:

1. the set of paths in records 1 through `N-1` MUST equal exactly the set of physical member paths required by that `BUNDLE-INDEX` plus the transitive-evidence rules applicable to the bundle;
2. every indexed path MUST occur exactly once as one record;
3. no record may exist outside that required set;
4. every record payload SHA-256 MUST equal the digest declared for that path by `BUNDLE-INDEX` or the applicable fixed/transitive rule; and
5. `member_count` MUST equal one plus the number of non-index physical members.

A mismatch in count, path set or digest fails closed before legal-operativeness processing.

## 6. Single-decoder and anti-polyglot rule

For an individual PatentGrantBundle, `ECLPLB1` is a protocol object, not a generic archive. Conformance is determined only by `urn:ecl-pl:container-decoder:eclplb1-v1`.

A validator MUST:

- reject automatic archive-format detection and extension/MIME heuristics;
- reject fallback to another parser after any `ECLPLB1` parsing error;
- reject any implementation mode that ignores unknown, malformed or trailing bytes;
- reject any wrapper that presents different logical members than the exact `ECLPLB1` parse;
- never treat an extracted directory tree as the reviewed package; and
- never accept a second interpretation produced by ZIP/TAR/filesystem APIs as normative ECL-PL state.

Thus two conforming validators given the same outer byte sequence either derive the same ordered `(path_bytes, content_bytes)` records or both reject it. Library-specific archive behavior has no authority to expand acceptance.

## 7. Privacy-review binding

For `urn:ecl-pl:container-profile:metadata-free-v1`, the privacy-review record required by `SECURITY-PROFILE.md` MUST identify both:

```text
container_profile_id = urn:ecl-pl:container-profile:metadata-free-v1
container_decoder_profile_id = urn:ecl-pl:container-decoder:eclplb1-v1
container_media_type = application/ecl-pl-bundle-v1
outer_container_sha256 = <sha256 of the exact complete ECLPLB1 byte sequence>
```

The record MUST additionally bind the exact ordered record inventory `(path, content_length, sha256)` produced by the decoder. Re-encoding the same logical members creates different outer bytes unless the serialization is byte-for-byte identical and therefore requires a different `outer_container_sha256` and a new physical-package privacy review.

## 8. Fail-closed precedence

Any ambiguity about magic, integer decoding, record boundary, UTF-8 path bytes, canonical path identity, ordering, count, payload extent, EOF, member-set equality or decoder selection is a validation failure.

No implementation-defined archive behavior may repair, reinterpret or normalize malformed `ECLPLB1` bytes.
