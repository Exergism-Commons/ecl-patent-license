# ECL-PL Physical Container Profile

> **Status: mandatory architecture-stage serialization profile for every operative PatentGrantBundle. No licensing effect.**

This document is the complete and exclusive definition of `urn:ecl-pl:container-profile:metadata-free-v1`, the physical container profile for **every operative PatentGrantBundle, regardless of `subject_type`**. It narrows and, where necessary, explicitly supersedes generic archive/container wording in `spec/SECURITY-PROFILE.md` and `spec/VERSIONING.md`. A validator that gives this profile identifier any other outer-container meaning, or accepts a different physical packaging profile for an operative bundle, is non-conforming.

In particular, the generic “archive format” language in `spec/SECURITY-PROFILE.md` section 3 and any packaging-neutral wording in `spec/VERSIONING.md` do **not** authorize ZIP, TAR, filesystem trees or another implementation-selected outer format for non-individual bundles. Section 1.4 of `SECURITY-PROFILE.md` adds individual-specific privacy-review obligations; it does not limit this physical-container requirement to individuals.

The profile binds exactly one decoder/grammar:

- container profile ID: `urn:ecl-pl:container-profile:metadata-free-v1`
- decoder profile ID: `urn:ecl-pl:container-decoder:eclplb1-v1`
- media type: `application/ecl-pl-bundle-v1`
- serialization name: `ECLPLB1`

ZIP, TAR, cpio, filesystem trees, MIME multipart, self-extracting archives, compressed archive wrappers, content sniffing and implementation-selected archive libraries are **not** alternate encodings of this profile for any operative bundle.

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
4. **Record 0 is a special physical path outside the indexed-member grammar.** Its decoded path MUST be exactly `BUNDLE-INDEX`. The canonical indexed-member path grammar in `spec/VERSIONING.md` section 4 rule 6 and the evidence-path grammars in `spec/SECURITY-PROFILE.md` do not apply to record 0, because `BUNDLE-INDEX` is deliberately excluded from its own index.
5. For records 1 through `N-1`, the decoded path MUST satisfy the canonical indexed PatentGrantBundle member-path rules in `spec/VERSIONING.md` and `spec/SECURITY-PROFILE.md`.
6. Records 1 through `N-1` MUST be in strictly increasing unsigned lexicographic order of their exact `path_bytes`.
7. Duplicate paths, byte-distinct aliases that the canonical path rules reject, or a second `BUNDLE-INDEX` fail validation.

Member identity is the exact validated path byte sequence. Extraction to a filesystem is never used to determine identity.

## 4. Payload rules

1. `content_length` names exactly the following number of payload bytes and MUST satisfy the normative limit in section 2.
2. The payload is not compressed, transformed, escaped, checksummed or normalized by the outer container.
3. The exact payload bytes are the bytes supplied to SHA-256 and to the member-specific decoder/privacy gate.
4. The outer decoder MUST NOT inspect a payload to infer an alternate outer-container boundary.
5. Nested/container-like bytes inside a member have no outer-container meaning. Whether such a member format is permitted is decided only by the member rules in `SECURITY-PROFILE.md`.

### 4.1 Raw JSON numeric-lexeme rule for individual manifests and evidence

For an individual bundle, this profile explicitly narrows the JSON traversal in `spec/SECURITY-PROFILE.md` sections 1.1, 1.2 and 1.4 so that **every JSON number token in the PatentGrantManifest and every retained JSON member is inspected from its exact raw lexeme before semantic parsing**. Number tokens are therefore part of the whole-manifest privacy gate even though section 1.1 also performs a decoded-value traversal after schema validation.

Before any JSON parser may coerce a number to IEEE-754, arbitrary precision, a language-native integer, scientific notation or another numeric representation, the same lossless raw-token pass used for duplicate-name and surrogate checks MUST expose every exact JSON numeric lexeme and its exact JSON path. A validator that parses or rounds the number first and then applies schema/privacy checks is non-conforming.

