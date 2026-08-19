# ECL-PL 1.0.0-draft.1 — Exergic composition and defensive-commons profile

> **Status: non-operative design/review note.** This file explains the ECL ↔ ECL-PL composition model implemented in the `draft.1` legal text. It does not create patent rights, does not turn any current ECL draft Schedule into an operative Schedule, and does not satisfy qualified patent/competition-law review.

## 1. Design thesis

ECL-PL is intended to use patent rights in two complementary ways:

1. **broad access by default** — a qualifying recipient receives a direct, royalty-free patent permission from the Patent Licensor for the exact Covered Patent Claims and exact Covered Implementation; and
2. **bounded defensive leverage** — the Patent Licensor may select narrowly reviewed patent consequences for patent aggression or for exact ECL policy outcomes.

The intended economic direction is therefore:

```text
peaceful practice of the Covered Implementation
    -> patent exclusion pressure decreases

offensive patent assertion / exact ECL-restricted conduct
    -> specified ECL-PL permission may be withheld, limited,
       suspended or terminated if the exact bundle says so
```

This is a defensive patent-commons model, not a transfer of patents into collective ownership and not an automatic cross-licensor retaliation pool.

## 2. Rights-plane separation

ECL and ECL-PL remain separate legal instruments:

```text
ECL
  -> copyright / software-right permissions and conditions

ECL-PL
  -> patent permissions under exact Covered Patent Claims
```

Using both does not merge the rights into one legal grant. Composition is an explicit adapter:

```text
Exact ECL Bundle
  = exact ECL LicenseRelease + exact ScheduleRelease
                  |
                  | immutable ecl_bundle_reference
                  v
Exact ECL-PL PatentGrantBundle
  = exact PatentLicenseRelease
  + exact PatentGrantManifest
  + retained evidence
                  |
                  v
       exact patent_specific_effect
```

## 3. No score-to-patent function

The formal Exergism layer is upstream analysis, not a patent switch.

The prohibited shortcut is:

```text
C > threshold
or B_0 < threshold
or any other numerical result
        -> lose ECL-PL patent rights
```

`draft.1` instead requires:

```text
evidence
  -> formal Exergism analysis
  -> ECL criterion fit
  -> attribution and adversarial review
  -> ECL governance decision
  -> exact immutable ECL Schedule / operative ECL rule
  -> exact ecl_bundle_reference
  -> exact patent_specific_effect
  -> ECL-PL consequence
```

The scores remain valuable because they make the ECL governance decision evidence-backed, comparable and falsifiable. They do not independently create patent ineligibility.

This preserves the ECL rule that formal Exergism is multicriteria and that there is no automatic `score -> R/S/U/N` function.

## 4. Restricted Party mapping

When a PatentGrantManifest selects `purpose: restricted-party-policy`, the exact incorporated ECL Bundle is the source of the classification.

### 4.1 Full restricted scope

Where the exact ECL Bundle gives an `R`-style Restricted Party designation, the ECL-PL consequence may apply only to the exact actor/apparatus/class actually covered by that Schedule entry.

A State-level apparatus entry must not become a patent exclusion against the population, nationality, territory, independent private actors or excluded institutions merely because the country name appears in a Schedule.

### 4.2 Scoped restriction

Where the exact ECL Bundle uses an `S`-style scoped designation, ECL-PL must preserve that scope.

Example:

```text
Entity X
  ECL status: scoped restriction
  restricted capacity: commercial-spyware operation supporting
                       qualifying repressive surveillance
```

The patent result may be:

```text
Entity X acting in that exact restricted capacity
  -> exact patent consequence may apply

Entity X acting outside that incorporated restricted capacity
  -> no person-wide/entity-wide patent exclusion merely from the S label
```

The patent layer cannot broaden a scoped ECL classification.

### 4.3 Non-restricted / incomplete analytical states

`U`, `N`, `Under Review`, dossier-only, registry-only, proposal-only, score-only and unresolved states do not create Restricted Party patent consequences unless the exact incorporated ECL legal artifacts expressly make that exact state operative.

Omission from a Schedule is not itself an affirmative `N` finding and must not be used to fabricate a governance result.

## 5. Restricted Project mapping

When `purpose: restricted-project-policy` is selected, project status is resolved under the exact frozen ECL rule.

The current ECL drafting model can make a project Restricted through, among other things:

- exact Schedule designation;
- Material Participation by a Restricted Party;
- Material Participation by a Covered Associate in connection with a Restricted Party;
- action at the direction of, on behalf of, or primarily for the material benefit of a Restricted Party; or
- deliberate circumvention under the exact ECL rule.

ECL-PL may consume such a result only if the PatentGrantManifest gives it an exact patent consequence.

The ECL-PL layer must not replace those rules with generic graph reachability, unlimited affiliate recursion or guilt by association.

## 6. Exclusions and remediation are normative boundaries

When ECL-PL imports an ECL classification, it also imports the material limitations on that classification.

Examples that must remain protected where the exact ECL Bundle protects them include:

- materially independent judicial activity;
- independent audit / oversight;
- legal defence;
- accountability/investigative functions;
- qualifying Independent Remediation Activity;
- humanitarian or other express Schedule exclusions;
- exclusions for unrelated corporate functions or actors; and
- safeguards against association-only status.

The patent layer must never be used as a way to erase an ECL exclusion that would prevent the copyright/software restriction from applying.

## 7. Public direct patent grant + ECL eligibility

`draft.1` uses a public direct grant rather than a sublicense chain.

With no normative ECL reference:

```text
qualifying person/entity
  -> direct ECL-PL grant from Patent Licensor
```

With an exact initial-grant ECL policy:

```text
qualifying person/entity
  -> resolve exact ECL policy trigger
     -> trigger absent: direct grant
     -> trigger present: exact withhold/condition result
```

A distributor, Affiliate, contractor, integrator, reseller or manufacturer cannot convert an ineligible party into an eligible party by relabeling the relationship or purporting to sublicense ECL-PL rights.

## 8. Patent aggression is a separate defensive axis

ECL ethical/governance restrictions and patent-aggression termination solve different problems.

```text
ECL-derived restriction
  -> responds to exact ECL Restricted Party / Project / other policy

ECL-PL defensive termination
  -> responds to exact patent-assertion trigger
```

A party may be unrestricted under ECL but still lose a particular ECL-PL grant because it initiates the exact offensive patent assertion selected by that bundle.

Conversely, being ECL-restricted does not imply that every ECL-PL bundle in existence terminates. The exact PatentGrantManifest must select the relevant ECL-derived patent consequence.

## 9. Distributed defensive effect without fictional collective ownership

If independent Patent Licensors publish many PatentGrantBundles using equivalent defensive profiles, the ecosystem can obtain a distributed defensive effect:

```text
Licensor A -> direct patent grant
Licensor B -> direct patent grant
Licensor C -> direct patent grant
...

Patent aggressor triggers A's rule -> may lose A grant
Patent aggressor triggers B's rule -> may lose B grant
Patent aggressor triggers C's rule -> may lose C grant
```

But no bundle automatically terminates another bundle. Each Patent Licensor acts only on rights it owns, controls or is authorized to license.

This is important to the anti-dominance thesis: the deterrent can emerge from many independent grants without pretending that the repository owns or centrally controls all participating patents.

## 10. Immutability and temporal behavior

The policy rule is frozen to the exact ECL Bundle incorporated by the PatentGrantManifest.

```text
PG-1 -> ECL Bundle A + Schedule A
```

Later publication of:

```text
ECL Bundle B + Schedule B
```

does not rewrite PG-1.

The frozen rule may evaluate later conduct if and only if the exact ECL rule and exact `patent_specific_effect` make that temporal operation objective. A later governance decision cannot be substituted for the incorporated rule.

This distinguishes:

- **mutable governance** — may create a new future Schedule; from
- **immutable licence rule** — controls the already-issued bundle.

## 11. Current ECL repository status is not an operative input

At the time of this `draft.1` note, the ECL repository's current 0.3 licence and 0.5 partial Restricted Parties Schedule are drafts/non-operative review artifacts.

They are useful test vectors for ECL-PL composition semantics but MUST NOT be represented as an operative ECL Bundle merely because ECL-PL can technically point to content hashes.

An operative ECL-derived ECL-PL restriction requires an exact ECL Bundle that itself satisfies the applicable ECL release/adoption requirements.

## 12. Competition / FRAND gate

ECL-derived patent restrictions are not presumed lawful merely because the ECL classification is well supported.

A PatentGrantBundle using restricted-party, restricted-project, field-of-use, customer, territorial or discriminatory eligibility rules must still pass the patent/competition-law review required by ECL-PL.

For EU-relevant technology-licensing arrangements, the current review baseline includes Commission Regulation (EU) 2026/877 (TTBER) and the 2026 Technology Transfer Guidelines. The block exemption is conditional; its applicability depends on the agreement and market circumstances, and some restrictions can remove or fall outside the block-exemption benefit. SEP/FRAND and other prior commitments are separate constraints.

## 13. Required adversarial vectors

The first review cycle should attempt at least the following:

1. **score bypass** — unfavorable Exergism score with no operative ECL designation must not remove a patent grant;
2. **mutable registry bypass** — changing a current registry entry must not alter an old PatentGrantBundle;
3. **later Schedule mutation** — a new Restricted Party in a later Schedule must not affect a bundle pinned to an earlier Schedule;
4. **R scope inflation** — a State apparatus entry must not become a population/nationality-wide patent exclusion;
5. **S scope inflation** — a scoped entity must not lose patent rights outside its exact restricted capacity merely because the entity name matches;
6. **U/N misuse** — unresolved/non-restricted analytical states must not be converted into a patent sanction by ECL-PL;
7. **Schedule omission** — absence from a partial Schedule must not be treated as a positive unrestricted determination;
8. **Covered Associate recursion** — association must not propagate indefinitely;
9. **remediation erasure** — Independent Remediation Activity and exact Schedule exclusions must survive patent translation;
10. **contractor laundering** — an ECL-ineligible actor must not become eligible by acting through a nominal contractor under the public-direct model;
11. **project factual drift** — later facts may be evaluated only under the exact frozen project rule, not later governance semantics;
12. **FRAND conflict** — an ECL-derived discriminatory restriction on a standards-essential claim must fail or narrow where a binding commitment requires otherwise;
13. **competition-law conflict** — an ECL-derived field/customer/territorial restriction must not be treated as automatically lawful;
14. **independent grant survival** — Apache/GPL/MPL/CERN-OHL/commercial/statutory/exhaustion rights remain independent;
15. **cross-bundle retaliation fiction** — a trigger in one PatentGrantBundle must not silently terminate another licensor's bundle.

## 14. Stable-release question

The core product decision to carry into qualified legal review is:

> Should ECL-PL 1.0 standardize an **Exergic Composition Profile** in which a Patent Licensor can opt into the exact ECL Restricted Party / Restricted Project machinery as patent-grant eligibility policy, while keeping formal Exergism scores strictly upstream and preserving every ECL scope limitation, exclusion and remediation safeguard?

`draft.1` now implements that answer as **yes, opt-in and exact-bundle-bound**, subject to adversarial review and the mandatory patent/competition-law gate.
