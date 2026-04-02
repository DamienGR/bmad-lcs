---
name: 'step-05-report'
description: 'Present pre-flight readiness report with go/no-go decision'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
---

# Step 5: Readiness Report

## STEP GOAL:

To present a clear, visual readiness report synthesizing all pre-flight findings, classify issues as BLOCKER/WARNING/INFO, and deliver a go/no-go decision for proceeding to the Audit phase.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER soften a BLOCKER — if it blocks, say so clearly
- 📖 CRITICAL: Read the complete step file before taking any action
- 🎯 The go/no-go decision is based ONLY on BLOCKER presence — no subjective judgment
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ If everything is ready: be encouraging, point to next steps
- ✅ If blockers exist: be clear and actionable, not discouraging
- ✅ The developer should leave this step knowing EXACTLY where they stand

### Step-Specific Rules:

- 🎯 Focus ONLY on synthesizing and presenting findings
- 🚫 FORBIDDEN to run additional checks — all data comes from sidecar-state
- 🚫 FORBIDDEN to write or modify any files
- 💬 Present findings visually — tables, clear categories, no walls of text
- 📋 Hard blocker = no-go. No exceptions, no workarounds suggested.

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 This is a read-only step — no file modifications
- 📖 Read sidecar-state.yaml for all data
- 🚫 This is the FINAL step — no next step to load

## CONTEXT BOUNDARIES:

- Available: sidecar-state.yaml with all data from steps 01-04
- Focus: synthesis and presentation only
- Limits: do not run checks, do not modify state
- Dependencies: sidecar-state.yaml must exist and be valid (step 04 guaranteed this)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Consolidation State

Read `{sidecarStatePath}` to get all pre-flight data:
- Project info
- Capabilities
- Task results

### 2. Classify All Findings

Organize all findings into three categories:

**BLOCKER (no-go):**
- Git not available or repo not accessible
- Local site not reachable

**WARNING (degraded — consolidation proceeds but specific phases are impacted):**
- DDEV absent → manual environment commands needed
- Node.js absent → Safety Nets and Deep Testing phases blocked
- Composer absent → Dependency Update phase blocked for PHP projects
- Critical MCP tools missing (Chrome DevTools, Playwright) → Audit site exploration and E2E test writing degraded
- Framework profile not loaded → generic analysis only

**INFO (optional — reduced capabilities):**
- PHP_CodeSniffer absent → PHP linting limited
- Non-critical MCP tools missing → specific features unavailable
- MCP smoke test warnings

### 3. Present Readiness Report

Display a structured report:

"**Pre-flight Readiness Report**

---

**Project:** {project_name}
**Framework:** {framework} {version} (confidence: {level})
**Repository:** {repo_path}
**Site:** {site_url}

---"

**IF blockers exist:**

"**BLOCKERS — Must resolve before proceeding:**

| # | Issue | Fix |
|---|-------|-----|
| 1 | {blocker description} | {actionable fix instruction} |
| 2 | ... | ... |

---"

**IF warnings exist:**

"**WARNINGS — Consolidation proceeds but with limitations:**

| # | Issue | Impact | Fix |
|---|-------|--------|-----|
| 1 | {warning description} | {which phase impacted} | {fix instruction} |
| 2 | ... | ... | ... |

---"

**IF info items exist:**

"**INFO — Optional improvements:**

| # | Note | Impact |
|---|------|--------|
| 1 | {info description} | {what capability is reduced} |
| 2 | ... | ... |

---"

### 4. Deliver Go/No-Go Decision

**IF zero blockers:**

"**DECISION: GO**

Your environment is ready for consolidation. {X} warnings noted — these affect specific phases but don't prevent starting.

**Next step:** Use `[AU]` to launch the full project Audit. Conrad and his team will analyze your code and navigate your site to build a comprehensive audit report.

Pre-flight check complete."

**IF blockers exist:**

"**DECISION: NO-GO**

**{X} blocker(s)** must be resolved before consolidation can begin. See the fix instructions above.

Once resolved, re-run the pre-flight check to verify your environment.

Pre-flight check complete — awaiting blocker resolution."

### 5. End of Workflow

This is the final step. No next step file to load. The workflow ends here.

The developer will return to Conrad's main menu where they can:
- Re-run pre-flight (if blockers were fixed)
- Launch Audit with `[AU]` (if go)
- Check status with `[ST]`

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Report is clear, visual, and well-organized
- All findings classified correctly (BLOCKER/WARNING/INFO)
- Go/no-go decision based strictly on blocker presence
- If go: clear next step provided ([AU])
- If no-go: actionable fix instructions for every blocker
- Developer knows exactly where they stand

### ❌ SYSTEM FAILURE:

- Softening or hiding blockers
- Making subjective go/no-go decisions (e.g., "mostly ready")
- Running additional checks in this step
- Modifying files in this step
- Not providing actionable fix instructions for blockers
- Vague or generic recommendations

**Master Rule:** Blockers = no-go. No exceptions. Be clear, be actionable, be honest.
