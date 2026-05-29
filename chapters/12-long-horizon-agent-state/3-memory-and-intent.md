---
title: Memory and Intent
description: Typed expertise the next agent inherits and explicit work decomposition as reusable intent-shapes
created: 2026-05-29
last_updated: 2026-05-29
tags: [agent-state, memory, intent, expertise, decomposition]
part: 3
part_title: Perspectives
chapter: 12
section: 3
order: 3.12.3
---

# Memory and Intent

Layers 2 and 3 of the five-layer model—memory and intent—are the two layers that most directly distinguish a stateful collaborator from a stateless tool. Memory is what an agent knows about a domain before it starts; intent is what the work is and how it decomposes. Both must live outside any single context window, and both are routinely mishandled by treating the context window as if it were the storage.

This section examines each in depth: memory as typed, accumulated expertise that the next agent inherits, and intent as explicit decomposition that can be templated and reused.

---

## Memory as Typed Expertise

Memory in this model is not a transcript and not a vector dump of everything that ever happened. It is a **typed store of expertise**: records classified by what kind of knowledge they hold, keyed by the domain they apply to, and linked to the evidence that justifies them.

A useful record taxonomy distinguishes at least five types:

- **Conventions** — how this codebase does a thing (naming, structure, required flags)
- **Patterns** — a reusable approach that has worked here before
- **Failures** — something that was tried and did not work, with the reason
- **Decisions** — a choice that was made and the rationale behind it
- **References** — pointers to authoritative files, commits, or external sources

Each record is scoped to a domain and surfaced on relevance at retrieval, so an agent priming for work on the build system does not receive records about the API layer. A git-native memory tool—where an agent runs a prime step at session start and a record step before finishing—is one concrete shape this takes.

### Decay by Tier

Knowledge ages at different rates, so records carry a classification tier that governs how long they stay authoritative:

| Tier          | Example                                                       | Decay     |
| ------------- | ------------------------------------------------------------- | --------- |
| Foundational  | "This service is the system of record for billing"            | Permanent |
| Tactical      | "The current migration path routes through the v2 adapter"    | Months    |
| Observational | "Build times spiked after the dependency bump on this branch" | Weeks     |

Graceful decay is what keeps the store from poisoning future runs. A tactical note that was true last quarter and false now is worse than no note at all, because an agent will act on it confidently. Tiered decay lets foundational truth persist while letting transient observations expire on their own.

### Memory vs. Context Stuffing

The tempting alternative to a memory layer is to stuff everything into the context window—load the whole history, every prior decision, all the docs, and let the model sort it out. This is the **anti-pattern** the memory layer exists to replace.

**Why context stuffing fails.** The model weights every token, so irrelevant history competes with relevant knowledge for attention. The window has a hard ceiling, so "everything" stops fitting the moment a project is non-trivial. And nothing decays, so stale facts persist with the same authority as current ones.

**Why typed memory works.** Retrieval is scoped to the domain and the task, so the agent sees a small, relevant set of records rather than the entire past. The store lives outside the window, so its size is not bounded by the context budget. And tiered decay removes stale knowledge automatically. The next agent inherits curated expertise, not a transcript it must re-read.

---

## Intent as Explicit Decomposition

Intent is the structured representation of _what the work is_. The weakest form of intent is a single prose sentence in a prompt; the strongest is an explicit decomposition that separates the goal from its boundaries and its finish line. A robust intent record carries four fields:

- **goal** — what success looks like, stated as an outcome
- **non-goals** — what is explicitly out of scope, to prevent drift
- **constraints** — the boundaries the work must respect
- **success_criteria** — the conditions that determine the work is done

The contrast with an implicit prompt is sharp:

```text
Fragile (implicit intent):
  "Add caching to the API."

Robust (explicit intent):
  goal:             Reduce p95 latency on the read path via a cache layer
  non-goals:        Do not change the write path or the public schema
  constraints:      No new infrastructure dependencies; cache must be
                    invalidatable within one request
  success_criteria: p95 read latency drops below 100ms; existing tests pass;
                    cache hit-rate observable in logs
```

The explicit form constrains the agent toward the work that was actually wanted. The non-goals prevent scope creep, the constraints rule out a technically-correct-but-unwanted solution, and the success criteria give both the agent and the reviewer an unambiguous finish line.

### Intent-Shapes: Reusable, Outcome-Weighted Templates

When the same _kind_ of work recurs—add an endpoint, fix a flaky test, migrate a dependency—the decomposition recurs with it. An **intent-shape** is a reusable template for a class of work: the goal pattern, the typical non-goals and constraints, and the success criteria that tend to apply, weighted by the outcomes of past instances.

An intent-shape makes the decomposition cheaper and more consistent each time the class recurs, and it carries forward what was learned about how that class tends to succeed or fail. If migrations of a certain kind have historically failed without a rollback criterion, the shape can encode that criterion as a default. The durable, learnable unit becomes the shape, with each concrete piece of work as an instance of it. (Whether the shape or the individual work item is the more fundamental unit is an open question—see the next sections.)

---

## How Memory and Intent Reinforce Each Other

Memory and intent are complementary halves of "what the agent brings to the work." Intent says what to do; memory says what is known about doing it here. An agent priming for a task reads the intent to scope the goal and the memory to inherit the expertise, and it writes back to both before finishing—new learnings to memory, progress and decisions to the intent record.

The pairing is what lets work compound. Without memory, every instance of an intent-shape re-learns the same lessons. Without explicit intent, accumulated memory has no structured goal to attach itself to. Together they turn a sequence of isolated runs into a program of work that gets cheaper and more reliable as it repeats.

---

## Connections

- **[The Five-Layer Model](1-five-layer-model.md):** This section is the deep treatment of layers 2 and 3 introduced there.
- **[Coordination and Orchestration](4-coordination-and-orchestration.md):** Intent records are owned by humans through the coordination layer's write-ACL; the next section covers that boundary.
- **[Context as Code](../9-mental-models/4-context-as-code.md):** Typed, decaying memory is this mental model in practice—knowledge engineered, versioned, and pruned like software rather than accumulated like documents.
- **[Specs as Source Code](../9-mental-models/3-specs-as-source-code.md):** Explicit intent (goal, non-goals, constraints, success_criteria) is a specification treated as the primary artifact; intent-shapes are reusable spec templates.
- **[Context Management Strategies](../4-context/2-context-strategies.md):** The memory-versus-context-stuffing contrast extends the same signal-versus-noise argument from in-window context to across-run state.
