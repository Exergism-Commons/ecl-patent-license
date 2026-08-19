# ECL-PL Patent Compatibility Matrix

> **Status: architecture-stage analysis framework. No compatibility conclusion in this file is a legal opinion.**

## 1. Purpose

Patent compatibility must be analyzed separately from copyright-license compatibility.

A project can be copyright-compatible while patent permissions differ, overlap, terminate differently, forbid additional restrictions, or depend on different rightsholders.

Accordingly, ECL-PL compatibility uses multiple dimensions rather than a single `compatible / incompatible` label.

## 2. Required dimensions

For each comparison model, record at least:

1. **Grant coexistence** — can the ECL-PL grant coexist with the other patent grant without purporting to rewrite it?
2. **Claim-scope overlap** — which claims may be covered by one, both or neither grant?
3. **Downstream path** — direct grant, sublicense, propagation or no patent grant.
4. **Additional restrictions** — whether ECL-style field-of-use or licensee restrictions conflict with obligations attached to the other grant.
5. **Defensive termination** — trigger, scope, defensive exceptions, timing and downstream effects.
6. **Notice / provenance** — required notices, contributor identity and known patent disclosures.
7. **Irrevocability / reinstatement** — whether and how patent rights can terminate or return.
8. **Exhaustion / independent rights** — rights that do not depend on either licence.
9. **Combination boundary** — whether combining covered technology with other material expands patent scope.
10. **Practical distribution result** — whether the combined product can realistically be distributed/practiced under all applicable terms.

## 3. Baseline matrix

| Model | Express patent grant | Scope anchor | Defensive termination | Initial ECL-PL observation |
| --- | --- | --- | --- | --- |
| Apache-2.0 | Yes | Contributor-licensable claims necessarily infringed by Contribution alone or with the Work | Patent litigation alleging Work/Contribution infringement; includes cross-claim/counterclaim | Existing Apache grant is an independent permission ECL-PL cannot narrow |
| GPLv3 | Yes | Contributor `essential patent claims` infringed by permitted ways of making/using/selling contributor version; excludes claims infringed only by further modification | GPL termination mechanics plus patent provisions; patent obligations are integrated into GPLv3 architecture | ECL-PL additional patent restrictions require careful GPLv3 compatibility analysis |
| MPL-2.0 | Yes | Licensable Patent Claims infringed by Contribution/Contributor Version acts defined by MPL | Initiated patent litigation against Contributor Version; declaratory actions, counterclaims and cross-claims excluded | MPL provides a useful narrow-retaliation comparator, but its grant remains independent |
| MIT/BSD-style copyright licences | Usually no express patent grant in canonical short forms | N/A unless another grant exists | Usually none | ECL-PL may coexist as a separate patent instrument, subject to facts/other grants |
| CERN-OHL-v2 | Patent treatment integrated into open-hardware licence | Variant-specific Covered Source/Product and patent-right definitions | Variant-specific | Important hardware comparator; ECL-PL must avoid contradicting existing CERN-OHL patent permissions |
| Commercial patent licence | Case-specific | Contract-specific | Contract-specific | Must be analyzed from exact agreement; ECL-PL cannot override independently granted rights |
| Standards/FRAND commitment | Case-specific | Essentiality / commitment scope | Case-specific | Potential BLOCKER for discriminatory/field-of-use restrictions if inconsistent with existing commitment |

This table is only a starting map. Stable compatibility documentation must bind conclusions to exact licence versions and exact ECL-PL text.

## 4. Apache License 2.0

Apache-2.0 section 3 expressly grants a perpetual, worldwide, no-charge, royalty-free patent licence (subject to its stated termination rule) to make, have made, use, offer to sell, sell, import and otherwise transfer the Work. Its claim scope is limited to patent claims licensable by the Contributor that are necessarily infringed by the Contributor's Contribution alone or in combination with the Work to which it was submitted.

Its retaliation trigger includes patent litigation against an entity alleging that the Work or an incorporated Contribution infringes, including a cross-claim or counterclaim.

### ECL-PL attack questions

- If the same Patent Licensor already granted a Covered Patent Claim under Apache-2.0, can an ECL-PL Restricted Party rule have any practical effect on that independently granted permission?
- Does an ECL-PL combination definition accidentally cover claims Apache deliberately leaves outside its Contribution/Work boundary?
- Does an ECL-PL retaliation trigger conflict with or merely operate alongside Apache's trigger?
- Are downstream recipients relying directly on Apache rather than ECL-PL?

Reference: https://www.apache.org/licenses/LICENSE-2.0

## 5. GNU GPLv3 / AGPLv3

GPLv3 section 11 defines a contributor's `essential patent claims` and provides an express non-exclusive, worldwide, royalty-free patent licence to make, use, sell, offer for sale, import and otherwise run, modify and propagate the contents of the contributor version.

