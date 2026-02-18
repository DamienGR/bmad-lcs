# Workflow Specification: Safety Nets

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Deploy multi-layer safety nets to protect the legacy codebase before any modifications.

**Description:** Orchestrates parallel deployment by two teammates (Safety Net Builder + Static Guard). Safety Net Builder writes e2e tests, snapshots, and smoke tests based on audit findings. Static Guard configures linting, type checking, git hooks, and CI. Together they create a comprehensive safety net that catches regressions.

**Workflow Type:** Core (Phase 2)

---

## Workflow Structure

### Entry Point

```yaml
---
name: safety-nets
description: Deploy multi-layer safety nets for legacy codebase protection
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/safety-nets'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Prioritize from Risk Map | Identify highest-risk areas for safety nets first |
| 02 | Spawn Teammates | Lead spawns Safety Net Builder + Static Guard (or sequential) |
| 03 | E2E Tests | (Headless) Write Playwright e2e tests for critical journeys |
| 04 | Snapshots | (Headless) Create visual regression snapshots for key pages |
| 05 | Smoke Tests | (Headless) Basic smoke tests for essential functionality |
| 06 | Static Guards | (Headless) Configure ESLint, type checking, git hooks, CI pipeline |
| 07 | Validate | Run all safety nets — confirm green baseline |
| 08 | Report | Summary of safety nets in place, coverage assessment |

---

## Workflow Inputs

### Required Inputs

- Audit report (`_bmad-output/lcs/audit/audit-report.md`)
- Risk map (`_bmad-output/lcs/risk-map.md`)
- Resolved open questions from batched Q&A

### Optional Inputs

- Priority overrides (user-specified areas to protect first)

---

## Workflow Outputs

### Output Format

- [x] Non-document (test files, config files, CI config)
- [x] Document-producing (report)

### Output Files

- Test files in project (e2e/, snapshots/)
- `.eslintrc`, `.gitignore`, git hooks config
- CI configuration (GitHub Actions or similar)
- `_bmad-output/lcs/safety-nets-report.md` — Safety nets deployment report

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — orchestrates, spawns teammates, validates.

### Teammates (Headless)

- **Safety Net Builder** — Steps 03, 04, 05. Tools: Playwright MCP, test framework
- **Static Guard** — Step 06. Tools: ESLint MCP, CI config

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- Steps 03-06 execute in parallel via Agent Teams (sequential fallback)
- Certain zones (from ADR-005) only get nets after Q&A is resolved
- Target: first safety net in place within ~1 hour of starting LCS
- Green baseline is mandatory before proceeding to any modifications
- # Inspired by TEA: test architecture patterns, fixture design

---

_Spec created on 2026-02-09 via BMAD Module workflow_
