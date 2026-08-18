# ECL-PL Versioning and Immutable Patent Grants

> **Status: architecture draft. No licensing effect.**

## 1. Core invariant

ECL-PL separates reusable legal text from project-specific patent grants:

```text
PatentLicenseRelease
        +
PatentGrantManifest
        =
PatentGrantBundle
```

Every stable component and every operative bundle must be immutable and content-addressable.

## 2. PatentLicenseRelease

A `PatentLicenseRelease` is an exact immutable legal text artifact.

Stable releases should use semantic versioning discipline:

- `MAJOR` — materially incompatible change to grant scope, termination, definitions, restrictions, downstream rights or legal model;
- `MINOR` — legally substantive but intended-compatible addition/clarification;
- `PATCH` — non-substantive editorial/corrective change only.

Any uncertainty over whether a change affects rights should be treated conservatively as legally substantive and should receive delta review.

Candidate display identifiers:

```text
ECL-PL-0.1.0-DRAFT
ECL-PL-1.0.0
ECL-PL-1.1.0
```

A version label is not enough for operative identity. Each release must also have a cryptographic content hash.

## 3. PatentGrantManifest identity

A `PatentGrantManifest` is an immutable declaration tied to one Patent Licensor and one exact PatentLicenseRelease.

Suggested identifier form:

```text
ECL-PG-<issuer-slug>-<YYYYMMDD>-<sequence>
```

Example:

```text
ECL-PG-example-labs-20261002-1
```

The human-readable identifier is not itself sufficient. The manifest must be content-addressed.

## 4. PatentGrantBundle identity

A PatentGrantBundle binds exactly:

```text
patent_license_id
patent_license_sha256
patent_grant_id
patent_grant_sha256
```

A display form may be:

```text
ECL-PL-1.0.0@PG-example-labs-20261002-1
```

The canonical machine identity remains the exact component hashes.

## 5. No retroactive mutation

Once a PatentGrantManifest is released, later changes must not edit that grant in place.

Events such as the following must be represented as new records or new grant state, not rewritten history:

- addition or removal of a patent/application identifier;
- change in represented ownership or licensing authority;
- assignment or acquisition;
- newly discovered encumbrance;
- patent grant, validation, lapse, abandonment, expiration, revocation or limitation;
- change in ECL policy reference;
- change in sublicensing/affiliate policy;
- change in Covered Implementation; or
- change in termination/reinstatement status.

A later manifest may supersede a prior manifest for future grants or future policy, but cannot pretend the earlier legal state never existed.

## 6. Legal changes versus provenance changes

The repository must distinguish:

### Legal grant change

A change intended to alter permissions, restrictions, termination or the scope of a grant.

This requires a new legally effective artifact and applicable legal review. A metadata edit cannot silently change rights.

### Provenance/status event

A record describing facts such as assignment, expiration, abandonment or validation.

A provenance event may affect how existing legal rights are understood under applicable law, but repository tooling must not manufacture that consequence. The record should preserve the event, evidence and date.

## 7. Optional ECL Bundle reference

If a PatentGrantManifest uses an ECL policy reference, it must contain immutable identity sufficient to resolve the exact ECL Bundle.

At minimum:

```text
ecl_bundle:
  id: <exact bundle id>
  license_sha256: <sha256>
  schedule_sha256: <sha256>
```

A mutable ECL channel must never be stored as the operative reference.

A later ECL Bundle does not change an existing PatentGrantManifest.

## 8. Draft versus operative

The repository must distinguish at least:

```text
draft
candidate
operative
superseded
withdrawn
```

`operative` must be reserved for an exact PatentGrantBundle that satisfies the future release/legal-review gate.

An exact PatentGrantBundle must not become `operative` unless the named Patent Licensor, acting through an authenticated person or mechanism with asserted authority to bind that Patent Licensor, performs an attributable approval/adoption act that is cryptographically bound to the exact immutable bundle identity. Repository maintainers may record and verify that act, but maintainer publication or approval cannot substitute for Patent Licensor assent.

The following do not by themselves make a grant operative:

- merge to `main`;
- schema validation;
- a maintainer signature;
- GitHub release publication;
- a patent number appearing in the manifest;
- a lawyer reviewing a different text; or
- an ECL Bundle being operative.

