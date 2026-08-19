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
| `defensive_termination` | No default retaliation; fixed narrow profile only in `draft.1`; schema `custom` is rejected until a licensed-right termination scope exists |
| `ecl_bundle_reference` | No implicit patent effect; exact immutable patent-specific consequence only; see `notes/ECL-EXERGIC-COMPOSITION-DRAFT-1.md` |
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
- where ECL-derived eligibility is used, a contractor/manufacturer must also qualify for the exact acts/project under the frozen ECL relationship/project/anti-circumvention rule rather than merely being a different legal entity;
- ECL-derived initial ineligibility must also resolve ECL-PL-authorized sales/dispositions that could create exhaustion in favor of a triggered recipient or project when the policy is intended to block that route; and
- there is no ECL-PL sublicense chain in `draft.1`.

This means that the words `customer`, `Affiliate`, `contract manufacturer`, `reseller`, `integrator` or similar factual roles are not themselves sources of patent permission. They may matter under applicable law, an exact ECL-derived project/association rule, or another Independent Patent Permission, but not as hidden recipient-identity channels under this release.

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

`draft.1` uses an even more bounded fixed `narrow-covered-implementation` profile: the trigger is an affirmative judicial or administrative infringement proceeding directed at the exact Covered Implementation, including a tightly bounded assertion initiated or maintained through a proxy the Patent Licensee directs, controls or knowingly causes. Defensive responses, validity challenges and enforcement of ECL-PL itself are excluded from that fixed trigger.

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

### D4 — Necessary infringement is immutable with respect to recipient-side permissions and defenses

The rule is not “anything that could infringe,” and it also is not “whatever this particular recipient still needs a licence for today.” A claim enters through `necessarily-infringed` only when the exact manifest-defined implementation necessarily satisfies the claim limitations and falls within that claim's substantive exclusionary scope under the relevant patent law.

Recipient-specific licences, covenants, statutory exceptions or limitations, exhaustion, compulsory licences, government-use authorizations, immunities, defenses and other Independent Patent Permissions are applied when deciding whether a particular act actually requires authorization. They do **not** add or remove Covered Patent Claims. This prevents a research exception, time-limited commercial licence or other mutable recipient circumstance from changing the bundle's claim set.

Optional modifications, unrelated additions, external combinations and later versions do not expand the scope unless the exact combination model permits it.

### D5 — Later-acquired claims attach prospectively

Even when the manifest selects `included-if-rule-satisfied`, a later acquisition cannot repair a period in which the Patent Licensor lacked authority. Eligibility begins only after authority is acquired and only if the same immutable claim-scope rule is independently satisfied.

### D6 — Public direct grant resolves recipient ambiguity

Because the manifest does not name an initial Patent Licensee, `draft.1` does not attempt to infer one from distribution history, a repository account, a customer relationship or another mutable fact.

Every qualifying person receives its grant directly from the Patent Licensor. This also makes termination recipient-specific: termination of A does not collapse B's independently qualifying direct grant.

The public-direct model does **not** mean that a terminated recipient can immediately re-enter under the same bundle while continuing the same covered offensive assertion. A still-pending assertion with the required historical narrow-profile nexus blocks both first acquisition and later reacquisition. The bar survives the end of current direction/control over a proxy and applies to qualifying initiation, maintenance, direction, control, knowing causation, and purpose-built transfers used to bring or maintain the assertion. Once the covered assertion ceases to remain pending, acquisition or reacquisition can occur only prospectively if every other condition is satisfied.

### D7 — Sublicensing is not supported in v1 draft.1

For operativeness under this release:

```text
downstream_policy.model = direct-grant
downstream_policy.sublicensing = not-granted
```

A `sublicense-chain`, `hybrid`, `conditional` or `expressly-granted` sublicensing state is rejected by release semantics.

A future release may support sublicensing only after the manifest identifies the upstream recipient and exact downstream authority well enough to avoid implied recipient identity.

### D8 — Optional downstream role fields are absent; outsourcing and disposition routes are still tested

`have_made`, `affiliates`, `contract_manufacturers` and `customers` MUST be absent for a `draft.1` operative candidate.

Under a public direct grant, allowing those labels to create separate permission paths would either be redundant or create an eligibility bypass. An otherwise ineligible actor must not become authorized merely because an eligible party calls another entity its contract manufacturer.

A contractor/manufacturer may rely on ECL-PL only if it independently qualifies **for the actual Covered Acts and project**. Where the bundle uses exact ECL-derived initial eligibility, that evaluation includes the incorporated ECL rule's applicable Covered Associate, Restricted Project, direction/material-benefit and anti-circumvention semantics.

