# ECL-PL Legal Adversarial Review

> **Status: architecture-stage review specification. No licensing effect.** This document defines attack surfaces that must be resolved before any ECL-PL release is represented as legally reviewed or operative.

## 1. Review objective

The review must try to falsify the patent-licensing mechanism rather than merely improve drafting.

The controlling question is:

> **Does the exact PatentLicenseRelease plus exact PatentGrantManifest grant, withhold, condition and terminate only patent rights the identified Patent Licensor can lawfully control, with objectively knowable scope and defensible downstream effects in the required jurisdictions?**

A valid copyright licence, a valid patent, a correct ECL designation and a syntactically valid manifest do not answer that question.

## 2. Immutable review inputs

A completed review must bind itself to immutable inputs, including at least:

- exact candidate `PATENT-LICENSE` hash;
- exact architecture/terminology/composition/versioning inputs used by the review;
- exact `patent-grant.schema.json` used by the review;
- one or more exact representative PatentGrantManifests or test vectors;
- review date;
- jurisdictional scope;
- reviewer identity, competence and independence; and
- every material amendment made in response to findings.

Review of a moving branch is not sufficient for a stable release.

## 3. Mandatory attack surfaces

Every surface `PLAR-01` through `PLAR-16` must receive a disposition for the exact release candidate.

### PLAR-01 — Patent Licensor title, control and authority

Attack whether the identified Patent Licensor actually owns, controls or is otherwise authorized to grant every purported Covered Patent Claim.

Test:

- assignment chain;
- employee/employer inventions;
- commissioned inventions;
- joint ownership;
- exclusive licences;
- sublicensing authority;
- security interests and encumbrances;
- corporate subsidiaries/parents;
- acquired portfolios;
- inventorship versus ownership; and
- third-party rights.

A representation in a manifest is not itself proof of authority.

### PLAR-02 — Claim-scope certainty

Attack the exact rule defining Covered Patent Claims.

Test whether a recipient can determine:

- which claims are covered;
- whether pending applications are relevant;
- whether later-acquired claims enter the grant;
- whether continuation/divisional/reissue/validation family members enter the grant;
- whether combinations expand coverage;
- whether modifications expand or lose coverage;
- whether an enumerated list is normative or evidentiary; and
- whether invalid, expired, revoked or limited claims are handled coherently.

Reject vague portfolio-wide language unless deliberately and lawfully intended.

### PLAR-03 — Covered Implementation boundary

Attack whether the Covered Implementation is objectively identifiable.

Test source, binary, hardware, firmware, specifications, protocols, interfaces, services, distributed systems, modifications, combinations and later versions.

A claim-scope rule based on necessary infringement is unusable if the implementation against which necessity is tested is indeterminate.

### PLAR-04 — Territorial Covered Acts

Map the operative grant to the actual exclusionary acts recognized in each jurisdiction.

Do not assume software vocabulary such as `deploy`, `operate`, `host`, `distribute` or `export` describes a universal patent-exclusive right.

At minimum test direct infringement, indirect/contributory infringement, process claims, imports, offers for sale and cross-border components where applicable.

### PLAR-05 — Direct grant, sublicense and have-made rights

Determine whether downstream recipients receive rights directly from the Patent Licensor or through sublicensing.

Test:

- contract manufacturers;
- foundries;
- cloud providers;
- integrators;
- resellers;
- distributors;
- end customers;
- affiliates; and
- changes of control.

The review must determine whether `have made` or similar rights are necessary and what limits apply.

### PLAR-06 — Exhaustion and authorized transactions

Attack any attempt to use ECL-PL to recreate patent control over an article after applicable exhaustion.

Test:

- authorized domestic sale;
- cross-border sale/import;
- component versus completed product;
- repair/reconstruction;
- downstream resale;
- post-termination inventory; and
- method claims connected to sold products.

The review must distinguish termination of a licence from rights independently resulting from exhaustion.

### PLAR-07 — Statutory exceptions and non-licence permissions

Test private/non-commercial exceptions, experimental use, regulatory-use exceptions, prior-user rights, compulsory licences, government-use regimes and other jurisdiction-specific limitations.

