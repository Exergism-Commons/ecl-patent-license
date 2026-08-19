# ECL-PL Versioning and Immutable Patent Grants

> **Status: architecture draft. No licensing effect.**

## 1. Core invariant

ECL-PL separates reusable legal text from project-specific patent grants:

```text
PatentLicenseRelease
        +
PatentGrantManifest
        +
retained evidence members
        =
PatentGrantBundle
```

Every stable component and every operative bundle must be immutable and content-addressable. A manifest, evidence snapshot or maintainer action is not independently sufficient to create patent rights.

The **legal grant core** is the exact `PatentLicenseRelease` plus the exact `PatentGrantManifest`. Retained evidence participates in bundle identity and reproducible release validation; it does not silently become additional licence text or grant terms unless the legal core expressly gives a retained artifact normative effect.

## 2. PatentLicenseRelease

A `PatentLicenseRelease` is an exact immutable legal-text artifact. Stable releases should use semantic-versioning discipline:

- `MAJOR` — materially incompatible change to grant scope, termination, definitions, restrictions, downstream rights or legal model;
- `MINOR` — legally substantive but intended-compatible addition or clarification;
- `PATCH` — non-substantive editorial/corrective change only.

Any uncertainty over whether a change affects rights should be treated conservatively as legally substantive and receive delta review. A version label is not operative identity; every release also has a cryptographic content hash.

## 3. PatentGrantManifest identity

A `PatentGrantManifest` is an immutable declaration tied to one Patent Licensor and one exact PatentLicenseRelease.

Suggested display form:

```text
ECL-PG-<issuer-slug>-<YYYYMMDD>-<sequence>
```

The human-readable identifier is not sufficient. The exact manifest bytes must be content-addressed.

## 4. PatentGrantBundle format and identity

A `PatentGrantBundle` is a closed immutable member set. It contains:

```text
BUNDLE-INDEX
license/PATENT-LICENSE
manifest/patent-grant.json
evidence/...
```

`BUNDLE-INDEX` defines the bundle identity and all retained evidence. Its exact byte format is `ECL-PL-BUNDLE-INDEX-v1`:

1. UTF-8 only, no BOM.
2. LF (`0x0A`) is the only line separator.
3. The first line is exactly `ECL-PL-BUNDLE-INDEX-v1`.
4. Every subsequent line is exactly `<lowercase-sha256><two ASCII spaces><canonical-member-path>`.
5. The member set is **exactly** `license/PATENT-LICENSE`, `manifest/patent-grant.json`, and every evidence member required by the applicable evidence-closure rule. The default closure begins with evidence directly referenced by the manifest; an applicable fixed release/security profile may require additional content-addressed transitive evidence. An evidence member outside the applicable closure or any other additional member is invalid even if listed in the index.
6. A `canonical-member-path` is exactly one of the two fixed paths above, `evidence/patents/sha256/<64 lowercase hex>`, or `evidence/identity/sha256/<64 lowercase hex>`. No other spelling is indexable.
7. Entries are sorted by the raw UTF-8 bytes of `canonical-member-path`, ascending.
8. Each member path occurs exactly once.
9. The index has one mandatory terminal LF.
10. `BUNDLE-INDEX` does not list itself.
11. A conforming package contains exactly `BUNDLE-INDEX` plus the members listed by it; missing, unindexed, duplicate or extra physical members are rejected.
12. Package validation is performed over the raw member-name byte strings before extraction. Every raw member name must equal its indexed canonical name byte-for-byte and must satisfy rule 6. Backslashes, dot segments, repeated separators, percent-encoded aliases, Unicode variants, case variants, trailing dots/spaces, symlinks, hardlinks and any name requiring platform normalization are invalid.
13. **No target-platform collision rule is used.** The validation result is platform-independent: because every accepted raw name is already in the closed canonical namespace, a package that depends on POSIX, Windows, macOS, archive-library or filesystem normalization is non-conforming. Extraction occurs only after validation and only to a destination capable of representing every accepted name exactly; otherwise extraction is refused.

For every listed member, tooling hashes the exact member bytes and requires equality with the digest in the index. No path normalization, filesystem aliasing, symlink following, case folding, percent decoding, Unicode normalization or separator rewriting is permitted during lookup.

