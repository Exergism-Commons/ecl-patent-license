# Composition with the Exergic Commons License

> **Status: architecture draft. No licensing effect.**

## 1. Separation rule

The Exergic Commons License (ECL) and the ECL Patent License (ECL-PL) are separate instruments governing different rights.

The relationship is intentionally **A + D**:

- ECL may remain stable and operative while expressly granting no patent rights.
- ECL-PL is an optional companion instrument that a patent owner or authorized Patent Licensor may adopt separately.

No recipient should need to infer whether a patent grant exists from the presence of ECL.

## 2. No automatic composition

None of the following, standing alone, creates an ECL-PL patent grant:

- applying ECL to software, documentation, hardware source or another artifact;
- incorporating an ECL Bundle;
- appearing as a contributor to an ECL-covered repository;
- owning copyright in ECL-covered material;
- publishing a patent/application number next to ECL-covered material;
- linking to this repository;
- using the words `ECL Patent License` without identifying an exact operative PatentGrantBundle; or
- a later ECL governance decision.

Likewise, applying ECL-PL does not itself grant copyright/software rights under ECL.

## 3. Independent identities

An ECL release resolves an exact ECL Bundle under the ECL repository's own versioning model.

An ECL-PL release resolves an exact PatentGrantBundle:

```text
ECL Bundle
  = exact ECL LicenseRelease
  + exact ECL ScheduleRelease

PatentGrantBundle
  = exact ECL-PL PatentLicenseRelease
  + exact PatentGrantManifest
```

Neither tuple is implicitly embedded into the other.

## 4. Optional immutable ECL reference

A PatentGrantManifest may expressly incorporate one exact ECL Bundle for one or more specifically identified **patent-policy purposes**.

The manifest must say what the reference controls. Examples for future legal review may include:

- eligibility for a particular patent grant;
- a field-of-use limitation;
- a Restricted Party or Restricted Project rule translated into patent-specific language;
- an anti-circumvention rule; or
- a policy profile identifier.

Merely referencing an ECL Bundle for provenance does not automatically import all ECL terms into the patent grant.

## 5. No mutable incorporation

A PatentGrantBundle must not incorporate as operative patent policy:

- `latest`;
- `stable`;
- `stable-1`;
- a Git branch;
- a mutable registry view;
- a governance dashboard;
- the current Schedule at evaluation time; or
- future ECL designations.

If a PatentGrantBundle incorporates ECL policy, it must identify the exact immutable ECL Bundle and its content identity.

### 5.1 Canonical ECL policy-item resolution gate

A normative `ecl_bundle_reference.patent_specific_effect.trigger.policy_item` is not satisfied by a human-readable label or by schema validity alone.

The canonical pointer is the tuple:

```text
(exact ECL artifact, item kind, 1-based start line, line count, selected-byte SHA-256)
```

where the artifact is exactly `license` or `schedule` from the ECL Bundle identified by `license_sha256` and `schedule_sha256`.

The hash is defined over an exact byte slice, not over a decoder's reconstructed text. For an ECL artifact to be eligible for line-range addressing, release tooling must first require all of the following:

- the exact artifact bytes match the bundle's declared SHA-256;
- the artifact is valid UTF-8;
- the artifact does not begin with the UTF-8 BOM bytes `EF BB BF`;
- line separators are single LF bytes (`0A`); any CR byte (`0D`) is rejected rather than normalized; and
- no decoding error, replacement character insertion or implicit newline conversion is permitted.

Line addressing is then defined directly on those verified bytes:

1. line 1 starts at byte offset 0;
2. each LF byte terminates the current line and the next byte, if any, starts the next line;
3. `start_line` selects the first addressed line and `line_count` selects that many consecutive lines;
4. the selected byte slice starts at the first byte of `start_line`;
5. the slice ends after the content bytes of the final selected line **and includes that line's terminating LF if and only if that LF exists in the immutable artifact**;
6. internal LF separators are therefore included exactly as stored;
7. a final unterminated line is valid and contributes no synthetic terminating LF; and
8. no bytes are trimmed, decoded/re-encoded, Unicode-normalized or otherwise transformed before hashing.

Before a PatentGrantBundle may become a release candidate or operative, release tooling must:

1. load the exact content-addressed ECL artifact identified by the corresponding bundle hash;
2. enforce the byte preconditions and line-addressing algorithm above;
3. reject a pointer whose line range is out of bounds or otherwise does not resolve exactly once;
4. SHA-256 the exact selected byte slice and require equality with `policy_item.text_sha256`;
5. decode the already-verified artifact as UTF-8 for parsing, without changing bytes, and verify under the canonical ECL parser/release metadata that the resolved range is an item of the declared `kind`; and
6. fail closed on any missing artifact, BOM, CR byte, invalid UTF-8, hash mismatch, parser disagreement, ambiguous target or unresolved item.

