# ECL-PL 1.0.0-draft.1 — drafting rationale and adversarial notes

> **Status: non-operative drafting notes.** These notes explain choices in `versions/licenses/ECL-PL-1.0.0-draft.1.txt`. They are not legal advice, do not satisfy the repository's qualified-review gate, and do not create patent rights.

## 1. Drafting objective

The first legal-text tranche is intentionally conservative. The goal is not to maximize the number of schema-valid manifest combinations that can become operative. The goal is to turn the architecture merged through #1 / PR #2 into a patent instrument whose grant, scope, recipient model, termination and non-retroactivity can be attacked clause by clause.

The governing drafting rule is fail-closed:

> If the architecture or schema exposes a value but the current immutable data model does not contain enough information to give that value one objective legal meaning, `draft.1` refuses to support that value rather than inventing implementation-local semantics.

## 2. Architecture-to-license mapping

| Architecture / manifest concept | `draft.1` legal treatment |
| --- | --- |
| `PatentLicenseRelease` | Exact reusable legal text; no effect by itself |
| `PatentGrantManifest` | Selects closed grant-specific choices; cannot redefine the License |
| `PatentGrantBundle` | Necessary legal-effect boundary; exact authenticated Patent Licensor approval required |
| `covered_implementation` | Immutable technology boundary for claim-scope analysis |
| `claim_scope.model` | Necessary / enumerated / hybrid / reviewed-other claim selection |
| `later_acquired_claims` | Prospective-only inclusion if selected; never repairs past lack of authority |
| `combination_expansion` | No expansion, exact defined combinations, or exact hash-bound rule |
| `downstream_policy` | **Public direct grant only** in `draft.1`: `model: direct-grant`, `sublicensing: not-granted`, optional downstream fields absent |
| `defensive_termination` | No default retaliation; fixed narrow profile or exact custom profile |
| `ecl_bundle_reference` | No implicit patent effect; exact immutable patent-specific consequence only |
| `known_patents` / provenance | Evidence, not a portfolio grant and not conclusive title |
| authority / identity evidence | Preconditions and review inputs; never self-manufacturing authority |
| `effective_date` | Resolved deterministically to 00:00:00 UTC, then bounded by later licensor assent |
| `expiration_policy` | Unsupported in `draft.1`; free-form duration semantics are rejected |

## 3. Why `draft.1` adopts a public direct grant

Drafting exposed a real semantic gap in the architecture: the manifest has downstream-policy choices but does not identify a named initial Patent Licensee. A sublicensing architecture therefore lacks a deterministic answer to the question “who receives the upstream grant from which sublicensing authority flows?”

`spec/ARCHITECTURE.md` already records a direct-grant model as the preferred initial hypothesis. `draft.1` makes that hypothesis concrete in the narrowest deterministic form available without adding mutable state or inventing a recipient outside the bundle:

- every person or legal entity is evaluated independently against the exact initial-grant conditions of the operative PatentGrantBundle;
- if the person qualifies, the patent permission runs **directly from the Patent Licensor** to that person;
- if the person does not qualify, an intermediary cannot make that person eligible by distribution, status label, contract-manufacturer designation or purported sublicense;
- there is no ECL-PL sublicense chain in `draft.1`.

This means that the words `customer`, `Affiliate`, `contract manufacturer`, `reseller`, `integrator` or similar factual roles are not themselves sources of patent permission. They may matter under applicable law or another Independent Patent Permission, but not as hidden recipient-identity channels under this release.

## 4. Comparative baselines

The draft is compared conceptually against established public patent-grant models without mechanically copying them.

### 4.1 Apache License 2.0

Official text: https://www.apache.org/licenses/LICENSE-2.0

Apache 2.0 uses a contributor-centered patent grant tied to claims necessarily infringed by a contributor's Contribution alone or in combination with the Work, together with a patent-litigation termination rule.

ECL-PL deliberately does **not** copy the contributor premise. Git authorship, copyright ownership and contribution are not patent authority. Claim scope instead resolves against the exact `Covered Implementation` and the identified Patent Licensor's actual licensable authority.

### 4.2 GNU GPLv3

Official text: https://www.gnu.org/licenses/gpl-3.0.html

GPLv3 section 11 uses contributor `essential patent claims`, an express royalty-free patent licence and patent protections integrated into a copyright/copyleft system.

