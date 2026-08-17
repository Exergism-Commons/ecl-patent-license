# Patent Grant Manifests

This directory is reserved for immutable ECL-PL `PatentGrantManifest` artifacts after the schema and operative patent licence are sufficiently stable.

## No grant by repository presence

**Nothing in this directory currently grants any patent rights.**

In particular, patent rights are not granted merely because:

- a patent/application identifier appears in a file, issue, pull request or example;
- an inventor, owner or organization is named;
- a contributor submits material to this repository;
- a manifest validates against `schemas/patent-grant.schema.json`;
- a draft manifest is merged to `main`; or
- a file claims `status: operative` without satisfying the project's actual legal-release gate.

## Intended stable model

A future operative patent grant will require, at minimum:

1. an exact stable `PatentLicenseRelease`;
2. an exact `PatentGrantManifest` from an identified Patent Licensor;
3. a bounded Covered Implementation and claim-scope rule;
4. represented authority and auditable provenance;
5. immutable component identities/hashes;
6. any required grant-specific legal review; and
7. an exact `PatentGrantBundle` binding the legal text and grant manifest.

The legal effect of any such artifact will depend on the operative licence and applicable law, not on repository metadata alone.

## Draft manifests

Architecture/test manifests, when introduced, should live under an explicitly non-operative namespace such as:

```text
examples/
fixtures/
```

until the project has a stable publication mechanism. Do not place fake/example grants in this directory in a way that can be mistaken for a real patent authorization.
