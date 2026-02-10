# Recipe: Tool-calling Router

A capability showcase for **router nodes** that choose between multiple tool paths.

## Goal

```
Name:        Adaptive Tool Selection
Description: Inspect user intent and route execution to the correct tool-backed
             node (search, scrape, summarize, or storage).

Success criteria:
  - Router emits explicit route decision
  - Correct tool path selected for each intent type
  - Unified final response format regardless of branch

Constraints:
  - (hard) Route must be one of declared branches
```

## Feature coverage

- Router node decision schemas
- Branch-specific `llm_tool_use` nodes
- Merge/fan-in pattern to common finalizer

## Workflow

```
[intent_classifier_router]
   ├──▶ [search_branch]
   ├──▶ [scrape_branch]
   ├──▶ [summarize_branch]
   └──▶ [store_branch]
                
[all branches] → [normalize_and_finalize]
```

## Why this recipe matters

Routing keeps agents modular as toolsets grow, instead of putting all decisions in one monolithic prompt.
