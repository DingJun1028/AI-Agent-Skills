# 05 · Domain Skills

## Overview

Domain skills are the specialized capabilities an agent needs to be
genuinely useful within a specific field — coding, writing, research,
finance, medicine, education, mathematics, creative work, and multimodal
domains (vision, speech, audio, video). Where earlier categories cover
domain-agnostic cognition and tooling, this category covers what changes
when you point those general skills at a specific vertical.

## Learning Objectives

- Identify which general skills (reasoning, tool use, RAG) map to which
  domain-specific needs
- Recognize domain-specific risk considerations (e.g. medical/financial
  agents need stricter verification than a general writing assistant)
- Know where to start when building an agent for a new domain

## Coding

Coding agents combine [tool use](../02-tool-use/README.md) (file access, code
execution), [task decomposition](../01-core-cognitive/planning/task-decomposition.md),
and tight feedback loops (running tests, linters, compilers) as the
verification signal. [Reflexion](../13-agent-patterns/reflexion.md) and
[CodeAct](../13-agent-patterns/codeact.md) are especially relevant patterns
here, since code has clear, checkable pass/fail signals.

## Writing

Writing agents lean heavily on [communication skills](../03-communication/README.md)
(structure, tone, style) and benefit from explicit style guides/examples
in-context. Self-reflection against a rubric (clarity, tone, correctness)
is a common quality-improvement loop.

## Research

Research agents combine [RAG](../10-rag/README.md) or web search with
multi-step [planning](../01-core-cognitive/planning/README.md) — gather
sources, synthesize, cross-check for consistency, and cite. Groundedness and
[hallucination detection](../04-decision-making/README.md#hallucination-detection)
are especially important given the correctness expectations of research
output.

## Finance

Finance agents (analysis, reporting, not autonomous trading) require strict
[verification](../04-decision-making/README.md#verification) of any
numerical claim, clear separation between factual retrieval and generated
interpretation, and typically human review before any action with real
financial consequences. Regulatory and compliance considerations are often
domain-specific and jurisdiction-dependent — always involve qualified human
oversight for real financial decisions.

## Medicine

Medical-adjacent agents (information synthesis, documentation support — not
diagnosis) demand the highest bar for grounding and verification in this
list, given the real-world stakes of incorrect medical information. Any
system in this space should be built with active involvement from qualified
medical professionals and appropriate regulatory awareness; this repository
provides general agent-engineering patterns, not medical or regulatory
guidance.

## Education

Educational agents (tutoring, content generation, feedback) benefit from
adaptive difficulty (see [Few-Shot / In-Context Learning](../08-learning-adaptation/README.md)),
Socratic-style scaffolded reasoning rather than just handing over answers,
and careful tone calibration for the learner's level.

## Mathematics

Math-focused agents benefit heavily from [Chain of Thought](../01-core-cognitive/reasoning/chain-of-thought.md)
and, for harder problems, [Tree of Thought](../01-core-cognitive/reasoning/tree-of-thought.md),
combined with external verification via calculators/symbolic math tools
rather than trusting generated arithmetic directly.

## Creative Work

Creative agents (fiction, brainstorming, design ideation) benefit from
higher-temperature/more exploratory generation, multiple-candidate
generation (analogous to [Tree of Thought](../01-core-cognitive/reasoning/tree-of-thought.md)
applied to creative branches), and iterative human feedback loops rather
than one-shot generation.

## Multimodal (Vision, Speech, Audio, Video)

Multimodal agents extend the same cognitive/tool-use patterns to non-text
inputs/outputs — analyzing images, transcribing/generating speech, and
reasoning over video. The core agent loop concepts (reasoning, tool use,
verification) still apply; what changes is the input/output representation
and the tools needed to process each modality.

## Key Concepts

| Term | Definition |
|---|---|
| Domain skill | A capability specialized for a particular field, built from general-purpose cognitive/tool-use primitives |
| Vertical agent | An agent purpose-built and tuned for one specific domain, as opposed to a general-purpose assistant |
| Ground truth signal | A domain-specific way of checking correctness (tests for code, calculators for math, citations for research) |

## Advantages / Disadvantages

| Advantages | Disadvantages |
|---|---|
| Domain specialization significantly improves reliability and usefulness | Requires domain expertise to design good verification/evaluation for that field |
| Reuses general-purpose primitives — no need to reinvent reasoning per domain | Higher-stakes domains (medicine, finance) require much stricter safety/verification investment |
| Enables tighter feedback loops using domain-native signals (tests, citations, calculators) | Domain-specific tooling/data integration adds engineering overhead per vertical |

## Common Mistakes

- **Mistake:** Applying the same verification bar to a low-stakes creative
  task and a high-stakes medical/financial task. **Fix:** Calibrate
  verification rigor to the real-world cost of being wrong in that domain.
- **Mistake:** Building a domain agent without involving domain experts in
  evaluation design. **Fix:** Involve subject-matter experts, especially for
  regulated or high-stakes domains.

## Related Categories

- [`01-core-cognitive/`](../01-core-cognitive/README.md), [`02-tool-use/`](../02-tool-use/README.md), [`10-rag/`](../10-rag/README.md) — the general-purpose primitives domain skills build on
- [`04-decision-making/`](../04-decision-making/README.md) — verification calibrated to domain stakes
- [`18-workflows/`](../18-workflows/README.md) — concrete domain workflows (customer support, legal, medical assistant, etc.)

## Research Papers

See [`papers/README.md`](../papers/README.md) for domain-specific research
entry points (code generation, medical QA, financial NLP).

## Further Reading

- [`18-workflows/README.md`](../18-workflows/README.md) — end-to-end domain workflow examples