ECL-PL keeps patent and copyright instruments separate. The Independent Patent Permission savings rule is explicit so an existing GPLv3 patent permission cannot be narrowed or terminated merely by ECL-PL.

### 4.3 Mozilla Public License 2.0

Official text: https://www.mozilla.org/MPL/2.0/

MPL 2.0 is a useful narrow-retaliation comparator because its patent-litigation rule distinguishes initiated litigation from declaratory actions, counterclaims and cross-claims.

`draft.1` uses an even more bounded fixed `narrow-covered-implementation` profile: the trigger is an affirmative judicial or administrative infringement proceeding directed at the exact Covered Implementation. Defensive responses, validity challenges and enforcement of ECL-PL itself are excluded from that fixed trigger.

### 4.4 CERN Open Hardware Licence v2

Steward page: https://cern-ohl.web.cern.ch/home

CERN-OHL-v2 is a critical hardware comparator. It reinforces the need to bind ECL-PL scope to objectively identified technology and to avoid pretending that software distribution verbs are universal patent-exclusive acts.

## 5. Current EU competition-law baseline

As of this draft (August 2026), the European Commission's current Technology Transfer Block Exemption Regulation is **Commission Regulation (EU) 2026/877**, in force from 1 May 2026 and scheduled to expire on 30 April 2038, together with the 2026 Technology Transfer Guidelines.

Official Commission page: https://competition-policy.ec.europa.eu/antitrust-and-cartels/legislation/ttber_en

EUR-Lex regulation: https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32026R0877

`draft.1` does not declare ECL-derived field-of-use, customer, eligibility or restricted-party patent policies categorically lawful. Their treatment remains grant-specific and subject to the repository's competition-law review track, including Article 101 TFEU, the TTBER/Guidelines where applicable, standards commitments and mandatory law.

## 6. Deliberate `draft.1` semantic decisions

### D1 — No rights from the licence file alone

The strongest formation invariant is repeated at the start and end of the file. Legal effect depends on an operative bundle and authenticated Patent Licensor assent to the exact bundle identity.

This is intended to defeat accidental patent grants from repository publication, mirroring, SPDX-like references, contribution or maintainer action.

### D2 — Effective date becomes one deterministic instant

The schema stores `effective_date` as a date, not a timestamp. `draft.1` resolves that date to 00:00:00 UTC and then takes the later of that instant and exact-bundle Patent Licensor assent.

A backdated manifest therefore cannot silently authorize acts that occurred before the licensor actually adopted the exact bundle.

### D3 — `expiration_policy` is unsupported

The current schema exposes `expiration_policy` as unconstrained text. Treating it as normative would create an unreviewed free-form channel capable of changing grant duration.

A bundle containing that field is therefore ineligible for operativeness under `draft.1`.

### D4 — Necessary infringement is tied to the immutable Covered Implementation

The rule is not “anything that could infringe.” A claim enters through `necessarily-infringed` only when practicing the exact manifest-defined implementation would necessarily require authorization under that claim in the relevant jurisdiction.

Optional modifications, unrelated additions, external combinations and later versions do not expand the scope unless the exact combination model permits it.

### D5 — Later-acquired claims attach prospectively

Even when the manifest selects `included-if-rule-satisfied`, a later acquisition cannot repair a period in which the Patent Licensor lacked authority. Eligibility begins only after authority is acquired and only if the same immutable claim-scope rule is independently satisfied.

### D6 — Public direct grant resolves recipient ambiguity

Because the manifest does not name an initial Patent Licensee, `draft.1` does not attempt to infer one from distribution history, a repository account, a customer relationship or another mutable fact.

Every qualifying person receives its grant directly from the Patent Licensor. This also makes termination recipient-specific: termination of A does not collapse B's independently qualifying direct grant.

### D7 — Sublicensing is not supported in v1 draft.1

For operativeness under this release:

```text
downstream_policy.model = direct-grant
downstream_policy.sublicensing = not-granted
```

A `sublicense-chain`, `hybrid`, `conditional` or `expressly-granted` sublicensing state is rejected by release semantics.

A future release may support sublicensing only after the manifest identifies the upstream recipient and exact downstream authority well enough to avoid implied recipient identity.

### D8 — Optional downstream role fields are absent in v1 draft.1

`have_made`, `affiliates`, `contract_manufacturers` and `customers` MUST be absent for a `draft.1` operative candidate.

