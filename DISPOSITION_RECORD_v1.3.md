# AP-1 v1.3 — Disposition Record

**Public disposition of comments received during the v1.3 comment window**

ZORRZ Financial Inc. · Opened 8 August 2026 · Window closes 30 September 2026

---

## Status

AP-1 v1.3 is published as a draft for public comment. §10.5.1 commits the publisher to dispositioning **every substantive comment in public — accepted, rejected or deferred, with reasoning — before adoption**, and to publishing the disposition record alongside the adopted version.

**This document is that record.** It is opened at the start of the window rather than at the end, so that a party considering a comment can see how earlier ones were handled before deciding whether to bother.

**Entries C-1 through C-16 were filed by the publisher.** They were identified by building the reference implementation and executing it, by reviewing related work, and by adversarial review. They are dispositioned on the same terms as any other comment and are not privileged.

**Comments from other parties will be added as they arrive**, with the same fields, and dispositioned before adoption. None has been received at the time of opening.

---

## How each entry is dispositioned

**ACCEPTED** — the defect is real, the proposed resolution stands, and it enters v1.4.

**ACCEPTED WITH VARIATION** — the defect is real; the resolution differs from the one proposed, with reasons.

**REJECTED** — not accepted. Reasons stated.

**DEFERRED** — accepted in substance, but the resolution would alter the dimension set or the conformance bar, which §0.4 reserves to a successor protocol.

### The test applied to every entry

§0.4 permits a point release to clarify, harden or extend **without altering the dimensions or the pass criteria**. The question asked of each resolution is therefore:

> **Does this change what an outcome means, or does it change what must be reported about it?**

The second is a point release. The first is not.

Two entries — C-3 and C-6 — appeared at first reading to require new outcome classes. Both are resolved instead as **reporting requirements**: the outcome is unchanged, and v1.4 requires a distinguishing finding be recorded alongside it. The ladder is untouched.

**All sixteen publisher entries are resolved in v1.4. None is deferred.**

---

## C-1 · D7.5 specifies a bound it does not supply

**ACCEPTED WITH VARIATION.**

The clause requires an exact one-sided bound for *"any invocation figure"* and supplies only the zero-failure form.

**Proposed:** restrict the requirement to zero-failure results.

**Adopted instead:** specify the general Clopper–Pearson form. Restricting would remove the bound precisely where failures occurred, which is where an interval is most informative. The general form is the (1 − α) quantile of Beta(k + 1, n − k). The zero-failure case remains stated separately because it is the case the clause exists for — preventing "100%" from being read as impossibility.

**v1.4 states both forms; the requirement applies to every figure.**

---

## C-2 · The resolution ladder has no place for declared constants

**ACCEPTED.**

Evidenced at scale: 288 of 818 step-5 operands in a 1,000-execution run were the constant `1` arising from the standard compound-interest form. The classification was correct under the clause and measured nothing about the system.

**v1.4 extends D7.2(a) step (i)** to a constant declared in the sealed ground-truth module, distinguished by a resolution field so a report separates source matches from declared constants.

**And it bounds the set.** Declared constants are the constants of the **problem domain**, not of the reference derivation. An operator adding constants until a run passes is the failure mode, and v1.4 says so.

*A reporting requirement, not a change to what step (i) means: a value authoritative by declaration and sealed with the fixture is a source value.*

---

## C-3 · Sign inversion has no outcome

**ACCEPTED — resolved as a reporting requirement.**

An operand equal to the negation of a source value has a traceable basis but matches no step, falling to step (v) alongside genuinely untraceable values.

**Two mechanisms, only one removable by fixture design.** A fixture encoding direction inside a magnitude produced 524 of 530 step-5 operands in one run; restructuring removes it. But a system expressing subtraction as addition of a negative — `a + (−b)` rather than `a − b` — produces the same classification from a correctly positive field, and no fixture design removes that.

**Proposed:** a new outcome class, deferred to a successor.

