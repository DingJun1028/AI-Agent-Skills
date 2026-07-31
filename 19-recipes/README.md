# 19 · Recipes

## Overview

Recipes are short, task-oriented cookbook entries — smaller in scope than a
full [workflow](../18-workflows/README.md), focused on solving one specific,
common implementation problem well. Think "how do I do X" rather than "here's
a full system."

## Learning Objectives

- Find a quick, focused solution to a common implementation problem
- Understand each recipe well enough to adapt it, not just copy it

## Recipe Index

| Recipe | Solves | Related deep-dive |
|---|---|---|
| Bounding a ReAct loop safely | Preventing runaway/infinite agent loops | [`13-agent-patterns/react.md`](../13-agent-patterns/react.md) |
| Adding citations to RAG answers | Making generated answers traceable to sources | [`10-rag/README.md`](../10-rag/README.md) |
| Structured output with retry-on-failure | Reliably getting parseable JSON from a model | [`03-communication/README.md#structured-outputs`](../03-communication/README.md#structured-outputs) |
| Gating a destructive tool behind approval | Preventing accidental irreversible actions | [`07-safety-alignment/README.md#human-approval`](../07-safety-alignment/README.md#human-approval) |
| Summarizing a long conversation to fit context | Managing growing conversation history | [`01-core-cognitive/memory/README.md#memory-compression`](../01-core-cognitive/memory/README.md#memory-compression) |
| Hybrid search fallback for exact-match queries | Fixing dense-only retrieval missing IDs/codes | [`10-rag/retrieval-strategies.md`](../10-rag/retrieval-strategies.md) |

## Example Recipe: Bounding a ReAct Loop Safely

**Problem:** A ReAct-style agent loop can, in principle, run forever if the
model never emits a final answer (e.g. it keeps trying failed actions).

**Solution:**

```python
def react_loop(model, tools, task, max_steps=8, repeat_threshold=2):
    history = [f"Task: {task}"]
    recent_actions = []
    for step in range(max_steps):
        response = model.generate("\n".join(history) + "\nThought:")
        action, args = parse_action(response)

        if action == "final_answer":
            return args["answer"]

        # Detect repeated identical actions as a stuck-loop signal
        action_signature = (action, tuple(sorted(args.items())))
        recent_actions.append(action_signature)
        if recent_actions.count(action_signature) >= repeat_threshold:
            return "Agent appears stuck repeating the same action — stopping early."

        observation = tools[action].run(**args)
        history.append(f"Action: {action}({args})\nObservation: {observation}")

    return "Reached maximum steps without a final answer."
```

**Why this works:** two independent safety nets — a hard step cap, and
detection of repeated identical actions — catch both "just runs too long"
and "stuck in a specific loop" failure modes.

## Key Concepts

| Term | Definition |
|---|---|
| Recipe | A short, focused solution to one specific implementation problem |

## Advantages / Disadvantages

| Advantages | Disadvantages |
|---|---|
| Fast to find and apply for a specific known problem | Narrower scope than a full workflow — doesn't show the whole system |
| Easy to adapt to your specific stack | Needs to be integrated into a larger system to be useful on its own |

## Common Mistakes

- **Mistake:** Copy-pasting a recipe without understanding why it works.
  **Fix:** Read the linked deep-dive page before adapting a recipe to your
  own system.

## Related Categories

- [`18-workflows/`](../18-workflows/README.md) — full end-to-end systems these recipes are components of
- Every numbered category — recipes are drawn from and link back to the
  relevant deep-dive pages throughout this repository

## Research Papers

Recipes are practical distillations, not novel research — see the linked
deep-dive pages for the underlying research citations.

## Further Reading

- [`18-workflows/README.md`](../18-workflows/README.md) — full workflow architectures