Under a public direct grant, allowing those labels to create separate permission paths would either be redundant or create an eligibility bypass. For example, an otherwise ineligible actor must not become authorized merely because an eligible party calls it a contract manufacturer.

A contractor or manufacturer may rely on ECL-PL only if it independently qualifies for the direct grant, or on another Independent Patent Permission.

### D9 — Future have-made / customer / affiliate models require explicit architecture

A later release can add special have-made, affiliate, customer or contract-manufacturer semantics, but should first decide whether those recipients receive:

- their own direct grant;
- a derivative permission;
- a true sublicense;
- a principal-agent service permission; or
- no ECL-PL permission at all because exhaustion/another licence supplies the needed right.

That distinction must be machine-resolvable, not inferred from prose.

### D10 — Fixed narrow defensive termination is intentionally narrower than Apache 2.0

The fixed profile requires initiation of a patent-infringement proceeding directed at the exact Covered Implementation. It does not trigger merely because the Patent Licensee:

- defends itself;
- asserts a compulsory counterclaim or cross-claim;
- seeks non-infringement relief after a concrete threat;
- challenges validity or enforceability;
- participates in an administrative validity proceeding; or
- enforces ECL-PL.

Pre-suit demands and licensing discussions are not included in the fixed trigger in `draft.1`; a broader desired trigger must use a reviewed custom profile.

### D11 — Narrow-profile schema extras are forbidden by release semantics

The schema permits custom-style subordinate fields to appear beside `narrow-covered-implementation`. That could mutate the supposedly fixed profile.

`draft.1` requires those custom fields to be absent. Only a non-conditional `cure_or_withdrawal` selection may accompany the fixed narrow profile.

### D12 — Exhaustion and independent grants cannot be clawed back

Termination is limited to the permission created by the relevant Patent Licensor under the exact bundle. It does not reverse exhaustion or terminate rights obtained under Apache-2.0, GPLv3, MPL-2.0, CERN-OHL-v2, FRAND/RAND, another ECL-PL bundle, another contract or statute.

### D13 — ECL composition is a patent-specific adapter

An ECL reference has no patent effect unless the manifest provides an exact immutable normative reference and exact `patent_specific_effect`.

Even then, only the expressly selected patent consequence operates. A later ECL Bundle, Schedule or registry state cannot mutate the grant.

### D14 — Suspension/limitation needs an objective lifecycle

A `suspend-existing-rights` or `limit-existing-rights` enum is not sufficient by itself. If the exact immutable policy item and `patent_specific_effect` do not objectively determine commencement, affected rights and the end/restoration condition, the bundle is not eligible for operativeness under `draft.1`.

### D15 — No automatic successor-title fiction

The draft does not promise that every patent assignee in every jurisdiction is automatically bound by identical doctrine. It preserves the immutable grant history, leaves successor effect to applicable law and transaction terms, and adds only a limited no-deliberate-defeat obligation to the extent enforceable.

## 7. Open legal questions / likely review targets

These are explicit review targets, not hidden TODOs.

### R1 — Is public direct grant the correct stable-v1 recipient model?

Review whether a public direct grant is legally and commercially coherent for software, hardware, standards and mixed implementations, and whether some use cases require a named/identified initial licensee instead.

### R2 — Eligibility and unilateral licence / contract formation

The Patent Licensor performs attributable assent, but recipients do not necessarily sign bilateral contracts. Review which provisions can function as conditions on a unilateral patent permission and which would require contractual assent in the US, Spain/EU, UK and other relevant jurisdictions.

This is particularly important for cure, anti-evasion, transfer covenants and ECL-derived restrictions.

### R3 — Royalty-free versus no-charge vocabulary

Review whether stable ECL-PL should additionally use `no-charge`, address pre-existing royalty obligations, or avoid wording that might suggest unrelated obligations are waived.

### R4 — Future have-made model

The absence of a special have-made permission is conservative but may make some manufacturing chains impractical where a contractor cannot independently qualify. Test US, European, Spanish and UK treatment and decide whether v1 needs a structured service/manufacturing permission.

### R5 — Customer and distributor scenarios under public direct grant

Test cloud services, embedded components, distributors, resellers, integrators, repair, resale, method claims and exhaustion. Ensure the public direct grant does not accidentally cover unrelated customer implementations or combinations.

### R6 — Narrow termination trigger

Review whether the fixed trigger is too narrow because it excludes pre-suit demands, some ITC/import proceedings, indirect-infringement assertions, controlled affiliates and proxy assertions. Any expansion must preserve legitimate defensive activity.

