# Erratum to AP-1 Evaluation V1

**Issued 26 July 2026 · ZORRZ Financial Inc. · CC-BY 4.0**
Applies to: `AP-1_Evaluation_V1_Results.md`, `README.md`, and the frozen
evaluation artifact at commit `986eff9`.

---

## 0. Status of this document

This erratum was issued on our own initiative. It corrects defects we found
in our own evaluation.

**The frozen artifact and the V1 results file are not modified.** Correction
is by addition only. Nothing in the original record has been edited,
re-scored in place, or re-run. Readers comparing this document against the
frozen artifact will find the original figures exactly as published, and this
document is the account of what is wrong with them.

Eight defect classes are recorded. Two were published on 25 July 2026 and are
restated here in §§2–3. Six are new. The consolidated list of withdrawn
findings is in §10.

---

## 1. Summary

| # | Defect | Effect |
|---|---|---|
| 1 | Automated determinism scoring counted non-answers as answers | D2 findings corrected; two published claims withdrawn |
| 2 | Fixture values drifted in the live sandbox environment | Ground truth not reproducible from the published fixture for affected items |
| 3 | The same scoring defect extends beyond D2 | All Section A outcome counts withdrawn pending re-adjudication |
| 4 | Sampling parameters were never sent to any arm | Comparative determinism claim withdrawn |
| 5 | Seven items were not answerable from the context three arms received | Those items void for cross-arm comparison |
| 6 | Expected values were verified after the fact, not constructed deterministically | Reproducibility partially established; independence not established |
| 7 | The instruction-removal condition also removed the tool declaration | D7.1b result withdrawn; the measurement is void, not merely imprecise |
| 8 | Invocation was externally verified for the comparators and self-reported by the system under test | D7 aggregate held; asymmetry favours the author's system |

The errors identified so far run, on net, in favour of the system built by
the evaluation's author. That is the reason for publishing them before the
recount rather than after it.

---

## 2. Defect 1 — automated determinism scoring

The determinism dimension (D2) asks whether repeated identical queries return
identical numeric answers. Scoring was performed by `classify_response()` in
`harness.py`, without transcript-level human adjudication. Three faults have
been identified in that function as applied to D2.

**2.1 Empty and errored responses were counted as distinct answers.** A
request that returned no content, or that failed at the transport layer, was
recorded as an answer differing from the others. Absence of a response is not
a different answer; it is the absence of a measurement.

**2.2 Response formatting was counted as numeric variation.** The scorer
extracted the first dollar amount appearing in the response text. Where a
response led with a different figure than the one requested — a component
value, an absolute magnitude before a signed total — the extracted number
varied although the answer did not.

**2.3 Differently phrased refusals were counted as answers.** Where no
numeric value could be extracted, the scorer fell back to comparing the
first sixty characters of response text. Two refusals worded differently were
therefore recorded as two distinct answers, when in fact no answer had been
given at all.

---

## 3. Corrected D2 findings, and Defect 2

### 3.1 Corrected findings

**PILVI: 9 of 9 items stable, not 7 of 9.** The two items originally recorded
as unstable were transport timeouts under §2.1, not variation in returned
values.

**Sol, item A12: zero computed answers, not five distinct answers.** All five
responses were refusals, differently phrased, and were counted as distinct
answers under §2.3. The correct record for this cell is that the arm declined
on every run.

**Gemini: unmeasured on 8 of 9 items.** For each of those items, 49 of 50
runs were rate-limited by the provider. One successful response is not a
determinism measurement. The published Gemini determinism figures are
**withdrawn** and the affected cells are reported as unmeasured.

**The claim that a comparator returned "up to five distinct answers" to an
identical query is withdrawn.** It rested entirely on §2.3.

### 3.2 Defect 2 — fixture reproducibility

V1 drew profile data from a live Plaid Sandbox environment rather than from a
sealed static fixture. Account balances in that environment drifted after the
run. Ground truth for affected items is therefore not reproducible by
re-reading the source the evaluation used. The account-level fixture as
delivered to the comparator arms has since been captured and is published;
see §7 for what it does and does not cover.

---

## 4. Defect 3 — the scoring defect is not confined to D2

