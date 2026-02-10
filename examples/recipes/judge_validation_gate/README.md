# Recipe: Judge Validation Gate

A capability showcase for **LLM Judge nodes** that score outputs and decide pass/fail routing.

## Goal

```
Name:        Structured Output Quality Gate
Description: Generate a draft output, evaluate it with a judge, and either pass
             to delivery or route to remediation.

Success criteria:
  - Judge returns transparent scoring rubric
  - Gate decision is deterministic from score threshold
  - Failed drafts are remediated before finalization

Constraints:
  - (hard) No final output when factuality score < 0.75
```

## Feature coverage

- `judge.py` scoring and rationale
- Conditional edge routing based on judge outputs
- Separation of generation and evaluation responsibilities

## Workflow

```
[generate_draft] → [judge_quality] ──(pass)──▶ [finalize]
                              └─(fail)──▶ [repair_draft] → [judge_quality]
```

## Why this recipe matters

Judge gates make quality controls explicit and reusable, avoiding hidden prompt-only validation.
