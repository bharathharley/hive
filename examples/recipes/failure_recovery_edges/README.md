# Recipe: Failure Recovery Edges

A capability showcase for **on-failure edge routing** and controlled fallback paths.

## Goal

```
Name:        Resilient Execution Flow
Description: Attempt a primary action, recover via fallback nodes on failure,
             and report the final path taken.

Success criteria:
  - Primary path executes when healthy
  - Fallback path triggers on failures
  - Recovery attempts are observable and bounded

Constraints:
  - (hard) Maximum 2 retries before fallback
```

## Feature coverage

- `on_failure` edge conditions
- Retry counters in shared state
- Graceful degrade nodes for partial completion

## Workflow

```
[primary_action] ──(success)──▶ [finalize_success]
      │
      └─(failure)──▶ [retry_or_fallback] ──(retry)──▶ [primary_action]
                                       └─(fallback)─▶ [finalize_partial]
```

## Why this recipe matters

Production agents need reliability patterns; explicit failure edges prevent brittle all-or-nothing behavior.
