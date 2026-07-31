# 20 · Case Studies

## Overview

Case studies describe how AI agent patterns from this repository play out in
real (or realistic, composite) deployments — the practical lessons,
tradeoffs, and surprises that only show up once a system meets real users
and real scale. Where [`18-workflows/`](../18-workflows/README.md) shows
starting architectures, this category shows what happened when
architectures like these were actually deployed.

## Learning Objectives

- Understand common gaps between a working prototype and a production-ready
  agent system
- Learn from documented failure modes, not just success stories
- Recognize patterns across case studies that generalize beyond any one
  company/domain

## Case Study Categories

| Category | Focus |
|---|---|
| Enterprise AI | Internal tooling, employee-facing agents, large-scale rollouts |
| Coding Assistant | Developer-facing coding agents in real engineering workflows |
| Autonomous Agents | Higher-autonomy systems and their guardrail/approval lessons |
| Customer Service | Public-facing support agents at scale |
| Research Automation | Agents automating literature review, synthesis, and reporting |

## Structure of a Case Study Entry

Each case study in this category (as they're added) follows a consistent
structure:

1. **Context** — what problem was being solved, and constraints
   (scale, latency, compliance)
2. **Architecture** — which patterns from this repository were used, and why
3. **What worked** — concrete wins and why they mattered
4. **What didn't work initially** — real failure modes encountered and how
   they were diagnosed
5. **Lessons learned** — generalizable takeaways
6. **Metrics** — how success was measured (see [`15-evaluation/`](../15-evaluation/README.md))

## Common Cross-Cutting Lessons (from the field generally)

While individual case study write-ups are still being added (see
[`ROADMAP.md`](../ROADMAP.md)), some patterns recur consistently across
real agent deployments broadly documented in the field:

- **Evaluation infrastructure pays for itself quickly** — teams that invest
  in [systematic evaluation](../15-evaluation/README.md) early catch
  regressions that would otherwise reach users.
- **The gap between "works in the demo" and "works reliably at scale" is
  usually about edge cases and failure handling**, not core capability —
  see [`04-decision-making/`](../04-decision-making/README.md) and
  [`09-integrations/README.md#resilience-patterns`](../09-integrations/README.md#resilience-patterns).
- **Human-in-the-loop approval is often what makes higher-autonomy systems
  deployable at all** — see
  [`07-safety-alignment/README.md#human-approval`](../07-safety-alignment/README.md#human-approval).
- **Observability is consistently under-invested in early**, and
  consistently the thing teams wish they'd built sooner — see
  [`14-observability/`](../14-observability/README.md).

## Key Concepts

| Term | Definition |
|---|---|
| Composite case study | A write-up synthesizing common patterns across multiple real deployments, when a single-company account isn't available/appropriate to publish |
| Failure mode | A specific, documented way a real system went wrong in production |

## Advantages / Disadvantages

| Advantages | Disadvantages |
|---|---|
| Grounds abstract patterns in real deployment reality | Case studies age — architectures and tooling referenced may become outdated |
| Surfaces failure modes prototypes rarely reveal | Individual case studies may not generalize to every context |

## Common Mistakes

- **Mistake:** Reading only the "what worked" section and skipping the
  failure modes. **Fix:** The failure modes are usually the most valuable
  and specific part — don't skip them.
- **Mistake:** Assuming a case study's specific architecture is the "right"
  answer for your context without checking constraint fit. **Fix:** Compare
  the case study's context/constraints to your own before adopting its
  architecture wholesale.

## Related Categories

- [`18-workflows/`](../18-workflows/README.md) — the architectures case studies are built from
- [`15-evaluation/`](../15-evaluation/README.md) — how case studies measured success
- [`14-observability/`](../14-observability/README.md) — how case studies diagnosed failures

## Research Papers

Case studies are practitioner accounts rather than peer-reviewed research;
see [`papers/README.md`](../papers/README.md) for the underlying academic
foundations referenced throughout this repository.

## Further Reading

- [`ROADMAP.md`](../ROADMAP.md) — tracking status of specific case study write-ups as they're added