The same fail-closed discipline applies to sales or other ECL-PL-authorized dispositions capable of producing exhaustion in favor of a triggered recipient or project. If an ECL-derived policy is intended to withhold that recipient/project's patent permission but cannot objectively resolve whether an eligible seller's disposition is inside or outside the ECL-PL grant, the candidate fails closed instead of allowing silence to create an authorized-sale bypass. Independent authorization under another legal basis remains independent and may still produce exhaustion under applicable law.

### D9 — Future have-made / customer / affiliate models require explicit architecture

A later release can add special have-made, affiliate, customer or contract-manufacturer semantics, but should first decide whether those recipients receive:

- their own direct grant;
- a derivative permission;
- a true sublicense;
- a principal-agent service permission; or
- no ECL-PL permission at all because exhaustion/another licence supplies the needed right.

That distinction must be machine-resolvable, not inferred from prose.

### D10 — Fixed narrow defensive termination includes pending and tightly bounded proxy assertions

The fixed profile covers an affirmative patent-infringement proceeding directed at the exact Covered Implementation when the Patent Licensee directly or indirectly initiates, maintains, directs, controls or knowingly causes the assertion **or purpose-built transfers/assigns patent or assertion-control rights principally to enable the bringing or maintenance of that covered assertion**. A consummated purpose-built transfer path does not require the transferor to retain continuing control after the transfer if the contemplated covered proceeding is in fact brought or maintained.

The same conduct list applies to the historical acquisition/reacquisition gate. A still-pending covered assertion blocks permission where the person previously initiated, **maintained**, directed, controlled, knowingly caused, or purpose-built-transfer enabled its bringing **or maintenance**. This applies both before first acquisition and after a prior ECL-PL permission has terminated, so the public-direct grant cannot automatically spring back while the same triggering assertion continues.

The proxy limb is deliberately bounded. Passive investment, an arm's-length patent transfer without an assertion purpose, and an unrelated Affiliate assertion do not automatically trigger termination. Ordinary non-controlling legal or litigation funding is also not, by itself, initiation, maintenance, direction, control, knowing causation or a purpose-built transfer. Mere knowledge that the funding facilitates or is necessary to the litigation is insufficient by itself. Funding can enter the trigger only when the funder materially directs/controls the covered assertion or the funding arrangement or a related act is principally intended to procure the bringing or maintenance of that specific assertion. The same funding boundary applies to the historical acquisition/reacquisition gate.

The profile still does not trigger merely because the Patent Licensee:

- defends itself;
- asserts a compulsory counterclaim or cross-claim;
- seeks non-infringement relief after a concrete threat;
- challenges validity or enforceability;
- participates in an administrative validity proceeding; or
- enforces ECL-PL.

Pre-suit demands and licensing discussions remain outside the fixed trigger in `draft.1`.

### D11 — Custom defensive termination is unsupported in draft.1

The schema's custom trigger can describe assertion conduct and target characteristics, but it does not contain a dedicated licensed-right termination-scope field. Two readers could therefore disagree whether the custom trigger forfeits no rights, all rights, selected claims or selected acts.

`draft.1` rejects `defensive_termination.profile: custom` rather than inventing that scope in legal prose. A future schema/release can add an explicit affected-rights field and then review custom termination on that deterministic basis.

### D12 — Narrow-profile schema extras are forbidden by release semantics

The schema permits custom-style subordinate fields to appear beside `narrow-covered-implementation`. That could mutate the supposedly fixed profile.

`draft.1` requires those custom fields to be absent. Only a non-conditional `cure_or_withdrawal` selection may accompany the fixed narrow profile.

### D13 — Cure prevents an interim unlicensed gap

When cure is `available`, the trigger does not terminate the grant immediately. The Patent Licensor must deliver authenticated notice identifying the exact bundle, proceeding and asserted trigger. The thirty-day window starts on receipt.

If the assertion is fully withdrawn/dismissed within that period, termination never becomes effective and Covered Acts during the cure period remain licensed. If the Patent Licensor delays notice, termination is correspondingly delayed; the delay does not manufacture retroactive infringement exposure.

The notice must be verifiably attributable to the Patent Licensor or an authorized representative through an authentication method at least as strong as, or cryptographically traceable to, the bundle's licensor-approval identity mechanism.

If cure fails and termination becomes effective, the public-direct nature of the grant does not independently restore permission while that same covered assertion remains pending; reacquisition is governed by Sections 3.1 and 9.2. A timely cure that prevents termination from ever becoming effective is not treated as a termination/reacquisition event.

### D14 — Exhaustion and independent grants cannot be clawed back

Termination is limited to the permission created by the relevant Patent Licensor under the exact bundle. It does not reverse exhaustion or terminate rights obtained under Apache-2.0, GPLv3, MPL-2.0, CERN-OHL-v2, FRAND/RAND, another ECL-PL bundle, another contract or statute.