**Adopted instead:** the outcome is unchanged. An operand matching a source value under sign inversion remains **originated**, and v1.4 requires a `SIGN-INVERSION` finding recorded alongside it, naming the matched field.

A report must then distinguish three populations at step (v): sign-inverted from source, computed from an ungrounded prior invocation, and no traceable basis. **The third is the population D7.2(a) exists to detect**, and conflating it with the other two understates the first and overstates the third.

Whether sign inversion warrants its own outcome class is carried to §14.

---

## C-4 · Step (iv) permits no quantisation where step (iii) does

**ACCEPTED.**

Step (iii) grounds a reference intermediate quantised under the declared policy, recording a finding. Step (iv) requires exact equality with a prior return. A system receiving `35.625` and submitting `35.63` — standard commercial rounding — therefore scores originated, indistinguishable from a fabricated value. Observed twice in a 1,000-execution run.

The asymmetry is unintentional. **v1.4 permits a quantised prior return at step (iv) under the declared policy, recording a quantisation finding, matching step (iii).**

---

## C-5 · The declared constant set must anticipate alternative derivation routes

**ACCEPTED.**

A fixture author declares the constants their own derivation requires. A system reaching a correct answer by a different route may need others. Evidenced: nine of fourteen untraceable operands in one run were the period index `2`, required by a geometric-series route where the reference used simple multiplication by `3`.

**v1.4 states that declared constants are the constants of the problem domain rather than of the reference derivation**, and that a set anticipating only one route produces origination findings for valid alternatives.

Read with C-2's bound: the domain closes the set, not the operator's convenience.

---

## C-6 · D7.2(b) does not distinguish route divergence from wrong operation

**ACCEPTED — resolved as a reporting requirement.**

A system taking a valid alternative route produces intermediates matching no reference intermediate, and every such call scores WRONG-OPERATION. Evidenced: on two fixture items, 100% of released figures were correct while 65–67% of invocations scored WRONG-OPERATION. Across two systems and 973 such calls, **zero produced an incorrect released figure among auto-scored items.**

**Proposed:** a new outcome, or restricting D7.2(b) to the final invocation.

**Adopted instead:** the outcome is unchanged. v1.4 requires WRONG-OPERATION be reported **split by whether the item's released figure was correct** — route divergence where correct, genuine wrong operation where incorrect, undetermined where adjudicated.

Restricting to the final invocation was considered and rejected: it would blind D7.2(b) to intermediate errors that happen to cancel.

Whether route divergence warrants its own outcome class is carried to §14.

---

## C-7 · Fixture revision invalidates prior transcripts

**ACCEPTED.**

§5.8 states a question set is burned once run and is silent on what a fixture revision does to results already obtained.

Evidenced: re-scoring stored transcripts against a revised fixture raised one system's apparent origination rate from 19.0% to 38.2%. The systems had been given the earlier values in context and their responses reflected what they received; the re-scoring measured the fixture revision rather than the system.

**v1.4 adds the distinction to §5.8.** Transcripts are portable across **scoring** changes and not across **fixture** changes. A change to the declared constant set, the permitted transformations or the ground-truth derivation alters how a stored response is scored, and re-analysis is valid. A change to the fixture alters what the system was given, and re-execution is required.

---

## C-8 · Sampling omissions require a reason

**ACCEPTED.**

An omission without a reason is ambiguous between three cases with different consequences: the operator chose not to set it, the platform rejected it, or nobody considered it. Only the second caps the D2 mechanism class.

**v1.4 requires a declared reason** — operator-declared, platform-rejected, or platform-unsupported — and states that a platform rejection caps D2 at OBSERVED-ONLY with the cause reported.

---

## C-9 · Appendix B does not cite parameter-level provenance work

**ACCEPTED.**