For this rule, an exact JSON path is a canonical RFC 6901 JSON Pointer built from the **losslessly decoded JSON structure**, never from raw source spellings of object-member tokens. Before a member name contributes a path segment, the raw tokenizer MUST decode all JSON string escapes to their Unicode scalar values under the same lone-surrogate rejection rule used by the raw lexical gate. It MUST NOT apply Unicode normalization, case folding or any other character transformation. The decoded member name is then escaped for RFC 6901 by replacing `~` with `~0` and `/` with `~1`, in that order. Array-element segments, where applicable, are the structural zero-based array index written as canonical decimal `0 | [1-9][0-9]*`, not text recovered from a value token. The root is the empty pointer. Because duplicate decoded member names are rejected before parsing, two escape spellings that decode to the same member name necessarily produce the same path and cannot create separate objects. Thus `"start_line"` and `"start_\u006cine"` both contribute the exact segment `start_line` and are tested against the same numeric-path entry.

#### 4.1.1 PatentGrantManifest numeric fields

For the exact closed schema-set v2 bound by `schemas/schema-set.json`, the only JSON-number fields in a `PatentGrantManifest` are:

```text
/ecl_bundle_reference/patent_specific_effect/trigger/policy_item/start_line
/ecl_bundle_reference/patent_specific_effect/trigger/policy_item/line_count
```

At either path, the exact raw token MUST match:

```text
[1-9][0-9]*
```

before any conversion. `start_line` MUST then be range-checked exactly as an arbitrary-precision integer in `1..1000000`; `line_count` MUST be range-checked exactly in `1..100000`.

Any other JSON number token anywhere in a schema-set-v2 manifest fails closed because no other manifest path is authorized to carry a JSON number. At the two authorized paths, `+`, `-`, decimal points, exponent markers (`e`/`E`), leading zeroes, whitespace inside the token or any other spelling fails before schema validation. Thus values such as `1.0`, `1e0`, `1.0000000000000000123456789` and `01` cannot be rounded or coerced into the integer `1` by a permissive parser.

The raw-token gate is an additional precondition to JSON Schema validation; it does not replace the schema. After the lexeme and exact-range checks pass, the normal closed schema-set validation MUST still accept the parsed manifest. If a future schema-set version adds, removes or changes a numeric field, that new immutable schema-set/profile MUST publish its own exact numeric-path table, raw grammar and range semantics; validators MUST NOT infer new numeric paths from implementation-local schema coercion.

#### 4.1.2 Retained JSON evidence and registry/snapshot members

For privacy-reviewed retained JSON evidence, the default rule is **numeric tokens forbidden**. A numeric token is accepted only when the exact immutable member/profile definition explicitly enumerates that JSON path as a public numeric field and supplies all of the following normative data:

1. one exact accepted raw-token grammar;
2. an exact inclusive semantic range;
3. the public, non-personal meaning of the field; and
4. a rule that no alternate numeric spelling denotes the same accepted value.

Unless a fixed profile states otherwise for one named path, the only permitted numeric grammar is canonical non-negative decimal integer text:

```text
0 | [1-9][0-9]*
```

A permitted path using that default grammar MUST reject `+`, `-`, decimal points, exponent markers (`e`/`E`), leading zeroes on nonzero values, whitespace, or any other spelling. Implementations MUST compare the exact raw token to that grammar before numeric conversion. They MUST NOT expand exponents, strip zeroes, round, parse to floating point, convert to a normalized numeric value, or use numeric equality to decide privacy acceptance.

Accordingly, tokens such as `1.23456789e8`, `123456789e0`, `0123456789`, `123456789.0` and `+123456789` are categorically rejected at every privacy-reviewed evidence path unless a future immutable profile explicitly defines that exact spelling class for that exact path. An unclassified canonical integer such as `123456789` is also rejected because no public numeric field/path authorizes it.

After a token passes the path-specific raw grammar, its exact integer value MUST be range-checked using arbitrary-precision integer arithmetic; overflow, precision loss or inability to perform the exact range check fails closed. The accepted spelling remains the raw token, not a reserialized value.

For retained JSON evidence and registry/snapshot members, an implementation MUST be able to identify the governing immutable profile and exact authorized numeric paths before accepting any numeric token. If no such path rule exists, or if the tokenizer cannot preserve the exact token/path before semantic parsing, the individual bundle is non-conforming.

## 5. Binding to BUNDLE-INDEX

After record 0 has been read and `BUNDLE-INDEX` has passed its own canonical parser:

