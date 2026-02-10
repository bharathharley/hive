# Recipes

A recipe describes an agent's design — the goal, nodes, prompts, edge logic, and tools — without providing runnable code. Think of it as a blueprint: it tells you *how* to build the agent, but you do the building.

## What's in a recipe

Each recipe is a markdown file (or folder with a markdown file) containing:

- **Goal**: What the agent accomplishes, including success criteria and constraints
- **Nodes**: Each step in the workflow, with the system prompt, node type, and input/output keys
- **Edges**: How nodes connect, including conditions and routing logic
- **Tools**: What external tools or MCP servers the agent needs
- **Usage notes**: Tips, gotchas, and suggested variations

## How to use a recipe

1. Read through the recipe to understand the design
2. Create a new agent using the standard export structure (see [templates/](../templates/) for a scaffold)
3. Translate the recipe's goal, nodes, and edges into code
4. Wire in the tools described
5. Test and iterate

## Available recipes

| Recipe | Description |
|--------|-------------|
| [hitl_approval_workflow](hitl_approval_workflow/) | Human-in-the-loop approval flow with revision loops and structured human feedback |
| [multi_turn_research_loop](multi_turn_research_loop/) | Event-loop based iterative research pattern with tool use and judge-driven stopping |
| [multi_node_pipeline](multi_node_pipeline/) | Three-stage context handoff pipeline for modular multi-node workflows |
| [goal_driven_agent](goal_driven_agent/) | Goal system example with weighted criteria, hard constraints, and refinement loops |
| [judge_validation_gate](judge_validation_gate/) | LLM judge scoring gate with pass/fail routing and iterative remediation |
| [planning_with_dependencies](planning_with_dependencies/) | Dependency-aware planning flow for DAG-style task execution |
| [failure_recovery_edges](failure_recovery_edges/) | Resilient graph design using on-failure edges, retries, and fallback paths |
| [tool_calling_router](tool_calling_router/) | Router-first tool selection pattern with branch fan-out and final fan-in |
| [conversation_memory_handoff](conversation_memory_handoff/) | Multi-turn memory capture with selective context handoff to specialist nodes |
| [marketing_agent](marketing_agent/) | Multi-channel marketing content generator with audience analysis and A/B copy variants |
