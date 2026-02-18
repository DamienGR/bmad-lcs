# Workflow Specification: Status

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Display global consolidation state — current phase, safety nets in place, coverage metrics.

**Description:** Reads the sidecar state and all phase reports to produce a clear, concise status overview. Shows where the project is in the consolidation journey, what's in place, and what's next. Available at any time via the [ST] menu command.

**Workflow Type:** Utility

---

## Workflow Structure

### Entry Point

```yaml
---
name: status
description: Display global consolidation state
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/status'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Load State | Read sidecar-state.yaml |
| 02 | Collect Metrics | Read phase reports for coverage, test counts, risk data |
| 03 | Display Status | Present formatted consolidation dashboard |

---

## Workflow Inputs

### Required Inputs

- `_bmad-output/lcs/sidecar-state.yaml`

### Optional Inputs

- Verbosity level (summary vs detailed)

---

## Workflow Outputs

### Output Format

- [ ] Document-producing
- [x] Non-document (console output)

### Output Files

None — displays directly to user.

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — executes directly.

### Other Agents

None.

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- Must be instant — just reads existing state files, no computation
- Display format: phase progression bar, safety net summary, coverage metrics
- Accessible via [ST] menu command at any time
- Should clearly indicate "what's next" in the consolidation journey

---

_Spec created on 2026-02-09 via BMAD Module workflow_
