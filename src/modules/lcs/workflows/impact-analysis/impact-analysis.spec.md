# Workflow Specification: Impact Analysis

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Before any modification, analyze potentially impacted zones to prevent regressions.

**Description:** The killer feature for daily use post-consolidation. Given a planned change (file, function, feature), analyzes the codebase to identify all potentially impacted zones: direct dependencies, indirect coupling, related tests, shared state, and framework-specific side effects. Central mechanism in the Guard phase.

**Workflow Type:** Feature

---

## Workflow Structure

### Entry Point

```yaml
---
name: impact-analysis
description: Analyze potentially impacted zones before any modification
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/impact-analysis'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Define Scope | Identify what will be modified (files, functions, features) |
| 02 | Direct Dependencies | Trace direct callers, importers, includers |
| 03 | Indirect Coupling | Identify shared state, global variables, hooks, filters (framework-specific) |
| 04 | Related Tests | Map existing tests that cover the impacted zones |
| 05 | Risk Assessment | Score impact severity, recommend pre-change safety net coverage |
| 06 | Report | Produce impact analysis report with recommended test scope |

---

## Workflow Inputs

### Required Inputs

- Planned change description (files or features to modify)
- Audit data (code structure, dependency graph)
- Framework profile (for framework-specific side effects)

### Optional Inputs

- Depth limit (how many levels of indirect coupling to trace)

---

## Workflow Outputs

### Output Format

- [x] Document-producing

### Output Files

- `_bmad-output/lcs/guard/impact-report-{date}.md` — Impact analysis report

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — may execute directly or spawn Code Analyst for deep analysis.

### Teammates (Headless)

- **Code Analyst** — Optional, for deep dependency tracing on complex changes

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- Must be fast for daily use — target: < 2 minutes for typical analysis
- Framework-specific: WordPress hooks/filters, Drupal hooks, action/filter chains
- This is the permanent companion feature post-consolidation
- Can integrate with git hooks for automatic pre-commit analysis
- Should highlight "surprising" impacts — the ones the developer wouldn't expect

---

_Spec created on 2026-02-09 via BMAD Module workflow_