1. record 0 is the physical `BUNDLE-INDEX` and is **not** required or permitted to list its own digest; `spec/VERSIONING.md` section 4 rule 10 continues to forbid self-listing;
2. the SHA-256 of record 0's exact payload MUST nevertheless be computed and bound separately wherever the PatentGrantBundle identity, release record and privacy-review record require the exact `BUNDLE-INDEX` digest;
3. the set of paths in records 1 through `N-1` MUST equal exactly the set of non-index member paths listed by that canonical `BUNDLE-INDEX`;
4. before the container can proceed to legal-operativeness processing, the `BUNDLE-INDEX` member set itself MUST independently satisfy the applicable evidence-closure rules in `spec/VERSIONING.md` and `spec/SECURITY-PROFILE.md`; those rules cannot authorize an unindexed physical member;
5. every indexed path MUST occur exactly once as one record in records 1 through `N-1`, and no record in that range may exist outside the indexed set;
6. for **records 1 through `N-1` only**, each payload SHA-256 MUST equal the digest declared for that exact path by `BUNDLE-INDEX`; and
7. `member_count` MUST equal one plus the number of indexed non-index members.

A mismatch in count, path set or any required digest fails closed before legal-operativeness processing. The `BUNDLE-INDEX` digest is deliberately verified through the separate bundle-identity/release/privacy bindings rather than by a prohibited self-entry.

## 6. Single-decoder and anti-polyglot rule

For **every operative PatentGrantBundle**, `ECLPLB1` is a protocol object, not a generic archive. Conformance is determined only by `urn:ecl-pl:container-decoder:eclplb1-v1`.

A validator MUST:

- reject automatic archive-format detection and extension/MIME heuristics;
- reject fallback to another parser after any `ECLPLB1` parsing error;
- reject any implementation mode that ignores unknown, malformed or trailing bytes;
- reject any wrapper that presents different logical members than the exact `ECLPLB1` parse;
- never treat an extracted directory tree as the reviewed package; and
- never accept a second interpretation produced by ZIP/TAR/filesystem APIs as normative ECL-PL state.

Thus two conforming validators given the same outer byte sequence and able to complete validation either derive the same ordered `(path_bytes, content_bytes)` records or both reject it for the same profile rule. A local resource-exhaustion result defined in section 2 is not an alternate validity judgment. Library-specific archive behavior has no authority to expand acceptance.

The generic archive/member-type language in `spec/SECURITY-PROFILE.md` section 3 is therefore interpreted only as fail-closed logical-member requirements inside this exact serialization. `ECLPLB1` itself has no archive entry-type metadata, directory entries, links, special filesystem nodes or alternate outer member semantics: each parsed record is exactly one finite data stream by grammar.

## 7. Privacy-review binding

For an individual bundle using `urn:ecl-pl:container-profile:metadata-free-v1`, the privacy-review record required by `SECURITY-PROFILE.md` MUST identify both:

```text
container_profile_id = urn:ecl-pl:container-profile:metadata-free-v1
container_decoder_profile_id = urn:ecl-pl:container-decoder:eclplb1-v1
container_media_type = application/ecl-pl-bundle-v1
outer_container_sha256 = <sha256 of the exact complete ECLPLB1 byte sequence>
```

The record MUST additionally bind the exact ordered record inventory `(path, content_length, sha256)` produced by the decoder, including the separately computed SHA-256 of record 0 (`BUNDLE-INDEX`). Re-encoding the same logical members creates different outer bytes unless the serialization is byte-for-byte identical and therefore requires a different `outer_container_sha256` and a new physical-package privacy review.

Non-individual bundles are subject to the same exact physical serialization and anti-polyglot rules even when this individual privacy-review record is not applicable.

## 8. Fail-closed precedence

Any ambiguity about magic, integer decoding, normative size limits, record boundary, UTF-8 path bytes, record-0 special path, canonical indexed path identity, ordering, count, payload extent, EOF, member-set equality, digest binding or decoder selection is a validation failure.

Local resource exhaustion for an otherwise within-limit input is not such an ambiguity: it is an incomplete validation and MUST NOT be converted into either acceptance or profile-invalid status.

No implementation-defined archive behavior may repair, reinterpret or normalize malformed `ECLPLB1` bytes.
