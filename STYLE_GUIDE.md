# Writing Style Guide

This document establishes the writing style for the Agentic Engineering book. All contributors and agents must follow these conventions to ensure consistency.

## Style Profile

**Category:** Technical Practitioner
**Voice:** Third-person only
**Evidence Standard:** Evidence-grounded
**Structure:** Flexible with required elements

---

## Voice Conventions

### Third-Person Only

Write in third-person throughout. Avoid first-person ("I", "we") and second-person ("you").

| Avoid | Use Instead |
|-------|-------------|
| "I've found that context management..." | "Context management tends to..." |
| "You should validate inputs..." | "Input validation prevents..." |
| "We can see that the pattern..." | "The pattern demonstrates..." |
| "When you build agents..." | "When building agents..." |

### Active Voice Preferred

Use active voice for clarity. Passive voice is acceptable when the actor is unknown or irrelevant.

| Passive (Avoid) | Active (Preferred) |
|-----------------|-------------------|
| "Errors are handled by the system" | "The system handles errors" |
| "The prompt is interpreted by the model" | "The model interprets the prompt" |

### Imperative for Instructions

When giving direct guidance, use imperative mood without "you":

| Avoid | Use Instead |
|-------|-------------|
| "You should structure prompts clearly" | "Structure prompts clearly" |
| "You need to validate tool outputs" | "Validate tool outputs" |

---

## Evidence Standards

### Evidence-Grounded Claims

Every significant claim must be grounded in one of:

1. **Observable behavior** - "Models exhibit recency bias, weighting tokens near the end of context more heavily."
2. **Documented patterns** - "The ACE paper demonstrated progressive degradation in dynamic cheatsheet approaches."
3. **Reproducible examples** - Include code or configuration that demonstrates the claim.
4. **Cited sources** - Reference papers, documentation, or prior work when available.

### Citation Policy: Verify or Cut

Every external citation (paper ID, CVE, benchmark, vendor announcement, named system) must be independently verifiable before it ships. If a citation cannot be checked against a real, locatable source, it is removed. When the underlying claim is real but its citation fails, prefer one of: re-cite a verifiable source, or reframe it as a third-person observation of behavior (dated if experiential, per Experiential Insights). Speculative or projected scenarios are never presented with citation formatting; if kept at all, they are explicitly labeled as projection in the running prose.

### Unsupported Assertions

Avoid claims that cannot be verified:

| Unsupported | Evidence-Grounded |
|-------------|-------------------|
| "This approach is better" | "This approach reduces token usage by ~40% in tested scenarios" |
| "Always use X" | "X prevents common failure modes: [list specific failures]" |
| "The best practice is..." | "A effective pattern observed across implementations..." |

### Experiential Insights and Provenance

Experiential observations are written as third-person observations of behavior, framed as a recurring force rather than as a timestamped log line. Inline dated annotations (e.g. `*[2025-12-10]*:` mid-paragraph) are deprecated for book prose: they read as lab-notebook entries in a printed edition. Triage existing annotations into two buckets:

- **Provenance** ("learned on this date", source-dating) → move to an endnote.
- **Evolution narrative** ("until mid-2025, model sat at the top...") → promote into the running prose as connective tissue for the lineage (see Durability and Time-Robustness).

Genuinely time-bound facts go in a snapshot container, not an inline annotation.

---

## Durability and Time-Robustness

This is a book about a field that churns weekly. Content must be written to survive that churn. The governing test: **a reader should be able to mentally delete every proper noun, version number, and date, and the core argument must still stand.** If deleting "Opus 4.6" breaks a paragraph, that paragraph was built on a snapshot, not a principle.

### Classify Every Claim

Every claim falls into one of three tiers. The tier dictates how it is written and where it lives.

| Tier          | What it is                                                                                       | How to write it                                                                                |
| ------------- | ------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------- |
| **Law**       | Rooted in information theory, economics, control theory, or human factors. Outlasts any model.   | Clean prose. Load-bearing. No expiry, no version numbers.                                       |
| **Heuristic** | A current rule of thumb derived from a law, whose specific value drifts (e.g. "keep 3-5 tools"). | State the rule _and the force beneath it_, so when the value shifts the reader understands why. |
| **Snapshot**  | Specific models, tools, prices, benchmarks, dates, vendor names.                                 | Quarantine in a dated snapshot container (below). Never load-bearing for an argument.           |

Examples:

- **Law:** "Context is finite, and noise competes with signal for attention."
- **Heuristic:** "Keep context utilization well below saturation" (with the attention-dilution force stated).
- **Snapshot:** "As of 2026-04, the accessible frontier ceiling is Opus 4.6."

### Snapshot Containers

Time-bound facts go in a clearly dated, visually distinct container so a future edition can refresh or remove them without touching the argument:

```markdown
> **Snapshot (2026-04):** The accessible frontier ceiling is Opus 4.6; Gemini 3 Pro and GPT-5.2 have converged within the same tier.
```

Snapshots are swappable by design. The surrounding prose must remain valid if the snapshot is deleted.

### The Lineage Framing Convention

Dated _concepts_ (ReAct, chain-of-thought, "RAG everything") are kept as historical record, never as current recommendations. Present each as a position on a trajectory, using three beats:

1. **The force it answered** - the problem or constraint that made it emerge. (Settled history; never rots.)
2. **What it got right, and where it broke** - why it was superseded. (Settled history; never rots.)
3. **What it became** - the impulse traced forward to where it lives in current practice. (The only beat that needs refreshing per edition.)

This is the mechanism that ties today's practice to the practice of a year-plus ago. The historical record becomes the connective tissue of the narrative, not dead weight. Beats 1 and 2 are permanent; only beat 3 is dated.

### Anti-Staleness Rules

- **No bare temporal superlatives in durable prose.** Forbidden in the backbone: "cutting-edge", "state-of-the-art", "the latest", "currently the best", "now the leading". A present claim must sit inside a snapshot container or be expressed as a position on the lineage.
- **No version number is load-bearing.** Model and tool versions appear only in snapshots or lineage beats, never as the premise of an argument.
- **Prefer the force over the figure.** When a number is needed, give the underlying reason so the claim degrades gracefully as the number moves.
- **The graveyard is the safest content.** Resolved dead-ends (why an approach failed) are permanent data points. Lean on them.

---

## Required Structural Elements

Every chapter/section entry must include:

### 1. Opening Statement
A clear, grounded statement establishing relevance. First 1-2 sentences should answer "what is this and why does it matter?"

```markdown
## Context Management

Context determines what an agent knows at any given moment. Poor context management
leads to capability degradation, hallucination, and task failure.
```

