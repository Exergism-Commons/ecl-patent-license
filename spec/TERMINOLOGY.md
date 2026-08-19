# ECL-PL Terminology

> **Status: architecture draft. No licensing effect.** Defined terms in a future operative patent licence may differ after legal review.

This document keeps patent concepts separate from ECL copyright/software concepts. Similar words must not silently import different legal meanings across the two instruments.

## Patent Licensor

A person or legal entity that owns, controls, or is otherwise authorized to grant the patent rights identified by a PatentGrantBundle.

A `Patent Licensor` is not created by Git authorship, copyright ownership, ECL contributor status, employment by an ECL adopter, organizational membership, repository maintainership, or contribution to this repository.

## Patent Licensee

A person or legal entity exercising a patent permission expressly granted under an operative PatentGrantBundle.

The future legal text must decide whether and when affiliates are included within the Patent Licensee definition or receive separate/direct permissions.

## PatentLicenseRelease

An immutable, content-addressed exact version of the operative ECL-PL legal text.

A PatentLicenseRelease states legal rules. It does not by itself prove that any person owns or has authority to license any particular patent.

## PatentGrantManifest

An immutable, content-addressed grant declaration made by a Patent Licensor and associated with one exact PatentLicenseRelease.

It records the identity, authority representation, Covered Implementation, claim-scope rule, known patent provenance and other grant-specific choices needed by the licence.

## PatentGrantBundle

A closed immutable release package whose legal grant core is one exact `PatentLicenseRelease` plus one exact `PatentGrantManifest`, and whose bundle identity also covers every retained evidence member required by the applicable release/security profiles:

```text
PatentGrantBundle
  = PatentLicenseRelease
  + PatentGrantManifest
  + required retained evidence
```

The retained evidence supports reproducible validation and provenance; it does not silently become additional licence text or grant terms unless the PatentLicenseRelease or PatentGrantManifest expressly gives a retained artifact normative effect.

`BUNDLE-INDEX` content-addresses the complete non-index member set. Every operative PatentGrantBundle is physically serialized as `ECLPLB1` under `spec/CONTAINER-PROFILE.md`.

## Covered Implementation

The objectively identifiable technology against which the PatentGrantBundle resolves patent scope. It may be software, hardware, a specification, an implementation profile, a product architecture, a mixed system, or another sufficiently determinate technical subject.

A Covered Implementation is not automatically identical to an ECL `Software` object or copyright work.

## Covered Patent Claim

A patent claim that:

1. falls within the exact claim-scope rule of the PatentGrantBundle; and
2. is owned, controlled or otherwise licensable by the Patent Licensor to the extent required for the grant.

The final claim-scope rule is intentionally not fixed by this terminology document.

## Covered Patent / Covered Patent Family

Provenance terms for a patent, application or patent family containing or potentially containing Covered Patent Claims.

A patent being listed in provenance does not necessarily mean every claim in it is licensed. Conversely, a future legal model based on a normative necessary-infringement rule must specify whether an omitted known identifier limits the grant or merely represents incomplete provenance.

## Claim-Scope Rule

The normative rule that decides whether a particular patent claim is a Covered Patent Claim.

Candidate models include:

- necessarily-infringed claims;
- enumerated claims;
- a hybrid necessary-infringement rule with an evidentiary inventory; or
- another bounded model approved through legal review.

`All patents`, `all IP`, or other undefined portfolio-wide formulations are rejected as architecture defaults.

## Covered Act

An act for which the PatentGrantBundle gives permission under a Covered Patent Claim because, absent the grant or another independent legal basis, applicable patent law would require authorization from the Patent Licensor.

`Covered Act` is jurisdiction-relative. Software/product verbs such as `deploy`, `operate`, `distribute`, `export`, `host` or `transfer` do not become universal patent-exclusive acts merely because a manifest uses those words to describe factual conduct.

## Direct Grant

A patent permission granted by the Patent Licensor directly to a recipient or class of recipients under the operative PatentGrantBundle, rather than through a sublicense from an intermediary.

## Sublicense

A patent permission granted by a Patent Licensee using sublicensing authority conferred by the Patent Licensor.

Sublicensing authority must be express where required. It must not be inferred from a general permission to distribute technology.

## Have-Made Right

A permission, where expressly granted and legally meaningful, allowing a Patent Licensee to have a third party manufacture or otherwise perform covered manufacturing acts for the Patent Licensee within the scope of the grant.

The final licence must determine boundaries for contract manufacturers, manufacturing for multiple customers, stock, resale and post-termination inventory.

## Affiliate

An entity linked through control relationships to another entity. The exact threshold and temporal treatment are not fixed here.

The legal draft must resolve acquisitions, divestitures, newly controlled entities, loss of control and whether affiliate rights are direct or derivative.

## Patent Grant Policy

Grant-specific normative choices selected by an exact PatentGrantManifest to the extent permitted by the PatentLicenseRelease. A policy may address such matters as scope profile, sublicensing, defensive termination or an expressly incorporated ECL policy reference.

A policy cannot create authority the Patent Licensor does not possess.

## ECL Bundle Reference

An optional immutable, content-addressed reference from a PatentGrantManifest to one exact ECL Bundle.

An ECL Bundle Reference has no effect unless the PatentLicenseRelease and PatentGrantManifest expressly state what patent-specific rule it controls.

A mutable branch, channel, registry view, `latest` pointer or later ECL Schedule is not an ECL Bundle Reference.

## ECL-Referenced Patent Policy

A candidate patent-specific policy under which eligibility, field-of-use or another expressly identified patent-grant rule resolves against one exact incorporated ECL Bundle.

It is **not** the ECL copyright grant and must receive independent patent/competition/exhaustion review before becoming operative.

## Restricted Patent Licensee / Restricted Patent Activity

Placeholder architecture terms for any future ECL-PL rule that withholds or terminates patent permission based on an expressly incorporated policy.

These terms must not be defined merely by importing `Restricted Party`, `Restricted Project` or `Prohibited Use` from ECL without specifying the exact ECL Bundle and the patent-law consequence.

## Offensive Patent Assertion

A candidate defined trigger for defensive termination. The exact operative definition remains open.

The review must distinguish offensive initiation from defensive counterclaims, cross-claims, declaratory relief, invalidity/non-infringement proceedings, compulsory responses and enforcement of ECL-PL itself.

## Defensive Termination

Termination or narrowing of a patent permission because the Patent Licensee satisfies an express patent-assertion trigger in the operative PatentGrantBundle.

Defensive Termination is not ECL copyright termination and must have its own scope, timing, cure, reinstatement and downstream-survival rules.

## Independent Patent Permission

Any right to perform an act independently of ECL-PL, including another patent licence, covenant, statutory exception, exhaustion, compulsory licence, government-use regime, authorization from another relevant rightsholder, or absence of infringement.

ECL-PL must not purport to revoke or narrow Independent Patent Permissions it does not control.

## Exhausted Article

A product or article for which applicable law has exhausted some or all relevant patent rights as a result of an authorized transaction or other legally sufficient event.

The term is descriptive only. Whether exhaustion occurred and its territorial/scope consequences are determined by applicable law.

## Provenance Record

Structured evidence about ownership, control, applications, patents, family relationships, assignments, jurisdictions, status, encumbrances and authority.

Provenance supports auditability; it does not manufacture title or licensing authority.

## Operative

A status reserved for a PatentGrantBundle that satisfies the project's release requirements, including any required qualified legal review.

Schema validity, publication in Git, maintainer approval or a `main` branch location does not by itself make an artifact operative.
