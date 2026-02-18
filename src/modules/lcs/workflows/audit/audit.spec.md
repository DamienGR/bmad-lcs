# Workflow Specification: Audit

**Module:** lcs
**Status:** Placeholder — To be created via create-workflow workflow
**Created:** 2026-02-09

---

## Workflow Overview

**Goal:** Full analysis of the legacy project: code structure, live site, dependencies, and business logic.

**Description:** Orchestrates parallel analysis by two teammates (Code Analyst + Site Explorer). Code Analyst performs static analysis, structure mapping, complexity assessment, framework pattern identification, and business logic extraction. Site Explorer navigates the live site via MCP Chrome DevTools, maps pages/journeys, and extracts functional specs. Results are synthesized by the Lead into a unified audit report.

**Workflow Type:** Core (Phase 1)

---

## Workflow Structure

### Entry Point

```yaml
---
name: audit
description: Full project audit - code analysis + live site exploration
web_bundle: true
installed_path: '{project-root}/_bmad/lcs/workflows/audit'
---
```

### Mode

- [x] Create-only (steps-c/)
- [ ] Tri-modal (steps-c/, steps-e/, steps-v/)

---

## Planned Steps

| Step | Name | Goal |
|------|------|------|
| 01 | Spawn Teammates | Lead spawns Code Analyst + Site Explorer (or sequential fallback) |
| 02 | Code Analysis | (Headless) Static analysis, structure, complexity, framework patterns |
| 03 | Site Exploration | (Headless) Live navigation, page mapping, journey mapping, functional specs |
| 04 | Dependency Audit | Analyze composer.json/package.json — vulnerabilities, outdated packages |
| 05 | Synthesize | Lead merges teammate outputs into unified audit report |
| 06 | Batched Q&A | Present open questions from Site Explorer to user (ADR-005) |
| 07 | Generate Risk Map | Trigger risk-map workflow with audit findings |

---

## Workflow Inputs

### Required Inputs

- Initialized sidecar-state.yaml (from pre-flight)
- Framework profile (loaded during pre-flight)
- Local site URL

### Optional Inputs

- Focus areas (specific directories or features to prioritize)

---

## Workflow Outputs

### Output Format

- [x] Document-producing

### Output Files

- `_bmad-output/lcs/audit/code-analysis.md` — Code Analyst findings
- `_bmad-output/lcs/audit/site-exploration.md` — Site Explorer findings
- `_bmad-output/lcs/audit/dependency-audit.md` — Dependency analysis
- `_bmad-output/lcs/audit/audit-report.md` — Unified audit report (Lead synthesis)
- `_bmad-output/lcs/audit/open-questions.md` — Batched Q&A for user

---

## Agent Integration

### Primary Agent

Conrad (LCS Lead) — orchestrates, spawns teammates, synthesizes.

### Teammates (Headless)

- **Code Analyst** — Steps 02, 04. Tools: code reading, AST, framework profile, Semgrep MCP, MySQL MCP
- **Site Explorer** — Step 03. Tools: MCP Chrome DevTools, Lighthouse MCP

---

## Implementation Notes

**Use the create-workflow workflow to build this workflow.**

- Steps 02 + 03 execute in parallel via Agent Teams (sequential fallback)
- Site Explorer collects open questions during exploration (ADR-005)
- Audit must produce visible results fast — target: audit complete in ~30 min
- Business logic discovery includes functional journeys, not just pages
- Spawn prompts must pass: workflow path + framework profile + project context

---

_Spec created on 2026-02-09 via BMAD Module workflow_
