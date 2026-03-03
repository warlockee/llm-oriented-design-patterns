# LLM-Oriented Design (LOD) Patterns

### No one read your code.

---

> **The first reader of your code is no longer human. It's an LLM.**
>
> OOP, SOLID, and deep inheritance were designed for human cognition. In the era of AI-first development, they are *context poison* — bloated modules that exceed attention windows, invisible indirection that obscures call graphs, and coupled concerns that force models to load thousands of irrelevant tokens.
>
> This project is a design manifesto for the post-human-readability age. It rests on **three principles**.

---

## TLDR

Copy [`LOD-for-LLM.md`](LOD-for-LLM.md) into your project's `CLAUDE.md`, `.cursorrules`, or system prompt. The three principles take effect immediately.

**For code review**: Use the [Summary Checklist](LOD-for-LLM.md#summary-checklist) to audit any codebase against the three principles.


## The Problem

Even with 1M-token context windows, the **"Lost in the Middle"** effect persists. Long, human-friendly code dilutes model attention, leading to degraded reasoning and increased hallucinations.

e.g., A 665-line god class forces the LLM to consume **~2,700 tokens** just to modify a single method. A monolithic 595-line entry point mixes 8 concerns into one unnavigable file. Factory-of-factory patterns create indirection chains that no model can reliably trace.

**Your codebase isn't just hard for AI to read. It's actively making AI dumber.**

---

## Three Principles

```
┌───────────────────────────────────────────────────────────────────────┐
│                    LLM-Oriented Design Patterns                       │
│                                                                       │
│   ┌─────────────────┐  ┌─────────────────┐  ┌──────────────────┐      │
│   │                 │  │                 │  │                  │      │
│   │    CONTEXT      │  │    FEEDBACK     │  │                  │      │
│   │   MANAGEMENT    │  │      LOOP       │  │     TOOLING      │      │
│   │                 │  │                 │  │                  │      │
│   │  Compress what  │  │  Detect, adapt, │  │  Seal what must  │      │
│   │  AI must read   │  │  and self-heal  │  │  be deterministic│      │
│   │                 │  │                 │  │                  │      │
│   └─────────────────┘  └─────────────────┘  └──────────────────┘      │
│                                                                       │
└───────────────────────────────────────────────────────────────────────┘
```

---

### Principle I. Context Management

> *Design must dance within AI's attention and compute limits. Context compression is the supreme priority.*

Every module is a **micro-gear** — a single-responsibility unit that fits within the model's optimal intelligence window (**4k-8k tokens**, ~200-800 LOC). Monolithic classes are shattered. Calling specs replace source reading. Variants live in separate files dispatched by plain dicts.

| Chapter | Core Idea |
|---|---|
| **Radical Fragmentation** | Shatter god classes into single-responsibility micro-gears |
| **Dynamic Assembly & In-Context Emergence** | Flat dispatch (dicts, `importlib`) over factory/strategy patterns |
| **Black-Boxing & Semantic Deflation** | 30-line calling specs replace 500+ LOC source reading |

**The result:** to understand an entire pipeline, an LLM reads **~65 tokens** of calling specs instead of **~10,500 tokens** of source — a **40x** reduction. A 665-LOC god class becomes a 250-LOC orchestrator + focused micro-gears.

---

### Principle II. Feedback Loop

> *Static code topology is dead. AI-native systems evolve through layered feedback like digital organisms.*

Systems that merely log metrics are blind. LLM-oriented systems **detect**, **diagnose**, and **adapt**. Every pipeline phase returns structured results with measurable outcomes. Three tiers of feedback form a self-aware organism:

```
MICRO  (per step)       NaN loss, gradient explosion, metric collapse     →  halt or warn
MACRO  (per iteration)  Stagnation, divergence trends, output collapse    →  suggest config changes
SYSTEM (across runs)    OOM, timeout, crash                               →  auto-retry with adjusted params
```

| Chapter | Core Idea |
|---|---|
| **Top-Down Goal Decomposition** | Entry points decompose into measurable, testable sub-goals |
| **Evolutionary Gear Orchestration** | Graduated response: adjust params > swap components > enable features > retire |
| **Multi-Tier Feedback Network** | Micro + macro + system loops create self-healing pipelines |

**The result:** problems are caught **during** execution, not after a run fails. The system diagnoses its own failures and adapts without human intervention.

---

### Principle III. Tooling

> *In a probabilistic world of generative models, deterministic walls must exist. Large software systems cannot roll dice on infrastructure.*

LLMs decide **what** to do (routing, parameters, strategy). Humans seal **how** it's done (math, I/O, validation). Every piece of deterministic logic is extracted into a pure, tested, standalone tool with an explicit contract. Pydantic validators with hard bounds strip the LLM of improvisational freedom on infrastructure.

| Component | Nature | Owner |
|---|---|---|
| "Choose variant B for this workload" | Routing, intent | LLM (probabilistic) |
| The variant B formula itself | Math | Human-written, sealed |
| Config validation (`extra='forbid'`) | Schema enforcement | Pydantic (deterministic) |
| File I/O, weight merging, tensor ops | Infrastructure | Standalone tools |

| Chapter | Core Idea |
|---|---|
| **Probabilistic vs Deterministic** | Draw the line. Seal the deterministic side. |
| **Infrastructure as LLM Tools** | Extract file I/O, distributed ops, tensor math into standalone tools |
| **Zero-Hallucination Contracts** | Pydantic validators with explicit bounds prevent invalid configs |

**The result:** duplicated utility methods become single reusable tools. Weight merging, checkpointing, normalization — written once, tested once, used everywhere.

---

## Measured Impact

Validated against [oxRL](https://github.com/warlockee/oxRL), a post-training framework with 18 algorithms. Before/after metrics:

| Task | Before | After | Reduction |
|---|---|---|---|
| Modify one algorithm variant | ~2,700 tokens | ~140 tokens | **19x** |
| Understand entire pipeline | ~10,500 tokens | ~260 tokens | **40x** |
| Debug state persistence | ~2,700 tokens | ~280 tokens | **9.6x** |
| Modify normalization logic | ~2,300 tokens | ~160 tokens | **14x** |
| Modify processing pipeline | ~2,400 tokens | ~480 tokens | **5x** |

| Metric | Before | After |
|---|---|---|
| Largest class (GRPO) | 665 LOC, 7 concerns | 250 LOC, orchestration only |
| Main entry point | 595 LOC, 8 concerns | 120 LOC, pure orchestration |
| Weight merging | Duplicated in 2 classes | Single tool, reused everywhere |
| Health monitoring | None | 3-tier feedback (micro + macro + system) |
| Module specs | None (read source) | Calling specs on every module |

---

## What's Inside

### [`LOD-for-human.md`](LOD-for-human.md) &mdash; The Full Treatise

A 9-chapter book for engineers and architects. Three chapters per principle, complete with philosophical foundation and before/after case studies.

- **Part I: Context Management** &mdash; From Monoliths to Micro-Gears
- **Part II: Feedback Loop** &mdash; Self-Healing Architecture
- **Part III: Tooling** &mdash; Strip Probability, Anchor Deterministic Boundaries

### [`LOD-for-LLM.md`](LOD-for-LLM.md) &mdash; The Concise Reference

A compressed, actionable guide designed to be dropped directly into an LLM's context. 7 hard rules, 9 patterns, a summary checklist. **Drop this file into your AI coding assistant's system prompt** and it will write LLM-friendly code by default.

---

## The Developer's New Role

Engineers mastering these three principles are no longer if-else laborers. We become:

| Principle | Role |
|---|---|
| **Context Management** | Providers of atomic gears &mdash; write small, single-purpose modules that fit in an LLM's attention window |
| **Feedback Loop** | Designers of goal-feedback systems &mdash; build attempt > evaluate > diagnose > fix > retry loops |
| **Tooling** | Architects of deterministic boundaries &mdash; define what is sealed (tools) vs. what is flexible (routing) |

> Use human wisdom to design **rules**, **feedback loops**, and **tool boundaries**.
> Let AI compute power **assemble**, **refactor**, and **evolve**.

---

## Contributing

This is a living document. If you've discovered patterns that improve LLM comprehension, or have before/after metrics from applying these principles to your own codebase, contributions are welcome.

## License

This work is shared for the benefit of the community. See [LICENSE](LICENSE) for details.

---

<p align="center"><i>The first principle of software design must shift — from Human-Friendly to LLM-Friendly.</i></p>
