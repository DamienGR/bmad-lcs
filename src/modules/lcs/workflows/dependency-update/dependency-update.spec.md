# Workflow Specification: Dependency Update

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Update vulnerable and outdated dependencies, protected by the safety nets already in place.

**Description:** With safety nets deployed, it's now safe to update dependencies. Analyzes composer.json/package.json for vulnerabilities (via Snyk MCP) and outdated packages. Proposes update plan prioritized by severity. Executes updates incrementally, running safety nets after each update to catch breakage immediately.

**Workflow Type:** Core (Phase 2b)

---

## Workflow Structure

### Entry Point

```yaml
---
name: dependency-update
description: Update vulnerable/outdated dependencies with safety net protection
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/dependency-update'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Analyze Dependencies | Vulnerability scan + outdated package detection |
| 02 | Propose Update Plan | Prioritized list (critical vulnerabilities first) with risk assessment |
| 03 | User Approval | Present plan, get approval for batch or individual updates |
| 04 | Execute Updates | Incremental updates with safety net validation after each |
| 05 | Report | Summary of updates applied, remaining issues |

---

## Workflow Inputs

### Required Inputs

- Safety nets in place and green (from Phase 2)
- Dependency audit data (from Phase 1 audit)

### Optional Inputs

- Exclusion list (packages to skip)

---

## Workflow Outputs

### Output Format

- [x] Non-document (updated package files)
- [x] Document-producing (report)

### Output Files

- Updated `composer.json`/`composer.lock` and/or `package.json`/`package-lock.json`
- `_bmad-output/lcs/dependency-update-report.md` — Update report

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — may execute directly or spawn a dedicated teammate.

### Other Agents

None required. Lead can handle this sequentially.

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- Each update is atomic — run safety nets after each one
- If a safety net breaks, rollback immediately and flag for manual review
- Snyk MCP provides CVSS scores for prioritization
- User approval required before executing updates

---

_Spec created on 2026-02-09 via BMAD Module workflow_
