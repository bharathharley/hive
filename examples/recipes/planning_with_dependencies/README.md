# Recipe: Planning with Dependency Resolution

A capability showcase for **Plan-based execution** with ordered tasks and dependencies.

## Goal

```
Name:        Dependency-aware Task Planner
Description: Build and execute a task plan where steps can only run after their
             prerequisites are complete.

Success criteria:
  - Plan includes explicit dependency graph
  - Executor never runs blocked tasks
  - Final output includes completed task trace

Constraints:
  - (hard) No task may execute before required dependencies
```

## Feature coverage

- `plan.py` task graph representation
- Dependency checks before node execution
- Transparent plan progress state (`pending`, `running`, `done`)

## Workflow

```
[create_plan] → [execute_next_ready_task] → [update_plan_state]
                                         └──────────loop──────────▶
                                                until all done
```

## Why this recipe matters

Many workflows are naturally DAGs; this pattern helps users model them explicitly instead of relying on implicit ordering.
