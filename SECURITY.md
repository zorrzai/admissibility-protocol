# Security & Sanitisation Notes

This repository publishes the AP-1 evaluation harness so that the results of
run `AP1_FROZEN_20260716_012503` can be independently reproduced, disputed, or
extended (AP-1 v1.2 §8, §10.2). The published `harness.py` is a **sanitised**
version of the internal harness that produced that run.

Two things are deliberately different from the internal harness. **Neither
changes any score, prompt, model setting, or classification rule.** They are
documented here in full so the sanitisation itself is auditable.

## 1. System-under-test authentication is withheld

The internal harness called the system under test ("PILVI") through a
production API behind an authenticated evaluation endpoint. The concrete
mechanism — the endpoint URL, the per-profile bearer tokens, and an internal
evaluation header — is **operational access to a live system**, not part of
the measurement, and is not published.

In `harness.py`, the system-under-test adapter (`run_system_under_test`):

- reads its endpoint from `SUT_ENDPOINT` (a placeholder that must be configured
  explicitly; the harness will not silently contact any host);
- uses only a **generic bearer token** read from the environment, if present;
- contains **no** internal evaluation/bypass header.

The **call shape** (request body, response normalisation, multi-turn history
handling) is preserved so the logic is fully auditable. Any party reproducing
AP-1 supplies their **own** system-under-test adapter by implementing that one
function; the rest of the harness — context construction, the five-way
classifier, logging — is system-agnostic.

> Why this is not a fairness problem: the withheld details govern *how the
> harness reaches the system under test*, not *what the system was asked* or
> *how its answers were scored*. Every prompt, setting, and scoring rule is
> published verbatim (AP-1 §5.4), and every raw response is in the run log.

## 2. The calculator tool uses `eval()` with restricted builtins

> **Correction (30 July 2026).** This section previously stated that the
> published harness "removes `eval()` entirely and instead parses each
> expression to an abstract syntax tree." That description was written for an
> AST-based evaluator that was developed but is **not** the version published
> here. The published `harness.py` uses `eval()` with restricted builtins, as
> described below. The prior text is struck through for the record; the
> correction follows.

~~The calculator tool offered to the three comparator models originally
evaluated expressions with an allow-listed `eval()`. The published harness
removes `eval()` entirely and instead parses each expression to an abstract
syntax tree and walks **only** an explicit set of node types.~~

The published calculator evaluator, at `harness.py` line 226, is:

```python
result = eval(expression, {"__builtins__": {}}, _CALC_ALLOWED)
```

where `_CALC_ALLOWED` restricts the namespace to a set of arithmetic functions
and constants. This is the code that produced the V1 evaluation results
(`AP1_FROZEN_20260716_012503`). It is published exactly as it ran, with
credentials removed and no other change, so that a third party can verify that
the published scoring instrument is the one that produced the published data.

An AST-based evaluator was written as a replacement. It is **not** published
in this repository, because publishing it would mean the repository's harness
is no longer the instrument that produced V1. The AP-1 runner
(`ap1-runner/example/calculator_tool.py`) uses an AST walker that contains
no `eval()`, `exec()`, or `compile()` in any form.

> [!CAUTION]
> **`eval()` with restricted builtins is not a sandbox.** The evaluated
> expression originates from the model under test. Known techniques exist to
> escape `__builtins__`-restricted `eval()` via attribute traversal on
> surviving type objects. If you run this harness, run it only against
> endpoints you trust, or substitute your own evaluator for
> `execute_calculator()`. The function\'s interface is a string in, string out;
> replacing it does not affect scoring.

## What is **not** sanitised (published exactly as run)

Everything that bears on the result is published unchanged:

- both shared system prompts, verbatim;
- the comparator model IDs and their settings, **including the rationale** for
  each (Sol `reasoning_effort="medium"` on every call — the "B12" note; Fable
  default adaptive thinking; Gemini defaults — a preview model, see AP-1
  §Limitations);
- the per-provider tool loops (OpenAI Responses, Anthropic Messages, Google
  GenAI), including provider-specific quirks;
- the five-way classifier (`CLASSIFIER-REFUSED` / `MODEL-DECLINED` / `COMPUTED`
  / `RETRIEVED` / `FABRICATED`) with tool invocation logged as a **separate
  axis** (the D7 finding);
- the deliberate **non-sanitisation of the data-embedded injection payload**:
  account names, including the profile-P5 injection string, are passed to the
  comparators verbatim — testing that behaviour is the point of the test.

## Data

The evaluation profiles (`_eval_profiles_v6.json`) and per-account contexts are
**synthetic** (Plaid Sandbox); they contain no real customer data. Any
access token field present in a profile is a synthetic/sandbox value, not a
live production credential.

## Reporting a vulnerability

If you believe you have found a way for the calculator evaluator to execute
anything beyond the permitted arithmetic, or any other security issue in this
harness, please open a private security advisory on this repository (or contact
the maintainer) rather than a public issue. We will confirm, fix, and credit.

**Pre-commit hook:** To prevent accidental credential exposure, install the included `gitleaks` guard by running `cp hooks/pre-commit .git/hooks/pre-commit && chmod +x .git/hooks/pre-commit`.

---

*This document accompanies the sanitised `harness.py`. The unsanitised internal
harness is not published because it embeds live-system access; the sanitisation
above is the only difference, and it is auditable line-for-line against this
description.*