ECL-PL must not represent an act as prohibited by patent law where applicable law does not require Patent Licensor authorization.

### PLAR-08 — ECL Restricted Party / Project field-of-use model

If any candidate grant incorporates ECL-based restrictions, attack the patent-specific legal mechanism independently from ECL copyright analysis.

Test:

- whether the relevant patent right supports the restriction;
- objective incorporation of the exact ECL Bundle;
- knowledge standards;
- temporal scope;
- initial ineligibility versus later termination;
- anti-circumvention;
- affiliates/intermediaries;
- exhaustion;
- competition/antitrust law;
- public policy;
- standards commitments; and
- remedies.

No ECL designation can enlarge the Patent Licensor's patent rights.

### PLAR-09 — EU competition / technology-transfer rules

For EU-relevant grants, perform an independent Article 101 TFEU / technology-transfer review.

At minimum evaluate the current Technology Transfer Block Exemption Regulation and Guidelines, including applicability, market-share assumptions, competitors/non-competitors, hardcore restrictions, excluded restrictions and circumstances requiring individual assessment.

A public/no-charge licence is not automatically outside competition law.

### PLAR-10 — Standards-essential patents and prior commitments

Determine whether any Covered Patent Claim is or may be standards-essential or subject to FRAND/RAND, patent-pool, standards-body, covenant or other prior commitment.

Test whether ECL-PL eligibility, field-of-use, retaliation or discriminatory restrictions conflict with those obligations.

### PLAR-11 — Overlapping patent grants

Identify permissions independently granted under:

- Apache-2.0;
- GPLv3 / AGPLv3;
- MPL-2.0;
- CERN-OHL-v2;
- commercial agreements;
- covenants not to sue;
- standards commitments;
- patent pools;
- prior settlements; or
- other PatentGrantBundles.

ECL-PL cannot terminate or narrow rights the asserting Patent Licensor lacks authority to revoke.

### PLAR-12 — Defensive termination / patent retaliation

Attack the trigger as both overbroad and underinclusive.

Test:

- affirmative infringement actions;
- indirect-infringement allegations;
- cease-and-desist demands;
- ITC/import proceedings where relevant;
- declaratory judgments;
- invalidity challenges;
- non-infringement actions;
- counterclaims;
- cross-claims;
- compulsory defensive responses;
- affiliates;
- acquired entities;
- NPE/portfolio transfers; and
- assistance to third-party claims.

The mechanism must define timing, scope, notice, cure/withdrawal, reinstatement and downstream survival.

### PLAR-13 — Termination scope and downstream survival

Determine exactly which rights terminate:

- from which Patent Licensor;
- under which PatentGrantBundle;
- for which Covered Patent Claims;
- for which Covered Implementation;
- at what time; and
- with what effect on manufacturers, affiliates, customers and previously authorized downstream recipients.

One Patent Licensor must not terminate another Patent Licensor's independent grant absent explicit authority.

### PLAR-14 — Transfer, acquisition and successor scenarios

Test what happens when:

- the Patent Licensor assigns the patent;
- the Patent Licensee is acquired;
- the Patent Licensor is acquired;
- a covered subsidiary is sold;
- a patent is transferred to an assertion entity;
- an exclusive license is later granted; or
- title is successfully challenged.

The repository record and legal consequences must not be conflated.

### PLAR-15 — Patent validity, expiration, abandonment and remedies

ECL-PL must not imply that a listed patent is valid, enforceable, in force, necessarily infringed or immune from challenge.

Test warranty/disclaimer language, expiration/lapse, revocation, limitation, pending applications, marking/notice, damages, injunction standards and jurisdiction-specific remedy limits.

### PLAR-16 — Cross-border conflicts and governing law

Review territorial patent law separately from contractual rules governing the licence instrument.

Test:

- choice of law;
- forum;
- territorial infringement law;
- recognition/enforcement;
- assignments and registration formalities;
- cross-border exhaustion;
- multinational products/processes; and
- conflicts between a worldwide licence promise and territorial patent rights.

