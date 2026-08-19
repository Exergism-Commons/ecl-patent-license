# ECL-PL 1.0.0-draft.1 — drafting rationale and adversarial notes

> **Status: non-operative drafting notes.** These notes explain choices in `versions/licenses/ECL-PL-1.0.0-draft.1.txt`. They are not legal advice, do not satisfy the repository's qualified-review gate, and do not create patent rights.

## 1. Drafting objective

The first legal-text tranche is intentionally conservative. The goal is not to maximize the number of manifest combinations that can be called valid. The goal is to turn the architecture already merged through #1 / PR #2 into a legal instrument whose grant, scope, downstream path, termination and non-retroactivity can be attacked clause by clause.

The draft therefore follows a fail-closed rule:

> If the architecture exposes a schema value but the current data model does not contain enough information to give that value one objective legal meaning, `draft.1` refuses to support that value rather than filling the gap with implementation-local prose.

## 2. Architecture-to-license mapping

| Architecture / manifest concept | `draft.1` legal treatment |
| --- | --- |
| `PatentLicenseRelease` | Exact reusable legal text; no effect by itself |
| `PatentGrantManifest` | Selects grant-specific choices; cannot redefine the License |
| `PatentGrantBundle` | Necessary legal-effect boundary; exact authenticated licensor approval required |
| `covered_implementation` | Immutable technology boundary for claim-scope analysis |
| `claim_scope.model` | Controls necessary / enumerated / hybrid / reviewed-other claim selection |
| `later_acquired_claims` | Prospective-only inclusion if selected; never repairs past lack of authority |
| `combination_expansion` | No expansion, exact defined combinations, or exact hash-bound rule |
| `downstream_policy` | Direct or sublicense-chain paths only in `draft.1`; ambiguous hybrid/conditional states rejected |
| `defensive_termination` | No default retaliation; narrow fixed profile or exact custom profile |
| `ecl_bundle_reference` | No implicit patent effect; exact immutable patent-specific consequence only |
| `known_patents` / provenance | Evidence, not a portfolio grant and not conclusive title |
| authority / identity evidence | Preconditions and review inputs; never self-manufacturing authority |
| `effective_date` | Resolved deterministically to 00:00:00 UTC, then bounded by later licensor assent |

## 3. Comparative baselines used

The draft was compared conceptually against the official/current texts identified by the architecture review. No clause is copied mechanically.

### 3.1 Apache License 2.0

Official text: https://www.apache.org/licenses/LICENSE-2.0

Apache 2.0 uses a contributor-centered patent grant tied to claims necessarily infringed by a contributor's contribution alone or in combination with the work. It also contains a broad patent-litigation termination trigger.

ECL-PL deliberately does **not** copy the contributor premise. Under ECL-PL, authorship, copyright ownership and contribution are not patent authority. Claim scope instead resolves against an exact `Covered Implementation` and the identified Patent Licensor's actual licensable authority.

### 3.2 GNU GPLv3

Official text: https://www.gnu.org/licenses/gpl-3.0.html

GPLv3 section 11 uses contributor `essential patent claims`, includes an express royalty-free patent grant and embeds patent obligations within a copyright/copyleft system.

ECL-PL borrows neither the automatic contributor-grant model nor GPL's integrated copyright/patent architecture. The independent-rights section is intentionally explicit so an existing GPLv3 patent permission cannot be narrowed by ECL-PL.

### 3.3 Mozilla Public License 2.0

Official text: https://www.mozilla.org/MPL/2.0/

MPL 2.0 is a useful comparator for narrower defensive termination because its patent-litigation trigger distinguishes initiated patent litigation from declaratory actions, counterclaims and cross-claims.

`draft.1` goes narrower still for the fixed `narrow-covered-implementation` profile: the trigger is an affirmative judicial or administrative infringement proceeding directed at the exact Covered Implementation. Defensive responses, validity challenges and enforcement of ECL-PL itself are excluded from the fixed trigger.

### 3.4 CERN Open Hardware Licence v2

