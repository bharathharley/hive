# Recipe: Conversation Memory Handoff

A capability showcase for **conversation state + handoff** between client-facing nodes.

## Goal

```
Name:        Multi-turn Guided Assistant
Description: Capture user preferences over multiple turns and hand off a clean
             memory snapshot to a downstream specialist node.

Success criteria:
  - Preferences persist across turns
  - Handoff passes only relevant memory fields
  - Specialist node uses memory to personalize output

Constraints:
  - (hard) Do not leak private memory fields to non-authorized nodes
```

## Feature coverage

- `conversation.py` / NodeConversation usage
- Session-scoped memory accumulation
- `context_handoff.py` selective memory transfer

## Workflow

```
[collect_preferences] → [confirm_memory] → [handoff_to_specialist] → [deliver_personalized_result]
```

## Why this recipe matters

This pattern demonstrates how to preserve conversational continuity while keeping node boundaries clean and auditable.
