---
title: The Four Surfaces
description: The four surfaces that determine agent quality and the leverage hierarchy that ranks where effort pays off
created: 2026-05-29
last_updated: 2026-05-29
tags:
  [agent-readiness, surfaces, context, tools, verification, memory, leverage]
part: 3
part_title: Perspectives
chapter: 11
section: 1
order: 3.11.1
---

# The Four Surfaces

An agent operates as a loop: read context, propose an action, execute it, observe the result, repeat. The model is a fixed input to that loop—a given capability that the practitioner does not control day to day. What the practitioner does control are the four surfaces surrounding the model, and those surfaces determine the quality of the output far more than prompt wording does.

Naming the surfaces separates "the agent is bad" from "this surface is starved." That separation is the precondition for fixing anything. The four surfaces are **context** (what the agent can see), **tools** (what the agent can do), **verification** (how the agent knows it worked), and **memory** (what survives across sessions).

---

## The Four Surfaces

| Surface          | Question it answers                | Concrete artifacts                                                                        |
| ---------------- | ---------------------------------- | ----------------------------------------------------------------------------------------- |
| **Context**      | What can the agent see?            | Files in scope, integration access to upstream systems, relevant logs, retrieved snippets |
| **Tools**        | What can the agent do?             | Build and test commands, skills for repeated tasks, integrations to external systems      |
| **Verification** | How does the agent know it worked? | Fast lint, typecheck, tests, coverage—runnable as an inner loop                           |
| **Memory**       | What survives across sessions?     | Decision logs, architecture notes, recorded learnings, durable session artifacts          |

The model is deliberately excluded from this list. Selecting a more capable model is a real lever, but it is a one-time decision with a fixed ceiling. The four surfaces are where ongoing engineering effort compounds.

### Context

Context is what the agent can see at the moment it reasons. A robust context surface puts the right files, the right upstream signals, and the right prior decisions within reach—and keeps everything else out.

**Fragile:** A repository where the relevant business logic lives in a service the agent has no access to, and where the failing behavior is only visible in production logs the agent cannot read. The agent reasons from source alone and guesses at runtime behavior.

**Robust:** The agent can read the source, query the error tracker for the actual stack trace, and pull the related ticket. The answer to "what does this system actually do" lives in reach, not in tribal knowledge.

### Tools

Tools are what the agent can act with. A capable model with no way to run the test suite or no integration to the system it must change is reduced to producing plausible text.

**Fragile:** A repeated three-step task—regenerate a fixture, run a migration, validate output—is described in prose in a prompt every time. The agent reconstructs the steps from memory and drifts.

**Robust:** The three-step task is a single skill or command. The agent invokes it, and the workflow is encoded in a tool affordance rather than in instructions that are forgotten.

### Verification

Verification is how the agent learns whether its work succeeded. Without a fast, deterministic signal, an agent either freezes or hallucinates confidence—it cannot distinguish a working change from a broken one.

**Fragile:** The only feedback is a 45-minute CI run that the agent cannot trigger inside its loop. The agent finishes, asserts success, and moves on with no evidence.

**Robust:** Lint, typecheck, and a relevant test slice all run in seconds from one command. The agent uses them as an inner loop, catching its own regressions before declaring done.

### Memory

Memory is what persists when the session ends. Without it, every session reinvents the same context and re-learns the same lessons.

**Fragile:** An agent discovers a non-obvious constraint—a service that must be migrated before a dependent one—solves it, and the discovery evaporates at session end. The next session hits the same wall.

**Robust:** The constraint is recorded as an architecture decision or a durable note that the next session loads. Expertise accumulates rather than resetting.

---

## The Leverage Hierarchy

Most practitioner effort concentrates on prompt phrasing, which is the lowest-leverage surface to tune. The productivity gains observed in the field concentrate in the middle and upper tiers—tools, verification, specs, and memory. Ranking the tiers does not mean abandoning the lower ones; it means knowing which tier a given problem actually lives in before reaching for a fix.

| Tier | Lever                       | What it changes                            | Leverage |
| ---- | --------------------------- | ------------------------------------------ | -------- |
| 1    | Prompt phrasing             | Wording of a single request                | Lowest   |
| 2    | Context curation            | What the agent sees and what is excluded   | Low      |
| 3    | Tools, skills, integrations | What the agent can act on                  | Medium   |
| 4    | Verification loop           | Whether the agent can self-correct         | High     |
| 5    | Specs and decomposition     | Whether the agent solves the right problem | Higher   |
| 6    | Memory across sessions      | Whether learning compounds                 | Highest  |

The hierarchy reads from a single-request scope at the bottom to a cross-session scope at the top. Tier 1 affects one prompt. Tier 6 affects every future session. The wider the scope a fix touches, the more it compounds, which is why the leverage increases with the tier.

A practical consequence: when an agent repeatedly produces the wrong output, the reflex to rephrase the prompt (tier 1) is usually misplaced. The more likely root causes are an absent verification loop (tier 4) or a vague specification (tier 5), and those are where a durable fix lives.

---

## When to Use This Framing

### When to Use

- Diagnosing why an agent underperforms on a specific task—identify which surface is starved before changing anything.
- Prioritizing investment in agent tooling—spend on the highest tier that is currently weak.
- Building a shared vocabulary across a team so "the agent is bad" becomes "verification is missing on this slice."

### When Not to Use

- Selecting a model for a genuinely capability-bound task. When the task exceeds what any available model can do, surface engineering does not help; the model itself is the constraint.

---

## Open Questions

- How should the surfaces be weighted for a given task type? A research task may be context-bound while a refactor is verification-bound, but a general weighting is not established.
- Where does a fifth surface—cost and token observability—belong? It is not visibility, action, verification, or memory, yet it bounds how far any of them can scale.

---

## Connections

- **[Context Fundamentals](../4-context/1-context-fundamentals.md):** The context surface is the subject of Chapter 4 in depth; this section treats it as one of four coordinated surfaces.
- **[Tool Design](../5-tool-use/1-tool-design.md):** The tools surface is where workflow gets encoded into affordances rather than instructions—the central argument of the tool-use chapter.
- **[Harness as Control System](../6-harnesses/4-harness-as-control-system.md):** The verification surface is implemented in the harness; a control-system view of the harness is the verification surface made concrete.
- **[Failure Modes](2-failure-modes.md):** Each failure mode maps back to one of these four surfaces; this section provides the surfaces that the next section diagnoses against.
- **[Long-Horizon Agent State](../12-long-horizon-agent-state/_index.md):** The memory surface, highest in the leverage hierarchy, is developed fully in the next chapter.
