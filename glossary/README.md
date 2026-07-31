# Glossary

Alphabetical glossary of terms used throughout this repository. Each term
links to the page where it's covered in depth. This is a seed set — see
[`ROADMAP.md`](../ROADMAP.md) for the target of 200+ terms.

## A

- **Agent** — A system using an LLM as a reasoning engine to perceive, decide, act, and observe in pursuit of a goal. See [`01-core-cognitive/README.md`](../01-core-cognitive/README.md#what-is-an-agent).
- **ANN (Approximate Nearest Neighbor)** — Search trading a small accuracy loss for large speed gains at scale. See [`10-rag/vector-databases.md`](../10-rag/vector-databases.md).
- **Authorization** — What an authenticated identity is permitted to do. See [`07-safety-alignment/README.md`](../07-safety-alignment/README.md).

## B

- **Benchmark** — A fixed, representative task set with known correct/judgeable answers. See [`15-evaluation/README.md`](../15-evaluation/README.md).
- **BM25** — A sparse (keyword-based) retrieval scoring formula. See [`10-rag/retrieval-strategies.md`](../10-rag/retrieval-strategies.md).

## C

- **Chain of Thought (CoT)** — Prompting a model to generate intermediate reasoning steps before an answer. See [`01-core-cognitive/reasoning/chain-of-thought.md`](../01-core-cognitive/reasoning/chain-of-thought.md).
- **Chunking** — Splitting documents into retrievable units. See [`10-rag/chunking.md`](../10-rag/chunking.md).
- **CodeAct** — Representing agent actions as executable code. See [`13-agent-patterns/codeact.md`](../13-agent-patterns/codeact.md).
- **Confused deputy problem** — An agent misusing its own legitimate permissions due to manipulated input. See [`11-mcp/security-and-transport.md`](../11-mcp/security-and-transport.md).
- **CRAG (Corrective RAG)** — Adding a retrieval-evaluator step that corrects for bad retrievals. See [`10-rag/advanced-rag.md`](../10-rag/advanced-rag.md).

## D

- **Delegation** — One agent assigning a sub-task to another better-suited agent. See [`06-multi-agent/README.md`](../06-multi-agent/README.md).
- **Dense retrieval** — Similarity search over embeddings. See [`10-rag/embeddings.md`](../10-rag/embeddings.md).

## E

- **Embedding** — A numeric vector representation of text capturing semantic meaning. See [`10-rag/embeddings.md`](../10-rag/embeddings.md).
- **Episodic memory** — Records of specific past events, tied to time/context. See [`01-core-cognitive/memory/README.md`](../01-core-cognitive/memory/README.md).
- **Evaluator (Reflexion)** — The mechanism determining episode success/failure. See [`13-agent-patterns/reflexion.md`](../13-agent-patterns/reflexion.md).

## F

- **Fallback strategy** — A defined alternative action when confidence is insufficient. See [`04-decision-making/README.md`](../04-decision-making/README.md).
- **Few-shot learning** — Steering model behavior via example input-output pairs in the prompt. See [`08-learning-adaptation/README.md`](../08-learning-adaptation/README.md).
- **Function calling / tool calling** — A model emitting a structured call to an available function. See [`02-tool-use/README.md`](../02-tool-use/README.md).

## G

- **Graph of Thought (GoT)** — A reasoning strategy generalizing Tree of Thought to allow merging branches. See [`01-core-cognitive/reasoning/graph-of-thought.md`](../01-core-cognitive/reasoning/graph-of-thought.md).
- **GraphRAG** — Retrieval over a knowledge graph for multi-hop questions. See [`10-rag/advanced-rag.md`](../10-rag/advanced-rag.md).
- **Guardrail** — A check constraining agent behavior to safe/intended bounds. See [`07-safety-alignment/README.md`](../07-safety-alignment/README.md).

## H

- **Hallucination** — Fluent, confident-sounding content not actually supported by evidence. See [`04-decision-making/README.md`](../04-decision-making/README.md).
- **HNSW** — A common high-recall approximate nearest-neighbor index structure. See [`10-rag/vector-databases.md`](../10-rag/vector-databases.md).
- **Host application** — The application embedding an LLM that needs external context/actions, in MCP terminology. See [`11-mcp/README.md`](../11-mcp/README.md).
- **Human-in-the-loop approval** — Requiring explicit user confirmation before executing higher-risk actions. See [`07-safety-alignment/README.md`](../07-safety-alignment/README.md).
- **Hybrid search** — Combining dense and sparse retrieval scores. See [`10-rag/retrieval-strategies.md`](../10-rag/retrieval-strategies.md).

## I

- **In-context learning** — A model adapting behavior based on information in its current context, without weight updates. See [`08-learning-adaptation/README.md`](../08-learning-adaptation/README.md).

## J

- **Jailbreak** — An attempt to bypass a model's safety training via crafted prompting. See [`07-safety-alignment/README.md`](../07-safety-alignment/README.md).
- **JSON-RPC** — The remote-procedure-call format MCP is built on. See [`11-mcp/protocol.md`](../11-mcp/protocol.md).

## L

- **Least privilege** — Granting only the minimum access necessary for a declared purpose. See [`07-safety-alignment/README.md`](../07-safety-alignment/README.md).
- **LLM-as-a-judge** — Using a model to evaluate another model's output against criteria. See [`15-evaluation/README.md`](../15-evaluation/README.md).
- **Long-term memory** — Information persisted across sessions, outside the context window. See [`01-core-cognitive/memory/README.md`](../01-core-cognitive/memory/README.md).

## M

- **MCP (Model Context Protocol)** — An open standard for connecting AI applications to external tools/data. See [`11-mcp/README.md`](../11-mcp/README.md).
- **Memory compression** — Summarizing/distilling raw history into a smaller, denser representation. See [`01-core-cognitive/memory/README.md`](../01-core-cognitive/memory/README.md).
- **Multi-hop question** — A question whose answer requires connecting information across multiple documents/entities. See [`10-rag/advanced-rag.md`](../10-rag/advanced-rag.md).

## O

- **Observation (agent loop)** — The result of executing an action, fed back into the next reasoning step. See [`13-agent-patterns/react.md`](../13-agent-patterns/react.md).

## P

- **Plan-and-Execute** — Separating upfront planning from step execution. See [`13-agent-patterns/plan-and-execute.md`](../13-agent-patterns/plan-and-execute.md).
- **Prompt injection** — Untrusted content manipulating an agent by being interpreted as instructions. See [`07-safety-alignment/README.md`](../07-safety-alignment/README.md).

## R

- **RAG (Retrieval-Augmented Generation)** — Grounding model output in externally retrieved documents. See [`10-rag/README.md`](../10-rag/README.md).
- **ReAct** — Interleaving reasoning and acting in a single agent loop. See [`13-agent-patterns/react.md`](../13-agent-patterns/react.md).
- **Reflexion** — Persisting self-reflection across episodes via memory. See [`13-agent-patterns/reflexion.md`](../13-agent-patterns/reflexion.md).
- **Reranking** — A second-stage model re-scoring first-stage retrieval candidates for precision. See [`10-rag/retrieval-strategies.md`](../10-rag/retrieval-strategies.md).
- **Resource (MCP)** — Read-only data an MCP server exposes as context. See [`11-mcp/primitives.md`](../11-mcp/primitives.md).

## S

- **Sandbox** — An isolated execution environment preventing generated code from affecting the host system. See [`13-agent-patterns/codeact.md`](../13-agent-patterns/codeact.md).
- **Self-consistency** — Sampling multiple reasoning chains and voting on the final answer. See [`01-core-cognitive/reasoning/chain-of-thought.md`](../01-core-cognitive/reasoning/chain-of-thought.md).
- **Self-Discover** — A pattern where a model composes its own reasoning structure per task. See [`13-agent-patterns/self-discover.md`](../13-agent-patterns/self-discover.md).
- **Self-RAG** — A model trained/prompted to decide when to retrieve and critique its own grounding. See [`10-rag/advanced-rag.md`](../10-rag/advanced-rag.md).
- **Self-reflection** — A model critiquing its own prior output/reasoning. See [`01-core-cognitive/reasoning/self-reflection.md`](../01-core-cognitive/reasoning/self-reflection.md).
- **Semantic memory** — General facts/knowledge independent of when/how they were learned. See [`01-core-cognitive/memory/README.md`](../01-core-cognitive/memory/README.md).
- **Sparse retrieval** — Keyword-based search (e.g. BM25). See [`10-rag/retrieval-strategies.md`](../10-rag/retrieval-strategies.md).
- **Supervisor pattern** — A coordinating agent routing tasks across worker agents. See [`06-multi-agent/README.md`](../06-multi-agent/README.md).

## T

- **Task decomposition** — Breaking a complex task into smaller, tractable sub-tasks. See [`01-core-cognitive/planning/task-decomposition.md`](../01-core-cognitive/planning/task-decomposition.md).
- **Tool (MCP)** — An action an MCP server exposes for a model to invoke. See [`11-mcp/primitives.md`](../11-mcp/primitives.md).
- **Tree of Thought (ToT)** — Exploring multiple reasoning branches as a search tree. See [`01-core-cognitive/reasoning/tree-of-thought.md`](../01-core-cognitive/reasoning/tree-of-thought.md).
- **Trace** — The full recorded trajectory of one agent run. See [`14-observability/README.md`](../14-observability/README.md).

## V

- **Vector database** — A storage/index system optimized for nearest-neighbor search over embeddings. See [`10-rag/vector-databases.md`](../10-rag/vector-databases.md).
- **Verbal reinforcement (learning)** — Using natural-language reflective feedback as a learning signal, rather than gradient updates. See [`13-agent-patterns/reflexion.md`](../13-agent-patterns/reflexion.md).
- **Voyager** — A pattern for open-ended, long-horizon, skill-accumulating agents. See [`13-agent-patterns/voyager.md`](../13-agent-patterns/voyager.md).

## W

- **Working memory** — Information held in the active context window/scratchpad during a single episode. See [`01-core-cognitive/memory/README.md`](../01-core-cognitive/memory/README.md).

## Contributing

See a term used in the repository that's missing here? Add it alphabetically
following the format `**Term** — one-sentence definition. See [link].`
