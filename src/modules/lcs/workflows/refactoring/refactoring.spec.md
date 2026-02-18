# Workflow Specification: Refactoring

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Targeted refactoring to make legacy code testable, protected by safety nets.

**Description:** The Refactorer teammate performs surgical refactoring on code identified by the audit as untestable, overly complex, or high-risk. Works in a separate git worktree for safety. Each refactoring is validated against existing safety nets before merging.

**Workflow Type:** Core (Phase 3)

---

## Workflow Structure

### Entry Point

```yaml
---
name: refactoring
description: Targeted refactoring to make legacy code testable
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/refactoring'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Identify Targets | Select refactoring targets from audit/risk map (highest impact first) |
| 02 | Create Worktree | Set up separate git worktree for safe refactoring |
| 03 | Spawn Refactorer | Lead spawns Refactorer teammate (or executes sequentially) |
| 04 | Execute Refactoring | (Headless) Surgical refactoring — extract functions, reduce complexity, decouple |
| 05 | Validate | Run safety nets against refactored code |
| 06 | Merge | Merge worktree if safety nets pass, rollback if not |
| 07 | Report | Summary of refactorings applied, testability improvements |

---

## Workflow Inputs

### Required Inputs

- Audit report with complexity/testability assessment
- Risk map with prioritized targets
- Safety nets in place and green

### Optional Inputs

- Specific refactoring targets (user override)

---

## Workflow Outputs

### Output Format

- [x] Non-document (refactored code)
- [x] Document-producing (report)

### Output Files

- Refactored source files (in project)
- `_bmad-output/lcs/refactoring-report.md` — Refactoring report

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — orchestrates, spawns teammate, validates.

### Teammates (Headless)

- **Refactorer** — Step 04. Tools: code read/write, Git MCP (worktree management)

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- Git worktree isolates refactoring from main branch
- Each refactoring is atomic — validate and merge one at a time
- Refactorer must understand framework-specific patterns (loaded from profile)
- Goal is testability, not perfection — minimal changes for maximum testability gain

---

_Spec created on 2026-02-09 via BMAD Module workflow_