The two legal-core members have additional cross-object binding requirements. After `manifest/patent-grant.json` has passed the raw lexical gate and closed-schema validation, tooling MUST compute:

```text
license_member_sha256 = SHA-256(exact bytes of license/PATENT-LICENSE)
manifest_member_sha256 = SHA-256(exact bytes of manifest/patent-grant.json)
```

It MUST then require, byte-for-byte as lowercase hexadecimal SHA-256 values:

```text
license_member_sha256
  = BUNDLE-INDEX digest for license/PATENT-LICENSE
  = PatentGrantManifest.patent_license.sha256

manifest_member_sha256
  = BUNDLE-INDEX digest for manifest/patent-grant.json
```

A manifest that names or hashes a different PatentLicenseRelease than the exact `license/PATENT-LICENSE` member is bundle-invalid even if both files independently hash correctly. `patent_license.id` is display/resolution metadata for that same immutable release and MUST NOT be used to override a hash mismatch.

The canonical machine identity is:

```text
ECL-PL-BUNDLE-v1:<sha256-of-exact-BUNDLE-INDEX-bytes>
```

Therefore two packages cannot have the same bundle identity while differing in the license, manifest, retained evidence, member names or member hashes. Display identifiers may additionally mention the PatentLicenseRelease and PatentGrantManifest IDs, but they are not the canonical bundle identity.

Every **operative** PatentGrantBundle is physically serialized only as the exact `ECLPLB1` protocol object defined by `spec/CONTAINER-PROFILE.md`. The logical member rules in this section do not authorize ZIP, TAR, raw filesystem trees or implementation-selected packaging alternatives.

## 5. No retroactive mutation

Once a PatentGrantManifest or PatentGrantBundle is released, later changes must not edit that historical object in place. Addition/removal of patent identifiers, authority changes, assignments, encumbrances, patent status changes, ECL references, downstream policy, Covered Implementation, evidence or termination state require new immutable records as appropriate.

A later manifest may supersede a prior manifest for future use, but cannot rewrite the earlier legal or evidentiary state.

## 6. Legal changes versus provenance changes

A legal grant change alters permissions, restrictions, termination or grant scope and requires a new legally effective artifact plus applicable legal review.

A provenance/status event records facts such as assignment, expiration, abandonment or validation. Repository tooling may preserve evidence and chronology but must not manufacture legal consequences that depend on applicable law.

## 7. Optional ECL Bundle reference

An ECL policy reference must identify one exact immutable ECL Bundle by content hashes. A mutable ECL channel must never be an operative reference, and a later ECL Bundle cannot mutate an existing PatentGrantManifest.

## 8. Draft versus operative

The repository distinguishes at least:

```text
draft
candidate
operative
superseded
withdrawn
```

`operative` is reserved for an exact PatentGrantBundle that passes all release/legal-review gates. `operative` is a Bundle state, not a permitted standalone `PatentGrantManifest.status` value.

A Bundle being newly marked operative MUST embed a manifest whose exact immutable `status` value is `candidate`. A manifest whose status is `draft`, `superseded` or `withdrawn` is ineligible for a new operative transition.

The schema intentionally permits unresolved values while authoring drafts/candidates. Before a Bundle may be marked operative, release tooling MUST reject every normative `not-decided` value in:

- `claim_scope.later_acquired_claims`;
- `claim_scope.combination_expansion`;
- `downstream_policy.model`;
- `downstream_policy.sublicensing`;
- `downstream_policy.have_made` when present;
- `downstream_policy.affiliates` when present;
- `downstream_policy.contract_manufacturers` when present;
- `downstream_policy.customers` when present;
- `defensive_termination.profile` when `defensive_termination` is present; and
- `defensive_termination.cure_or_withdrawal` when that field is present.

This semantic-completion gate does not convert provenance uncertainty such as a historically `unknown` patent status into a legal conclusion; it only prevents an operative grant from carrying unresolved normative choices about its own grant mechanics.

In addition, `provenance.authority_checked` MUST be the JSON boolean `true` before a Bundle may become operative. A schema-valid candidate that explicitly records `authority_checked: false` is ineligible for the operative transition; maintainer publication or the Patent Licensor's later assent does not retroactively substitute for the required authority/provenance check.

