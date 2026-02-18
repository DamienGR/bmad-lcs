# Workflow Specification: Pre-flight Check

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Verify environment and prerequisites before starting consolidation.

**Description:** Checks that all prerequisites are in place: repo access, local site running, tools installed (DDEV, Node.js, Composer, etc.), MCP tools available. Detects framework (WordPress/Drupal) and loads the corresponding framework profile. Sets up the consolidation output structure.

**Workflow Type:** Core (Phase 0)

---

## Workflow Structure

### Entry Point

```yaml
---
name: pre-flight
description: Verify environment and prerequisites for legacy consolidation
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/pre-flight'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Check Environment | Verify repo access, local site URL, dev tools installed |
| 02 | Detect Framework | Identify WordPress/Drupal/Other, load framework profile |
| 03 | Check MCP Tools | Verify which MCP tools are available, note missing ones |
| 04 | Initialize State | Create sidecar-state.yaml, set up output directory structure |
| 05 | Report | Summary of environment readiness, proceed/block decision |

---

## Workflow Inputs

### Required Inputs

- Project repository path
- Local site URL (for Site Explorer)

### Optional Inputs

- Framework override (if auto-detection fails)
- MCP tool preferences (enable/disable specific tools)

---

## Workflow Outputs

### Output Format

- [x] Non-document (setup + state initialization)

### Output Files

- `_bmad-output/lcs/sidecar-state.yaml` — Initialized consolidation state
- `_bmad-output/lcs/pre-flight-report.md` — Environment readiness report

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — executes this workflow directly, no teammates spawned.

### Other Agents

None. Lead alone for Phase 0.

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- This is the first workflow executed — must be fast and clear
- Target: environment validated in < 5 minutes
- Framework profile loaded here feeds all subsequent phases
- If prerequisites are missing, provide actionable fix instructions

---

_Spec created on 2026-02-09 via BMAD Module workflow_
