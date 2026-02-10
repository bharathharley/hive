# Recipe: Multi-node Context Handoff Pipeline

A capability showcase recipe for **Context Handoff** across specialized nodes.

## Goal

```
Name:        Three-stage Analysis Pipeline
Description: Collect raw data, analyze it, and generate an executive report
             while preserving clean handoffs between specialist nodes.

Success criteria:
  - Data collector produces normalized records
  - Analyst outputs actionable insights from normalized input
  - Report writer generates an audience-ready summary
  - Handoff metadata preserves provenance between stages

Constraints:
  - (hard) No node reads hidden internal state from non-upstream peers
  - (soft) Keep final report under 600 words
```

## Feature coverage

- `context_handoff.py` structured state passing
- Node specialization with strict input/output contracts
- Provenance tracking (`handoff_meta`) between nodes
- Clear pipeline observability for debugging

## Input / Output

**Input:**
- `dataset_uri` (str)
- `analysis_focus` (str)
- `report_audience` (str)

**Output:**
- `final_report` (str)
- `insights` (list[str])
- `handoff_meta` (dict)

## Workflow

```
[collect_data] → [analyze_patterns] → [write_report]
```

## Nodes

### 1. collect_data

| Field | Value |
|-------|-------|
| Type | `llm_tool_use` or `function` |
| Inputs | `dataset_uri`, `analysis_focus` |
| Outputs | `normalized_data`, `handoff_meta` |

Responsibilities:
- load data
- clean/normalize schema
- attach source metadata and timestamp

### 2. analyze_patterns

| Field | Value |
|-------|-------|
| Type | `llm_generate` |
| Inputs | `normalized_data`, `analysis_focus`, `handoff_meta` |
| Outputs | `insights`, `risk_flags`, `handoff_meta` |

Responsibilities:
- identify key trends
- flag uncertainty
- update provenance (`analysis_node_version`, assumptions)

### 3. write_report

| Field | Value |
|-------|-------|
| Type | `llm_generate` |
| Inputs | `insights`, `risk_flags`, `report_audience`, `handoff_meta` |
| Outputs | `final_report` |

Responsibilities:
- translate technical analysis to audience-specific narrative
- include caveats based on risk flags

## Edges

| Source | Target | Condition |
|--------|--------|-----------|
| collect_data | analyze_patterns | `on_success` |
| analyze_patterns | write_report | `on_success` |

## Why this recipe matters

Teams often struggle to keep multi-step agents modular. This pattern demonstrates how context handoff creates explicit contracts, making large workflows easier to reason about and maintain.
