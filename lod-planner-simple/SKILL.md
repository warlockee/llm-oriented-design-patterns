---
name: lod-planner-simple
description: Lightweight feature planner constrained by LOD (LLM-Oriented Design) -- architectural patterns that optimize code for AI comprehension by enforcing small files (<800 LOC), single responsibilities, explicit module specs, and separation of deterministic vs probabilistic logic. Use when planning features for LLM-maintained codebases. Keywords: lod, llm-oriented, architecture plan, lod plan, modular, simple plan.
---

# LOD Feature Planner (Simple)

## What is LOD?

**LLM-Oriented Design (LOD)** is a set of architectural patterns that optimize code for AI comprehension. LLMs have a finite intelligence window -- large files, mixed responsibilities, and implicit interfaces increase hallucination risk and waste context. LOD enforces small files (<800 LOC), single responsibilities, explicit calling specs on every module, and clean separation of deterministic (sealed, tested) vs probabilistic (flexible, LLM-domain) logic.

## Purpose

Generate phase-based feature plans where LOD is enforced as hard architectural constraints. Phase 0 decomposes architecture, Phase 1-N implements with TDD Red-Green-Refactor.

**Use this skill** when the codebase is primarily maintained by LLMs, or when the feature involves 3+ files or modifies modules over 300 LOC.

---

## LOD Hard Rules (ALWAYS ENFORCED)

These 7 rules are non-negotiable in every plan.

1. **Max 800 LOC per file.** Split if exceeded.
2. **One file, one responsibility.** Never mix schema with logic, or algorithms with I/O.
3. **Pure functions over methods.** If logic doesn't need `self`/`this`, make it standalone.
4. **Flat over deep.** No inheritance >1 level. No factory-of-factory. Use dict dispatch.
5. **Explicit over implicit.** Every module has a calling spec (inputs, outputs, side effects).
6. **Deterministic logic is sealed.** Math, I/O, validation = tested tools, never AI-regenerated.
7. **Probabilistic logic is flexible.** Routing, selection, variant choice = LLM's domain.

---

## LOD Design Patterns (SITUATIONAL)

Apply only when triggered. Hard Rules set the minimum bar; patterns add structured methodology.

| # | Pattern | Apply when... | Skip when... |
|---|---------|--------------|-------------|
| 1 | **Radical Fragmentation** | Files would exceed 200 LOC without splitting | Single utility <100 LOC |
| 2 | **Calling Specs as Black Boxes** | 2+ new modules with cross-module deps | Modifying internals of one module |
| 3 | **Variant Registry** | 2+ implementations of same interface, or if-else on type | Only one implementation |
| 4 | **Toolification** | Deterministic logic shared across 2+ components | Logic unique to one stateful component |
| 5 | **Orchestrator Recipes** | Top-level entry coordinating 3+ phases (<200 LOC) | Leaf-level utility, no coordination |
| 6 | **Schema/Logic Separation** | Data structures AND behavior in same scope | Only schemas or only behavior |
| 7 | **Zero-Hallucination Contracts** | Config with numeric/enum/bounded params | No user-configurable parameters |
| 8 | **Dict Dispatch** | Routing to implementations by string/enum key | Single code path, no branching |
| 9 | **Feedback Loops** | Iterative processing producing metrics | One-shot operation |

**Rule/Pattern overlap**: Pattern skipped != Rule skipped. Rule 5 always requires calling specs; Pattern 2 adds recursive black-box hierarchy design. Rule 4 always bans factories; Pattern 8 provides the dict alternative when routing exists.

---

## Planning Workflow

### Step 1: Requirements Analysis
Read relevant files. Identify dependencies, complexity, scope (small: 2-3 phases / medium: 4-5 / large: 6-7).

### Step 2: LOD Architecture Decomposition (Phase 0)
1. Produce target file tree with LOC estimates (all <= 800 LOC)
2. Define module boundaries and calling specs
3. Map responsibilities to files (one file, one responsibility)
4. Run each pattern through detection guide — list applicable ones with justification
5. Verify all 7 Hard Rules are satisfied

### Step 3: TDD Phase Breakdown (Phase 1-N)
Break into 3-7 phases. Each phase:
- Writes tests BEFORE code (Red-Green-Refactor)
- References Phase 0 file tree (every task maps to a Phase 0 file)
- Has LOD conformance check in quality gate
- Delivers working, testable functionality