Equivalent prose labels, alternate anchors, shortened names and other aliases are not interchangeable pointer identities. A publisher that wishes to reference a different range must publish a different manifest identity. This resolver gate is semantic release validation in addition to JSON Schema validation.

## 6. Non-retroactivity

Later changes to ECL, ECL governance, ECL Schedules or ECL tooling do not change an already issued PatentGrantBundle.

Example:

```text
PG-001
  PatentLicenseRelease: ECL-PL-1.0.0
  PatentGrantManifest: PG-001
  ECL Bundle Reference: ECL-1.0.0@RP-2026.10.02.1
```

If `RP-2027.03.01.1` later adds Entity X, PG-001 remains resolved against `RP-2026.10.02.1` unless the operative patent licence contains some independently lawful mechanism expressly stating otherwise. The architecture rejects silent retroactive incorporation.

A Patent Licensor wishing to use the later policy state must issue a new grant/bundle or another legally effective instrument. Whether and how the Patent Licensor may alter rights already granted depends on the operative patent licence and applicable law.

## 7. Separate termination planes

Copyright/software rights and patent rights must be analyzed independently.

Default architecture:

```text
ECL breach
  -> evaluate ECL rights under ECL
  -> no automatic ECL-PL termination

ECL-PL breach / patent-retaliation trigger
  -> evaluate patent rights under ECL-PL
  -> no automatic ECL copyright termination
```

Any future cross-trigger must be:

1. express;
2. grant-specific;
3. limited to rights the relevant licensor controls;
4. legally reviewed for each required jurisdiction; and
5. tested for downstream and overlapping-grant effects.

## 8. Restricted Party / Restricted Project translation

ECL concepts cannot simply be imported by name and assumed to have the same legal effect in patent law.

If ECL-PL uses an ECL Bundle to restrict patent permissions, the operative patent text must independently define:

- which patent grant is withheld or conditioned;
- which Patent Licensor's rights are affected;
- which exact ECL Bundle supplies the factual/policy classification;
- which Covered Patent Claims and Covered Implementation are affected;
- which acts would otherwise require Patent Licensor authorization;
- the knowledge standard, if any;
- the temporal rule;
- exhaustion/statutory-rights savings;
- termination versus initial ineligibility;
- cure/reinstatement; and
- the effect on downstream recipients.

The ECL designation supplies no patent rights beyond those already possessed by the Patent Licensor.

## 9. Independent permissions survive independently

A recipient may possess multiple independent legal bases for the same technical activity.

For example:

```text
Covered technical act
  ├── ECL-PL grant from Patent Owner A
  ├── Apache/GPL/MPL/CERN-OHL patent grant from A or B
  ├── commercial licence
  ├── covenant not to sue
  ├── exhaustion
  ├── compulsory licence / government-use rule
  └── no infringement / invalid claim / statutory exception
```

Termination or ineligibility under one ECL-PL PatentGrantBundle cannot erase an independent permission that the terminating Patent Licensor lacks authority to revoke.

## 10. Copyright licence compatibility is not patent-grant compatibility

A software distribution can be copyright-compatible while its patent permissions conflict or differ.

Compatibility analysis must therefore separate:

- copyright redistribution compatibility;
- patent-grant coexistence;
- patent sublicensing;
- field-of-use restrictions;
- defensive termination;
- notice obligations;
- standards commitments;
- exhaustion; and
- whether additional patent restrictions would contradict obligations imposed by another licence.

The project must not describe a combination as simply `compatible` without stating which layer is being evaluated.

## 11. Recommended publisher UX

An ECL-covered project with no patent grant should be explicit:

```text
Copyright/software license: <exact ECL Bundle>
Patent license: none granted
```

A project using ECL-PL should identify both independently:

```text
Copyright/software license: <exact ECL Bundle>
Patent grant: <exact PatentGrantBundle>
```

A project not using ECL at all may still use ECL-PL if the eventual PatentLicenseRelease permits that use:

```text
Copyright/software license: Apache-2.0
Patent grant: <exact ECL-PL PatentGrantBundle>
```

That composition is a feature of the companion architecture: ECL-PL is not structurally captive to the ECL copyright licence.

## 12. Upstream ECL requirement

ECL itself should continue to state clearly when no patent rights are granted. Completion of this repository is therefore not required merely to remove patent ambiguity from ECL.

The eventual ECL-PL release can be referenced by ECL documentation as an optional companion, but ECL should not silently convert existing releases into patent-bearing releases.