## 9. Immutable legal-review inputs

Before a PatentLicenseRelease or PatentGrantBundle is represented as stable/operative, qualified legal review must bind to immutable inputs.

The future review structure should support a namespace such as:

```text
reviews/legal/inputs/<review_id>/
  PATENT-LICENSE
  ARCHITECTURE.md
  TERMINOLOGY.md
  COMPOSITION-WITH-ECL.md
  VERSIONING.md
  patent-grant.schema.json

reviews/legal/records/<review_id>.json
```

A completed review record must hash the exact inputs it reviewed.

Later legitimate edits to canonical `spec/` files must not retroactively invalidate a historical review; validation must use the frozen per-review inputs.

Material changes after review require delta review against the exact changed artifact.

## 10. Grant release workflow

Expected workflow:

```text
1. Select exact stable PatentLicenseRelease
2. Prepare PatentGrantManifest
3. Validate raw JSON lexical safety, syntax and schema
4. Recompute and validate all declared local content hashes
5. Resolve and verify patent-publication identities, registry metadata and retained provenance evidence
6. Verify the Patent Licensor identity reference under its declared scheme
7. Attach and resolve any exact ECL Bundle reference
8. Complete grant-specific review required by policy
9. Produce immutable PatentGrantBundle identity
10. Obtain authenticated Patent Licensor approval/adoption bound to that exact bundle identity
11. Preserve and verify the attributable approval record and its binding to the bundle
12. Mark the PatentGrantBundle operative only if all release gates pass
```

### 10.1 Semantic machine-resolution gates

JSON Schema validation is necessary but is not sufficient for any manifest to become `candidate` or `operative`. Release tooling must fail closed unless all of the following semantic checks succeed.

#### Patent publication identity and state

For every property key in `claim_scope.enumerated_claims` and `known_patents`:

1. use the registry namespace embedded in the canonical `patentDocumentKey` to resolve the publication at the authoritative registry;
2. require the registry's canonical publication identifier to round-trip exactly to the manifest key — aliases, serial/application-number spellings and alternate punctuation are not equivalent identities;
3. require the manifest `source` HTTPS URI to resolve to the same publication record, not merely to the same registry home page;
4. derive or verify the publication/document kind from the authoritative record and reject a contradictory `kind` value;
5. verify the recorded `status` against authoritative evidence appropriate to `provenance.recorded_at`;
6. locate the exact PatentGrantBundle member named by that patent entry's `evidence_snapshot.bundle_path`;
7. hash the exact retained member bytes and require equality with `evidence_snapshot.sha256`;
8. require those retained bytes to substantiate the same publication identity, kind and status observed at `provenance.recorded_at`; and
9. reject a missing record, unsupported kind code, impossible authority/kind/number form, ambiguous match, stale alias, contradictory state, source mismatch, missing bundle member or evidence-hash mismatch.

The evidence snapshot is therefore not an external mutable URL. The bytes themselves are an immutable, content-addressed PatentGrantBundle member and must remain recoverable with the bundle for historical revalidation.

`provenance.recorded_at` is an assertion-level RFC 3339 timestamp with a **known** offset. `Z` and explicit numeric offsets are permitted; the RFC 3339 unknown-offset marker `-00:00` is forbidden. Release tooling must not accept a non-date placeholder, an unknown offset, or silently substitute its own current time.

A syntactically valid key therefore cannot manufacture a patent publication, override authoritative registry metadata or leave a historical status assertion dependent on an unretained mutable registry response.

For USPTO identifiers, schema-level canonicalization intentionally distinguishes post-2000 kind-code families: A1/A2 application-publication identities use the four-digit publication year plus seven-digit annual sequence, and B1/B2 patent grants require a seven- or eight-digit patent number. Release tooling must still round-trip every identifier through the authoritative registry; schema syntax is not evidence that a publication exists.

#### Patent Licensor identity schemes

`patent_licensor.identity_reference` must be verified using the declared `scheme` and `record_uri`. The identifier grammar is scheme-specific.

