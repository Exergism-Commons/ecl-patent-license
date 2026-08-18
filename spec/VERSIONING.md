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
5. The member list includes `license/PATENT-LICENSE`, `manifest/patent-grant.json`, and **every** evidence member referenced by the manifest.
6. Entries are sorted by the raw UTF-8 bytes of `canonical-member-path`, ascending.
7. Each member path occurs exactly once.
8. The index has one mandatory terminal LF.
9. `BUNDLE-INDEX` does not list itself.
10. A conforming package contains exactly `BUNDLE-INDEX` plus the members listed by it; missing or unindexed extra members are rejected.

For every listed member, tooling hashes the exact member bytes and requires equality with the digest in the index. No path normalization, filesystem aliasing, symlink following, case folding, percent decoding, Unicode normalization or separator rewriting is permitted during lookup.

The canonical machine identity is:

```text
ECL-PL-BUNDLE-v1:<sha256-of-exact-BUNDLE-INDEX-bytes>
```

Therefore two packages cannot have the same bundle identity while differing in the license, manifest, retained evidence, member names or member hashes. Display identifiers may additionally mention the PatentLicenseRelease and PatentGrantManifest IDs, but they are not the canonical bundle identity.

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

`operative` is reserved for an exact PatentGrantBundle that passes all future release/legal-review gates.

The bundle must not become operative unless the named Patent Licensor, through an authenticated person or mechanism with asserted authority to bind that Patent Licensor, performs an attributable approval/adoption act cryptographically bound to the exact bundle identity. Maintainer publication or approval cannot substitute for Patent Licensor assent.

Merge to `main`, schema validity, maintainer signature, GitHub release publication, a patent number, review of different bytes, or an operative ECL Bundle do not independently make an ECL-PL grant operative.

## 9. Immutable legal-review inputs

Stable/operative artifacts require legal review bound to immutable inputs. Historical review inputs and records must remain frozen and content-addressed; later edits to canonical specifications do not retroactively alter what a reviewer reviewed. Material changes require delta review.

## 10. Grant release workflow

Expected workflow:

```text
1. Select exact stable PatentLicenseRelease
2. Prepare exact PatentGrantManifest bytes
3. Validate raw JSON lexical safety before general parsing
4. Parse and validate syntax/schema
5. Recompute all declared hashes
6. Resolve patent-publication identities and authoritative state
7. Verify every retained evidence member against BUNDLE-INDEX
8. Verify the Patent Licensor identity reference and retained attestation
9. Resolve any exact ECL Bundle reference
10. Complete grant-specific legal/policy review
11. Construct canonical BUNDLE-INDEX and verify the closed member set
12. Compute exact PatentGrantBundle identity
13. Obtain authenticated Patent Licensor approval bound to that identity
14. Preserve and verify the approval record
15. Mark operative only if every gate passes
```

### 10.1 Patent publication identity and state

For every property key in `claim_scope.enumerated_claims` and `known_patents`, tooling must:

1. resolve the publication using the registry namespace embedded in `patentDocumentKey`;
2. require the registry's canonical publication identifier to round-trip exactly to the manifest key;
3. require `source` to identify the same publication record;
4. derive/verify `kind` and reject contradictions;
5. verify `status` at `provenance.recorded_at`;
6. resolve `evidence_snapshot.bundle_path` by **literal canonical member-name equality** in `BUNDLE-INDEX`;
7. require that path to appear exactly once in the index;
8. hash the exact retained member bytes and match both the index digest and `evidence_snapshot.sha256`;
9. require those bytes to substantiate the same publication identity, kind and status; and
10. fail closed on missing, ambiguous, contradictory, aliased or unavailable data.

Evidence bytes are bundle members, not mutable external snapshot identities.

`provenance.recorded_at` requires a definite instant: `Z` or a known numeric UTC offset. RFC 3339 `-00:00` is rejected.

For USPTO identifiers, A1/A2 application-publication identities use the 2001+ year-plus-seven-digit publication series. B1/B2 utility-patent kind codes are only valid for grants issued on or after January 2, 2001; the schema therefore excludes numbers below `6,167,569`; USPTO records show Nos. 6,167,567 and 6,167,568 were issued on December 26, 2000 under the old `A` kind, while No. 6,167,569 issued on January 2, 2001 as `B1`. Registry round-trip remains mandatory; schema syntax alone is never evidence that a document exists.

### 10.2 Patent Licensor identity schemes

Legal entities may use public, authority-specific identifiers (`lei`, `company-registry`, `court-or-agency-record`, or `other-authoritative-registry`) with a corroborating HTTPS record and integrity evidence.

Individuals use only:

```text
scheme: authoritative-opaque-token
identifier: idtok:v1:<base64url-token>
attestation_snapshot:
  bundle_path: evidence/identity/<canonical-member>
  sha256: <digest>
```

The token protocol is deliberately **not** a hash of a DNI, SSN, passport number or other low-entropy identifier:

1. an authoritative identity verifier generates at least 256 bits of cryptographically secure random entropy;
2. the token payload is unpadded base64url of that random value and is prefixed `idtok:v1:`;
3. the token must be statistically independent of government-ID spelling, number, date of birth, name or other enumerable personal data;
4. the verifier produces an authenticated attestation binding the opaque token to the named Patent Licensor and relevant jurisdiction/verification event without embedding the plaintext government identifier;
5. the exact attestation bytes are retained as an indexed `evidence/identity/...` bundle member; and
6. release tooling verifies the attestation and its snapshot hash according to the verifier profile.

For an individual, `record_uri` and plaintext-derived identity fields are forbidden. Tooling must never derive, request, insert or publish a plaintext government identifier in the manifest, bundle index, evidence member path or identity token.

### 10.3 Raw JSON lexical gate

Before a general JSON parser sees the manifest bytes, release tooling must perform lossless lexical validation over exact raw UTF-8 bytes:

1. reject BOM, malformed UTF-8 and decoder replacement;
2. tokenize all JSON strings while preserving escape structure;
3. reject unpaired UTF-16 surrogate escapes before any parser can replace them;
4. decode object-member names according to JSON string escape rules **without Unicode normalization**;
5. within each object scope, reject any duplicate decoded member name, including escape-equivalent spellings such as `"status"` and `"\u0073tatus"`;
6. compare decoded member names by exact Unicode scalar sequence; NFC/NFKC or case folding is not performed;
7. only after these checks may the bytes be passed to the general parser and schema validator.

A parser configuration that independently guarantees strict UTF-8, rejects unpaired surrogates and rejects duplicate decoded member names is conforming only if those properties are tested on the release path.

### 10.4 Canonical evidence paths

Every `evidence_snapshot.bundle_path` is a canonical relative member path:

- `/` is the only separator;
- it begins with `evidence/patents/` or `evidence/identity/`;
- every subsequent segment begins with an ASCII alphanumeric character and contains only ASCII alphanumerics, `.`, `_` or `-`;
- empty segments, `.` segments, `..` segments, repeated separators, leading/trailing separators and backslashes are impossible/forbidden;
- lookup uses the literal UTF-8 member-name bytes from `BUNDLE-INDEX`; a filesystem-normalized equivalent is not an alias.

### 10.5 Combination-rule hash binding

For `claim_scope.combination_expansion: rule-based`, `hash_mode` is `utf8-json-string-value-v1`.

After the lexical gate succeeds, `rule_sha256` hashes exactly the strict UTF-8 encoding of the parsed Unicode scalar-value `rule_text`, with no BOM, Unicode normalization, newline conversion, trimming, replacement substitution, terminal newline or NUL. Tooling recomputes and compares the digest. Indirect placeholders such as `See applicable rule` are not complete controlling rules.

### 10.6 Patent Licensor approval

The approval/adoption record identifies the Patent Licensor, approving actor/mechanism, asserted authority, exact `ECL-PL-BUNDLE-v1:<digest>` identity, act time and integrity/authentication evidence. Approval of a different bundle identity cannot retroactively approve this one.

Tooling may verify integrity, authentication evidence and declared state. It must not pretend to determine patent ownership, legal authority, claim coverage, enforceability, infringement, exhaustion or lawyer competence.

## 11. Suggested repository layout

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
        <retained-snapshot>
      identity/
        <retained-attestation>

reviews/
  legal/
    inputs/
    records/
```

Physical packaging may later use an archive, but a packaging format must preserve the logical member names and exact indexed bytes. Symlinks and archive path aliases are not bundle semantics.

## 12. Revocation, withdrawal and termination

Repository state distinguishes withdrawal of an unpublished/draft grant, supersession for future use, termination of a particular licensee's rights, expiration/lapse/revocation of a patent right, and assignment. These are not interchangeable.

A maintainer must not mark an immutable grant file `revoked` and assume that repository action legally rescinds previously granted rights. Legal consequence comes from the operative patent licence and applicable law; the repository records immutable events.

## 13. Hash and canonicalization policy

SHA-256 is the initial content-addressing algorithm. `BUNDLE-INDEX` canonicalization is defined in section 4. JSON artifact canonicalization for any future JSON-level content identity must be specified before stable release; until then, when exact JSON bytes are hashed, the hash is over those exact retained bytes rather than an implementation-dependent reserialization.

A future algorithm migration must preserve old identities and must not rewrite historical artifacts.
