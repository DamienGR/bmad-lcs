# Workflow Specification: Deep Testing

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Increase test coverage and depth after refactoring has made the code testable.

**Description:** The Deep Tester teammate writes integration tests, unit tests, and edge case tests for the now-refactored codebase. Prioritized by risk map — highest risk areas get deepest coverage. Builds on the safety nets already in place.

**Workflow Type:** Core (Phase 4)

---

## Workflow Structure

### Entry Point

```yaml
---
name: deep-testing
description: Increase test coverage and depth on refactored code
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/deep-testing'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Coverage Analysis | Assess current coverage gaps against risk map |
| 02 | Prioritize | Rank test targets by risk × coverage gap |
| 03 | Spawn Deep Tester | Lead spawns Deep Tester teammate (or sequential) |
| 04 | Integration Tests | (Headless) Write integration tests for key interactions |
| 05 | Unit Tests | (Headless) Write unit tests for critical functions |
| 06 | Edge Cases | (Headless) Write edge case tests for high-risk areas |
| 07 | Validate | Run full test suite, verify no regressions |
| 08 | Report | Coverage metrics, remaining gaps, confidence assessment |

---

## Workflow Inputs

### Required Inputs

- Refactored codebase (from Phase 3)
- Risk map with prioritized areas
- Existing safety nets (baseline)

### Optional Inputs

- Coverage targets (user-specified thresholds)

---

## Workflow Outputs

### Output Format

- [x] Non-document (test files)
- [x] Document-producing (report)

### Output Files

- Test files in project (unit/, integration/)
- `_bmad-output/lcs/deep-testing-report.md` — Coverage and testing report

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — orchestrates, spawns teammate, validates.

### Teammates (Headless)

- **Deep Tester** — Steps 04, 05, 06. Tools: test framework, Playwright MCP

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- # Inspired by TEA: risk-based test prioritization, fixture architecture, test isolation
- Prefer lower test levels when possible (unit > integration > e2e)
- API tests are first-class citizens alongside UI tests
- Tests must mirror actual usage patterns

---

_Spec created on 2026-02-09 via BMAD Module workflow_
