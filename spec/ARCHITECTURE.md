# ECL Patent License Architecture

> **Status: architecture draft. No licensing effect.** This document defines the proposed structure of ECL-PL before operative patent-license text exists.

## 1. Purpose

ECL-PL is intended to let a patent owner or other authorized patent licensor make an explicit, auditable and bounded patent grant while preserving a deliberate relationship—when chosen—to the Exergic Commons License ecosystem.

ECL-PL is not a mechanism for inferring patent rights from copyright contributions, repository participation, publication, source availability, ECL adoption, or vague references to intellectual property.

The design principle is:

> **Patent rights must be express, attributable, bounded, immutable as released, and independently enforceable under the law that actually governs the patent right.**

## 2. Frozen architecture: separate companion instrument

ECL-PL and ECL are separate legal instruments.

The following invariants are normative for this architecture:

1. Applying ECL to software or other material does **not** apply ECL-PL.
2. Applying ECL-PL does **not** alter the copyright/software-right grant under ECL.
3. ECL contributor status does **not** make a person a Patent Licensor.
4. Contribution to this repository does **not** itself grant any patent right.
5. No patent right is granted except through an express operative patent grant from a person or entity with authority to grant it.
6. ECL-PL versions and patent grants are versioned independently from ECL versions and ECL Schedules.
7. Mutable repository, registry, governance, branch or channel state never silently changes an already released patent grant.
8. Patent-specific termination does not automatically terminate copyright/software rights under ECL, and ECL termination does not automatically terminate ECL-PL rights, unless independently authorized operative text expressly creates that result and it survives legal review.

## 3. Core object model

### 3.1 `PatentLicenseRelease`

An immutable exact release of the operative ECL-PL legal text.

A `PatentLicenseRelease` contains legal rules but, standing alone, does not identify a particular patent owner, patent family or covered implementation and therefore does not create a project-specific patent grant unless the operative text expressly provides otherwise.

### 3.2 `PatentGrantManifest`

An immutable declaration by a specific Patent Licensor describing the grant to which a `PatentLicenseRelease` applies.

At minimum it must identify:

- the Patent Licensor;
- represented authority to grant;
- the exact PatentLicenseRelease;
- the Covered Implementation or other grant scope;
- the claim-scope rule;
- known patent/application/family identifiers where available;
- jurisdictions where known;
- effective date;
- sublicensing policy;
- affiliates/contractors/manufacturers/customer policy where applicable;
- optional immutable ECL Bundle reference;
- known encumbrances or independently granted rights material to interpretation where disclosure is required by the eventual legal instrument; and
- immutable content identity.

### 3.3 `PatentGrantBundle`

The release unit that combines the exact operative patent-license text and exact project-specific patent grant:

```text
PatentGrantBundle = exact PatentLicenseRelease + exact PatentGrantManifest
```

The Bundle must content-address both components.

An ECL Bundle reference, if any, is an expressly incorporated policy reference inside the PatentGrantManifest. It is never inferred from repository location, current ECL state, a mutable channel or a later Schedule.

### 3.4 `Covered Implementation`

The objectively identifiable technology, implementation, specification, design, product architecture, source/hardware artifact or other subject to which the grant's patent scope is tied.

A Covered Implementation must be defined narrowly enough that a recipient can determine what implementation the claim-scope rule tests against.

### 3.5 `Covered Patent Claim`

A patent claim that falls within the claim-scope rule chosen by the operative PatentGrantBundle and that the Patent Licensor owns, controls, or is otherwise authorized to license to the necessary extent.

No architecture document may assume that all patents owned by a Patent Licensor are covered.

## 4. Claim-scope design

The operative licence must adopt a bounded claim-scope model. The architecture leaves the final wording open but rejects an unqualified grant of `all patents` or `all intellectual-property rights`.

The starting hypothesis is a **necessary-infringement / licensable-claims model**, informed by established public licences but independently drafted and reviewed:

- claims must be owned, controlled or licensable by the Patent Licensor; and
- the claims must be necessarily infringed by practicing the Covered Implementation within the defined grant scope, subject to express treatment of combinations, modifications and later-acquired claims.

The design review must separately decide:

- whether later-acquired claims can enter the grant;
- whether combinations with third-party technology expand coverage;
- whether modifications remain covered and to what boundary;
- treatment of method, process, apparatus, system and product-by-process claims;
- treatment of continuation/divisional/reissue/validation family members; and
- whether an enumerated claim list can narrow, supplement or evidence the normative rule.

