# Recipe: Goal-driven Agent with Weighted Criteria

A capability showcase recipe for the **Goal System** with weighted success criteria and hard constraints.

## Goal

```
Name:        Outreach Optimization Agent
Description: Produce a high-quality outreach plan that maximizes relevance and
             conversion potential under strict cost and risk limits.

Success criteria (weighted):
  - Message relevance to target persona (weight: 0.50)
  - Personalization depth from available signals (weight: 0.30)
  - Clarity and CTA quality (weight: 0.20)

Constraints:
  - (hard) Estimated cost must remain <= $2.00 per prospect
  - (hard) No unverifiable claims
  - (soft) Keep total output under 220 words
```

## Feature coverage

- `goal.py` declarative goal definition
- Weighted criteria scoring and aggregate threshold checks
- Hard constraint validation during execution
- Stop/continue decisions based on goal state

## Input / Output

**Input:**
- `prospect_profile` (dict)
- `company_context` (dict)
- `budget_limit` (float)

**Output:**
- `outreach_message` (str)
- `goal_score` (float)
- `criteria_breakdown` (dict)
- `constraint_status` (dict)

## Workflow

```
[plan_message] → [score_against_goal] → [constraint_guard]
                                   |            |
                                   |            └─(constraint_violation)→[fallback_output]
                                   |
                       (score < threshold)→[refine_message]→[score_against_goal]
                                   |
                       (score >= threshold)───────────────→[finalize]
```

## Nodes

### 1. plan_message

| Field | Value |
|-------|-------|
| Type | `llm_generate` |
| Inputs | `prospect_profile`, `company_context` |
| Outputs | `draft_message`, `estimated_cost` |

### 2. score_against_goal

| Field | Value |
|-------|-------|
| Type | `judge` |
| Inputs | `draft_message`, `prospect_profile` |
| Outputs | `goal_score`, `criteria_breakdown`, `improvement_plan` |

Example scoring output:

```json
{
  "goal_score": 0.74,
  "criteria_breakdown": {
    "relevance": 0.8,
    "personalization": 0.6,
    "clarity_cta": 0.75
  },
  "threshold": 0.8
}
```

### 3. constraint_guard

| Field | Value |
|-------|-------|
| Type | `function` |
| Inputs | `estimated_cost`, `budget_limit`, `draft_message` |
| Outputs | `constraint_status` |

### 4. refine_message

| Field | Value |
|-------|-------|
| Type | `llm_generate` |
| Inputs | `draft_message`, `improvement_plan`, `criteria_breakdown` |
| Outputs | `draft_message`, `estimated_cost` |

### 5. fallback_output

| Field | Value |
|-------|-------|
| Type | `function` |
| Inputs | `constraint_status` |
| Outputs | `outreach_message`, `goal_score` |

### 6. finalize

| Field | Value |
|-------|-------|
| Type | `function` |
| Inputs | `draft_message`, `goal_score`, `criteria_breakdown`, `constraint_status` |
| Outputs | `outreach_message`, `goal_score`, `criteria_breakdown`, `constraint_status` |

## Edges

| Source | Target | Condition |
|--------|--------|-----------|
| plan_message | score_against_goal | `on_success` |
| score_against_goal | constraint_guard | `on_success` |
| constraint_guard | fallback_output | `constraint_status.hard_violation == true` |
| constraint_guard | refine_message | `constraint_status.hard_violation == false AND goal_score < 0.8` |
| refine_message | score_against_goal | `on_success` |
| constraint_guard | finalize | `constraint_status.hard_violation == false AND goal_score >= 0.8` |

## Why this recipe matters

This pattern demonstrates how to keep agents aligned to explicit objectives instead of ad-hoc prompting, while still enforcing non-negotiable operational constraints.
