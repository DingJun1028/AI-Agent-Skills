# Style Guide

## Voice and tone

- Write like a senior engineer explaining a concept to a smart colleague who's
  new to *this specific area* — not condescending, not academic-dry.
- Prefer concrete examples over abstract description. If you can show a
  3-line example instead of a paragraph, do that.
- Second person ("you") is fine in Overview/Workflow sections. Avoid "we" in
  a way that implies a specific company or product built this repo.

## Formatting rules

| Rule | Example |
|---|---|
| Sentence case for section headings | `## Retrieval strategies`, not `## Retrieval Strategies` |
| Title case for page titles only | `# Chain of Thought Reasoning` |
| Tables for any comparison of ≥2 items | Advantages/Disadvantages, Comparison Tables |
| Mermaid for any process/architecture | `flowchart`, `sequenceDiagram`, `stateDiagram-v2` |
| Fenced code blocks with language tags | ` ```python `, ` ```mermaid ` |
| Relative links only | `[RAG](../10-rag/README.md)` not a github.com URL |
| Emoji only in nav/index pages | README tables, catalog, roadmap — not inside explanations |

## Required page sections (in order)

See [`page-template.md`](page-template.md) for the literal copy-paste
skeleton. Every topic page includes, at minimum:

1. Overview
2. Learning Objectives
3. Key Concepts
4. Architecture / Mermaid Diagram
5. Workflow
6. Examples
7. Advantages / Disadvantages
8. When to Use / When NOT to Use
9. Common Mistakes
10. Comparison Table (where a natural comparison exists)
11. Related Topics
12. Research Papers
13. Further Reading

Folder-level `README.md` files additionally need an Overview of the whole
category and a table of the sub-pages within it (see
[`folder-readme-template.md`](folder-readme-template.md)).

## Citing papers

Format: `**Title** — Authors (or "et al."), Year. [Link](url)`

```
**ReAct: Synergizing Reasoning and Acting in Language Models** — Yao et al., 2022. [arXiv:2210.03629](https://arxiv.org/abs/2210.03629)
```

Never invent a paper, a benchmark score, or a statistic. If you're not sure a
claim is accurate, either qualify it ("informally reported by practitioners")
or omit it.

## Mermaid conventions

- Prefer `flowchart TD` (top-down) for pipelines/architectures, `sequenceDiagram`
  for multi-turn interactions (e.g., agent ↔ tool ↔ environment), and
  `stateDiagram-v2` for lifecycle/status flows.
- Keep node labels short; put detail in the surrounding prose, not the diagram.
- Use consistent color semantics repo-wide where practical:
  - Blue (`#4C6EF5`) — core/entry concepts
  - Green (`#37B24D`) — successful/terminal states, production-readiness
  - Red/orange (`#F03E3E` / `#F59F00`) — failure states, risk, anti-patterns

## Anti-patterns

Any intentionally-bad example must be labeled inline:

```python
# ANTI-PATTERN — do not use in production
def run_tool(user_input):
    eval(user_input)  # arbitrary code execution, no sandboxing
```

Follow every anti-pattern with the corrected version.
