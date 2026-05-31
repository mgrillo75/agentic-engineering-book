---
title: Dimensions of Readiness
description: Why agent readiness varies independently across individual practice, the environment, and the organization rather than reducing to a single score
created: 2026-05-29
last_updated: 2026-05-31
tags: [agent-readiness, maturity, diagnostic, dimensions, organization]
part: 3
part_title: Perspectives
chapter: 11
section: 4
order: 3.11.4
---

# Dimensions of Readiness

"Agent-ready" is not a binary, and it does not reduce cleanly to a single score. A team can have strong individual practice and a hostile codebase, or excellent infrastructure and engineers who still treat agents as employees to onboard. Collapsing those situations into one number hides exactly the information that would tell a team what to do next. Resolving readiness into a few dimensions that move independently is more actionable than an aggregate rating.

Three dimensions are useful to track separately because they improve at different rates and respond to different interventions—**individual practice** (how engineers actually work with agents), **the environment** (what the codebase and tooling give agents to work with), and **the organization** (how work, ownership, and decisions are structured around agents). These are not a fixed taxonomy; they are a practical decomposition. The value is in looking at them separately rather than in the exact list.

---

## Three Dimensions That Move Independently

| Dimension               | Question it answers                                  | What moves it                                                          |
| ----------------------- | ---------------------------------------------------- | ---------------------------------------------------------------------- |
| **Individual practice** | How do engineers actually work with agents?          | Mental model, vocabulary, where effort lands in the leverage hierarchy |
| **The environment**     | What does the codebase give agents to work with?     | The four surfaces and the readiness principles, mechanized             |
| **The organization**    | How are work and ownership structured around agents? | Shared interfaces, ownership, policy, decision flow                    |

The dimensions improve at different rates. Individual practice and the environment are where most early improvement lands, because both are within reach of a single engineer or team. Organizational change is the long pole—it requires sponsorship, budget, and cultural shift that sit outside any one contributor's authority.

---

## A General Direction of Travel

Rather than a rigid rung-by-rung rubric, each dimension tends to move through the same broad progression—from ad hoc, to deliberately mechanized, to self-improving. The stages below are illustrative markers, not a scored ladder.

### Individual Practice

| Stage              | What it looks like                                                                                                                |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| **Early**          | Treats the agent as an employee to onboard with documentation; tunes prompt wording when output is wrong                          |
| **Deliberate**     | Curates context, builds tools and skills for recurring tasks, invests in the verification loop, and names failure modes precisely |
| **Self-improving** | Treats memory and learning across sessions as first-class; the engineer's own practice improves over time                         |

The most common gap is an engineer stuck at the early stage—optimizing prompt wording when the real leverage is in the system around the agent. The shift usually follows the lived experience of effort wasted on the wrong knob, after which the reframe lands.

### The Environment

| Stage              | What it looks like                                                                                                            |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| **Early**          | No reliable verification; setup is tribal knowledge; standards live in prose                                                  |
| **Deliberate**     | Fast verification in the inner loop; the repo describes itself; standards fail builds; blast radius bounded by infrastructure |
| **Self-improving** | Memory, evaluation suites for the workflow, and cost observability in place; the environment improves monotonically           |

The jump from early to deliberate is the one that most changes day-to-day agent reliability, because it puts a fast verification loop and a self-describing repo in place at the same time. The self-improving stage adds the properties long checklists tend to miss—memory, workflow evals, and cost visibility.

### The Organization

| Stage              | What it looks like                                                                                                                |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| **Early**          | Ad hoc; every engineer interacts with agents differently; no shared vocabulary                                                    |
| **Deliberate**     | A shared interface exists—every service exposes a setup command, a verify command, and a self-description; readiness has an owner |
| **Self-improving** | A widely usable system that many people, including non-engineers, can prompt to ship to production                                |

Agreement on a shared _interface_—a setup command, a verify command, a self-description—is what makes organizational progress tractable at scale. What lives inside that interface can vary by team. Forcing one toolchain across many teams tends to produce malicious compliance—a self-description file that says "see the wiki"—rather than real readiness.

#### Conway's Law and Shared Fate

Two socio-technical forces shape the organization dimension beyond interfaces and ownership. **Conway's Law** holds that organizations build systems whose structure mirrors their own communication structure (Conway, Melvin E., "How Do Committees Invent?", _Datamation_, 1968). The consequence for readiness is direct: org structure and culture shape _what_ agents build, not only _how_ humans coordinate around them. **Shared fate** names how tightly the components of an ecosystem are coupled—a monorepo with trunk-based development is high shared fate, where one change can reach everything at once. That coupling is a trade-off that is simultaneously technical and social: high shared fate makes a single change propagate everywhere, which is both powerful and dangerous (_Software Engineering at Google_, O'Reilly, 2020). The full treatment of both forces lives in the Software Ecology section; here they name what the organization dimension is measuring.

---

## Reading the Dimensions Together

The dimensions are largely independent, and the gaps between them are diagnostic in themselves.

- **Strong practice, weak environment:** Skilled engineers fighting a hostile codebase. The fix is environment investment, not training.
- **Strong environment, weak practice:** A capable environment that engineers underuse because they still treat the agent as something to onboard. The fix is the reframe and vocabulary, not more tooling.
- **Strong practice and environment, weak organization:** Strong local results that do not propagate. The fix is a shared interface and an owner, not more individual effort.

A team that is self-improving on all three dimensions—an environment anyone can prompt to ship, with practice and organization to match—is rare. Most realistic improvement concentrates on individual practice and the environment, because organizational change depends on lived productivity wins to justify the cultural and budget shifts it requires. Pushing organization-wide change before practice and environment are real tends to read as overhead.

---

## Open Questions

- Are the dimensions truly independent, or does a strong environment eventually pull individual practice along by making the better path easier? The pit-of-success argument suggests partial coupling.
- What is the realistic ceiling for a brownfield organization? The evidence for fully self-improving operation concentrates in greenfield contexts; legacy constraints may cap the environment dimension well short of it.

---

## Connections

- **[Software Ecology](6-software-ecology.md):** Conway's Law and shared fate are developed there as the socio-technical forces operating on the organization dimension.
- **[Prompt Maturity Model](../9-mental-models/2-prompt-maturity-model.md):** That model describes the sophistication of an individual _prompt_; this diagnostic describes the readiness of the _repository and organization_ around the agent. The individual-practice dimension overlaps with prompt maturity, but the environment and organization dimensions measure context that prompt maturity does not address.
- **[Software Factories](../9-mental-models/7-software-factories.md):** The factory model describes what changes at scale once readiness is high; these dimensions describe whether a team has reached the readiness a factory presumes. Factory operation presumes the self-improving end of all three.
- **[The Four Surfaces](1-the-four-surfaces.md):** The environment dimension is the four surfaces and the readiness principles expressed as a progression.
- **[Readiness Principles](3-readiness-principles.md):** The eight principles populate the deliberate and self-improving stages of the environment dimension.