The bundle must not become operative unless the named Patent Licensor, through an authenticated person or mechanism with asserted authority to bind that Patent Licensor, performs an attributable approval/adoption act cryptographically bound to the exact bundle identity. Maintainer publication or approval cannot substitute for Patent Licensor assent.

Merge to `main`, schema validity, maintainer signature, GitHub release publication, a patent number, review of different bytes, or an operative ECL Bundle do not independently make an ECL-PL grant operative.

## 9. Immutable legal-review and validation-profile inputs

Stable/operative artifacts require legal review bound to immutable inputs. Historical review inputs and records must remain frozen and content-addressed; later edits to canonical specifications do not retroactively alter what a reviewer reviewed. Material changes require delta review.

The immutable release/revalidation record MUST additionally bind the exact bytes or SHA-256 of every normative validation input used for that operative decision, including at least:

- `spec/VERSIONING.md`;
- `spec/SECURITY-PROFILE.md`;
- `spec/CONTAINER-PROFILE.md`;
- `schemas/schema-set.json` and every schema resource listed by it; and
- `spec/COMPOSITION-WITH-ECL.md` when an operative ECL policy reference is present.

A human-readable profile ID or a later file carrying the same profile ID is not permission to substitute changed semantics. Revalidation of an historical operative record uses the exact retained/content-addressed validation inputs bound to that record, not mutable repository `main`, local defaults or a later specification revision.

## 10. Grant release workflow

Expected workflow:

```text
1. Select exact stable PatentLicenseRelease
2. Prepare exact PatentGrantManifest bytes
3. Validate raw JSON lexical safety before general parsing
4. Parse and validate syntax/schema using the closed schema resource set
5. If targeting operativeness, enforce manifest-status, authority-checked and normative semantic-completion gates
6. Recompute all declared hashes and enforce the legal-core license/manifest-to-index equality invariants
7. Resolve patent-publication identities and authoritative state
8. Verify every retained evidence member against BUNDLE-INDEX and the applicable evidence closure
9. Verify the Patent Licensor identity reference, pinned attestation profile, verifier key and retained attestation
10. Resolve any exact ECL Bundle reference
11. Complete grant-specific legal/policy review
12. Construct canonical BUNDLE-INDEX and verify the closed member set
13. Serialize and validate the exact ECLPLB1 physical container
14. Compute exact PatentGrantBundle identity and required physical-package hashes
15. Bind the immutable validation-profile/release inputs
16. Obtain authenticated Patent Licensor approval bound to that identity
17. Preserve and verify the approval record
18. Mark operative only if every gate passes
```

### 10.1 Patent publication identity and state

For every property key in `claim_scope.enumerated_claims` and `known_patents`, tooling must:

1. resolve the publication using the registry namespace embedded in `patentDocumentKey`;
2. require the registry's canonical publication identifier to round-trip exactly to the manifest key;
3. require `source` to identify the same publication record;
4. derive/verify `kind` and reject contradictions;
5. verify `status` at `provenance.recorded_at`;
6. resolve `evidence_snapshot.bundle_path` by literal canonical member-name equality in `BUNDLE-INDEX`;
7. require that path to be exactly `evidence/patents/sha256/<digest>` and appear exactly once in the index;
8. hash the exact retained member bytes and require the path digest, index digest and `evidence_snapshot.sha256` all to equal that recomputed digest;
9. require those bytes to substantiate the same publication identity, kind and status; and
10. fail closed on missing, ambiguous, contradictory, aliased or unavailable data.

Evidence bytes are bundle members, not mutable external snapshot identities.

`provenance.recorded_at` requires a definite instant: `Z` or a known numeric UTC offset. RFC 3339 `-00:00` is rejected.

For USPTO identifiers, A1/A2 application-publication identities use the 2001+ year-plus-seven-digit publication series. B1/B2 utility-patent kind codes are only valid for grants issued on or after January 2, 2001; the schema therefore excludes numbers below `6,167,569`. Registry round-trip remains mandatory; schema syntax alone is never evidence that a document exists.

### 10.2 Patent Licensor identity schemes and individual-attestation profile

