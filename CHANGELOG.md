# Changelog

All notable changes to this repository are documented here.
Format loosely follows [Keep a Changelog](https://keepachangelog.com/).

## [0.1.0] - 2026-07-30

### Added — Initial scaffold and flagship content

- Root navigation: `README.md`, `SKILL_CATALOG.md`, `ROADMAP.md`, `GLOSSARY` index
- Governance: `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`, `LICENSE`
- GitHub templates: issue templates, PR template, Discussions category guide
- Full folder tree for all 20 numbered categories (`01-core-cognitive` through
  `20-case-studies`), plus `papers/`, `resources/`, `glossary/`, `docs/`,
  `examples/`, `assets/`
- Fully written flagship folders (complete depth — Overview through Further
  Reading on every page):
  - `01-core-cognitive/` — reasoning, planning, memory foundations
  - `11-mcp/` — Model Context Protocol deep dive
  - `10-rag/` — Retrieval-Augmented Generation deep dive
  - `13-agent-patterns/` — ReAct, Reflexion, Plan-and-Execute, CodeAct, etc.
- README-level (overview + roadmap) coverage for all remaining folders, marked
  `Status: Stub → Expanding`, tracked in `ROADMAP.md`

### Planned (see `ROADMAP.md` for live status)

- Full-depth pages for `02-tool-use` through `09-integrations`
- Full-depth pages for `12-memory`, `14-observability` through `20-case-studies`
- `papers/` curated bibliography with 100+ entries
- `glossary/` with 200+ terms
- Populate `assets/diagrams/` with exported PNG/SVG versions of Mermaid diagrams

---

### Versioning note

Because this is a living knowledge base rather than shipped software, versions
mark **content milestones** (e.g., "all 20 folders at full depth") rather than
semantic API changes.
