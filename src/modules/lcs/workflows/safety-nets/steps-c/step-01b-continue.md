---
name: 'step-01b-continue'
description: 'Resume interrupted safety-nets deployment by reading task statuses and routing to the appropriate step'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
step02File: './step-02-spawn.md'
step03File: './step-03-monitor.md'
step04File: './step-04-validate-triage.md'
step05File: './step-05-discovery-integration.md'
step06File: './step-06-merge-report.md'
spawnPromptSafetyNetBuilder: '../data/spawn-prompt-safety-net-builder.md'
spawnPromptStaticGuard: '../data/spawn-prompt-static-guard.md'
---

# Step 1b: Resume Safety Nets Deployment

## STEP GOAL:

To resume an interrupted safety-nets deployment by reading task statuses from sidecar-state.yaml, resetting any failed/in-progress tasks, archiving partial artifacts, and routing to the correct step.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the status assessment
- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — resuming an interrupted safety-nets deployment
- ✅ Be clear about what was completed and what needs to be redone
- ✅ The developer needs to understand the current state quickly

### Step-Specific Rules:

- 🎯 Focus ONLY on status assessment, cleanup, and routing
- 🚫 FORBIDDEN to start any test writing, config, or analysis
- 💬 Present a clear status table before routing
- 📋 Reset `in_progress` and `failed` tasks to `pending`

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update sidecar-state with cleaned statuses
- 📖 Archive any partial artifacts before resetting

## CONTEXT BOUNDARIES:

- Previous safety-nets session was interrupted
- sidecar-state.yaml contains task statuses from the previous session
- Some teammate artifacts may be partial or complete
- Need to determine exactly where to resume

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Read Current State

Read `{sidecarStatePath}` and extract all safety-nets task statuses:

- `safety-net-builder`: {status}
- `static-guard`: {status}
- `validation`: {status}
- `discovery-integration`: {status}
- `merge-report`: {status}

Also read stored context:
- `fixture_strategy`: {value}
- `prioritized_zones`: {value}
- `current_branch`: {value — e.g., lcs/pre-safety-nets}

### 2. Welcome Back & Present Status

"**Bon retour ! Voici l'état du déploiement des safety nets :**

| Task | Statut | Artefact |
|------|--------|----------|
| Safety Net Builder | {status} | {exists/missing} |
| Static Guard | {status} | {exists/missing} |
| Validation | {status} | {exists/missing} |
| Discovery Integration | {status} | {exists/missing} |
| Merge + Report | {status} | {exists/missing} |

**Branche de sécurité :** {branch name — exists/missing}
**Stratégie fixtures :** {fixture_strategy}
**Zones prioritaires :** {count} zones"

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
IF `safety-net-builder: pending` OR `static-guard: pending`:
- Check if safety branch exists (git branch check)
- If branch missing → route to `{step02File}` (create branch + spawn)
- If branch exists → re-spawn only the pending teammate(s)
  - Load `{spawnPromptSafetyNetBuilder}` and/or `{spawnPromptStaticGuard}` as needed
  - After spawn → load, read entirely, then execute `{step03File}`

**Case B: Both teammates done, validation pending**
IF `safety-net-builder: done` AND `static-guard: done` AND `validation: pending`:
- "Les deux teammates ont terminé. Reprise à la validation."
- Load, read entirely, then execute `{step04File}`

**Case C: Validation done, discovery integration pending**
IF `validation: done` AND `discovery-integration: pending`:
- "La validation est terminée. Reprise à l'intégration des découvertes."
- Load, read entirely, then execute `{step05File}`

**Case D: Discovery integration done, merge + report pending**
IF `discovery-integration: done` AND `merge-report: pending`:
- "Les découvertes sont intégrées. Reprise au merge et rapport."
- Load, read entirely, then execute `{step06File}`

**Case E: All pending (full reset)**
IF all safety-nets tasks are `pending`:
- "Safety nets complètement réinitialisé. Reprise depuis le début."
- Check if priorities and fixture strategy still stored in sidecar-state
  - If yes → route to `{step02File}` (skip re-prioritization)
  - If no → present message: "Les priorités et la stratégie de fixtures doivent être re-définies." Then continue with step-01 initialization sequence from section 4 onwards

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
- Not checking for safety branch existence

**Master Rule:** Always reset stale states and archive partial work before resuming. Route based on the CLEANED state, not the raw state.
