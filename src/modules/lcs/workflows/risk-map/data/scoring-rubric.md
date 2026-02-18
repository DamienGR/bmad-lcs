# Risk Map Scoring Rubric

This file defines the scoring methodology for the LCS risk map. Conrad uses this rubric to score all Level 2 zones automatically from audit data.

---

## Zone Hierarchy

| Level | Name | Definition | Scoring | Example |
|-------|------|-----------|---------|---------|
| 1 | Functional | User journey or major feature area | Roll-up (avg of L2 children) | Checkout, Login, Admin |
| 2 | Component | Group of files forming a logical unit | **PRIMARY scoring unit** | Payment module, Cart logic |
| 3 | File | Individual high-risk file | Drill-down only for CRITICAL/HIGH zones | functions.php (3000 lines) |

**Rule:** Score at Level 2. Roll up to Level 1 for overview. Drill down to Level 3 only for zones scored CRITICAL or HIGH.

---

## 5 Scoring Dimensions

Each dimension is scored 1-5 per Level 2 zone.

### 1. Complexity (Weight: 0.20)

| Score | Meaning | Indicators |
|-------|---------|-----------|
| 1 | Trivial | < 50 lines, no branching, simple CRUD |
| 2 | Low | < 200 lines, few branches, straightforward logic |
| 3 | Moderate | 200-500 lines, multiple branches, some nested logic |
| 4 | High | 500-1000 lines, deep nesting, complex state management |
| 5 | Extreme | > 1000 lines, god functions, recursive patterns, heavy meta-programming |

**Data source:** Code analysis report → file sizes, cyclomatic complexity, nesting depth
**Fallback:** If no complexity data → score 3 (neutral)

### 2. Coupling (Weight: 0.20)

| Score | Meaning | Indicators |
|-------|---------|-----------|
| 1 | Isolated | Self-contained, no external dependencies, pure functions |
| 2 | Low | 1-2 imports, minimal shared state |
| 3 | Moderate | 3-5 imports, some shared state, event listeners |
| 4 | High | 6-10 imports, heavy shared state, global mutations |
| 5 | Extreme | > 10 imports, god object pattern, everything depends on this |

**Data source:** Code analysis report → import counts, dependency graph, shared state references
**Fallback:** If no coupling data → score 3 (neutral)

### 3. Volatility (Weight: 0.15)

| Score | Meaning | Indicators |
|-------|---------|-----------|
| 1 | Stable | No recent changes, mature code, rarely touched |
| 2 | Low | Minor changes in last 6 months |
| 3 | Moderate | Regular updates, active development area |
| 4 | High | Frequent changes, multiple contributors, unstable API |
| 5 | Extreme | Constant churn, breaking changes, under active rewrite |

**Data source:** Code analysis report → git history (commit frequency, recent changes, contributor count)
**Fallback:** If no volatility data → **score 3 (neutral)** — volatility is hardest to assess without git history

### 4. Coverage Gap (Weight: 0.20)

| Score | Meaning | Indicators |
|-------|---------|-----------|
| 1 | Well covered | Existing tests, documented behavior, clear contracts |
| 2 | Mostly covered | Some tests exist, partial documentation |
| 3 | Partial | Few tests, minimal documentation, some manual testing |
| 4 | Poor | No automated tests, undocumented, manual QA only |
| 5 | None | Zero coverage, no documentation, no known test process |

**Data source:** Code analysis report → test file presence, coverage metrics, documentation references
**Fallback:** If no coverage data → score 4 (assume poor — legacy projects rarely have good coverage)

### 5. Business Criticality (Weight: 0.25)

| Score | Meaning | Indicators |
|-------|---------|-----------|
| 1 | Negligible | Static content, decorative, no user impact if broken |
| 2 | Low | Nice-to-have features, admin-only tools, internal pages |
| 3 | Moderate | Standard features, affects user experience but not critical |
| 4 | High | Core user journey, revenue-generating, frequent user interaction |
| 5 | Critical | Payment, authentication, data integrity, security, legal compliance |

**Data source:** Site exploration report → user journeys, page importance, interactive elements, forms
**Fallback:** If unclear → score 3 (neutral) — user validates in step 03

---

## Scoring Formula

```
base_score = (complexity × 0.20) + (coupling × 0.20) + (volatility × 0.15) + (coverage_gap × 0.20) + (business_criticality × 0.25)
```

**Range:** 1.00 – 5.00

### Vulnerability Modifier

Applied AFTER base score calculation, using data from the dependency scan:

| CVEs in Zone | Modifier | Rationale |
|-------------|----------|-----------|
| 0 critical CVEs | × 1.0 | No adjustment |
| 1-2 critical CVEs | × 1.2 | Elevated risk |
| 3+ critical CVEs | × 1.5 | Significant vulnerability exposure |

```
final_score = base_score × vulnerability_modifier
```

**Range after modifier:** 1.00 – 7.50

---

## Risk Tiers

| Tier | Score Range | ASCII Bar | Priority |
|------|-----------|-----------|----------|
| CRITICAL | ≥ 4.5 | ████████ | Immediate — safety nets first |
| HIGH | 3.5 – 4.49 | ██████ | High priority — early phases |
| MEDIUM | 2.5 – 3.49 | ████ | Standard priority |
| LOW | < 2.5 | ██ | Low priority — later phases |

---

## Example: Scored Zone

**Zone:** payment-gateway (Level 2, under "Checkout" Level 1)

| Dimension | Score | Source | Notes |
|-----------|-------|--------|-------|
| Complexity | 4 | Code analysis: 800 lines, deep nesting | Heavy validation logic |
| Coupling | 3 | Code analysis: 4 imports, session state | Moderate shared state |
| Volatility | 2 | Code analysis: stable, few recent commits | Mature code |
| Coverage Gap | 5 | Code analysis: no test files found | Zero coverage |
| Business Criticality | 5 | Site exploration: payment form, revenue path | Critical user journey |

```
base_score = (4×0.20) + (3×0.20) + (2×0.15) + (5×0.20) + (5×0.25)
           = 0.80 + 0.60 + 0.30 + 1.00 + 1.25
           = 3.95

vulnerability_modifier = 1.2 (1 critical CVE in stripe-legacy dependency)

final_score = 3.95 × 1.2 = 4.74 → CRITICAL
```