Steward page: https://cern-ohl.web.cern.ch/home

CERN-OHL-v2 is the principal hardware comparator. Its patent treatment shows why a patent grant for mixed hardware/software should bind scope to objectively identified technology rather than assume that software distribution verbs describe patent-exclusive acts.

ECL-PL therefore uses `Covered Act` as a jurisdiction-relative patent concept and keeps `Covered Implementation` technology-neutral.

## 4. Current EU competition-law baseline

As of this draft (August 2026), the European Commission's current Technology Transfer Block Exemption Regulation is **Commission Regulation (EU) 2026/877**, which entered into force on 1 May 2026 and is scheduled to expire on 30 April 2038, together with the 2026 Technology Transfer Guidelines.

Official Commission page: https://competition-policy.ec.europa.eu/antitrust-and-cartels/legislation/ttber_en

EUR-Lex regulation: https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32026R0877

Relevant architecture implications include:

- the TTBER applies only when its scope and conditions are met; a public or royalty-free licence is not automatically exempt from Article 101 analysis;
- the block-exemption market-share thresholds differ for competitors and non-competitors;
- hardcore restrictions can remove the benefit of the block exemption;
- some restrictions, including specified non-challenge and grant-back provisions, are excluded from the benefit of the block exemption; and
- field-of-use, customer, territorial and other restrictions require fact-specific competition analysis.

For that reason Section 14 does not declare ECL-derived field-of-use or eligibility restrictions categorically lawful. Every normative ECL patent effect remains grant-specific and subject to the repository's competition-law review track.

## 5. Deliberate `draft.1` semantic decisions

### D1 — No rights from the licence file alone

The strongest formation invariant is repeated at the start and end of the file. Legal effect depends on an operative bundle and authenticated Patent Licensor assent to the exact bundle identity.

This is intended to defeat accidental grants from repository publication, SPDX-like references, contribution or mirroring.

### D2 — Effective date becomes one deterministic instant

The schema stores `effective_date` as a date, not a timestamp. `draft.1` resolves that date to 00:00:00 UTC and then takes the later of that instant and exact-bundle licensor assent.

This prevents timezone-dependent grant formation and prevents a backdated manifest from silently authorizing historical acts before assent.

### D3 — `expiration_policy` is unsupported

The current schema exposes `expiration_policy` as unconstrained text. Treating it as normative would create an unreviewed free-form channel capable of changing grant duration.

`draft.1` therefore rejects an operative bundle containing that field. A future release should replace it with a closed model before assigning it legal effect.

### D4 — Necessary infringement is tied to the immutable Covered Implementation

The rule is intentionally not “anything that could infringe.” A claim enters through the necessary-infringement model only when the exact manifest-defined implementation requires authorization under that claim in the relevant jurisdiction.

Optional modifications, external combinations and later versions do not expand the scope unless the manifest's exact combination model permits that expansion.

### D5 — Later-acquired claims attach prospectively

Even when the manifest selects `included-if-rule-satisfied`, later acquisition does not repair a past period in which the Patent Licensor lacked authority. Coverage begins only after authority is acquired and only if the same immutable scope rule is satisfied.

### D6 — Downstream `conditional` values are not supported

The schema permits `conditional` for several downstream fields but does not contain one general structured condition object that tells the validator or the court what the condition is.

`draft.1` rejects those states instead of treating free-form contextual material as an implied condition.

### D7 — Downstream `hybrid` is not supported

The current `hybrid` model does not assign each downstream class to a direct-grant or sublicense path. A hybrid selection could therefore be interpreted differently by two implementations.

`draft.1` supports:

- `direct-grant` only with `sublicensing: not-granted`; or
- `sublicense-chain` only with `sublicensing: expressly-granted`.

A later schema/release can add a closed hybrid path map.

### D8 — Have-made and contract-manufacturer rights are separated

A have-made permission is limited to acts performed for the Patent Licensee's account. Contract-manufacturer inclusion is treated as a service-purpose permission, not an independent right to manufacture for unrelated customers.