A manifest's known patent list is provenance. Unless the eventual legal text expressly chooses an enumerated-only model, an incomplete inventory must not accidentally define a broader or narrower legal grant than intended.

## 5. Covered Acts are jurisdiction-relative

ECL-PL must not transplant software verbs into patent law as if patent-exclusive acts were universal.

The eventual grant should be framed around acts that would otherwise require authorization from the Patent Licensor under applicable patent law with respect to a Covered Patent Claim, and may give jurisdiction-specific examples.

For example, United States patent law identifies making, using, offering to sell, selling and importing as direct-infringement acts in 35 U.S.C. § 271(a), while also separately addressing induced, contributory, export-component and process-product infringement. Spanish patent law uses its own statutory formulation in Ley 24/2015 arts. 59–60. European patents take effect through the rights of the relevant national patent and infringement is governed by national law under EPC art. 64.

Accordingly, terms such as `deploy`, `operate`, `distribute`, `export` or `transfer` may describe factual activity but must not be presented as freestanding universal patent rights unless the applicable-law analysis supports that result.

## 6. Patent Licensor authority and provenance

A Patent Licensor must be a person or entity that owns, controls, or is otherwise authorized to grant the relevant Covered Patent Claims.

The manifest and review system must be capable of recording, where applicable:

- owner / granting entity;
- inventor(s) as provenance without confusing inventorship with ownership;
- assignments and chain of title;
- employee/employer or commissioned-invention basis;
- co-ownership;
- authority to sublicense where sublicensing is offered;
- security interests, exclusive licences or other encumbrances that may limit authority;
- standards/FRAND or other pre-existing commitments;
- patent/application numbers and jurisdictions;
- family relationships;
- grant, validation, expiration, lapse, abandonment, revocation or transfer events; and
- the date and evidence basis for represented authority.

The repository must never infer patent authority solely from a copyright header, Git authorship, contributor status, organizational membership or maintainer approval.

## 7. Sublicensing and downstream model

Sublicensing is a design choice, not an assumed property.

The legal draft must decide separately:

- direct grants to downstream recipients versus sublicensing;
- whether distributors need a sublicensing right at all;
- `have made` treatment for contract manufacturers;
- affiliates and changes of control;
- cloud/service providers acting for the licensee;
- resellers and integrators;
- end customers; and
- survival of downstream rights after an upstream licensee's termination.

A direct-grant model is preferred as the initial hypothesis where it can reduce dependency on intermediary authority, but no choice is frozen until legal review.

## 8. ECL composition

ECL composition is **opt-in and immutable**.

A PatentGrantManifest may explicitly reference an exact content-addressed ECL Bundle and may adopt a patent-specific policy whose eligibility or field-of-use conditions resolve against that exact ECL Bundle.

It must not reference `latest`, `stable`, a mutable branch, current registry state or future governance decisions as a mechanism that retroactively changes an existing patent grant.

Example architecture:

```text
ECL PatentGrantBundle PG-001
  PatentLicenseRelease: ECL-PL-1.0.0
  PatentGrantManifest: PG-001
  Optional ECL policy reference:
    ECL-1.0.0@RP-2026.10.02.1
```

If a later ECL Schedule designates a new Restricted Party, that later Schedule does not alter PG-001. A Patent Licensor must issue a new grant/bundle if it wishes new patent permissions to resolve against a later policy state, subject to law governing existing grants and any irrevocability promises.

## 9. Restricted Parties, projects and field-of-use

ECL-style restrictions are not presumed valid merely because a patent is an exclusionary right.

Before an operative licence incorporates Restricted Party, Restricted Project or field-of-use restrictions, the project must independently test:

- patent-law scope and misuse/abuse doctrines where relevant;
- exhaustion;
- competition/antitrust law;
- EU technology-transfer rules;
- standards-essential patent and FRAND commitments;
- compulsory licences and public-interest limitations;
- government-use or sovereign regimes;
- contractual notice/assent where the theory depends on contract rather than the patent right itself; and
- territorial differences in the acts the patent owner may control.

An ECL designation must never be treated as automatically enlarging the legal scope of a patent.

## 10. Exhaustion, statutory exceptions and independent rights

