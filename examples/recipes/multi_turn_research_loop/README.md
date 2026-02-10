# Recipe: Multi-turn Research Loop

A capability showcase recipe for **Event Loop Node** orchestration with tool use and judge-based stopping criteria.

## Goal

```
Name:        Iterative Research Agent
Description: Research a topic in multiple passes until evidence quality is
             sufficient, then synthesize a final brief.

Success criteria:
  - At least one tool-based retrieval pass is completed
  - Judge provides explicit quality score and rationale
  - Loop exits only when quality threshold is met
  - Final summary cites gathered evidence

Constraints:
  - (hard) Maximum 5 iterations
  - (hard) Every claim in final brief maps to retrieved notes
```

## Feature coverage

- `event_loop_node.py` iterative control flow
- `llm_tool_use` research/action steps
- `judge.py` quality evaluation (`continue` vs `stop`)
- Loop state tracking (`iteration_count`, `quality_score`)

## Input / Output

**Input:**
- `research_question` (str)
- `target_depth` (str) — e.g. `"overview" | "deep"`

**Output:**
- `research_summary` (str)
- `evidence_notes` (list[dict])
- `quality_score` (float)

## Workflow

```
[init_research_state] → [research_loop]

research_loop internal cycle:
  think → search_tool_call → extract_notes → judge_quality → (continue|stop)

[research_loop] → [synthesize_final_brief]
```

## Nodes

### 1. init_research_state

| Field | Value |
|-------|-------|
| Type | `function` |
| Inputs | `research_question`, `target_depth` |
| Outputs | `iteration_count`, `evidence_notes`, `quality_score` |

### 2. research_loop

| Field | Value |
|-------|-------|
| Type | `event_loop` |
| Inputs | `research_question`, `iteration_count`, `evidence_notes`, `quality_score` |
| Outputs | `iteration_count`, `evidence_notes`, `quality_score`, `loop_exit_reason` |

Internal actions per iteration:
1. plan next query (`llm_generate`)
2. run search tool (`llm_tool_use`)
3. normalize findings (`function`)
4. evaluate confidence/completeness (`judge`)

Judge policy example:

```
stop when:
  quality_score >= 0.8 AND
  evidence diversity >= 3 independent sources
else continue
```

### 3. synthesize_final_brief

| Field | Value |
|-------|-------|
| Type | `llm_generate` |
| Inputs | `research_question`, `evidence_notes`, `quality_score` |
| Outputs | `research_summary` |

## Edges

| Source | Target | Condition |
|--------|--------|-----------|
| init_research_state | research_loop | `on_success` |
| research_loop | synthesize_final_brief | `loop_exit_reason in ["threshold_met", "max_iterations"]` |

## Why this recipe matters

Many real tasks require multiple tool-use cycles, not a single step. This recipe demonstrates a reusable think → act → judge loop for dependable agent behavior.