`contract_manufacturers: included` combined with `have_made: excluded` is rejected as contradictory by this release.

### D9 — Customer `derivative-right` is not a hidden direct grant

`derivative-right` is interpreted as “no separate direct ECL-PL grant.” The customer must rely on a valid sublicense, exhaustion, another licence, or another independent legal basis.

This prevents the word “derivative” from manufacturing a new source of patent authority.

### D10 — Fixed narrow defensive termination is intentionally narrower than Apache 2.0

The fixed profile requires initiation of a patent infringement proceeding directed at the exact Covered Implementation. It does not trigger merely because the Licensee:

- defends itself;
- files a compulsory counterclaim/cross-claim;
- seeks non-infringement relief after a concrete threat;
- challenges validity or enforceability;
- participates in an administrative validity proceeding; or
- enforces ECL-PL.

Cease-and-desist letters and licensing discussions are not included in the fixed trigger in `draft.1`; a broader desired trigger should use a reviewed custom profile.

### D11 — Narrow-profile schema extras are forbidden by release semantics

The schema technically permits custom-style subordinate fields to appear alongside `narrow-covered-implementation`. That would allow a manifest to claim the fixed profile while mutating it.

`draft.1` requires those custom fields to be absent. Only a non-conditional cure selection may accompany the fixed narrow profile.

### D12 — Exhaustion and independent grants cannot be clawed back

Termination is expressly limited to the permission created by the relevant Patent Licensor under the exact bundle. It does not reverse exhaustion or terminate permissions obtained under Apache-2.0, GPLv3, MPL-2.0, CERN-OHL-v2, FRAND/RAND, another ECL-PL bundle, another contract or statute.

### D13 — ECL composition is a patent-specific adapter, not imported copyright logic

An ECL reference has no patent effect unless the manifest provides an exact immutable normative reference and exact `patent_specific_effect`.

Even then, the only legal consequence is the patent consequence expressly selected. A later ECL Bundle cannot mutate the grant.

### D14 — Suspension/limitation must have an objective end condition

A `suspend-existing-rights` or `limit-existing-rights` effect is not usable merely because those enum strings exist. If the exact immutable policy item and `patent_specific_effect` do not objectively determine when the state starts, what it affects and when it ends, the bundle is ineligible for operativeness under `draft.1`.

### D15 — No automatic successor-title fiction

The draft does not claim that every patent assignee in every jurisdiction is automatically bound by identical doctrine. Instead it states the immutable grant history, defers successor effect to applicable law/transaction terms, and adds a limited no-deliberate-defeat covenant to the extent enforceable.

## 6. Open legal questions / likely review findings

These are not hidden TODOs. They are explicit targets for the first PR review.

### R1 — Affiliate control threshold

`draft.1` currently uses >50% voting control or another legally effective power to direct management. Review whether the alternative-control limb is too broad and whether temporary control, joint ventures and insolvency control need dedicated treatment.

### R2 — Royalty-free versus no-charge vocabulary

The draft states `royalty-free`. Review whether stable ECL-PL should additionally use `no-charge`, address existing royalty obligations, or avoid suggesting that unrelated obligations are waived.

### R3 — Customer direct-grant boundary

`customers: direct-grant` needs scenario testing for distributors, resellers, cloud services, embedded components, repair, resale and method claims. A direct customer grant must not accidentally cover unrelated customer products.

### R4 — Have-made scope across jurisdictions

The legal meaning and utility of have-made rights, contract manufacturing and component supply should be tested separately for the US, Europe, Spain and UK.

### R5 — Sublicense survival after upstream termination

`draft.1` intentionally does not guarantee universal survival. The stable rule needs jurisdictional and commercial review, especially where direct downstream grants are preferable to sublicense chains.

### R6 — Narrow termination trigger

Review whether the fixed trigger is too narrow because it excludes pre-suit demands, ITC-like proceedings in some formulations, indirect-infringement assertions, controlled affiliates and proxy assertions. Any expansion must preserve defensive exclusions.

### R7 — Cure period

