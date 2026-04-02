# Safety Net Selection Protocol

## Purpose

Defines how to select, prioritize, and execute safety nets for impacted zones during a guard cycle.

---

## Selection Process

### 1. Load Data Sources

- **Sidecar State** (`sidecar-state.yaml`): safety-nets section — registry of all deployed tests
- **Risk Map** (`risk-map.md`): risk scores per zone
- **Impact Analysis** (from step-02): list of impacted zones

### 2. Cross-Reference

For each impacted zone from the impact analysis:

1. Look up the zone's risk score from the risk map
2. Find all safety nets registered for that zone in sidecar-state
3. Classify the zone's impact level based on risk score

### 3. Prioritize by Risk Score

| Risk Score | Impact Level | Tests to Run |
|-----------|--------------|--------------|
| >= 8 | CRITICAL | All: lint + smoke + e2e + visual snapshots |
| 5 - 7 | HIGH | lint + smoke + e2e |
| 3 - 4 | MEDIUM | lint + smoke only |
| 1 - 2 | LOW | Skip (mention in report) |

---

## Execution Order

Execute safety nets in this strict order:

1. **Lint** — fast, catches syntax and style issues early
2. **Smoke tests** — quick functional verification (parallelizable by zone)
3. **E2E tests** — full integration tests (parallelizable by zone)
4. **Visual snapshots** — visual regression checks (parallelizable by zone)

### Parallelization Rules

- Lint runs first (sequential) — fast and may block further execution
- After lint passes: smoke, e2e, and visual can run in parallel **by zone**
- Within a zone: maintain the order (smoke before e2e before visual)
- If subprocess/parallel execution unavailable: run everything sequentially

### Early Exit

- If lint fails on a CRITICAL zone: stop and report BLOCKED immediately
- If smoke fails on a CRITICAL zone: skip e2e/visual for that zone, report BLOCKED

---

## Result Collection

For each safety net executed, record:

| Field | Values |
|-------|--------|
| Safety Net Name | (test identifier) |
| Zone | (zone name from risk map) |
| Risk Level | CRITICAL / HIGH / MEDIUM / LOW |
| Result | PASS / FAIL / SKIP / ERROR |
| Details | (failure message, skip reason, or empty) |
| Duration | (execution time) |

---

## Verdict Computation

After all safety nets execute:

| Verdict | Condition |
|---------|-----------|
| **BLOCKED** | At least 1 FAIL on a CRITICAL zone (risk score >= 8) |
| **WARNING** | FAILs only on HIGH or lower zones (no CRITICAL failures) |
| **CLEAR** | All tests PASS (or only SKIPs on LOW zones) |

### Verdict Messaging

- **BLOCKED**: "Changes impact critical zones with failing tests. Do NOT proceed without fixing."
- **WARNING**: "Some tests failed on non-critical zones. Review before proceeding."
- **CLEAR**: "All safety nets passed. Changes are safe to proceed."

---

## Edge Cases

- **No safety nets available for a zone**: Report as "UNCOVERED" in the results — recommend deploying safety nets
- **Zone not in risk map**: Treat as MEDIUM (risk score 4) — conservative default
- **Safety net errors (not failures)**: Report as ERROR, do not count toward verdict — investigate separately
