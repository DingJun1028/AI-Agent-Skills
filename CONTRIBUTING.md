# Contributing to AI-Agent-Skills

Thank you for considering a contribution! This repository aims to be the most
comprehensive, accurate, and well-organized open knowledge base for building
AI agents. That's only possible with community help.

## Ways to Contribute

| Type | Examples |
|---|---|
| 📝 New content | A new pattern, a missing technique, a new domain skill |
| 🛠️ Improve existing pages | Fix inaccuracies, add examples, improve diagrams |
| 🔗 Fix links | Dead references, outdated paper links |
| 🌐 Translations | Translate a folder's README into another language |
| 🧪 Examples | Small, runnable, well-commented educational snippets |
| 📊 Case studies | Real-world (anonymized if needed) agent deployment write-ups |
| 🐛 Report issues | Inaccuracies, unclear explanations, structural problems |

## Before You Start

1. **Search existing issues and PRs** to avoid duplicate work.
2. For large additions (a new numbered folder, a new pattern family), open an
   issue first using the "New Content Proposal" template so maintainers can
   help you scope it.
3. Read `docs/style-guide.md` for formatting conventions.

## Repository Conventions

Every topic page should generally include, in this order:

1. **Overview** — one paragraph, plain language
2. **Learning Objectives** — 3-6 bullet points
3. **Key Concepts** — definitions, ideally as a table
4. **Architecture / Mermaid Diagram** — at least one diagram
5. **Workflow** — step-by-step
6. **Examples** — minimal, commented
7. **Advantages / Disadvantages** — as a table
8. **When to Use / When NOT to Use**
9. **Common Mistakes**
10. **Comparison Table** (vs. related techniques, where relevant)
11. **Related Topics** — cross-links using relative paths
12. **Research Papers** — real, correctly cited papers only
13. **Further Reading**

See `01-core-cognitive/reasoning/chain-of-thought.md` for a reference example
of this structure fully applied.

## Style Guide (short version)

- Use sentence case for headings (`## Tool selection strategies`, not
  `## Tool Selection Strategies`) — except top-level doc titles.
- Use GitHub-flavored Markdown tables for comparisons.
- Use Mermaid for diagrams — no external image dependencies where avoidable.
- Emojis: used sparingly, only in navigation/index pages, never inside
  technical explanations.
- Every internal link must be a relative path (`../11-mcp/README.md`), never
  an absolute URL to this repo.
- Cite papers as `**Title** — Authors, Year. [Link](url)`.
- Do not invent papers, benchmarks, or statistics. If unsure, say "informally
  reported" or omit the claim.

## Pull Request Process

1. Fork the repo and create a branch: `git checkout -b add/topic-name`
2. Follow the structure above for any new page.
3. Run the (future) link checker / markdown lint if available.
4. Fill out the PR template completely.
5. A maintainer will review for accuracy, structure, and tone — expect at
   least one round of feedback.
6. Once approved, squash-merge is used to keep history clean.

## Adding a New Numbered Folder

If you believe a new top-level category is needed:

1. Open a "New Content Proposal" issue.
2. Get sign-off from a maintainer on the numbering (folders are numbered by
   conceptual layer, not alphabetically).
3. Include a `README.md` following `docs/folder-readme-template.md`.
4. Add the folder to the root `README.md` navigation table and
   `SKILL_CATALOG.md`.

## Code of Conduct

Participation in this project is governed by our [Code of Conduct](CODE_OF_CONDUCT.md).

## Recognition

All contributors are listed in `docs/contributors.md` (generated from PR
history). Significant content contributions may be highlighted in
`CHANGELOG.md`.