`classify_response()` scored every item in the evaluation, on every
dimension, for every arm. Having established that it misclassifies refusals
and mis-extracts figures, we examined seven Section A items across all four
arms — 28 scored cells — and found **nine misclassifications**.

**Seven cells: correct refusals scored as fabrication.** On items A12, A13,
A14, A22, A23, A31 and A35, the Fable arm declined explicitly, stating in each
case that it did not have the income or transaction data the question
required. Every one was scored FABRICATED. The mechanism is visible in the
responses: these declines enumerate the account data the arm did hold, the
scorer extracted those recited balances as a numeric claim, and the claim did
not match the answer key. Arms that declined without reciting figures were
scored MODEL-DECLINED on the same items.

**One cell: the same fault in another arm.** The Sol arm's response to A14 is
also a decline, and is also scored FABRICATED.

**One cell in the opposite direction.** PILVI's response to A12 states the
correct signed figure, matching the answer key, with a tool invocation
recorded. It is scored FABRICATED. The response leads with the unsigned
magnitude before the signed total, which is the fault described in §2.2.

The mechanism is general and the seven items were not a random sample.
**All Section A outcome counts published in V1 are therefore withdrawn, for
every arm.** They should not be cited. Re-adjudication will cover all items
across all arms at transcript level and will be published as a further
erratum, with the full disagreement set.

### 4.1 A rubric insufficiency, distinct from the above

Item A31 asks for monthly income. PILVI returned its answer to a different
question — a monthly cash-flow summary — within which the correct income
figure appears. It was scored COMPUTED, correctly under the five-outcome
rubric v1.2 defines, because the required figure was present and grounded.
This is not a scoring error. It is evidence that the rubric lacked an outcome
for a figure that is computed and grounded but answers a different question
than the one asked. v1.3 proposes WRONG-SCOPE for exactly this case.

---

## 5. Defect 4 — sampling parameters were never sent

No `temperature`, `top_p` or `seed` value was included in any request to any
of the four arms. This was not stated in V1's methodology section, which
listed other request parameters.

All three comparator arms therefore ran at provider defaults, which are not
zero. The determinism dimension as executed compares a system with no
stochastic component on its numeric path against three systems at unpinned
default sampling.

**The comparative determinism claim is withdrawn.** PILVI's stability is a
structural property of its architecture and that finding stands on its own
terms; the comparison does not, because variation under unpinned sampling is
what the sampling parameter exists to produce. Pinned and default sampling
conditions will be reported as separate arms in any future evaluation.

---

## 6. Defect 5 — seven items were not answerable by three of the four arms

The context delivered to the comparator arms contained account balances,
credit limits and APRs. It contained no income or expense figure in any form.

Items A12, A13, A14, A22, A23, A31 and A35 require monthly income or monthly
expense data. The system under test read those values from its own
transaction source. The comparator arms never received them, in any form, on
any item.

This is not the context-delivery asymmetry disclosed in V1's limitations
section, which concerned how equivalent data was formatted and transmitted to
each arm. This is an asymmetry in what data existed to be reasoned over. On
these seven items the arms were not answering the same question.

**These seven items are void for cross-arm comparative purposes.** Responses
recorded for them remain in the frozen artifact and are informative about each
arm's behaviour when asked for a figure it cannot derive — several arms
declined correctly and that is worth recording — but they cannot support a
comparison, and no comparative statement in V1 that rests on them survives.

The values themselves exist nowhere in the published materials as source
data. They appear only as expected values in the evaluation set, in the
system under test's own logged responses, and in the scripts that provisioned
the sandbox environment.

---

## 7. Defect 6 — expected values were verified retrospectively

V1's expected values are static literals, authored at design time and stored
in the evaluation set. No script in the repository generated them from the
fixture, and none reproduced them.

They have since been recomputed. Deterministic code, written against the
published account-level fixture and the question text, was run and its output
compared against the evaluation set afterwards. Of the numeric Section A
items:

- **39 reproduce** within the tolerance declared in the evaluation set.
- **7 cannot be reproduced from published materials at all** — the seven items
  of §6, whose ground truth depends on data held outside the fixture. This is
  a failure of the requirement that ground truth be reproducible from the
  published fixture alone.