Legal entities may use public, authority-specific identifiers (`lei`, `company-registry`, `court-or-agency-record`, or `other-authoritative-registry`), but the mutable HTTPS locator is provenance only. Every legal-entity identity reference MUST also carry `record_snapshot`, and that snapshot MUST retain the exact reviewed registry/authority response as an indexed content-addressed identity-evidence member.

For a legal entity, release tooling MUST:

1. require `record_snapshot.bundle_path` to be exactly `evidence/identity/sha256/<record_snapshot.sha256>`;
2. require that path exactly once in `BUNDLE-INDEX`;
3. hash the exact retained member bytes and require the path digest, index digest and `record_snapshot.sha256` all to equal the recomputed digest;
4. require `record_snapshot.media_type = application/json`;
5. require `record_snapshot.decoder_profile_id = urn:ecl-pl:identity-evidence-decoder:strict-json-v1`;
6. decode only the retained bytes, never a fresh response from `record_uri`, when reproducing the release decision; and
7. require the decoded retained record to substantiate the same exact `scheme`, `identifier`, `jurisdiction` and authoritative record represented by `record_uri`, under the exact reviewed scheme-specific resolver/mapping profile bound into the immutable release/revalidation inputs. If no such deterministic mapping profile is bound, or if the retained bytes are ambiguous or insufficient, validation fails closed.

`urn:ecl-pl:identity-evidence-decoder:strict-json-v1` means: exact bytes must be strict UTF-8 with no BOM or replacement decoding; before semantic parsing they pass the raw JSON lexical gate in section 10.3; duplicate decoded member names and unpaired surrogates are rejected; no Unicode normalization, number coercion, network lookup, external-reference substitution or parser repair may change the retained evidence. The decoder only establishes one deterministic JSON data model. Scheme-specific interpretation remains governed by the separately bound reviewed resolver/mapping profile.

`record_uri` may be used during preparation to obtain the source evidence and as provenance for human audit, but later mutation, disappearance or different content at that URI cannot change or repair the retained snapshot.

Individuals use only `scheme: authoritative-opaque-token`. The manifest must additionally contain the fixed `attestation_profile`, a bundle-pinned `attestation_verifier` Ed25519 key, and an `attestation_snapshot`.

The machine profile is exactly:

```text
id: urn:ecl-pl:identity-attestation-profile:ed25519-jcs-v1
canonicalization: RFC8785-JCS
signature_algorithm: Ed25519-RFC8032
token_policy: csprng-32-byte-base64url-v1
privacy_policy: no-government-id-or-derived-low-entropy-identifier-v1
trust_model: bundle-pinned-verifier-key-plus-grant-review-and-licensor-approval
```

These values are schema `const` values and therefore are bound into the immutable manifest. An implementation must not silently substitute another profile.

#### 10.2.1 Opaque token

1. The verifier generates exactly 32 bytes (256 bits) of cryptographically secure random entropy independently of government-ID spelling, number, date of birth, name or other enumerable personal data.
2. The token payload is the RFC 4648 base64url encoding of those 32 bytes with padding omitted, producing exactly 43 characters, prefixed `idtok:v1:`.
3. For 32-byte input, the canonical final base64url character is one of `AEIMQUYcgkosw048`.
4. Release tooling uses a strict base64url decoder, rejects padding and non-alphabet characters, requires exactly 32 decoded bytes, and re-encodes them canonically; the re-encoded token must equal the manifest token byte-for-byte.
5. A hash, HMAC without a high-entropy secret, normalized form, encrypted spelling, checksum or other deterministic derivative of a DNI, SSN, passport number or similar low-entropy identifier is not a conforming token.

#### 10.2.2 Pinned verifier key

`attestation_verifier.public_key` is `ed25519:v1:<canonical-unpadded-base64url-of-32-public-key-bytes>`. `attestation_verifier.key_id` is `ed25519-sha256:<lowercase SHA-256 of those exact 32 public-key bytes>`. Release tooling strictly decodes/re-encodes the public key and recomputes the key ID.

The key is a cryptographic origin pin, not an automatic legal-authority oracle. Grant-specific review must accept use of that verifier key, and the Patent Licensor's later approval of the exact bundle also binds the selected verifier key and profile.

