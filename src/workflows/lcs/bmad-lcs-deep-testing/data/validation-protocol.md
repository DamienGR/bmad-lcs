# Validation Protocol

**Purpose:** Two-category validation protocol for the deep-testing workflow. Loaded by step-02 after each zone's tests are written.

---

## Two-Category Validation

### Category 1: Safety Nets (Existing Tests) — MUST PASS

**Source:** Tests deployed during Phase 2 (safety-nets)
**Expectation:** ALL must pass — these are the baseline
**If fail:** STOP the cycle. This indicates a regression or test isolation problem.

**Failure interpretation:**
- Safety nets were green before Deep Tester started
- If they fail AFTER new tests are added → the new tests or fixtures are interfering
- Root causes: shared state, fixture contamination, port conflicts, environment pollution

**Action on failure:**
1. Identify which safety net tests failed
2. Check if new test fixtures conflict with existing ones
3. Revert the new test files temporarily to confirm safety nets return to green
4. If confirmed interference → Deep Tester must fix isolation before proceeding

### Category 2: Deep Tests (New Tests) — INFORMATIONAL

**Source:** Tests written by the Deep Tester in the current zone
**Expectation:** Informational — failures are expected and valuable

**Failure interpretation:**

| Result | Meaning | Action |
|--------|---------|--------|
| PASS | Test validates expected behavior | Record in artifact |
| FAIL — existing bug | Test exposes a pre-existing bug | Log as `known_issue` in artifact, keep test with `skip` marker |
| FAIL — test defect | Test itself is wrong (bad assertion, wrong assumption) | Deep Tester fixes the test |
| FAIL — environment | Environment issue (missing dependency, config) | Flag to user, not a code problem |

---

## Execution Order

Run tests in this order for fastest feedback:

1. **Safety nets — smoke tests** (fastest — seconds)
2. **Safety nets — linting** (fast — existing baseline)
3. **New unit tests** (fast — isolated)
4. **Safety nets — e2e tests** (slower)
5. **New integration tests** (slower)
6. **New edge case tests** (variable)
7. **Safety nets — visual snapshots** (slowest — if applicable)

**Rationale:** If safety nets fail early, we stop before running slower tests.

---

## Baseline Check (Before Each Zone)

Before spawning the Deep Tester for a new zone:

1. Run ONLY the existing safety nets (Category 1)
2. All must pass
3. If any fail → STOP, do not spawn, investigate regression

This confirms the baseline is intact before adding new tests.

---

## Post-Zone Validation (After Deep Tester Completes)

After the Deep Tester finishes writing tests for a zone:

1. Run safety nets (Category 1) → MUST pass
2. Run new tests (Category 2) → informational
3. Present results using the two-category format:

```
**Validation — Zone: {zone_name}**

**Category 1 — Safety Nets (baseline):** {✅ ALL PASS / 🛑 FAILURES}
{if failures: list failed tests}

**Category 2 — Deep Tests (new):**
- Passing: {N} tests
- Known issues: {N} (existing bugs exposed)
- Test defects: {N} (to be fixed)
- Environment: {N} (non-code issues)

**Verdict:** {READY FOR APPROVAL / BLOCKED — baseline regression}
```

---

## Triage Protocol (For Category 2 Failures)

When new tests fail, Conrad presents each failure to the user with context:

"**Test en échec :** `{test_name}`
**Assertion :** {what was expected vs actual}
**Classification :**
- [K] Known issue — bug existant découvert
- [F] Fix — le test est incorrect, Deep Tester corrige
- [E] Environnement — problème d'environnement, pas un bug"

User classifies each failure. Conrad updates the artifact accordingly.