GPLv3 also contains patent provisions aimed at discriminatory patent arrangements and downstream protection. AGPLv3 incorporates the GPLv3 patent architecture while adding network-source obligations at the copyright-licence layer.

### ECL-PL attack questions

- Would an ECL-PL field-of-use restriction impose an additional restriction inconsistent with GPLv3 obligations for the same covered program/rights?
- Is ECL-PL being offered independently for claims not already granted under GPLv3, or is it attempting to narrow GPLv3-granted rights?
- Does a PatentGrantBundle create distribution conditions that a GPLv3 distributor cannot pass downstream consistently?
- Are patent and copyright incompatibilities being wrongly collapsed into one label?

References:

- https://www.gnu.org/licenses/gpl-3.0.html
- https://www.gnu.org/licenses/agpl-3.0.html

## 6. Mozilla Public License 2.0

MPL-2.0 defines `Patent Claims` of a Contributor and grants patent rights in section 2.1. Section 5.2 terminates grants where the licensee initiates patent litigation alleging a Contributor Version infringes, while excluding declaratory-judgment actions, counterclaims and cross-claims from that trigger.

### ECL-PL attack questions

- Should ECL-PL emulate MPL's distinction between offensive initiation and defensive litigation?
- If a claim is already licensed by MPL, does ECL-PL add any permission or only a separate conditional permission?
- What happens to downstream end-user rights after termination?

Reference: https://www.mozilla.org/MPL/2.0/

## 7. MIT / BSD-style copyright licences

Canonical MIT/BSD-style short-form licences generally do not contain the explicit contributor patent grants found in Apache-2.0, GPLv3 or MPL-2.0.

That does **not** mean a recipient necessarily has no patent rights: separate express/implied licences, exhaustion, covenants, estoppel or other legal doctrines may matter depending on jurisdiction and facts.

### ECL-PL opportunity

This is one of the cleanest companion-instrument scenarios:

```text
copyright: MIT/BSD
patents: exact ECL-PL PatentGrantBundle
```

The ECL-PL architecture therefore must not require ECL copyright licensing as a prerequisite.

## 8. CERN-OHL-v2

CERN-OHL-v2 is a critical comparator for open hardware because its variants expressly address patent rights within a hardware-source licensing architecture.

### ECL-PL attack questions

- Is the same hardware already receiving patent permissions under CERN-OHL-v2?
- Would ECL-PL impose additional restrictions inconsistent with the selected CERN-OHL variant?
- Does a mixed software/hardware implementation need separate patent-grant boundaries?
- Are `Product`, `Covered Source`, `Available Component`, modification and conveyance concepts being confused with ECL-PL's Covered Implementation?

Reference: https://ohwr.org/licences/

## 9. Standards commitments / FRAND

A Patent Licensor may already be bound by a standards-body patent policy, FRAND/RAND commitment, covenant or other undertaking concerning Standards-Essential Patents.

ECL-PL must not assume the licensor is free to discriminate among licensees, fields of use or projects when another commitment constrains that discretion.

This is a mandatory provenance and legal-review question whenever a Covered Patent Claim may be standards-essential.

## 10. EU competition-law layer

Patent licence restrictions also need a competition-law analysis independent from licence-text comparison.

As of the architecture draft, the European Commission's Technology Transfer Block Exemption Regulation framework is Regulation (EU) 2026/877, in force from 1 May 2026, together with the 2026 Technology Transfer Guidelines.

The ECL-PL review must evaluate whether relevant agreements fall within or outside block-exemption conditions and how market shares, competitors/non-competitors, hardcore restrictions, excluded restrictions and other Article 101 TFEU issues affect the proposed model.

Reference: https://competition-policy.ec.europa.eu/antitrust-and-cartels/legislation/ttber_en

## 11. Compatibility verdict vocabulary

Stable analysis should use explicit verdicts such as:

- `coexists-independent-grants`
- `coexists-with-scope-caveat`
- `requires-claim-by-claim-analysis`
- `additional-restriction-conflict`
- `termination-conflict`
- `sublicensing-conflict`
- `standards-commitment-review-required`
- `distribution-possible-but-patent-permission-incomplete`
- `combined-practice-not-permitted-on-current-record`
- `insufficient-information`

Avoid bare `compatible` where the layer is ambiguous.

## 12. Stable-review requirement

No row in a final compatibility matrix may rely solely on licence names. It must identify:

- exact external licence/version;
- exact ECL-PL PatentLicenseRelease;
- relevant claim/grant scenario;
- relevant Patent Licensor(s);
- legal layer analyzed;
- jurisdiction where material; and
- unresolved assumptions.