ECL-PL must contain a savings rule making clear that it does not recreate patent control after applicable exhaustion or restrict acts that applicable law permits independently of the Patent Licensor's authorization.

The legal review must cover at least:

- authorized sale / exhaustion scenarios;
- private/non-commercial and experimental exceptions where applicable;
- regulatory-use exceptions;
- prior-user rights;
- compulsory licences;
- government-use regimes;
- repair/reconstruction issues where relevant; and
- cross-border exhaustion differences.

A PatentGrantBundle does not terminate or narrow an independently existing permission arising from statute, exhaustion, another licence or another rightsholder.

## 11. Overlapping grants

ECL-PL operates only on rights the particular Patent Licensor can grant and condition.

It cannot rescind, rewrite or narrow rights independently obtained under Apache-2.0, GPLv3, MPL-2.0, CERN-OHL, a commercial patent licence, a standards commitment, a covenant not to sue, exhaustion, compulsory licence or another legal basis.

Compatibility analysis must therefore distinguish:

1. whether ECL-PL can coexist with another grant;
2. whether the same patent claim is already granted on broader or inconsistent terms;
3. whether sublicensing or downstream propagation conflicts exist;
4. whether termination rules conflict; and
5. whether combining technologies is practically possible even when the copyright licences coexist.

## 12. Patent retaliation / defensive termination

A narrow defensive mechanism is preferred to broad patent-aggression termination.

The starting hypothesis is that termination should target **offensive assertion against the Covered Implementation or a tightly defined covered ecosystem**, rather than unrelated patent litigation by the licensee.

The draft must separately resolve:

- direct claims;
- induced/contributory infringement allegations;
- declaratory-judgment actions;
- validity/non-infringement proceedings;
- counterclaims;
- cross-claims;
- defensive claims compelled by existing litigation;
- affiliates and controlled entities;
- acquired entities and patent portfolios;
- notices and cure/withdrawal;
- reinstatement; and
- downstream survival.

No architecture text itself triggers patent termination.

## 13. Release lifecycle

The intended lifecycle is:

```text
architecture
  -> terminology
  -> manifest/schema
  -> compatibility/adversarial model
  -> candidate PATENT-LICENSE
  -> exact candidate + immutable inputs
  -> qualified jurisdictional review
  -> fixes / delta review
  -> stable PatentLicenseRelease
  -> specific PatentGrantManifest
  -> PatentGrantBundle
```

The project must not call a draft grant `operative` merely because the schema validates.

## 14. Required legal-review tracks

Before stable release, the project must obtain dedicated review covering at least:

- United States patent law;
- EPC framework plus relevant national implementation;
- Spain;
- United Kingdom;
- EU competition law / technology-transfer rules; and
- cross-border licensing, assignment and exhaustion.

AI, maintainer and community review are useful adversarial inputs but do not replace qualified independent patent/licensing counsel.

## 15. Comparative authorities to test

The project should compare rather than mechanically copy established models, including:

- Apache License 2.0, section 3: https://www.apache.org/licenses/LICENSE-2.0
- GNU GPLv3, section 11: https://www.gnu.org/licenses/gpl-3.0.html
- Mozilla Public License 2.0, sections 1.11, 2.1 and 5: https://www.mozilla.org/MPL/2.0/
- CERN Open Hardware Licence v2 variants: https://ohwr.org/licences/

Key statutory/review anchors include:

- 35 U.S.C. § 271: https://uscode.house.gov/view.xhtml?req=(title:35%20section:271%20edition:prelim)
- EPC art. 64: https://www.epo.org/en/legal/epc/2020/a64.html
- Spain, Ley 24/2015 de Patentes: https://www.boe.es/buscar/act.php?id=BOE-A-2015-8328
- EU Technology Transfer Block Exemption Regulation / Guidelines: https://competition-policy.ec.europa.eu/antitrust-and-cartels/legislation/ttber_en

These are review inputs, not a representation that any draft is legally valid in all circumstances.

## 16. Non-goals

This architecture does not:

- create or grant any patent right;
- claim ownership of any patent;
- define an ECL-PL 1.0 legal text;
- guarantee that an ECL field-of-use restriction is enforceable;
- turn ECL into a patent licence;
- make ECL-PL an OSI or open-source licence;
- override mandatory law, exhaustion, third-party rights or existing grants; or
- claim universal enforceability.
