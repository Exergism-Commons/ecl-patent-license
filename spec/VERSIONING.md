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
3. Validate syntax/schema
4. Validate content hashes
5. Check required provenance fields
6. Attach any exact ECL Bundle reference
7. Complete grant-specific review required by policy
8. Produce immutable PatentGrantBundle identity
9. Obtain authenticated Patent Licensor approval/adoption bound to that exact bundle identity
10. Preserve and verify the attributable approval record and its binding to the bundle
11. Mark the PatentGrantBundle operative only if all release gates pass
```

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

provenance/
  <grant-or-family-id>/

reviews/
  legal/
    inputs/
    records/
```

The precise layout may change before the first stable release, but the separation between immutable legal text, immutable grant declaration, provenance and review records should remain.

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
