---
name: 'step-01b-continue'
description: 'Resume interrupted audit by reading task statuses and routing to the appropriate step'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
step02File: './step-02-dep-scan-monitor.md'
step03File: './step-03-synthesize.md'
step04File: './step-04-batched-qa.md'
step05File: './step-05-risk-map-trigger.md'
spawnPromptCodeAnalyst: '../data/spawn-prompt-code-analyst.md'
spawnPromptSiteExplorer: '../data/spawn-prompt-site-explorer.md'
---

# Step 1b: Resume Audit

## STEP GOAL:

To resume an interrupted audit by reading task statuses from sidecar-state.yaml, resetting any failed/in-progress tasks, archiving partial artifacts, and routing to the correct step.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the status assessment
- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — resuming an interrupted audit
- ✅ Be clear about what was completed and what needs to be redone
- ✅ The developer needs to understand the current state quickly

### Step-Specific Rules:

- 🎯 Focus ONLY on status assessment, cleanup, and routing
- 🚫 FORBIDDEN to start any analysis or synthesis
- 💬 Present a clear status table before routing
- 📋 Reset `in_progress` and `failed` tasks to `pending`

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update sidecar-state with cleaned statuses
- 📖 Archive any partial artifacts before resetting

## CONTEXT BOUNDARIES:

- Previous audit session was interrupted
- sidecar-state.yaml contains task statuses from the previous session
- Some artifacts may be partial or complete
- Need to determine exactly where to resume

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Read Current State

Read `{sidecarStatePath}` and extract all audit task statuses:

- `code-analysis`: {status}
- `site-exploration`: {status}
- `dependency-scan`: {status}
- `audit-synthesis`: {status}
- `batched-qa`: {status}
- `risk-map`: {status}

### 2. Welcome Back & Present Status

"**Bon retour ! Voici l'état de l'audit :**

| Task | Statut | Artefact |
|------|--------|----------|
| Code Analysis | {status} | {exists/missing} |
| Site Exploration | {status} | {exists/missing} |
| Dependency Scan | {status} | {exists/missing} |
| Audit Synthesis | {status} | {exists/missing} |
| Batched Q&A | {status} | {exists/missing} |
| Risk Map | {status} | {exists/missing} |"

### 3. Reset Failed/In-Progress Tasks

For each task with status `in_progress` or `failed`:
1. Reset status to `pending` in sidecar-state
2. If the task's artifact file exists, archive it: rename to `{filename}-{timestamp}.md`
3. Note the reset to the user

Update `{sidecarStatePath}` with cleaned statuses.

"**Nettoyage effectué :** {N} task(s) réinitialisée(s). Les artefacts partiels ont été archivés."

### 4. Route to Correct Step

**Routing Table (based on CLEANED task statuses):**

**Case A: Teammates need (re)spawning**
IF `code-analysis: pending` OR `site-exploration: pending`:
- Re-spawn the pending teammate(s) using spawn prompts
- Load `{spawnPromptCodeAnalyst}` and/or `{spawnPromptSiteExplorer}` as needed
- Inject variables from sidecar-state (same as step 01)
- After spawn → load, read entirely, then execute `{step02File}`

**Case B: All 3 inputs ready, synthesis pending**
IF `code-analysis: done` AND `site-exploration: done` AND `dependency-scan: done` AND `audit-synthesis: pending`:
- "Les 3 analyses sont complètes. Reprise à la synthèse."
- Load, read entirely, then execute `{step03File}`

**Case C: Synthesis done, Q&A pending**
IF `audit-synthesis: done` AND `batched-qa: pending`:
- "Le rapport d'audit est prêt. Reprise au Q&A batché."
- Load, read entirely, then execute `{step04File}`

**Case D: Q&A done, risk map pending**
IF `batched-qa: done` AND `risk-map: pending`:
- "L'audit est presque terminé. Reprise au déclenchement du Risk Map."
- Load, read entirely, then execute `{step05File}`

**Case E: Dependency scan pending + teammates done**
IF `code-analysis: done` AND `site-exploration: done` AND `dependency-scan: pending`:
- "Les teammates ont terminé. Reprise au scan de dépendances."
- Load, read entirely, then execute `{step02File}`

**Case F: All pending (full reset)**
IF all audit tasks are `pending`:
- "Audit complètement réinitialisé. Reprise depuis le début."
- Route back to step-01-init-spawn logic (spawn teammates again)

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All task statuses read from sidecar-state
- Clear status table presented to user
- Failed/in-progress tasks reset to pending
- Partial artifacts archived
- Correct routing to the appropriate step

### ❌ SYSTEM FAILURE:

- Not resetting in_progress/failed tasks
- Not archiving partial artifacts before reset
- Routing to the wrong step
- Skipping the status presentation

**Master Rule:** Always reset stale states and archive partial work before resuming. Route based on the CLEANED state, not the raw state.
