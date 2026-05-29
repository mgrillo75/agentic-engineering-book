---
title: Readiness Principles
description: Eight principles of an agent-ready environment and a compact five-point frame that captures them
created: 2026-05-29
last_updated: 2026-05-29
tags: [agent-readiness, principles, verification, standards, reversibility]
part: 3
part_title: Perspectives
chapter: 11
section: 3
order: 3.11.3
---

# Readiness Principles

Detailed agent-readiness rubrics run to dozens of criteria, but the criteria cluster. Behind a long checklist sits a much smaller set of principles—each criterion is a specific instantiation of one underlying claim about what makes a repository legible to a non-human collaborator. The principles are more durable than any single check: checks change with tooling, but the claims they encode do not.

Eight principles cover the cluster. Each is stated as a property the environment should have, with a concrete example of what satisfies it and what violates it. A compact five-point frame follows for recall.

---

## The Eight Principles

| #   | Principle                     | The underlying claim                                                           |
| --- | ----------------------------- | ------------------------------------------------------------------------------ |
| 1   | Verifiability in seconds      | An agent that cannot verify its own work freezes or fakes confidence           |
| 2   | The repo describes itself     | The agent should never need tribal knowledge to go from clone to running tests |
| 3   | Standards are mechanized      | Agents follow rules that fail builds, not rules in a PDF                       |
| 4   | Blast radius bounded by infra | Assume the agent will ship something wrong; bound the damage structurally      |
| 5   | Observability closes the loop | Without runtime signal, "did it work?" becomes a multi-day question            |
| 6   | Work arrives well-scoped      | Output quality is bounded by input quality; vague tickets produce vague work   |
| 7   | Reversibility everywhere      | Reversibility is what makes velocity safe                                      |
| 8   | Agents are first-class users  | Acknowledge agents exist and give them ergonomics                              |

### 1. Verifiability in seconds, not days

Lint, typecheck, test, build, and coverage should all run from one command, fast enough to use as an inner loop. A large share of any readiness rubric is some variation of this single property, because it is the precondition for self-correction.

**Satisfies:** `make test` runs a relevant slice in under a minute and exits nonzero on failure.
**Violates:** The only signal is a 45-minute CI pipeline the agent cannot trigger mid-task.

### 2. The repo describes itself

An agent-facing description file, a README, an environment template, single-command setup, and documented build commands let an agent get from `git clone` to a running test suite without asking anyone. Critically, these documents must be validated in continuous integration, or they drift into lies—a setup command that no longer works is worse than none.

**Satisfies:** A CI job confirms every path and command referenced in the self-description still resolves.
**Violates:** An onboarding doc that points at a script deleted six months ago.

### 3. Standards are mechanized, not aspirational

Anything a style guide asserts, a linter should enforce. Naming conventions, file-size caps, complexity thresholds, dead-code detection, duplication limits, unused-dependency checks—all as automated checks, not human review items. Agents reliably follow rules that fail builds; they do not reliably follow prose.

**Satisfies:** A file-size cap is a check that fails the build at the threshold.
**Violates:** A style guide that says "keep files small" with no enforcement.

### 4. Blast radius is bounded by infrastructure, not by trust

Branch protection, review requirements, code-ownership rules, feature flags, progressive rollout, and rollback automation all assume the agent will eventually ship something wrong. The design question is whether that mistake becomes a chat message or a production incident.

**Satisfies:** Changes land through a protected branch with required checks and a flag that can disable the new path instantly.
**Violates:** An agent with direct push access to a shared deploy and no flag to turn anything off.

### 5. Observability closes the loop

Structured logs, error tracking with stack traces, metrics, and deployment signals give the agent a way to answer "did my change actually work?" Without them, that question takes days and the agent has no signal to learn from.

**Satisfies:** A change can be traced from deploy to error rate within minutes.
**Violates:** Confirming an effect requires manually reproducing the issue across environments over several days.

### 6. Work arrives in well-scoped units

Issue templates, labels, and a priority and area taxonomy raise the input quality of the work. The output quality of an agent is bounded by the input quality of the ticket—much of "the agent isn't reliable" is "the ticket wasn't specific."

**Satisfies:** A ticket states the change, the acceptance criteria, and the affected area precisely enough to verify.
**Violates:** A one-line ticket that names a symptom and assumes shared context.

### 7. Reversibility is everywhere

Pinned lockfiles, a minimum release age on dependencies, release automation that doubles as rollback automation, and immutable artifacts make velocity safe. Reversibility is the property that lets a wrong change be a temporary state rather than a permanent one.

**Satisfies:** Any release can be rolled back with one command to a known-good immutable artifact.
**Violates:** A deploy that mutates shared state in place with no clean path backward.

### 8. Agents are first-class users

A self-description file, skills, code-ownership rules that include bots, agent co-authorship in commits, automated review, and agent-aware secret scanning all acknowledge that agents operate in the repository. The environment designs for them as users rather than treating them as humans with bugs.

**Satisfies:** Commits carry agent co-authorship and a skills surface exists for recurring tasks.
**Violates:** No acknowledgment that agents touch the repo, so every agent affordance is improvised per session.

---

## What Long Checklists Tend to Miss

Even thorough rubrics tend to underweight four properties, all of which map to the upper tiers of the leverage hierarchy:

- **Memory across sessions.** Whether a learning from one run informs the next—decision logs, architecture records, recorded conventions. Without it, every session reinvents the same context.
- **Spec-first culture.** Whether teams write specifications before code. The difference between a good and bad implementation is almost entirely upstream of the first tool call.
- **Cost and token observability.** Whether the team knows what an agent costs per task. This becomes the dominant blocker to scaling agent usage.
- **Evals for the workflow itself.** Teams test their code but not their prompts and skills. Drift in agent quality is invisible without it.

---

## The Compact Frame

The eight principles compress into a five-point frame that is easier to carry into a conversation. An agent-ready environment is one where:

1. Any change is **verifiable in under sixty seconds**.
2. The repo **answers its own onboarding questions**.
3. Every standard is **enforced by a machine**.
4. Every action is **reversible**.
5. The team treats **agents as users it designs for**, not as junior developers it manages.

Every item in a long readiness rubric is a specific instantiation of one of these five. The compact frame is what a decision-maker remembers; the full rubric is what a team runs.

---

## Open Questions

- Do the eight principles have a fixed priority order, or does it depend on context? Verifiability appears foundational, but a regulated codebase may need blast-radius controls first.
- How is each principle measured without gaming? A repository can technically satisfy "the repo describes itself" with a file that adds no real legibility, which is why CI validation of the self-description matters.

---

## Sources

This section derives from agent-readiness practice as observed across codebase audits and rubrics. The principles are a generic distillation; the specific checks vary by stack and are intentionally not tied to any one tool.

## Connections

- **[The Four Surfaces](1-the-four-surfaces.md):** Verifiability maps to the verification surface, self-description and mechanized standards to context, and the missing-properties list to the upper leverage tiers.
- **[Failure Modes](2-failure-modes.md):** Each principle, when absent, produces a specific failure mode—no verifiability yields the verification gap, no scoping yields spec drift.
- **[Harnesses](../6-harnesses/6-security-permissions-trust.md):** Blast-radius bounding is the security and permissions surface of the harness; trust is replaced by structural limits.
- **[Specs as Source Code](../9-mental-models/3-specs-as-source-code.md):** The spec-first property that rubrics underweight is the mental model that treats specifications as the primary programming surface.