For an `individual`, the manifest permits only `scheme: government-id-hash` with the explicit `sha256:<digest>` representation. Court, agency and other public-record locator schemes are deliberately reserved for legal entities because a prefixed free-form locator can itself contain a plaintext personal identifier.

Accordingly, values such as `case:SSN-...`, `record:<government-id>` or `public-record:ssn:...` cannot be made acceptable merely by adding a record prefix. Release tooling must fail closed on any scheme/subject mismatch or a `record_uri` that does not corroborate the named Patent Licensor. Tooling must never publish, derive, request or substitute the plaintext government identifier into the manifest.

#### Raw JSON Unicode-scalar gate

Before passing manifest bytes to a general JSON parser, release tooling must perform a lossless lexical validation over the exact raw UTF-8 JSON bytes:

1. require strict UTF-8 input and reject a BOM, malformed byte sequence or any decoder replacement;
2. tokenize JSON strings without normalizing or decoding away escape structure;
3. for every raw `\uXXXX` escape, reject a low-surrogate escape (`\uDC00`–`\uDFFF`) unless it is immediately paired with a preceding high-surrogate escape in the same JSON string;
4. require every high-surrogate escape (`\uD800`–`\uDBFF`) to be immediately followed by a low-surrogate `\uXXXX` escape in the same string;
5. reject any unpaired surrogate escape before invoking a parser that might replace it with U+FFFD; and
6. only after this lexical gate may the manifest be parsed and schema-validated.

A release implementation is non-conforming if its parser silently replaces malformed surrogate escapes and the release gate then hashes the replacement. Implementations may instead use a JSON parser that itself rejects unpaired surrogate escapes, provided that behavior is tested and guaranteed for the release path.

#### Combination-rule hash binding

For `claim_scope.combination_expansion: rule-based`, `combination_rule.hash_mode` is `utf8-json-string-value-v1`.

After the raw JSON Unicode-scalar gate has succeeded, the bytes hashed for `rule_sha256` are exactly:

1. take the parsed Unicode scalar-value string of `combination_rule.rule_text` after JSON escape decoding;
2. encode that accepted string as UTF-8 with strict error handling and no BOM;
3. perform no Unicode normalization, newline conversion, whitespace trimming, replacement-character substitution or other transformation; and
4. append no terminating newline or NUL byte.

Release tooling must recompute SHA-256 over exactly those bytes and require equality with `rule_sha256`. The rule text must itself contain the complete controlling rule; an indirect instruction such as `See applicable rule` cannot be cured by attaching an arbitrary hash.

The approval/adoption record must identify the Patent Licensor, the approving actor or authenticated mechanism, the authority asserted for that act, the exact bundle identity being adopted, the time of the act, and integrity information sufficient to detect substitution. A later approval of different hashes cannot retroactively adopt an earlier bundle.

Tooling may verify integrity, authentication evidence and declared state. It must not pretend to determine patent ownership, legal authority, claim coverage, enforceability, infringement, exhaustion or lawyer competence.

## 11. Suggested repository layout

```text
versions/
  licenses/
    ECL-PL-1.0.0.txt

grants/
  <grant-id>.json

bundles/
  <bundle-id>.json
  evidence/
    patents/
      <retained-snapshot>

provenance/
  <grant-or-family-id>/

reviews/
  legal/
    inputs/
    records/
```

The precise layout may change before the first stable release, but the separation between immutable legal text, immutable grant declaration, retained provenance evidence and review records should remain.

## 12. Revocation, withdrawal and termination

Repository state must distinguish:

- **withdrawal of an unpublished/draft grant**;
- **supersession for future use**;
- **termination of a particular licensee's rights under the operative legal text**;
- **expiration/lapse/revocation of a patent right**; and
- **assignment of the patent to another owner**.

These are not interchangeable.

A maintainer must not mark an immutable grant file `revoked` and assume that action legally rescinds previously granted rights. The legal consequence must come from the operative patent licence and applicable law, while the repository records the event immutably.

## 13. Hash algorithm

SHA-256 is the initial content-addressing algorithm for interoperability with the ECL ecosystem. The canonicalization rules for JSON artifacts must be defined before stable release so hashes are reproducible across tooling.

A future algorithm migration must preserve old identities and must not rewrite historical artifacts.