### Step 4: User Approval
**CRITICAL**: Use AskUserQuestion before creating the plan document.
- "Does this LOD architecture decomposition make sense?"
- "Should I proceed with creating the plan?"

### Step 5: Document Generation
Create plan at `docs/plans/PLAN_<feature-name>.md` using the template below.

---

## Plan Template

When generating a plan, use this structure:

```markdown
# Implementation Plan: [Feature Name]

**Status**: Planning | In Progress | Complete
**Started**: YYYY-MM-DD
**Last Updated**: YYYY-MM-DD

---

**CRITICAL**: After each phase: check off tasks, run quality gate, update date, document learnings, then proceed.

## Overview
### Feature Description
[What and why]

### Success Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Architecture Decisions
| Decision | Rationale | LOD Pattern | Trade-offs |
|----------|-----------|-------------|------------|

## Phase 0: LOD Architecture Decomposition
**Goal**: Define target architecture satisfying all LOD Hard Rules.

### Target File Tree
[Directory structure with LOC estimates, one responsibility per file, all <= 800 LOC]

### Module Calling Specs
[For each module: inputs, outputs, side effects, deterministic yes/no]

### Responsibility Mapping
| File | Responsibility | Logic Type | LOC Est |
|------|---------------|------------|---------|

### Applicable LOD Patterns
| Pattern | Applies? | Justification | Affected Files |
|---------|----------|---------------|----------------|

### Phase 0 Quality Gate
- [ ] Every file <= 800 LOC estimate
- [ ] Each file has exactly one responsibility
- [ ] Pure functions preferred where no instance state needed
- [ ] No inheritance >1 level; no factory-of-factory; dict dispatch for routing
- [ ] Calling specs defined for every module boundary
- [ ] Deterministic logic identified and marked as sealed
- [ ] Probabilistic logic identified and marked as flexible
- [ ] Target file tree covers all feature requirements
- [ ] Module boundaries clear (no circular dependencies)

## Phase 1: [Foundation]
**Goal**: [Deliverable]
**Status**: Pending

### Tasks
**RED**: Write failing tests
- [ ] Test 1.1: [test description] — File: [from Phase 0 tree]

**GREEN**: Make tests pass
- [ ] Task 1.2: [implementation] — File: [from Phase 0 tree]

**REFACTOR**: Clean up
- [ ] Task 1.3: Improve design, add calling specs, remove duplication

### Quality Gate
**TDD**: Tests first, Red-Green-Refactor followed, coverage >= 80%, all pass
- [ ] Red phase: tests written first and initially failed
- [ ] Green phase: minimal code to pass tests
- [ ] Refactor phase: improved while tests stay green

**LOD Conformance**:
- [ ] Phase output conforms to Phase 0 architecture (files match tree, no file >800 LOC)

**Build & Quality**:
- [ ] Builds without errors, linting passes, type checking passes
- [ ] No security vulnerabilities, no performance regressions

## Phase 2: [Core Feature]
[Same structure as Phase 1]

## Phase 3: [Enhancement]
[Same structure as Phase 1]

## Risk Assessment
| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|

## Rollback Strategy
[Per phase: what to undo, referencing Phase 0 file tree]

## Progress Tracking
| Phase | Estimated | Actual | Variance |
|-------|-----------|--------|----------|

## Consolidated LOD + TDD Compliance Checklist
**Run after ALL phases complete. Every item must pass.**

**LOD Hard Rules (all 7 mandatory)**:
- [ ] No file exceeds 800 LOC
- [ ] Each file has exactly one responsibility
- [ ] Pure functions used where no instance state needed
- [ ] No inheritance >1 level; no factory-of-factory patterns
- [ ] Every module has calling spec (inputs, outputs, side effects)
- [ ] Deterministic logic is sealed (tested, not AI-regenerated)
- [ ] Probabilistic logic is flexible (LLM's domain)

**Applicable LOD Patterns** (only those from Phase 0):
- [ ] [Pattern]: [specific verification]

**TDD Compliance**:
- [ ] Tests before code in every phase
- [ ] Red-Green-Refactor followed
- [ ] Coverage >= 80% business logic

**Code Quality**:
- [ ] Linting, type checking, formatting pass
- [ ] No security vulnerabilities or performance regressions

## Notes & Learnings
[Implementation insights, blockers, LOD observations]
```
