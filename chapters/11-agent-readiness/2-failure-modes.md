---
title: Failure Modes
description: A shared triage vocabulary for agent failure mapped to root surfaces and concrete fixes
created: 2026-05-29
last_updated: 2026-05-29
tags: [agent-readiness, failure-modes, diagnostics, triage, vocabulary]
part: 3
part_title: Perspectives
chapter: 11
section: 2
order: 3.11.2
---

# Failure Modes

"The agent is bad" is not a diagnosis. It bundles together unrelated problems with unrelated fixes, and it points effort at the model when the model is rarely the cause. A shared vocabulary for failure modes converts a vague complaint into a specific, fixable claim—and once a team shares that vocabulary, triage gets measurably faster.

Each failure mode below has a definition, a symptom that signals it, a root surface (mapping to the four surfaces from §1), and a fix. The point of mapping each mode to a surface is that the fix follows from the surface: a context failure is fixed by changing what the agent sees, not by rephrasing the prompt.

---

## Diagnostic Table

| Failure mode           | Symptom                                                                     | Root surface                | Fix                                                            |
| ---------------------- | --------------------------------------------------------------------------- | --------------------------- | -------------------------------------------------------------- |
| **Context starvation** | Agent asks for or invents information it should have                        | Context                     | Put the missing files, logs, or system access in reach         |
| **Context poisoning**  | Agent fixates on stale or irrelevant detail; quality drops as context grows | Context                     | Remove always-on noise; load context on demand                 |
| **Verification gap**   | Agent declares success without evidence; regressions slip through           | Verification                | Add fast lint, typecheck, and test slices runnable in the loop |
| **Tool poverty**       | Agent reconstructs the same multi-step task from prose each time            | Tools                       | Encode the task as a skill, command, or integration            |
| **Spec drift**         | Agent builds something coherent but not what was wanted                     | Specs (upstream of context) | Tighten the ticket; decompose into well-scoped units           |
| **Lossy handoff**      | Next session repeats a solved discovery or mistake                          | Memory                      | Record decisions and learnings the next session can load       |

---

## Context Starvation

**Definition:** The agent cannot see what it needs to complete the task—missing files, no access to the upstream system, no view of production behavior.

**Symptom:** The agent asks for information that should be available, or fabricates a plausible-but-wrong value because the real one was out of reach.

A common form is an agent fixing a bug from source alone when half the answer lives in logs or tickets it cannot read. Half of any non-trivial bug fix is "what does this system actually do," and that answer often lives in runtime signals, not in the source tree.

**Fix:** Widen the context surface deliberately. Grant access to the error tracker, the issue system, and the runbooks; bring the relevant files into scope. The fix is additive and targeted—not "load everything," which produces the opposite failure.

## Context Poisoning

**Definition:** The agent drowns in irrelevant or stale information. The signal it needs competes with noise for attention.

**Symptom:** Output quality degrades as context grows. The agent cites outdated instructions or fixates on a detail that no longer applies.

The usual sources are always-on markdown files loaded into every session and oversized prompts that accumulate instructions nobody prunes. A model weights all tokens it receives, so stale context actively competes with current context.

**Fix:** Prune the always-on surface and load context on demand. Lazy-loaded skills and retrieval-on-need beat a standing wall of documentation. This is the direct counterpart to context starvation: the two failures bracket a curation problem from opposite sides.

## Verification Gap

**Definition:** The agent cannot tell whether its work succeeded because no fast, deterministic check exists inside its loop.

**Symptom:** The agent asserts success with no supporting evidence, and regressions reach review or production unnoticed.

When the only feedback is a slow CI run the agent cannot trigger, the verification surface is effectively absent during the work. The agent finishes, claims done, and the first real signal arrives long after the session.

**Fix:** Make lint, typecheck, and a relevant test slice runnable in seconds from one command. Verification that fits inside the inner loop turns guesswork into evidence. This is the highest-frequency readiness gap and one of the highest-leverage to close.

## Tool Poverty

**Definition:** The agent knows what to do but has no affordance to do it cleanly—no skill for the repeated task, no integration to the upstream system.

**Symptom:** A multi-step procedure is described in prose every session, and the agent reconstructs it inconsistently.

Tool poverty is the gap between knowledge and action. The agent can describe the migration-then-validate sequence perfectly and still execute it differently each time, because the workflow lives in instructions rather than in a tool.

**Fix:** Encode the recurring workflow as a skill, a command, or an integration. The meta-principle holds: make the right thing the easy thing by putting the workflow in a tool affordance, not in instructions that are forgotten.

## Spec Drift

**Definition:** The agent solves the wrong problem because the specification was vague. The implementation is internally coherent but does not match the intent.

**Symptom:** A technically clean change that misses the actual requirement, often discovered only at review.

Spec drift sits upstream of context: the input quality of the ticket bounds the output quality of the work. Much of what gets reported as "the agent isn't reliable" is more accurately "the ticket wasn't specific." The difference between a good and a bad implementation is largely decided before the first tool call.

**Fix:** Raise the input quality. Use issue and spec templates, decompose vague work into well-scoped units, and state acceptance criteria precisely enough to check. Decomposition is itself a high-leverage surface (§1, tier 5).

## Lossy Handoff

**Definition:** Knowledge gained in one session does not survive to the next. Each session starts from the same blank state.

**Symptom:** A constraint discovered and solved once is rediscovered—or violated—in a later session.

Without a memory surface, expertise does not accumulate. The cost is highest in legacy systems, where the discoveries are expensive and the knowledge was never written down to begin with.

**Fix:** Capture durable learnings—architecture decision records, decision logs, recorded conventions—that the next session loads. Memory is the highest tier in the leverage hierarchy precisely because it converts one-time discovery into standing capability.

---

## Anti-Pattern: Blaming the Model

**What it looks like:** Every failure is attributed to the model, and every fix is a model upgrade or a prompt rewrite.

**Why it fails:** Five of the six failure modes above have nothing to do with model capability. They are environment gaps. A model upgrade does not add the missing logs (starvation), prune the stale docs (poisoning), build the absent test (verification gap), encode the missing skill (tool poverty), or write the spec (drift). Attributing environment failures to the model points effort at the one surface the practitioner can least change.

**Better approach:** Run the failure through the diagnostic table first. Identify the root surface, then apply the surface-appropriate fix. Reach for the model only when the task genuinely exceeds model capability.

---

## Open Questions

- Can these failure modes be detected automatically? A verification gap is observable from CI configuration; context poisoning is harder to instrument without inspecting what was loaded.
- How do the modes compound? A lossy handoff plus spec drift may present identically to a model-capability failure, and disentangling stacked failures is not well understood.

---

## Connections

- **[The Four Surfaces](1-the-four-surfaces.md):** Every failure mode here maps to one of the four surfaces defined in §1; the surfaces are the diagnostic axes this vocabulary sorts against.
- **[Debugging Agents](../8-practices/1-debugging-agents.md):** The practices chapter develops a diagnostic decision tree for agent failure; this vocabulary feeds that triage by naming the failure before the tree narrows the cause.
- **[Context Strategies](../4-context/2-context-strategies.md):** Starvation and poisoning are both context-curation failures; the strategies chapter covers how to load the right context without the noise.
- **[Evaluation](../8-practices/2-evaluation.md):** Detecting spec drift and verification gaps systematically depends on evaluating the workflow, not only the code it produces.