#### 10.2.3 Attestation document

The retained attestation media type is exactly `application/ecl-pl-identity-attestation+jcs`; arbitrary subtype/parameter strings are forbidden.

The attestation bytes must be the RFC 8785 JCS UTF-8 serialization of one JSON object containing **exactly** these members:

```text
profile_id
token
patent_licensor_name
jurisdiction
verified_at
verifier_key_id
verification_method
token_generation
government_identifier_retained
derived_low_entropy_identifier_retained
signature
```

Required values are:

- `profile_id` = `urn:ecl-pl:identity-attestation-profile:ed25519-jcs-v1`;
- `token` = the exact manifest `identity_reference.identifier`;
- `patent_licensor_name` = the exact manifest `patent_licensor.name`;
- `jurisdiction` = the exact manifest `identity_reference.jurisdiction`;
- `verified_at` = a definite RFC 3339 timestamp using the same no-`-00:00` rule as provenance;
- `verifier_key_id` = the exact manifest `attestation_verifier.key_id`;
- `verification_method` = `government-identity-document-checked-out-of-band`;
- `token_generation` = `csprng-32-byte-independent`;
- `government_identifier_retained` = JSON `false`;
- `derived_low_entropy_identifier_retained` = JSON `false`;
- `signature` = `ed25519:v1:<canonical-unpadded-base64url-of-64-signature-bytes>`.

No additional member is permitted. In particular, the attestation cannot carry a government identifier, government-ID hash, passport number, SSN, DNI, date of birth, arbitrary URI, arbitrary media-type parameter, case number or free-form verification payload.

For signature verification, remove only the `signature` member, serialize the remaining object with RFC 8785 JCS, encode those canonical characters as UTF-8, and verify the 64-byte Ed25519 signature against that exact byte sequence and the manifest-pinned public key. A canonical 64-byte Ed25519 signature has 86 unpadded base64url characters and a final character in `AQgw`.

The exact full attestation bytes are retained at `evidence/identity/sha256/<digest>`, where `<digest>` is their lowercase SHA-256. The terminal path digest, `attestation_snapshot.sha256`, `BUNDLE-INDEX` digest and recomputed member digest must all be identical.

### 10.3 Raw JSON lexical gate

Before a general JSON parser sees the manifest bytes, release tooling must perform lossless lexical validation over exact raw UTF-8 bytes:

1. reject BOM, malformed UTF-8 and decoder replacement;
2. tokenize all JSON strings while preserving escape structure;
3. reject unpaired UTF-16 surrogate escapes before any parser can replace them;
4. decode object-member names according to JSON string escape rules without Unicode normalization;
5. within each object scope, reject any duplicate decoded member name, including escape-equivalent spellings such as `"status"` and `"\u0073tatus"`;
6. compare decoded member names by exact Unicode scalar sequence; NFC/NFKC or case folding is not performed; and
7. only after these checks may the bytes be passed to the general parser and schema validator.

A parser configuration that independently guarantees strict UTF-8, rejects unpaired surrogates and rejects duplicate decoded member names is conforming only if those properties are tested on the release path.

The retained identity attestation is subject to the same strict UTF-8, lone-surrogate and duplicate-member rejection before JCS processing.

### 10.4 Canonical evidence paths

Every evidence member path is content-addressed and valid before it can appear in `BUNDLE-INDEX`:

- patent evidence is only `evidence/patents/sha256/<64 lowercase hexadecimal SHA-256 characters>`;
- identity evidence is only `evidence/identity/sha256/<64 lowercase hexadecimal SHA-256 characters>`;
- the terminal digest must equal the SHA-256 of the exact member bytes and the corresponding snapshot `sha256`;
- `/` is the only separator;
- empty segments, `.` segments, `..` segments, repeated separators, leading/trailing separators, backslashes, percent encodings, Unicode variants, case variants and trailing dot/space aliases are forbidden;
- every evidence path in the index must belong to the applicable evidence closure, and every member required by that closure must appear exactly once in the index;
- raw physical member names are validated against this exact ASCII grammar and the closed index before extraction.

