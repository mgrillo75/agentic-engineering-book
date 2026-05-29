---
title: The Composition Loop
description: How the five layers close into a feedback loop and the design principles that make durable agent state mergeable
created: 2026-05-29
last_updated: 2026-05-29
tags: [agent-state, feedback-loop, git-native, design-principles, checkpoints]
part: 3
part_title: Perspectives
chapter: 12
section: 2
order: 3.12.2
---

# The Composition Loop

The five layers earn their value only when they compose. Treated as independent features, each is a partial answer: memory with no intent to apply it is trivia, intent with no coordination is fire-and-forget, coordination with no orchestration still needs a human to sequence every step. Treated as a system, they close into a feedback loop where each run leaves the next one better equipped.

This section traces that loop end to end, then names four design principles that determine whether the loop is robust or brittle. The principles are taught generically—they apply to any system assembling durable agent state, regardless of the specific tools used to implement each layer.

---

## The Loop

A single program of work moves through all five layers and returns to its starting point with state accumulated. The cycle looks like this:

```text
  Human writes intent                          (Layer 4: Coordination)
        │                goal / non-goals / constraints / success_criteria
        ▼
  Intent decomposes into a plan                (Layer 3: Intent)
        │                ordered steps, dependencies
        ▼
  Orchestrator dispatches step 1               (Layer 5: Orchestration)
        │
        ▼
  Agent runs in a sandbox, primes from memory  (Layers 1 + 2: Execution + Memory)
        │
        ▼
  Agent hits a blocker, poses a question       (Layer 4: Coordination)
        │                work pauses
        ▼
  Human answers; agent resumes                 (Layers 4 + 5)
        │
        ▼
  Agent records learnings before finishing     (Layer 2: Memory)
        │
        ▼
  Change opens; human reviews and merges       (Layer 5: Orchestration)
        │                merge = checkpoint
        ▼
  Orchestrator dispatches step 2 with          (Layers 5 → 1)
  accumulated context
        │
        ▼
  ...repeat until the plan completes...
        │
        ▼
  Coordination object transitions to done.
  The NEXT agent on this project inherits the memory.
```

The closing line is the point of the whole structure. Work does not end when a change merges; it ends when the next agent inherits what this one learned. The loop is closed because the output of one pass—accumulated memory, a completed plan, a recorded decision trail—is the input to the next.

---

## Design Principle 1: Git-Native, Not Database-Dependent

State that must outlive infrastructure should live where the code lives. Memory, intent, coordination, and prompt definitions can all be stored as files in version control—append-heavy data as line-delimited records, structured objects as JSON. Several git-native tools take exactly this shape: a memory store, an issue tracker, and a prompt manager that each persist to plain files under the repository.

The payoff is durability and mergeability. Agents read and write state with standard tools, the state survives a change of hosting or runtime because it is in the repository, and concurrent edits from multiple agents merge with the same conflict-resolution machinery used for code. A database can serve runtime state, but the durable definitions belong in git so they cannot be stranded when the runtime is rebuilt.

---

## Design Principle 2: A Write-ACL Enforces the Human-Agent Boundary

The coordination layer is governed by an access-control rule: agents may append decisions, artifacts, and questions, but they may not edit intent. Setting goals, changing status, and answering blockers are human-only operations. This is not a limitation bolted on for safety—it is the design.

The boundary prevents the most insidious failure in autonomous systems: an agent silently redefining its own objective. When an agent cannot edit intent, a disagreement with the goal can only surface as a question, which a human must answer. The boundary turns "the agent quietly built the wrong thing" into "the agent paused and asked." That conversion is the entire value of the rule.

---

## Design Principle 3: Merge-Gates as Natural Checkpoints

Sequential agent work needs a checkpoint between steps. Rather than inventing a new approval mechanism—a timer, a webhook, a custom sign-off—the orchestrator reuses the checkpoint the team already has: code review. The coordinator waits for the previous step's change to merge before dispatching the next.

This choice has two advantages. It reuses an existing, well-understood human workflow instead of adding a parallel one, and it makes the human review a structural gate rather than an optional courtesy. The agent literally cannot proceed until a human merges, so review is enforced by the architecture, not by discipline.

---

## Design Principle 4: Fire-and-Log, Not Transactional

Side effects on the higher layers—appending to the coordination log, merging memory, updating an issue—must never roll back core state. If the coordination service is unreachable, the run still succeeds. If a memory merge fails, the change still lands. These side effects _enhance_ the system; they do not _gate_ it.

The principle keeps the durable-state layers from becoming a single point of failure for execution. A system where a flaky memory write can fail a successful code change has inverted its priorities. Fire-and-log preserves the rule that the primary outcome—working, merged work—survives even when the bookkeeping around it stumbles.

---

## Why the Loop Needs All Four Principles

Each principle protects a different failure mode the loop would otherwise be vulnerable to:

| Principle                  | Failure it prevents                                                                     |
| -------------------------- | --------------------------------------------------------------------------------------- |
| Git-native storage         | State stranded when runtime or hosting changes; merge conflicts with no resolution path |
| Write-ACL boundary         | Agents silently redefining their own objectives                                         |
| Merge-gates as checkpoints | Unreviewed work advancing; bespoke approval mechanisms that duplicate code review       |
| Fire-and-log side effects  | Bookkeeping failures rolling back successful core work                                  |

Together they make the difference between a loop that compounds value and a loop that accumulates risk. The loop is what turns five separate layers into a stateful collaborator; the principles are what keep that collaborator trustworthy across a long horizon.

---

## Connections

- **[The Five-Layer Model](1-five-layer-model.md):** This section composes the layers defined there into a single feedback cycle.
- **[Memory and Intent](3-memory-and-intent.md):** The "prime from memory" and "decompose into a plan" steps of the loop are treated in depth in the next section.
- **[Autonomous Loops](../7-patterns/4-autonomous-loops.md):** The composition loop is the durable-state backbone underneath an autonomous loop—what the loop reads and writes between iterations.
- **[Context as Code](../9-mental-models/4-context-as-code.md):** Git-native storage of memory and intent is this mental model applied to state—knowledge versioned and merged like software rather than stored as disposable documents.