### 2. Concrete Examples
Show before explaining. Examples should be:
- Specific (not "some code" but actual code)
- Contrasted ("Fragile" vs "Robust", "Before" vs "After")
- Minimal (show only what's necessary)

```markdown
### Fragile
```
Handle errors appropriately
```

### Robust
```
When an error occurs:
1. Log the error message and stack trace
2. Return a user-friendly message (never expose internals)
3. Continue execution if possible, exit gracefully if not
```
```

### 3. Connections Section
Link to related chapters/sections with explanation of the relationship:

```markdown
## Connections

- **[Context](../4-context/_index.md)**: Tool outputs become context for subsequent reasoning.
  Large tool outputs can consume context budget rapidly.
- **[Patterns](../6-patterns/_index.md)**: The Plan-Build-Review pattern structures tool use
  across phases.
```

---

## Optional Structural Elements

Include when relevant to the content:

### When to Use / When Not to Use
Prevents cargo-cult adoption:

```markdown
### When to Use
- Multi-step tasks requiring state persistence
- Workflows with clear phase boundaries

### When Not to Use
- Simple single-turn interactions
- Tasks where context freshness is critical
```

### Anti-Patterns
Name what fails and explain why:

```markdown
### Anti-Pattern: Context Dumping

Loading all available information into context regardless of relevance.

**Why it fails:** Dilutes signal with noise. Models weight all tokens, so
irrelevant information competes with relevant information for attention.

**Better approach:** Progressive disclosure—load minimal context initially,
expand based on task needs.
```

### Open Questions
Acknowledge uncertainty rather than false completeness:

```markdown
### Open Questions

- How does context window size interact with reasoning depth?
- What's the optimal ratio of examples to instructions in few-shot prompts?
```

### Tables for Comparisons
Use tables for trade-off analysis and feature comparison:

```markdown
| Approach | Latency | Cost | Complexity |
|----------|---------|------|------------|
| Synchronous | High | Low | Low |
| Async Queue | Medium | Medium | Medium |
| Streaming | Low | High | High |
```

---

## Formatting Conventions

### Headers
- H1 (`#`) - Document title only
- H2 (`##`) - Major sections
- H3 (`###`) - Subsections
- H4 (`####`) - Rarely used, for deep nesting

### Lists
- **Numbered lists** for sequences/steps
- **Bulleted lists** for options/attributes (no implied order)
- **Definition lists** (bold term + description) for glossaries

### Code Blocks
- Always specify language for syntax highlighting
- Keep examples minimal and focused
- Use comments sparingly—code should be self-explanatory

### Emphasis
- **Bold** for key terms on first use
- *Italics* for emphasis within sentences
- `Code` for inline technical terms, file names, commands

---

## Terminology Precision

### Define on First Use
When introducing a term, provide a clear definition:

```markdown
**Context window**—the maximum number of tokens a model can process in a single
invocation. This includes both input (prompt + context) and output (generation).
```

### Consistent Term Usage
Once a term is defined, use it consistently:

| Inconsistent | Consistent |
|--------------|------------|
| "prompt", "instruction", "input" (interchangeably) | "prompt" (for the full input) |
| "agent", "system", "assistant" (interchangeably) | "agent" (for autonomous systems) |

### Avoid Jargon Without Explanation
Technical terms are acceptable; undefined jargon is not:

| Jargon | With Explanation |
|--------|------------------|
| "The RAG pipeline" | "The retrieval-augmented generation (RAG) pipeline" |
| "Use CoT prompting" | "Use chain-of-thought (CoT) prompting—structuring prompts to elicit step-by-step reasoning" |

---

## Sentence Structure

### Short Paragraphs
Maximum 3-4 sentences per paragraph. One idea per paragraph.

### Declarative + Consequence
State a fact, then show why it matters:

```markdown
Transformers use attention to weight input tokens. This means every token in the
context influences output probability—irrelevant tokens add noise to the signal.
```

### Avoid Hedging
Write with appropriate confidence. Avoid weak qualifiers unless uncertainty is genuine:

| Hedged | Direct |
|--------|--------|
| "It might be worth considering..." | "Consider..." |
| "Perhaps the approach could..." | "The approach..." |
| "It seems like this might help" | "This helps when..." |

When genuine uncertainty exists, state it clearly:

```markdown
The relationship between context length and reasoning quality remains unclear.
Some evidence suggests diminishing returns above 8K tokens, but this varies by task type.
```

---

## What This Style Is Not

- **Not academic** - No passive-heavy prose, no "it can be argued that"
- **Not casual** - No colloquialisms, no "basically", no rhetorical questions
- **Not narrative** - No personal anecdotes, no "I remember when"
- **Not exhaustive** - Prioritize clarity over completeness

---

## Quick Checks

Line-level prose hygiene. Run these before delivering any prose:

- **Any adverbs?** Kill them.
- **Any passive voice?** Find the actor, make them the subject.
- **Inanimate thing doing a human verb** ("the decision emerges")? Name the actor.
- **Sentence starts with a Wh- word?** Restructure it.
- **Any "here's what/this/that" throat-clearing?** Cut to the point.
- **Any "not X, it's Y" contrasts?** State Y directly.
- **Three consecutive sentences match length?** Break one.
- **Paragraph ends with a punchy one-liner?** Vary it.
- **Em-dash anywhere?** Remove it.
- **Vague declarative** ("The implications are significant")? Name the specific implication.
- **Narrator-from-a-distance** ("Nobody designed this")? Put the reader in the scene.
- **Meta-joiners** ("The rest of this section...")? Delete. Let the prose move.

---

## Checklist for New Content

Before submitting content, verify:

- [ ] Third-person voice throughout (no "I", "we", "you")
- [ ] Claims are evidence-grounded; external citations are verifiable (verify-or-cut)
- [ ] Every claim classified: law (durable prose), heuristic (rule + force), or snapshot (dated container)
- [ ] No bare temporal superlatives in the backbone ("cutting-edge", "latest", "currently best")
- [ ] No version number is load-bearing for an argument
- [ ] Dated concepts framed as lineage (force it answered → where it broke → what it became)
- [ ] Opening statement establishes relevance
- [ ] Concrete examples included (before/after, fragile/robust)
- [ ] Connections section links to related content
- [ ] Short paragraphs (3-4 sentences max)
- [ ] Technical terms defined on first use
- [ ] Active voice preferred
- [ ] No hedging without genuine uncertainty
- [ ] Quick Checks pass (no adverbs, no em-dashes, no throat-clearing, no vague declaratives)
- [ ] Frontmatter complete (`title`, `description`, `created`, `last_updated`, `tags`, `part`, `chapter`, `section`, `order`)
