# Workflow Specification: Framework Profile

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Load or create framework-specific knowledge profile for the target project.

**Description:** Loads the appropriate framework profile (WordPress, Drupal, or Other) from `data/frameworks/`. The profile provides framework-specific patterns, known pitfalls, CLI tools, page builder awareness, and testing strategies. If no profile exists for the detected framework, guides the user through creating a minimal one.

**Workflow Type:** Feature

---

## Workflow Structure

### Entry Point

```yaml
---
name: framework-profile
description: Load or create framework-specific knowledge profile
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/framework-profile'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Detect Framework | Identify framework from project files (wp-config.php, settings.php, etc.) |
| 02 | Load Profile | Load matching profile from `data/frameworks/` |
| 03 | Validate Profile | Check profile completeness for this specific project (plugins, page builders, etc.) |
| 04 | Enrich | If needed, add project-specific notes to profile (active plugins, theme, custom post types) |

---

## Workflow Inputs

### Required Inputs

- Project repository path

### Optional Inputs

- Framework override (user specifies if auto-detection fails)

---

## Workflow Outputs

### Output Format

- [x] Non-document (loaded profile in context)

### Output Files

- `_bmad-output/lcs/framework-profile-active.md` — Enriched profile for this specific project

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — executes during Pre-flight phase.

### Other Agents

None.

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- Called by Pre-flight workflow (step 02)
- V1 profiles: WordPress + Drupal (detailed placeholders created in module structure)
- WordPress profile must handle: Elementor, DIVI, ACF, shortcodes, procedural code in functions.php
- Adding a new framework: just add a file to `data/frameworks/` — no module refactoring needed (ADR-006)

---

_Spec created on 2026-02-09 via BMAD Module workflow_