- **1 key carries a rounding artifact.** Items A28 and B3 have an expected
  value of $149.80. The exact value is $149.7916…, which is $149.79 at two
  decimal places; $149.80 is the result of rounding three component figures
  before summing them. Both fall within the ±$0.01 tolerance the evaluation
  set declares, so no outcome changes, but the key is imprecise and a single
  quantisation point should have been specified.

**Two limitations of this verification, stated plainly.** It was performed by
the same AI coding agent that implemented the evaluation harness, which had
already been exposed to the expected values in earlier work; the agent
disclosed this without being asked. And the computation logic, while written
without reference to the stored keys, was written by a party that had seen
them. The exercise therefore demonstrates **reproducibility** — published
code, static fixture, same result on re-execution — and does **not**
demonstrate **independence**. Those are different properties, and a
conformant evaluation requires both.

### 7.1 On the authorship of the evaluation set

For the avoidance of doubt, since the point has been raised: the questions
and expected values in V1 were authored by a language model at design time.
AP-1 v1.2 §11.8 requires blind authorship — a party that has not tuned the
system under test against the specific items, with the set withheld from the
system's builders until freeze. Both conditions were met and are evidenced by
timestamps in the run record: the pre-registration was completed before the
set was received, and no item text appears in the build agent's record before
that point.

v1.2 does not require that expected values be constructed by deterministic
computation from the fixture. That is a gap in the standard, exposed by this
evaluation, and v1.3 proposes closing it.

---

## 8. Defect 7 — the instruction-removal measurement is void

D7.1b measures computation invocation with any instruction to compute
removed. The published result stated that all three comparator arms dropped
to 0% invocation under that condition, and inferred that their invocation in
the base condition was instruction-contingent rather than structural.

**The condition removed the tool declaration as well as the instruction.**
For each of the three comparator arms, the request-construction code omits
the `tools` parameter whenever the instruction-removal mode is active. The two
removals are coupled in code; there is no condition in which one occurs
without the other. This is confirmed structurally in the frozen log: for the
arm whose provider echoes the parameter back, the tools array contains one
entry in the base condition and is empty under instruction removal.

A provider API cannot return a tool-call record for a tool it was never told
about. Non-invocation under this condition was therefore **structurally
guaranteed by the harness**, not observed in the model. The design varied two
variables and attributed the outcome to one of them.

Three further faults compound it.

**The baseline was already zero.** Two items carried the D7.1b marking. In the
single-run base condition — instruction present, tools present — invocation on
both items was zero for all three comparator arms. The published figure
describes a drop from zero to zero. (The fifty-run determinism entries for
these two items were not examined and could show a different base rate; that
would not repair the confound.)

**One arm was never measured.** Of the six comparator observations under the
condition, two are provider rate-limit errors with empty response bodies. One
of the three arms produced no usable response at all and is unmeasured, not
zero.

**The two items were unsuitable for the measurement.** Both are among the
seven items of §6, requiring monthly income or expense data that no
comparator arm received. On an item where the necessary data is absent,
declining without invoking a calculator is the correct behaviour — there is
nothing to compute over. These were the least suitable items in the set for
measuring invocation.

**The D7.1b result is withdrawn**, in the V1 results file and in the README
table. So is the inference that comparator invocation was
instruction-contingent rather than structural: the design cannot support it.
The measurement is void rather than imprecise, and no revised figure replaces
it.

The requirement itself stands. Reporting invocation with a facilitating
instruction removed is the right measurement, and v1.2 §7.6 mandates it. What
§7.6 does not say — and what this defect makes plain — is that a perturbation
intended to vary an instruction must hold tool availability constant, and
that the measurement is undefined where base-condition invocation is zero.
Both are specification gaps and both are addressed in §11.

---

## 9. Defect 8 — invocation was verified for three arms and self-reported by the fourth

For the comparator arms, the invocation flag is set from the provider's
structural tool-call record: it is true if and only if the API response
contained a tool-call item. It is not derived from response text, and the
automated scorer of §2 reads it without modifying it.