The ECL-derived initial-grant gate can define whether **ECL-PL itself** authorizes a sale or other disposition before exhaustion arises. It cannot reverse exhaustion already completed under applicable law or negate an independently authorized transaction outside ECL-PL.

### D15 — ECL composition is a patent-specific adapter

An ECL reference has no patent effect unless the manifest provides an exact immutable normative reference and exact `patent_specific_effect`.

Even then, only the expressly selected patent consequence operates. A later ECL Bundle, Schedule, score, dossier or registry state cannot mutate the grant. Formal Exergism remains upstream of the exact ECL classification; the detailed mapping is in `notes/ECL-EXERGIC-COMPOSITION-DRAFT-1.md`.

### D16 — Suspension/limitation needs an objective lifecycle

A `suspend-existing-rights` or `limit-existing-rights` enum is not sufficient by itself. If the exact immutable policy item and `patent_specific_effect` do not objectively determine commencement, affected rights and the end/restoration condition, the bundle is not eligible for operativeness under `draft.1`.

### D17 — No automatic successor-title fiction

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

The absence of a special have-made permission is conservative. Test whether the public-direct + exact-project eligibility model is workable for foundries, contract manufacturers and multi-customer suppliers and whether stable v1 needs a structured service/manufacturing permission that cannot launder an ineligible principal's activity.

### R5 — Customer, distributor and exhaustion scenarios under public direct grant

Test cloud services, embedded components, distributors, resellers, integrators, repair, resale, method claims and exhaustion. Ensure the public direct grant does not accidentally cover unrelated customer implementations or combinations and does not let an ECL-restricted recipient/project obtain an exhaustion-producing ECL-PL-authorized sale through an otherwise eligible intermediary where the exact policy is intended to block that route.

Also distinguish ECL-PL authorization from an independent authorization that may lawfully produce exhaustion despite the ECL-PL policy.

### R6 — Narrow termination trigger

Review whether the fixed trigger is still too narrow because it excludes pre-suit demands, some ITC/import proceedings or certain indirect-infringement assertions, and whether the bounded proxy rule catches deliberate assertion-entity routing without capturing legitimate arm's-length transfers, financing or defensive conduct.

Review both acquisition and **reacquisition** treatment of a covered proceeding that remains pending. The historical gate should match the post-grant trigger's initiation/maintenance/direction/control/knowing-causation and purpose-built bring/maintain transfer paths, while preserving the narrow proxy exclusions. Also test when a proceeding has ceased to remain pending sufficiently for prospective reacquisition.

Review the funding boundary specifically: mere non-controlling funding and knowledge that funds facilitate the case are intended to remain outside the fixed trigger, whereas material direction/control or a funding arrangement/related act principally intended to procure the specific covered assertion may enter it. Qualified counsel should test whether that distinction is objective and enforceable enough for stable v1.

### R7 — Cure period and authenticated notice

Review whether cure should exist, whether dismissal must be with prejudice, whether material relief already obtained changes the result, whether thirty days is appropriate, and whether the chosen authentication standard is sufficiently objective across individual and legal-entity Patent Licensors.

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

The legal review must also test whether the exact ECL `R`/scoped-`S`, Restricted Project, Covered Associate, exclusion and Independent Remediation boundaries survive patent translation without either an outsourcing/authorized-sale loophole or scope inflation.

### R13 — Suspension and limitation semantics

If the existing ECL policy pointer cannot deterministically encode restoration/end conditions, narrow those consequences or extend the manifest before stable release.

### R14 — Affiliate/control definition

Because `draft.1` public direct grant does not rely on Affiliate status for permission, the current Affiliate definition has limited operative work except where proxy control or an exact ECL relationship rule genuinely uses it. Consider narrowing or removing it from stable v1.

### R15 — Version stewardship

The bundle architecture already freezes exact release bytes. Stable ECL-PL should still define how new versions are named/published and make clear that a later version never silently substitutes for an earlier release.

### R16 — Custom termination scope schema gap

Stable support for a custom defensive profile requires an explicit affected-rights model. Decide whether that field belongs in the manifest schema and whether it should support whole-bundle, enumerated-claims, Covered Acts, implementation partitions or a deliberately smaller closed set.

## 8. First adversarial test scenarios

The first PR review should test at least:

