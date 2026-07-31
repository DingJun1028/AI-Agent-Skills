# Examples

This repository is documentation-first: rather than duplicating code in a
separate `examples/` tree, minimal, commented examples live directly inside
the relevant topic page (next to the concept they illustrate), so the
example and its explanation never drift apart. This page is an index
pointing to all of them.

## Why examples live inline, not here

A code snippet without its surrounding "why" is easy to copy-paste
incorrectly. Keeping examples embedded in context — right after the
Workflow section of each page — means the example is always read alongside
the reasoning behind it. This directory exists as a **navigational index**,
not a separate code tree.

## Index of In-Page Examples

| Example | Page | What it demonstrates |
|---|---|---|
| Self-consistency voting | [`01-core-cognitive/reasoning/chain-of-thought.md`](../01-core-cognitive/reasoning/chain-of-thought.md#example) | Sampling multiple CoT chains and voting |
| Beam-search ToT loop | [`01-core-cognitive/reasoning/tree-of-thought.md`](../01-core-cognitive/reasoning/tree-of-thought.md#example) | A minimal Tree of Thought search implementation |
| Reflect-and-correct loop | [`01-core-cognitive/reasoning/self-reflection.md`](../01-core-cognitive/reasoning/self-reflection.md#example) | Pairing self-reflection with correction |
| LLM-driven task decomposition | [`01-core-cognitive/planning/task-decomposition.md`](../01-core-cognitive/planning/task-decomposition.md#example) | Structuring a goal into a dependency-aware plan |
| Recursive chunk splitter | [`10-rag/chunking.md`](../10-rag/chunking.md#example) | A minimal recursive text chunker |
| Cosine similarity + top-k search | [`10-rag/embeddings.md`](../10-rag/embeddings.md#example) | Embedding-based similarity search |
| Reciprocal Rank Fusion | [`10-rag/retrieval-strategies.md`](../10-rag/retrieval-strategies.md#hybrid-search) | Combining dense and sparse rankings |
| MCP tool discovery + call | [`11-mcp/protocol.md`](../11-mcp/protocol.md#example) | Raw JSON-RPC message exchange |
| MCP server tool handler | [`11-mcp/servers.md`](../11-mcp/servers.md#example) | A server-side tool implementation pattern |
| MCP client orchestration | [`11-mcp/clients.md`](../11-mcp/clients.md#example) | Merging multiple servers' tools for a model |
| ReAct loop | [`13-agent-patterns/react.md`](../13-agent-patterns/react.md#example) | A minimal Thought-Action-Observation loop |
| Reflexion loop | [`13-agent-patterns/reflexion.md`](../13-agent-patterns/reflexion.md#example) | Multi-attempt learning with stored reflections |
| Plan-and-Execute loop | [`13-agent-patterns/plan-and-execute.md`](../13-agent-patterns/plan-and-execute.md#example) | Two-phase planning and execution with re-planning |
| CodeAct action composition | [`13-agent-patterns/codeact.md`](../13-agent-patterns/codeact.md#example) | Composing multiple tool calls in one code action |
| Bounded ReAct loop | [`19-recipes/README.md`](../19-recipes/README.md#example-recipe-bounding-a-react-loop-safely) | Preventing runaway/stuck agent loops |

## Contributing an Example

New examples should be added directly to the relevant topic page's
`## Example` section (following [`docs/page-template.md`](../docs/page-template.md)),
then indexed here — not added as standalone files in this directory.

Examples should be:

- **Minimal** — illustrate the concept, not a production-ready implementation
- **Commented** — explain the non-obvious parts
- **Runnable in spirit** — pseudocode is fine where a real dependency would
  distract from the concept, but should be clearly illustrative, not
  misleadingly presented as copy-paste-ready production code
