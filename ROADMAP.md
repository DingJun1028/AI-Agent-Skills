# Roadmap

This tracks the completeness of every section of the knowledge base. Because
the scope is intentionally huge (20 categories × full-depth pages), the
repository is built incrementally. This file is the source of truth for
"what's done vs. what's next" — check here before assuming a gap is
permanent.

## Status Legend

| Symbol | Meaning |
|---|---|
| 🟢 Full depth | Overview → Further Reading, all sections, diagrams, comparisons |
| 🟡 Core README | Solid overview + roadmap + key concepts, sub-pages pending |
| 🔴 Planned | Folder exists, name reserved, content not started |

## Category Status

| # | Category | Status | Notes |
|---|---|---|---|
| 01 | [Core Cognitive Skills](01-core-cognitive/README.md) | 🟢 Full depth | Reasoning, planning, memory foundations complete |
| 02 | [Tool Use](02-tool-use/README.md) | 🟡 Core README | Function calling, browser automation pages next |
| 03 | [Communication](03-communication/README.md) | 🟡 Core README | Summarization, structured output pages next |
| 04 | [Decision Making](04-decision-making/README.md) | 🟡 Core README | Hallucination detection deep dive next |
| 05 | [Domain Skills](05-domain-skills/README.md) | 🟡 Core README | Coding + research agent skills next |
| 06 | [Multi-Agent Systems](06-multi-agent/README.md) | 🟡 Core README | Debate & supervisor patterns next |
| 07 | [Safety & Alignment](07-safety-alignment/README.md) | 🟡 Core README | Guardrails deep dive next |
| 08 | [Learning & Adaptation](08-learning-adaptation/README.md) | 🟡 Core README | Feedback loop case studies next |
| 09 | [Integrations](09-integrations/README.md) | 🟡 Core README | Vendor-neutral integration patterns next |
| 10 | [RAG](10-rag/README.md) | 🟢 Full depth | Chunking → GraphRAG complete |
| 11 | [MCP](11-mcp/README.md) | 🟢 Full depth | Protocol, servers, clients, security complete |
| 12 | [Memory Systems](12-memory/README.md) | 🟡 Core README | Cross-links to 01-core-cognitive/memory |
| 13 | [Agent Patterns](13-agent-patterns/README.md) | 🟢 Full depth | ReAct, Reflexion, Plan-and-Execute, CodeAct, Voyager |
| 14 | [Observability](14-observability/README.md) | 🟡 Core README | Tracing deep dive next |
| 15 | [Evaluation](15-evaluation/README.md) | 🟡 Core README | LLM-as-judge deep dive next |
| 16 | [Deployment](16-deployment/README.md) | 🟡 Core README | K8s reference architecture next |
| 17 | [Models](17-models/README.md) | 🟡 Core README | Vendor comparison table next |
| 18 | [Workflows](18-workflows/README.md) | 🟡 Core README | Customer support workflow first |
| 19 | [Recipes](19-recipes/README.md) | 🟡 Core README | Cookbook format next |
| 20 | [Case Studies](20-case-studies/README.md) | 🟡 Core README | First anonymized case study next |

## Supporting Directories

| Directory | Status | Notes |
|---|---|---|
| `papers/` | 🟡 | Seed list of ~25 foundational papers, growing to 100+ |
| `resources/` | 🟡 | Curated tools, courses, communities |
| `glossary/` | 🟡 | ~60 terms seeded, target 200+ |
| `docs/` | 🟢 | Style guide, templates, contributor docs |
| `examples/` | 🟡 | Cross-references into topic folders |

## How This Roadmap Is Used

- New contributors: pick anything marked 🟡 or 🔴 and follow the structure in
  `CONTRIBUTING.md`.
- Maintainers: bump a row to 🟢 only once **all** required sections (see
  `docs/folder-readme-template.md`) are present.
- This file is updated in the same PR that changes a folder's status.

## Milestones

- **v0.1** (current) — scaffold + 4 flagship categories at full depth
- **v0.3** — all 20 categories at 🟡 or better (done)
- **v0.6** — 10+ categories at 🟢 full depth
- **v1.0** — all 20 categories 🟢, glossary 200+ terms, papers 100+ entries
