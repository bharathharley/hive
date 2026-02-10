# Recipe: HITL Approval Workflow

A capability showcase recipe for **Human-in-the-Loop (HITL)** using approval gates and multiple human input styles.

## Goal

```
Name:        Customer Email Approval Flow
Description: Draft a customer-facing message and require explicit human approval
             before final delivery.

Success criteria:
  - Agent drafts a complete response email
  - Human can review and either approve or request edits
  - Human feedback is captured in structured form
  - Final output includes an approval status and revision trail

Constraints:
  - (hard) No message is marked "ready_to_send" without human approval
  - (soft) Keep final email under 180 words
```

## Feature coverage

- `hitl.py` style human approval checkpoints
- Approval gate edge routing (`approve` vs `revise`)
- Multiple human input shapes in one flow:
  - free text feedback
  - structured fields
  - selection list
  - binary approval decision
  - optional confidence score

## Input / Output

**Input:**
- `customer_context` (str) — conversation history + customer details
- `intent` (str) — what the message needs to accomplish
- `tone` (str) — voice requirement

**Output:**
- `final_email` (str) — approved email draft
- `approval_status` (str) — `approved` or `needs_revision`
- `feedback_log` (list[dict]) — revision feedback trail

## Workflow

```
[draft_email] → [human_review_gate] → [apply_feedback] ─┐
                     |                                   |
                     └──────────(approved)──────────────▶[finalize]
                                 (needs_revision)────────┘
```

## Nodes

### 1. draft_email

| Field | Value |
|-------|-------|
| Type | `llm_generate` |
| Inputs | `customer_context`, `intent`, `tone` |
| Outputs | `email_draft` |

### 2. human_review_gate

| Field | Value |
|-------|-------|
| Type | `human_input` |
| Inputs | `email_draft` |
| Outputs | `decision`, `feedback`, `priority`, `required_changes`, `confidence` |

Suggested prompt shown to human:

```
Review this draft email and choose one action:
- approve
- needs_revision

If revising, provide:
1) Free-text comments
2) Priority: low / medium / high
3) Required changes (bullet list)
4) Confidence score (0-1)
```

### 3. apply_feedback

| Field | Value |
|-------|-------|
| Type | `llm_generate` |
| Inputs | `email_draft`, `feedback`, `priority`, `required_changes` |
| Outputs | `email_draft`, `feedback_log` |

### 4. finalize

| Field | Value |
|-------|-------|
| Type | `function` |
| Inputs | `email_draft`, `decision`, `feedback_log` |
| Outputs | `final_email`, `approval_status`, `feedback_log` |

## Edges

| Source | Target | Condition |
|--------|--------|-----------|
| draft_email | human_review_gate | `on_success` |
| human_review_gate | finalize | `decision == "approve"` |
| human_review_gate | apply_feedback | `decision == "needs_revision"` |
| apply_feedback | human_review_gate | `on_success` |

## Why this recipe matters

Most production assistants need a safe checkpoint before high-impact actions. This pattern shows how to blend automation with explicit human control without breaking graph flow.
