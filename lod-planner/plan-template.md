# Implementation Plan: [Feature Name]

**Status**: Planning
**Started**: YYYY-MM-DD
**Last Updated**: YYYY-MM-DD
**Estimated Completion**: YYYY-MM-DD

---

**CRITICAL INSTRUCTIONS**: After completing each phase:
1. Check off completed task checkboxes
2. Run all quality gate validation commands
3. Verify ALL quality gate items pass (including LOD conformance)
4. Update "Last Updated" date above
5. Document learnings in Notes section
6. Only then proceed to next phase

**DO NOT skip quality gates or proceed with failing checks**

---

## Overview

### Feature Description
[What this feature does and why it's needed]

### Success Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

### User Impact
[How this benefits users or improves the product]

---

## Architecture Decisions

| Decision | Rationale | LOD Pattern | Trade-offs |
|----------|-----------|-------------|------------|
| [Decision 1] | [Why this approach] | [Which LOD pattern, if any] | [What we're giving up] |
| [Decision 2] | [Why this approach] | [Which LOD pattern, if any] | [What we're giving up] |

---

## Dependencies

### Required Before Starting
- [ ] Dependency 1: [Description]
- [ ] Dependency 2: [Description]

### External Dependencies
- Package/Library 1: version X.Y.Z
- Package/Library 2: version X.Y.Z

---

## Test Strategy

### Testing Approach
**TDD Principle**: Write tests FIRST, then implement to make them pass

### Test Pyramid for This Feature
| Test Type | Coverage Target | Purpose |
|-----------|-----------------|---------|
| **Unit Tests** | >= 80% | Business logic, models, core algorithms |
| **Integration Tests** | Critical paths | Component interactions, data flow |
| **E2E Tests** | Key user flows | Full system behavior validation |

### Test File Organization
```
test/
  unit/
    [domain/business_logic]/
    [data/models]/
  integration/
    [feature_name]/
  e2e/
    [user_flows]/
```

### Coverage Requirements by Phase
- **Phase 1 (Foundation)**: Unit tests for core models/entities (>= 80%)
- **Phase 2 (Business Logic)**: Logic + repository tests (>= 80%)
- **Phase 3 (Integration)**: Component integration tests (>= 70%)
- **Phase 4 (E2E)**: End-to-end user flow test (1+ critical path)

### Test Naming Convention
Follow your project's testing framework conventions:
```
// Example structure (adapt to your framework):
describe/group: Feature or component name
  test/it: Specific behavior being tested
    // Arrange -> Act -> Assert pattern
```

---

## Phase 0: LOD Architecture Decomposition

**Goal**: Define the target architecture that satisfies all LOD Hard Rules before any code is written.
**Status**: Pending

### 0a. Target File Tree

Proposed directory/file structure. Every file must be <= 800 LOC with exactly one responsibility.

```
src/
  [feature]/
    [module_a].ext        # [responsibility] (~XXX LOC)
    [module_b].ext        # [responsibility] (~XXX LOC)
    [module_c].ext        # [responsibility] (~XXX LOC)
  [feature]/[sub]/
    [module_d].ext        # [responsibility] (~XXX LOC)
test/
  [feature]/
    [module_a]_test.ext   # Tests for module_a
    [module_b]_test.ext   # Tests for module_b
```

### 0b. Module Boundaries & Calling Specs

For each module, define how it is called. The LLM reads the spec (~20-30 tokens) instead of the implementation.

**[module_a]**
```
CALLING SPEC:
    result = module_a_fn(
        input_1: Type,
        input_2: Type,
    ) -> ReturnType

    Side effects: None | [describe]
    Deterministic: Yes | No
```

**[module_b]**
```
CALLING SPEC:
    [define inputs, outputs, side effects]
```

### 0c. Responsibility Mapping

| File | Responsibility | Logic Type | LOC Est |
|------|---------------|------------|---------|
| [module_a].ext | [single responsibility] | Deterministic (sealed) | ~XXX |
| [module_b].ext | [single responsibility] | Probabilistic (flexible) | ~XXX |
| [module_c].ext | [single responsibility] | Deterministic (sealed) | ~XXX |

### 0d. Applicable LOD Patterns

Based on the Pattern Relevance Detection Guide, these patterns apply to this feature:

| Pattern | Applies? | Justification | Affected Files |
|---------|----------|---------------|----------------|
| 1. Radical Fragmentation | Yes/No | [Why or why not] | [files] |
| 2. Calling Specs | Yes/No | [Why or why not] | [files] |
| 3. Variant Registry | Yes/No | [Why or why not] | [files] |
| 4. Toolification | Yes/No | [Why or why not] | [files] |
| 5. Orchestrator Recipes | Yes/No | [Why or why not -- if Yes, orchestrator must be <200 LOC] | [files] |
| 6. Schema Separation | Yes/No | [Why or why not] | [files] |
| 7. Zero-Hallucination Contracts | Yes/No | [Why or why not] | [files] |
| 8. Dict Dispatch | Yes/No | [Why or why not] | [files] |
| 9. Feedback Loops | Yes/No | [Why or why not] | [files] |

### Phase 0 Quality Gate

**DO NOT proceed to Phase 1 until ALL checks pass**

**LOD Hard Rules (Architecture Level -- all 7 must pass)**:
- [ ] Every file in target tree <= 800 LOC estimate
- [ ] Each file has exactly one responsibility (no mixed concerns)
- [ ] Pure functions preferred over methods where logic does not need instance state
- [ ] No inheritance chains > 1 level; no factory-of-factory patterns; dict dispatch used for routing
- [ ] Calling specs defined for every module boundary (inputs, outputs, side effects)
- [ ] Deterministic logic identified and marked as sealed (tested, not AI-regenerated)
- [ ] Probabilistic logic identified and marked as flexible (LLM's domain)

**Architecture Completeness**:
- [ ] Target file tree covers all feature requirements
- [ ] Module boundaries are clear (no circular dependencies)
- [ ] Applicable LOD patterns identified with justification
- [ ] Each file's responsibility can be stated in one sentence

---

## Phase 0 Worked Example

<details>
<summary>Example: User notification service decomposition</summary>

**Target File Tree:**
```
src/notifications/
  schema.py          # Notification data models (~80 LOC)
  sender.py          # Send logic: email, SMS, push dispatch (~120 LOC)
  templates.py       # Template rendering, pure function (~90 LOC)
  registry.py        # Channel registry: dict dispatch (~40 LOC)
  channels/
    email.py         # Email channel implementation (~70 LOC)
    sms.py           # SMS channel implementation (~60 LOC)
    push.py          # Push channel implementation (~65 LOC)
  orchestrator.py    # Coordinate: validate -> render -> send (~100 LOC)
```

**Calling Spec (sender.py):**
```
send_notification(
    notification: Notification,
    channel: str,
    recipient: Recipient,
) -> SendResult

Side effects: External API call (email/SMS/push provider)
Deterministic: No (depends on external service)
```

**Responsibility Mapping:**
| File | Responsibility | Logic Type | LOC Est |
|------|---------------|------------|---------|
| schema.py | Notification + Recipient data models | Deterministic | ~80 |
| sender.py | Dispatch to correct channel | Probabilistic | ~120 |
| templates.py | Render notification body from template | Deterministic | ~90 |
| registry.py | Map channel names to implementations | Deterministic | ~40 |
| email.py | Send via email provider | Deterministic | ~70 |
| orchestrator.py | Validate -> render -> send pipeline | Probabilistic | ~100 |

**Applicable Patterns:**
- Radical Fragmentation: Yes (split monolithic NotificationService into 7 files)
- Variant Registry: Yes (3 channel implementations with identical interface)
- Toolification: Yes (templates.py is a pure function tool)
- Orchestrator Recipes: Yes (orchestrator.py coordinates the pipeline, <200 LOC)
- Schema Separation: Yes (schema.py separate from sender.py logic)
- Dict Dispatch: Yes (registry.py maps channel names to implementations)
</details>

---

## Phase 1: [Foundation Phase Name]

**Goal**: [Specific working functionality this phase delivers]
**Estimated Time**: X hours
**Status**: Pending | In Progress | Complete

### Tasks

**RED: Write Failing Tests First**
- [ ] **Test 1.1**: Write unit tests for [specific functionality]
  - File(s): `test/unit/[feature]/[component]_test.ext` (from Phase 0 tree)
  - Expected: Tests FAIL (red) because feature doesn't exist yet
  - Details: Test cases covering:
    - Happy path scenarios
    - Edge cases
    - Error conditions

- [ ] **Test 1.2**: Write integration tests for [component interaction]
  - File(s): `test/integration/[feature]_test.ext` (from Phase 0 tree)
  - Expected: Tests FAIL (red) because integration doesn't exist yet
  - Details: Test interaction between [list components]

**GREEN: Implement to Make Tests Pass**
- [ ] **Task 1.3**: Implement [component/module]
  - File(s): `src/[feature]/[component].ext` (from Phase 0 tree)
  - Goal: Make Test 1.1 pass with minimal code
  - Details: [Implementation notes]

- [ ] **Task 1.4**: Implement [integration/glue code]
  - File(s): `src/[feature]/[integration].ext` (from Phase 0 tree)
  - Goal: Make Test 1.2 pass
  - Details: [Implementation notes]

**REFACTOR: Clean Up Code**
- [ ] **Task 1.5**: Refactor for code quality
  - Files: Review all new code in this phase
  - Goal: Improve design without breaking tests
  - Checklist:
    - [ ] Remove duplication (DRY principle)
    - [ ] Improve naming clarity
    - [ ] Extract reusable components
    - [ ] Add calling specs to module headers (LOD Rule 5)
    - [ ] Add inline documentation where logic isn't self-evident
    - [ ] Optimize performance if needed

### Phase 1 Quality Gate

**DO NOT proceed to Phase 2 until ALL checks pass**

**TDD Compliance** (CRITICAL):
- [ ] **Red Phase**: Tests were written FIRST and initially failed
- [ ] **Green Phase**: Production code written to make tests pass
- [ ] **Refactor Phase**: Code improved while tests still pass
- [ ] **Coverage Check**: Test coverage meets requirements
  ```bash
  # Example commands (adapt to your testing framework):
  # npm test -- --coverage
  # pytest --cov=src --cov-report=html
  # go test -cover ./...

  [Your project's coverage command here]
  ```

**LOD Conformance**:
- [ ] Phase output conforms to Phase 0 architecture (files match target tree, no file exceeds 800 LOC)

**Build & Tests**:
- [ ] **Build**: Project builds/compiles without errors
- [ ] **All Tests Pass**: 100% of tests passing (no skipped tests)
- [ ] **Test Performance**: Test suite completes in acceptable time
- [ ] **No Flaky Tests**: Tests pass consistently (run 3+ times)

**Code Quality**:
- [ ] **Linting**: No linting errors or warnings
- [ ] **Formatting**: Code formatted per project standards
- [ ] **Type Safety**: Type checker passes (if applicable)
- [ ] **Static Analysis**: No critical issues from static analysis tools

**Security & Performance**:
- [ ] **Dependencies**: No known security vulnerabilities
- [ ] **Performance**: No performance regressions
- [ ] **Memory**: No memory leaks or resource issues
- [ ] **Error Handling**: Proper error handling implemented

**Documentation**:
- [ ] **Code Comments**: Complex logic documented
- [ ] **API Docs**: Public interfaces documented
- [ ] **README**: Usage instructions updated if needed

**Manual Testing**:
- [ ] **Functionality**: Feature works as expected
- [ ] **Edge Cases**: Boundary conditions tested
- [ ] **Error States**: Error handling verified

**Validation Commands** (customize for your project):
```bash
# Test Commands
[your test runner command]

# Coverage Check
[your coverage command]

# Code Quality
[your linter command]
[your formatter check command]
[your type checker command]

# Build Verification
[your build command]

# Security Audit
[your dependency audit command]
```

**Manual Test Checklist**:
- [ ] Test case 1: [Specific scenario to verify]
- [ ] Test case 2: [Edge case to verify]
- [ ] Test case 3: [Error handling to verify]

---

## Phase 2: [Core Feature Phase Name]

**Goal**: [Specific deliverable]
**Estimated Time**: X hours
**Status**: Pending | In Progress | Complete

### Tasks

**RED: Write Failing Tests First**
- [ ] **Test 2.1**: Write unit tests for [specific functionality]
  - File(s): `test/unit/[feature]/[component]_test.ext` (from Phase 0 tree)
  - Expected: Tests FAIL (red) because feature doesn't exist yet
  - Details: Test cases covering:
    - Happy path scenarios
    - Edge cases
    - Error conditions

- [ ] **Test 2.2**: Write integration tests for [component interaction]
  - File(s): `test/integration/[feature]_test.ext` (from Phase 0 tree)
  - Expected: Tests FAIL (red) because integration doesn't exist yet
  - Details: Test interaction between [list components]

**GREEN: Implement to Make Tests Pass**
- [ ] **Task 2.3**: Implement [component/module]
  - File(s): `src/[feature]/[component].ext` (from Phase 0 tree)
  - Goal: Make Test 2.1 pass with minimal code
  - Details: [Implementation notes]

- [ ] **Task 2.4**: Implement [integration/glue code]
  - File(s): `src/[feature]/[integration].ext` (from Phase 0 tree)
  - Goal: Make Test 2.2 pass
  - Details: [Implementation notes]

**REFACTOR: Clean Up Code**
- [ ] **Task 2.5**: Refactor for code quality
  - Files: Review all new code in this phase
  - Goal: Improve design without breaking tests
  - Checklist:
    - [ ] Remove duplication (DRY principle)
    - [ ] Improve naming clarity
    - [ ] Extract reusable components
    - [ ] Add calling specs to module headers (LOD Rule 5)
    - [ ] Add inline documentation where logic isn't self-evident
    - [ ] Optimize performance if needed

### Phase 2 Quality Gate

**DO NOT proceed to Phase 3 until ALL checks pass**

**TDD Compliance** (CRITICAL):
- [ ] **Red Phase**: Tests were written FIRST and initially failed
- [ ] **Green Phase**: Production code written to make tests pass
- [ ] **Refactor Phase**: Code improved while tests still pass
- [ ] **Coverage Check**: Test coverage meets requirements

**LOD Conformance**:
- [ ] Phase output conforms to Phase 0 architecture (files match target tree, no file exceeds 800 LOC)

**Build & Tests**:
- [ ] **Build**: Project builds/compiles without errors
- [ ] **All Tests Pass**: 100% of tests passing (no skipped tests)
- [ ] **Test Performance**: Test suite completes in acceptable time
- [ ] **No Flaky Tests**: Tests pass consistently (run 3+ times)

**Code Quality**:
- [ ] **Linting**: No linting errors or warnings
- [ ] **Formatting**: Code formatted per project standards
- [ ] **Type Safety**: Type checker passes (if applicable)
- [ ] **Static Analysis**: No critical issues from static analysis tools

**Security & Performance**:
- [ ] **Dependencies**: No known security vulnerabilities
- [ ] **Performance**: No performance regressions
- [ ] **Memory**: No memory leaks or resource issues
- [ ] **Error Handling**: Proper error handling implemented

**Documentation**:
- [ ] **Code Comments**: Complex logic documented
- [ ] **API Docs**: Public interfaces documented
- [ ] **README**: Usage instructions updated if needed

**Manual Testing**:
- [ ] **Functionality**: Feature works as expected
- [ ] **Edge Cases**: Boundary conditions tested
- [ ] **Error States**: Error handling verified

**Validation Commands**:
```bash
[Same as Phase 1 - customize for your project]
```

**Manual Test Checklist**:
- [ ] Test case 1: [Specific scenario to verify]
- [ ] Test case 2: [Edge case to verify]
- [ ] Test case 3: [Error handling to verify]

---

## Phase 3: [Enhancement Phase Name]

**Goal**: [Specific deliverable]
**Estimated Time**: X hours
**Status**: Pending | In Progress | Complete

### Tasks

**RED: Write Failing Tests First**
- [ ] **Test 3.1**: Write unit tests for [specific functionality]
  - File(s): `test/unit/[feature]/[component]_test.ext` (from Phase 0 tree)
  - Expected: Tests FAIL (red) because feature doesn't exist yet
  - Details: Test cases covering:
    - Happy path scenarios
    - Edge cases
    - Error conditions

- [ ] **Test 3.2**: Write integration tests for [component interaction]
  - File(s): `test/integration/[feature]_test.ext` (from Phase 0 tree)
  - Expected: Tests FAIL (red) because integration doesn't exist yet
  - Details: Test interaction between [list components]

**GREEN: Implement to Make Tests Pass**
- [ ] **Task 3.3**: Implement [component/module]
  - File(s): `src/[feature]/[component].ext` (from Phase 0 tree)
  - Goal: Make Test 3.1 pass with minimal code
  - Details: [Implementation notes]

- [ ] **Task 3.4**: Implement [integration/glue code]
  - File(s): `src/[feature]/[integration].ext` (from Phase 0 tree)
  - Goal: Make Test 3.2 pass
  - Details: [Implementation notes]

**REFACTOR: Clean Up Code**
- [ ] **Task 3.5**: Refactor for code quality
  - Files: Review all new code in this phase
  - Goal: Improve design without breaking tests
  - Checklist:
    - [ ] Remove duplication (DRY principle)
    - [ ] Improve naming clarity
    - [ ] Extract reusable components
    - [ ] Add calling specs to module headers (LOD Rule 5)
    - [ ] Add inline documentation where logic isn't self-evident
    - [ ] Optimize performance if needed

### Phase 3 Quality Gate

**DO NOT proceed until ALL checks pass**

**TDD Compliance** (CRITICAL):
- [ ] **Red Phase**: Tests were written FIRST and initially failed
- [ ] **Green Phase**: Production code written to make tests pass
- [ ] **Refactor Phase**: Code improved while tests still pass
- [ ] **Coverage Check**: Test coverage meets requirements

**LOD Conformance**:
- [ ] Phase output conforms to Phase 0 architecture (files match target tree, no file exceeds 800 LOC)

**Build & Tests**:
- [ ] **Build**: Project builds/compiles without errors
- [ ] **All Tests Pass**: 100% of tests passing (no skipped tests)
- [ ] **Test Performance**: Test suite completes in acceptable time
- [ ] **No Flaky Tests**: Tests pass consistently (run 3+ times)

**Code Quality**:
- [ ] **Linting**: No linting errors or warnings
- [ ] **Formatting**: Code formatted per project standards
- [ ] **Type Safety**: Type checker passes (if applicable)
- [ ] **Static Analysis**: No critical issues from static analysis tools

**Security & Performance**:
- [ ] **Dependencies**: No known security vulnerabilities
- [ ] **Performance**: No performance regressions
- [ ] **Memory**: No memory leaks or resource issues
- [ ] **Error Handling**: Proper error handling implemented

**Documentation**:
- [ ] **Code Comments**: Complex logic documented
- [ ] **API Docs**: Public interfaces documented
- [ ] **README**: Usage instructions updated if needed

**Manual Testing**:
- [ ] **Functionality**: Feature works as expected
- [ ] **Edge Cases**: Boundary conditions tested
- [ ] **Error States**: Error handling verified

**Validation Commands**:
```bash
[Same as previous phases - customize for your project]
```

**Manual Test Checklist**:
- [ ] Test case 1: [Specific scenario to verify]
- [ ] Test case 2: [Edge case to verify]
- [ ] Test case 3: [Error handling to verify]

---

## Risk Assessment

| Risk | Probability | Impact | Mitigation Strategy |
|------|-------------|--------|---------------------|
| [Risk 1] | Low/Med/High | Low/Med/High | [Specific mitigation steps] |
| [Risk 2] | Low/Med/High | Low/Med/High | [Specific mitigation steps] |

---

## Rollback Strategy

### If Phase 1 Fails
- Undo code changes in: [list files from Phase 0 tree]
- Remove dependencies: [if any were added]

### If Phase 2 Fails
- Restore to Phase 1 complete state
- Undo changes in: [list files]

### If Phase 3 Fails
- Restore to Phase 2 complete state

---

## Progress Tracking

### Completion Status
- **Phase 0**: Pending | In Progress | Complete
- **Phase 1**: Pending | In Progress | Complete
- **Phase 2**: Pending | In Progress | Complete
- **Phase 3**: Pending | In Progress | Complete

**Overall Progress**: X% complete

### Time Tracking
| Phase | Estimated | Actual | Variance |
|-------|-----------|--------|----------|
| Phase 0 | X hours | Y hours | +/- Z hours |
| Phase 1 | X hours | - | - |
| Phase 2 | X hours | - | - |
| Phase 3 | X hours | - | - |
| **Total** | X hours | Y hours | +/- Z hours |

---

## Consolidated LOD + TDD Compliance Checklist

**Run this checklist after ALL phases complete. Every item must pass.**

### LOD Hard Rules (7 items -- all mandatory)
- [ ] No file exceeds 800 LOC
- [ ] Each file has exactly one responsibility
- [ ] Pure functions used where logic does not need instance state
- [ ] No inheritance chains > 1 level; no factory-of-factory patterns
- [ ] Every module has calling spec at the top (inputs, outputs, side effects)
- [ ] Deterministic logic is sealed (tested, not AI-regenerated)
- [ ] Probabilistic logic is flexible (routing, variant selection left to LLM domain)

### LOD Design Patterns (only patterns marked applicable in Phase 0)
<!-- Fill in only for patterns marked "Yes" in Phase 0d -->
- [ ] [Pattern Name]: [Specific verification -- e.g., "variant registry exists at src/feature/registry.py"]
- [ ] [Pattern Name]: [Specific verification]

### TDD Compliance
- [ ] Tests written before production code in every phase
- [ ] Red-Green-Refactor cycle followed consistently
- [ ] Coverage target met (>= 80% business logic)
- [ ] No skipped or flaky tests

### Code Quality
- [ ] Linting passes with no errors
- [ ] Type checking passes (if applicable)
- [ ] Code formatting consistent with project standards
- [ ] No security vulnerabilities introduced
- [ ] No performance regressions

---

## Notes & Learnings

### Implementation Notes
- [Add insights discovered during implementation]
- [Document decisions that deviate from Phase 0 architecture, with justification]

### Blockers Encountered
- **Blocker 1**: [Description] -> [Resolution]

### LOD Observations
- [Which patterns were most useful?]
- [Any Hard Rules that created friction? Why?]
- [Would the Phase 0 decomposition change in hindsight?]

---

---

## References

### Documentation
- [Link to relevant docs]
- [Link to API references]
- [Link to design mockups]

### Related Issues
- Issue #X: [Description]
- PR #Y: [Description]

---

## Final Checklist

**Before marking plan as COMPLETE**:
- [ ] All phases completed with quality gates passed
- [ ] Full integration testing performed
- [ ] LOD Hard Rules verified via consolidated checklist
- [ ] Applicable LOD patterns verified via consolidated checklist
- [ ] Documentation updated
- [ ] Performance benchmarks meet targets
- [ ] Security review completed
- [ ] Accessibility requirements met (if UI feature)
- [ ] All stakeholders notified
- [ ] Plan document archived for future reference

---

## TDD Example Workflow

### Example: Adding a Notification Channel

**Phase 1: RED (Write Failing Tests)**

```
# Pseudocode - adapt to your testing framework

test "should send notification via email channel":
  // Arrange
  sender = new NotificationSender(mockRegistry)
  notification = {type: "welcome", recipient: "user@example.com"}

  // Act
  result = sender.send(notification, channel="email")

  // Assert
  expect(result.isSuccess).toBe(true)
  expect(result.channel).toBe("email")
  // TEST FAILS - NotificationSender doesn't exist yet
```

**Phase 2: GREEN (Minimal Implementation)**

```
# sender.py - pure function, no class state (LOD Rule 3)
def send_notification(notification, channel, registry):
    channel_fn = registry[channel]  # dict dispatch (LOD Pattern 8)
    return channel_fn(notification)
    // TEST PASSES - minimal functionality works
```

**Phase 3: REFACTOR (Improve Design)**

```
# sender.py - add calling spec (LOD Rule 5)
"""Send notification via registered channel.

CALLING SPEC:
    result = send_notification(
        notification: Notification,
        channel: str,
        registry: dict[str, Callable],
    ) -> SendResult

    Side effects: External API call via channel implementation
    Deterministic: No
"""
def send_notification(notification, channel, registry):
    if channel not in registry:
        return SendResult(success=False, error="Unknown channel")
    channel_fn = registry[channel]
    return channel_fn(notification)
    // TESTS STILL PASS - improved code quality + LOD compliance
```

### TDD Red-Green-Refactor Cycle Visualization

```
Phase 1: RED
  Write test for feature X
  Run test -> FAILS
  Commit: "Add failing test for X"

Phase 2: GREEN
  Write minimal code
  Run test -> PASSES
  Commit: "Implement X to pass tests"

Phase 3: REFACTOR
  Improve code quality
  Run test -> STILL PASSES
  Add calling specs (LOD Rule 5)
  Run test -> STILL PASSES
  Verify LOD conformance
  Run test -> STILL PASSES
  Commit: "Refactor X for LOD compliance and better design"

Repeat for next feature ->
```

### Benefits of This Approach

**Safety**: Tests catch regressions immediately
**Design**: Tests force you to think about API design first
**Documentation**: Tests document expected behavior
**Confidence**: Refactor without fear of breaking things
**Quality**: Higher code coverage from day one
**LOD Compliance**: Calling specs and pure functions verified during refactor phase

---

**Plan Status**: Planning
**Next Action**: [What needs to happen next]
**Blocked By**: [Any current blockers] or None