1. contributor with no patent title attempts to publish a bundle — no grant;
2. patent owner grants an enumerated claim and later obtains a continuation claim — no automatic retroactive coverage;
3. recipient holds a separate time-limited commercial licence — its existence/expiration must not mutate the ECL-PL Covered Patent Claim set;
4. recipient relies on a research/statutory exception and later commercializes or the exception changes — the exception must not mutate the ECL-PL Covered Patent Claim set;
5. `direct-grant` + `sublicensing: expressly-granted` — must be incompatible with `draft.1`;
6. `sublicense-chain` — must be incompatible with `draft.1`;
7. `hybrid` downstream model — must be incompatible with `draft.1`;
8. any optional `have_made`, `affiliates`, `contract_manufacturers` or `customers` field — must be incompatible with `draft.1`;
9. ineligible Restricted Party hires a nominally eligible contract manufacturer for the restricted project — exact ECL project/association/anti-circumvention rule must resolve the acts or the candidate fails closed;
10. eligible distributor makes an arm's-length sale to an ineligible recipient under an ECL-derived policy intended to withhold patent permission — the exact frozen rule/effect must resolve the exhaustion-producing disposition or the candidate fails closed;
11. the same sale is independently authorized under another surviving licence — ECL-PL must not pretend it can erase exhaustion produced by that independent authorization;
12. independent contractor acts on an unrelated eligible project — relationship label alone must not make it restricted;
13. narrow termination profile plus custom trigger fields — must be incompatible with the fixed profile;
14. `defensive_termination.profile: custom` — must be incompatible with `draft.1` until the schema contains an explicit licensed-right termination scope;
15. defensive counterclaim after Patent Licensor sues first — must not trigger fixed retaliation;
16. Patent Licensee directs or knowingly causes an assertion entity to sue over the exact Covered Implementation — proxy path must trigger even if the proxy is not formally acting on the Licensee's behalf;
17. unrelated Affiliate independently sues without Licensee direction/control/knowing causation — must not trigger solely by affiliation;
18. covered proceeding was initiated, maintained, directed, controlled, knowingly caused, or purpose-built-transfer enabled before first acquisition and remains pending — person must not acquire the grant even if the qualifying nexus later ceased;
19. Patent Licensee triggers termination, permission terminates, and the same covered proceeding remains pending — public-direct eligibility must not immediately recreate the permission;
20. a person purpose-transfers rights before acquisition to **maintain** an already-filed covered assertion — the historical gate must treat that maintenance path consistently with §9.2;
21. covered proceeding ceases to remain pending — acquisition or reacquisition may occur only prospectively if all other conditions are met;
22. cure available + withdrawal within thirty days of authenticated notice — termination never becomes effective and no reacquisition analysis is needed;
23. delayed Patent Licensor notice — cure clock and termination are delayed rather than creating retroactive infringement exposure;
24. offensive assertion against a modified product outside the exact Covered Implementation — must not trigger merely by similarity;
25. existing Apache-2.0 patent permission remains independently usable after ECL-PL termination;
26. authorized sale causes exhaustion before later ECL-PL termination — exhaustion is not reversed;
27. exact ECL Bundle later receives a successor Schedule — old PatentGrantBundle does not mutate;
28. unfavorable Exergism score with no exact operative ECL classification — no ECL-PL restriction;
29. scoped ECL `S` classification — patent consequence must not expand outside the exact restricted capacity;
30. ECL Independent Remediation Activity or exact Schedule exclusion — patent translation must preserve it;
31. FRAND-encumbered SEP plus discriminatory ECL eligibility policy — specialist review required and policy may be unusable;
32. covered patent is assigned after grant — immutable history remains, successor effect requires applicable-law analysis;
33. ECL policy attempts `suspend-existing-rights` without an objectively resolvable restoration condition — must be ineligible for operativeness;
34. recipient does not sign a bilateral contract but relies on the public patent permission — identify which conditions remain enforceable and on what legal theory;
35. Patent Licensee purpose-transfers patent/assertion-control rights principally to enable a proxy to bring or maintain the covered assertion, the proxy does so, and the Licensee retains no continuing control — the post-grant purpose-built-transfer path must still trigger;
36. Patent Licensee provides ordinary non-controlling litigation funding and knows the funds facilitate or are necessary to the covered case, but does not direct/control it and has no arrangement/act principally intended to procure the assertion — funding alone must not trigger;
37. funding is coupled with material direction/control, or the funding arrangement/related act is principally intended to procure the specific covered assertion — the funding path may satisfy the same narrow trigger and historical gate.

## 9. Qualified review remains mandatory

The repository's `spec/LEGAL-ADVERSARIAL-REVIEW.md` remains controlling for the stable legal-review gate. AI and maintainer review can discover defects but do not satisfy the required independent qualified patent-law and competition-law review.

Issue #3 should not close, and no stable `ECL-PL-1.0.0` PatentLicenseRelease should be created, until the required PLAR surfaces and jurisdiction tracks have actual dispositions for the exact candidate bytes.