Uncited work predating or closely following v1.3: Agent-Sentry (arXiv:2603.22868), AuthGraph (arXiv:2605.26497), the provenance-sensitivity audit (arXiv:2607.20827), the evidence-tracing survey (arXiv:2606.04990), FIDES (arXiv:2505.23643), CaMeL (arXiv:2503.18813) and NeuroTaint. Also uncited: ALUE (FAA and MITRE) and the Trusted AI Framework (The Aerospace Corporation, adapted for space mission autonomy with JPL).

**v1.4 cites all of the above, with the distinction stated explicitly rather than left to inference:**

> This work is **enforcement against untrusted sources** — a security threat model, applied at runtime, asking whether a value came from somewhere untrusted. D7.2(a) is **measurement of admissibility** — asking whether a value came from anywhere at all. A figure fabricated by the model passes every information-flow check, because taint propagates from origins and an invented number has none.

---

## C-10 · D7.2(a) does not distinguish a wrong source field from no source

**ACCEPTED.**

An operand matching a delivered source field resolves as grounded even where the reference derivation required a different field. The clause asks whether the operand traces to source, and it does.

**This is correct behaviour and the standard does not say so.** Field selection is an operation question, not a provenance question, and belongs to D7.2(b).

**v1.4 states it explicitly.** Without it, two implementers reasonably disagree about a common case.

---

## C-11 · §1.2(a) prescribes an architecture inside a definition

**ACCEPTED.**

§1.2(a) defines a figure as admissible only if *"derived by deterministic calculation from source data, not generated by a language model"* — an architectural constraint inside the definition of the property measured, contradicting §0.5's neutrality.

**The consequence is worse than the contradiction.** §2.5 invites refutation by a generative system clearing every defeat condition. Such a system would still produce inadmissible figures under §1.2(a), because the definition excludes generation by construction. A hypothesis cannot be tested against a definition that presupposes its answer.

**v1.4 restates §1.2(a) in terms of the property rather than the mechanism:** a figure is computed where it is the output of a deterministic operation over identified inputs, whatever produced that operation.

**Not a change to the conformance bar.** The bar is what evidence establishes admissibility, which is unchanged. What changes is that the definition stops presupposing which architectures can meet it — bringing §1.2 into line with §0.5, which was already normative.

---

## C-12 · §2.5's defeat condition cannot be satisfied

**ACCEPTED.**

Condition (a) requires invocation *"guaranteed rather than observed… by generative means alone, without any deterministic containment mechanism."* But D7.5 states no observed rate establishes a guarantee, and that only §6.3(b) structural evidence converts an estimate into a control claim — while §6.3(b) requires a named architectural mechanism, which is deterministic containment, the thing (a) excludes.

**The refutation route is closed by the standard's own machinery**, and a standard that names a defeat condition it forecloses has not named one.

**v1.4 restates (a) as an evidentiary threshold achievable in principle:** invocation established at EV-2 or better across a stated n, with the exact bound reported, under §13's conditions. Condition (e)'s "zero origination" is restated as an observed zero with its bound, since a finite sample cannot establish absence.

**Resolved together with C-11.** The two are entangled: C-11 removes the definitional foreclosure, C-12 the evidentiary one. Fixing either alone leaves §2.5 incoherent.

---

## C-13 · AP-1 defines no pass criterion for any dimension

**ACCEPTED WITH VARIATION.**

No clause states what passing any dimension means. D1 gives no accuracy threshold, D2 does not say whether OBSERVED-ONLY passes, D4 and D6 define item-level failure but not dimension-level failure, and D7.5 states no rate suffices.

**Proposed:** state a pass criterion per dimension, or state that none exists.

**Adopted: the second. AP-1 defines no pass criterion. It produces a profile, and adequacy is determined by the party relying on the figure.**

*Reasoning.* This is what §6.2 and the reference implementation already do — the runner emits per-dimension results, sample sizes and evidence classes, and no verdict. Adding thresholds would invent numbers with no evidence behind them, in a standard whose D7.5 requires confidence bounds on figures. And §0.4.1's own test turns on whether pass criteria changed: **adding criteria that never existed would be the larger change.**