For the system under test, the same flag is derived from a routing field in
that system's own API response.

Comparator invocation was therefore established externally. **The system
under test's invocation was established from its own account of itself.** D7
exists to determine whether a deterministic computation was invoked; where
that determination rests on the system's self-report, what is measured is the
report, not the fact.

We have found no evidence that any self-report was inaccurate, and the
mechanism has since been inspected in the system's own code. But the
asymmetry favours the author's system, it was not disclosed in V1, and it is
a defect in measurement validity rather than in scoring. **The D7 aggregate
is held pending re-adjudication for this reason in addition to those in §4.**

---

## 10. Consolidated list of withdrawn findings

The following should not be cited from V1:

1. All Section A outcome counts, for every arm (§4).
2. The D7 invocation aggregate, held pending re-adjudication (§4, §9).
3. The D7.1b instruction-removal result, and the inference drawn from it
   (§8). Withdrawn without replacement.
4. All comparative determinism claims between arms (§5).
5. The published Gemini determinism figures (§3.1).
6. The claim that a comparator returned up to five distinct answers to an
   identical query (§3.1).
7. Any comparative statement resting on items A12, A13, A14, A22, A23, A31
   or A35 (§6).

The following stand:

1. PILVI's determinism, corrected to 9 of 9, as a structural property of an
   architecture with no stochastic component on its numeric path (§3.1, §5).
2. The comparator arms' invocation flag as a structurally sourced signal, read
   from provider tool-call records (§9). This does not extend to the system
   under test.
3. The instance of figure origination by the system under test on an empty
   profile, which is published in full in the frozen artifact and whose
   mechanism has since been located in the system's own code.
4. Compliance with §11.8 blind authorship, evidenced by timestamp (§7.1).

---

## 11. What changes as a result

Each defect above is caught by a requirement proposed for AP-1 v1.3. Those
requirements were written because this evaluation failed them, so the mapping
is not a coincidence — but it is worth setting out, because it is the reason
the requirements exist.

| Defect | Proposed requirement |
|---|---|
| 1, 3 | Transcript-level human adjudication on every dimension, not selectively |
| 1 | Empty, errored and rate-limited responses are never counted as answers; affected cells reported unmeasured |
| 4 | Sampling parameters reported per arm; where a platform rejects them, that is itself reported |
| 2, 6 | Static fully specified fixtures; every expected value reproducible from the published fixture alone |
| 5 | Parity of available data across arms, not merely parity of delivery; any asymmetry disclosed; items an arm cannot derive are declared void for that arm rather than scored |
| 6 | Expected values implemented independently of the system under test, by a party without sight of its computation code |
| 3 | Two scorers, blind where response text permits, with a published agreement statistic |
| 7 | Instruction-removal perturbations shall vary the instruction only; tool availability and all other request parameters held constant and reported |
| 7 | An invocation-delta measurement is undefined where base-condition invocation is zero, and shall be declared undefined rather than reported as a complete drop |
| 7 | Items selected for invocation measurement shall be items on which invocation is the correct behaviour |
| 7 | Minimum item count declared per dimension; n reported alongside every figure |
| 8 | Invocation shall be established from a signal not under the control of the system under test; where only a self-report is available, the limitation is declared and the figure is not comparable to externally verified arms |
| — | Correction by addition only; frozen artifacts never modified |

Of the requirements above, V1 satisfies one: the last.

We record that plainly. An evaluation that fails almost every rule its
successor standard proposes is not a result to be defended, and we are not
defending it. It is a reference run whose most durable output is the
specification of what a conformant run must do differently.

---

## 12. Provenance of this erratum

The investigations underlying §§4–9 were carried out on the repository and
the frozen run log by the same AI coding agent that implemented the
evaluation harness, under direction. Its prior exposure to the expected
values is disclosed in §7. No frozen file was modified in the course of any
investigation.

This erratum has not been independently reviewed. Correction and challenge
are invited.

---

*AP-1 v1.2 · DOI 10.5281/zenodo.21324954 ·
github.com/zorrzai/admissibility-protocol · CC-BY 4.0*
*Corrections and challenges to mrupp@zorrz.com*