### R7 — Cure period

The fixed cure period is thirty days after authenticated notice. Review whether cure should exist, whether dismissal must be with prejudice, whether material relief already obtained changes reinstatement, and whether affiliates/proxies need a more precise anti-evasion rule.

### R8 — Transfer / successor enforceability

The no-deliberate-defeat clause requires country-specific review. Stable text must not promise transferee binding that applicable law does not support.

### R9 — Warranty and limitation clauses

Review US state contract law, Spanish/EU mandatory rules, UK reasonableness/consumer rules where applicable, fraud/misrepresentation and whether a patent-only licence needs a broader liability limitation.

### R10 — Governing law and forum

`draft.1` intentionally contains no universal governing-law or exclusive-forum clause. Patent infringement is territorial, while licence/contract interpretation may require conflicts analysis. Stable 1.0 needs a deliberate decision whether to remain forum-neutral or add a grant-specific mechanism.

### R11 — `claim_scope.model: other`

`draft.1` permits `other` only with a complete objectively executable definition plus exact legal-review approval. Consider whether stable v1 should reject `other` entirely and require schema evolution for every new scope model.

### R12 — ECL field-of-use / Restricted Party mechanisms

These remain the highest competition/standards-risk feature. Before stable enablement, test Regulation (EU) 2026/877, the 2026 Technology Transfer Guidelines, Article 101 TFEU outside the block exemption, Article 102 where relevant, SEP/FRAND commitments and national public-policy constraints.

### R13 — Suspension and limitation semantics

If the existing ECL policy pointer cannot deterministically encode restoration/end conditions, narrow those consequences or extend the manifest before stable release.

### R14 — Affiliate/control definition

Because `draft.1` public direct grant does not rely on Affiliate status for permission, the current Affiliate definition has limited operative work. Consider removing it from stable v1 unless another clause genuinely needs it.

### R15 — Version stewardship

The bundle architecture already freezes exact release bytes. Stable ECL-PL should still define how new versions are named/published and make clear that a later version never silently substitutes for an earlier release.

## 8. First adversarial test scenarios

The first PR review should test at least:

1. contributor with no patent title attempts to publish a bundle — no grant;
2. patent owner grants an enumerated claim and later obtains a continuation claim — no automatic retroactive coverage;
3. `direct-grant` + `sublicensing: expressly-granted` — must be incompatible with `draft.1`;
4. `sublicense-chain` — must be incompatible with `draft.1`;
5. `hybrid` downstream model — must be incompatible with `draft.1`;
6. any optional `have_made`, `affiliates`, `contract_manufacturers` or `customers` field — must be incompatible with `draft.1`;
7. ineligible actor is labelled a contract manufacturer by an eligible Patent Licensee — label must not bypass eligibility;
8. independent contractor independently satisfies the public grant conditions — receives its own direct grant;
9. narrow termination profile plus custom trigger fields — must be incompatible with the fixed profile;
10. defensive counterclaim after Patent Licensor sues first — must not trigger fixed retaliation;
11. offensive assertion against a modified product outside the exact Covered Implementation — must not trigger merely by similarity;
12. existing Apache-2.0 patent permission remains independently usable after ECL-PL termination;
13. authorized sale causes exhaustion before later ECL-PL termination — exhaustion is not reversed;
14. exact ECL Bundle later receives a successor Schedule — old PatentGrantBundle does not mutate;
15. FRAND-encumbered SEP plus discriminatory ECL eligibility policy — specialist review required and policy may be unusable;
16. covered patent is assigned after grant — immutable history remains, successor effect requires applicable-law analysis;
17. ECL policy attempts `suspend-existing-rights` without an objectively resolvable restoration condition — must be ineligible for operativeness;
18. recipient does not sign a bilateral contract but relies on the public patent permission — identify which conditions remain enforceable and on what legal theory.

## 9. Qualified review remains mandatory

The repository's `spec/LEGAL-ADVERSARIAL-REVIEW.md` remains controlling for the stable legal-review gate. AI and maintainer review can discover defects but do not satisfy the required independent qualified patent-law and competition-law review.

Issue #3 should not close, and no stable `ECL-PL-1.0.0` PatentLicenseRelease should be created, until the required PLAR surfaces and jurisdiction tracks have actual dispositions for the exact candidate bytes.