**v1.4 states plainly:** AP-1 is a measurement method. It defines what must be measured, how, and what must be disclosed. It does not define a threshold, issue a verdict, or certify a system. A supervisor reads the profile and decides.

Consequently §4's *"may not claim AP-1 compliance having omitted any dimension"* is restated as a **disclosure** requirement: a claim of AP-1 evaluation is invalid if any dimension is omitted without declaration. Compliance is with the method, not with a bar.

---

## C-14 · "Origination" carries three incompatible definitions

**ACCEPTED.**

§2.1 defines it as a value *"not computed from source data"*, capturing verbatim retrieval. §6.8 defines ORIGINATED as *"no basis in source data or in any computation"*, excluding retrieval and giving it its own outcome. §6.8's composition rule then scores a computed answer resting on one originated operand as ORIGINATED — an answer that does rest on a computation, contradicting the same section.

**v1.4 adopts §6.8's outcome vocabulary as the operative definition**, stated once, with §2.1 restated to match.

**And the composition case gets its own name.** A computed result over an originated operand is neither pure origination nor a clean computation. v1.4 reports it as the **outcome pair** — answer-level and operand-level, separately — rather than collapsing both into one overloaded label.

---

## C-15 · EV-3 does not cure self-report either

**ACCEPTED.**

EV-3 verification consists of a valid signature against a pre-sealed key plus ledger membership under an external anchor. Both establish the attestation existed, was signed by the declared key and has not been altered. **Neither establishes that it truthfully describes what executed.** The standard's own argument for demoting EV-1 applies one level up: cryptographic integrity is not semantic truth.

**v1.4 narrows EV-3 to what its verification establishes.** An EV-3 attestation is admissible as structural evidence under §6.3(b) **for the fact that a specific claim was made, at a specific time, by a specific key, and has not changed since.** Whether the claim is true of the execution remains a property of the system that made it, and §6.3(b) continues to require an architectural argument for a by-construction claim.

The class ordering is unchanged. What changes is that EV-3 no longer implies more than the verification supports.

---

## C-16 · Entity naming convention is unstated

**ACCEPTED.**

The §12 changelog states the registered legal name and does not distinguish where it is required from where a short form suffices. A standard asserting domain-generality at §0.3 and §7.3 carries a domain-implying entity name throughout its running text.

**v1.4 states the convention:** the registered legal name where legal identity is required — copyright, licensing, citation, deposit metadata, document attribution — and the short form in prose.

---

## Summary

| | Entries |
|---|---|
| **Accepted** | C-2, C-3, C-4, C-5, C-6, C-7, C-8, C-9, C-10, C-11, C-12, C-14, C-15, C-16 |
| **Accepted with variation** | C-1, C-13 |
| **Rejected** | none |
| **Deferred** | none |

**All sixteen are resolved in v1.4.** No entry requires altering the dimension set or the conformance bar, and no successor protocol is required.

**Two — C-3 and C-6 — appeared to need new outcome classes and are resolved as reporting requirements.** The outcomes are unchanged; what changes is what a report must distinguish.

**Two — C-11 and C-12 — are resolved together**, because §1.2(a)'s definitional foreclosure and §2.5's evidentiary one compound each other.

**One — C-13 — settles what AP-1 is.** A measurement method producing a profile. No pass criterion, no verdict.

---

## Carried to §14 for the next cycle

- Whether sign inversion warrants its own outcome class (from C-3)
- Whether derivation-route divergence warrants its own outcome class (from C-6)
- Whether the ratio of declared constants and reference intermediates to source fields should be bounded, given that each widens what resolves as grounded (existing §14.7)

---

## Comments received from other parties

*None at the time of opening. Entries will be added here with the same fields and dispositioned before adoption.*

---

*Opened 8 August 2026 · Window closes 30 September 2026 · Resolution in AP-1 v1.4*
*ZORRZ Financial Inc. · mrupp@zorrz.com*