## 4. Required jurisdiction tracks

Before an ECL-PL 1.0 release can satisfy the project's legal gate, substantive review must cover at least:

| Track | Required scope |
| --- | --- |
| United States | Patent grant scope; 35 U.S.C. § 271 infringement architecture; exhaustion; remedies; government/other statutory regimes where relevant; patent misuse/antitrust interaction |
| EPC / Europe | EPC effect framework plus the fact that infringement/effects depend materially on national law; validation/territorial issues |
| Spain | Ley 24/2015 patent rights, limits/exhaustion, contractual licences, sublicensing defaults, authority/liability and related contract/competition issues |
| United Kingdom | Patent infringement, exceptions, exhaustion/retained EU effects where relevant, licence construction, competition and remedies |
| EU competition | Article 101 TFEU, current TTBER/Technology Transfer Guidelines and any applicable standards/FRAND layer |
| Cross-border | Territoriality, conflicts, assignment, multinational supply chains, exhaustion and recognition/enforcement |

A required track may identify a material limitation. Omitting the track is not equivalent to documenting the limitation.

## 5. Minimum reviewer gate

The stable ECL-PL legal-review gate should require at least:

1. two substantive independent qualified reviews of the exact candidate;
2. at least one reviewer with patent-licensing / patent-litigation competence;
3. meaningful United States coverage;
4. meaningful Europe/EU coverage including Spain or a dedicated Spanish review;
5. one explicit adversarial/falsification pass;
6. competition-law expertise where an ECL-style field-of-use or discriminatory eligibility model is enabled; and
7. specialist standards/FRAND review where the candidate is intended for standards-essential patents.

AI, maintainer self-review, schema validation and community comments may identify findings but do not satisfy this minimum.

## 6. Findings and severity

Every material finding must be recorded with one of:

- `BLOCKER` — credible failure could defeat core patent scope/authority or make the stable grant materially misleading;
- `MAJOR` — material legal uncertainty requiring fix, narrowing or explicit accepted limitation;
- `MINOR` — drafting/clarity problem unlikely alone to defeat the intended model; or
- `NOTE` — hardening or documented limitation without current gate effect.

Each finding should record:

```text
id
status
severity
reviewed_artifact_hashes
PLAR_surface
provision
jurisdiction
attack
legal_authority
consequence
proposed_mitigation
resolution
reviewer
review_date
```

## 7. Stable-release conditions

A stable release must not be represented as having completed legal review unless:

- all PLAR surfaces have a disposition;
- all required jurisdiction tracks have a disposition;
- the required independent qualified reviews are complete;
- no unresolved BLOCKER remains;
- every MAJOR is resolved, narrowed or expressly accepted as a documented limitation/risk under the project's release policy;
- all material legal changes receive delta review; and
- the release record binds to the exact PatentLicenseRelease and exact review inputs actually reviewed.

A grant-specific PatentGrantManifest may require additional review where its patents, field-of-use policy, standards status, jurisdictions or encumbrances introduce facts not covered by the generic licence review.

## 8. Initial authoritative anchors

Architecture-stage reviewers should at minimum test against current primary/official sources, including:

- 35 U.S.C. § 271: https://uscode.house.gov/view.xhtml?req=(title:35%20section:271%20edition:prelim)
- EPC art. 64: https://www.epo.org/en/legal/epc/2020/a64.html
- Spain Ley 24/2015: https://www.boe.es/buscar/act.php?id=BOE-A-2015-8328
- European Commission TTBER/Technology Transfer Guidelines: https://competition-policy.ec.europa.eu/antitrust-and-cartels/legislation/ttber_en

Comparative licence texts include:

- Apache-2.0: https://www.apache.org/licenses/LICENSE-2.0
- GPLv3: https://www.gnu.org/licenses/gpl-3.0.html
- MPL-2.0: https://www.mozilla.org/MPL/2.0/
- CERN-OHL-v2: https://ohwr.org/licences/

These references are starting authorities, not a substitute for qualified legal review or jurisdiction-specific research.