The current fixed cure period is thirty days after authenticated notice. Review whether cure should exist at all for retaliation, whether dismissal must be with prejudice, and whether re-filing through affiliates/proxies needs a more precise anti-evasion rule.

### R8 — Transfer / successor enforceability

The no-deliberate-defeat clause requires country-specific review. The stable text must not promise transferee binding that applicable law does not support.

### R9 — Warranty and limitation clauses

The current disclaimer is intentionally modest and preserves non-waivable liability. Review US state contract law, Spanish/EU mandatory rules, UK reasonableness/consumer rules where relevant, fraud/misrepresentation, and whether a patent-only licence needs any broader liability limitation.

### R10 — Governing law and forum

`draft.1` intentionally contains no universal choice-of-law or exclusive-forum clause. Patent infringement remains territorial, while contract/licence interpretation may require conflict-of-laws analysis. The stable release needs a deliberate choice on whether to remain forum-neutral or provide a grant-specific mechanism.

### R11 — `claim_scope.model: other`

The current draft permits `other` only with a complete objectively executable definition plus exact legal-review approval. Consider whether stable 1.0 should reject `other` entirely and require schema evolution for every new model.

### R12 — ECL field-of-use / Restricted Party mechanisms

These are the highest competition/standards-risk features. Before enabling them in a stable PatentLicenseRelease, test Regulation (EU) 2026/877, the 2026 Technology Transfer Guidelines, Article 101 TFEU outside the block exemption, Article 102 where relevant, SEP/FRAND commitments and national public-policy constraints.

### R13 — Suspension and limitation semantics

The architecture supports `suspend-existing-rights` and `limit-existing-rights`, but stable use requires an objectively resolvable restoration/end rule. If this cannot be made deterministic from the existing ECL policy pointer model, the enum should be narrowed or the manifest extended.

### R14 — Patent Licensee assent / contract formation

The architecture requires attributable Patent Licensor assent, but a recipient may rely on the resulting patent permission without signing a bilateral contract. Review the distinction between a unilateral patent licence/covenant and any contractual obligations imposed on the Licensee, especially termination/cure and transfer covenants.

### R15 — Version stewardship

Stable ECL-PL needs a version-stewardship clause or external governance rule that prevents a later version from silently replacing the exact release bytes. The bundle architecture already prevents technical mutation; the legal text should still state how new versions are published and named.

## 7. First adversarial test scenarios to add

The next review iteration should test at least:

1. contributor with no patent title attempts to publish a bundle;
2. patent owner grants only an enumerated claim and later obtains a continuation claim;
3. direct-grant model with `sublicensing: expressly-granted` (must fail under draft.1);
4. sublicense-chain with `sublicensing: not-granted` (must fail);
5. hybrid downstream model (must fail under draft.1);
6. conditional customer/affiliate/have-made flag with no condition object (must fail);
7. narrow termination profile plus custom trigger fields (must fail);
8. defensive counterclaim after Patent Licensor sues first (must not trigger fixed profile);
9. offensive claim against a modified product outside the exact Covered Implementation (must not trigger fixed profile merely by similarity);
10. existing Apache-2.0 patent grant remains usable after ECL-PL termination;
11. authorized sale creates exhaustion before later ECL-PL termination;
12. exact ECL Bundle reference later gains a successor Schedule (old bundle must not mutate);
13. FRAND-encumbered SEP combined with a discriminatory ECL eligibility rule (must receive specialist review / may fail);
14. assignment of the covered patent after the ECL-PL grant;
15. customer receives an article through a contract manufacturer and asserts exhaustion/independent rights.

## 8. Qualified review remains mandatory

The repository's existing `spec/LEGAL-ADVERSARIAL-REVIEW.md` remains controlling for the stable legal-review gate. AI and maintainer review can discover defects but do not satisfy the required independent qualified patent-law and competition-law review.

Issue #3 should not close, and no `1.0.0` stable PatentLicenseRelease should be created, until the required PLAR surfaces and jurisdiction tracks have actual dispositions for the exact candidate bytes.