Because both evidence namespaces and digest spellings are fixed lowercase ASCII, conforming evidence names cannot differ only by platform case-folding or trailing-dot rules. Validation never consults a target filesystem to decide whether two names collide.

### 10.5 Combination-rule hash binding

For `claim_scope.combination_expansion: rule-based`, `hash_mode` is `utf8-json-string-value-v1`.

After the lexical gate succeeds, `rule_sha256` hashes exactly the strict UTF-8 encoding of the parsed Unicode scalar-value `rule_text`, with no BOM, Unicode normalization, newline conversion, trimming, replacement substitution, terminal newline or NUL. Tooling recomputes and compares the digest. Indirect placeholders such as `See applicable rule` are not complete controlling rules.

### 10.6 Patent Licensor approval

The approval/adoption record identifies the Patent Licensor, approving actor/mechanism, asserted authority, exact `ECL-PL-BUNDLE-v1:<digest>` identity, act time and integrity/authentication evidence. Approval of a different bundle identity cannot retroactively approve this one.

Tooling may verify integrity, authentication evidence and declared state. It must not pretend to determine patent ownership, legal authority, claim coverage, enforceability, infringement, exhaustion or lawyer competence.

### 10.7 Closed JSON Schema resource set

`schemas/schema-set.json` is the machine-readable registry manifest for the Draft 2020-12 PatentGrantManifest schema resource set. Each schema resource has an immutable ECL-PL URN `$id`, and every cross-resource `$ref` uses one of those exact URNs. A mutable Git branch URL, GitHub HTML `blob` page or other network location is never a schema dependency.

Conforming release validation is closed and deterministic:

1. load `schema-set.json` from the exact reviewed repository/release inputs;
2. require exactly the resources listed by that manifest and no substitute resource;
3. hash each resource's exact bytes and require equality with the listed SHA-256;
4. parse each resource as JSON Schema Draft 2020-12 and require its `$id` to equal the listed URN;
5. register all resources in the validator registry by those exact URNs before evaluating the root schema;
6. validate using the listed `root_id`; and
7. forbid HTTP(S), filesystem fallback, package-manager lookup or any other network/ambient resolution for an unregistered `$ref`.

A validator that is given only `patent-grant.schema.json` without the verified registered resource set is not a conforming ECL-PL release validator. Release/review records that bind schema semantics must retain or hash `schema-set.json` and all resources listed by it.

## 11. Suggested repository layout

The directory form below is a **repository/staging representation only**. It is useful for authoring and inspection but is not itself an operative physical PatentGrantBundle:

```text
versions/
  licenses/
    ECL-PL-1.0.0.txt

grants/
  <grant-id>.json

bundles/
  <bundle-id>/
    BUNDLE-INDEX
    license/
      PATENT-LICENSE
    manifest/
      patent-grant.json
    evidence/
      patents/
        sha256/
          <digest>
      identity/
        sha256/
          <digest>

reviews/
  legal/
    inputs/
    records/
```

Every operative physical PatentGrantBundle is the one exact `ECLPLB1` serialized byte sequence required by `spec/CONTAINER-PROFILE.md`. A ZIP, TAR, raw filesystem tree, copied directory or other archive/container is not an alternate operative encoding. Extraction may be used only after validation for inspection and never defines package identity or operativeness.

## 12. Revocation, withdrawal and termination

Repository state distinguishes withdrawal of an unpublished/draft grant, supersession for future use, termination of a particular licensee's rights, expiration/lapse/revocation of a patent right, and assignment. These are not interchangeable.

A maintainer must not mark an immutable grant file `revoked` and assume that repository action legally rescinds previously granted rights. Legal consequence comes from the operative patent licence and applicable law; the repository records immutable events.

## 13. Hash and canonicalization policy

SHA-256 is the initial content-addressing algorithm. `BUNDLE-INDEX` canonicalization is defined in section 4. JSON artifact canonicalization for any future JSON-level content identity must be specified before stable release; until then, when exact JSON bytes are hashed, the hash is over those exact retained bytes rather than an implementation-dependent reserialization.

The individual identity attestation is the one current exception with an explicitly specified JSON canonicalization profile: RFC 8785 JCS as defined in section 10.2.3.

A future algorithm migration must preserve old identities and must not rewrite historical artifacts.
