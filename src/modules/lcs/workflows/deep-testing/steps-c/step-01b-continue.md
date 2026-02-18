---
name: 'step-01b-continue'
description: 'Resume interrupted deep-testing workflow by reading sidecar state, verifying baseline, and routing to the correct step'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
deepTestingReportPath: '{output_folder}/lcs/deep-testing-report.md'
safetyNetsReportPath: '{output_folder}/lcs/safety-nets-report.md'
step02File: './step-02-test-cycle.md'
step03File: './step-03-discovery-integration.md'
step04File: './step-04-report.md'
---

# Step 1b: Resume Deep Testing Workflow

## STEP GOAL:

To resume an interrupted deep-testing workflow by reading zone statuses from sidecar-state.yaml, re-verifying the safety nets baseline, presenting the current state to the user, and routing to the correct step.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the status assessment
- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — resuming an interrupted deep-testing session
- ✅ Be clear about what was completed and what needs to be done
- ✅ The developer needs to understand the current state quickly

### Step-Specific Rules:

- 🎯 Focus ONLY on status assessment, baseline verification, and routing
- 🚫 FORBIDDEN to write tests, spawn teammates, or run test suites
- 💬 Present a clear status table before routing

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update sidecar-state with cleaned statuses if needed

## CONTEXT BOUNDARIES:

- Previous deep-testing session was interrupted
- sidecar-state.yaml contains zone state from the previous session
- Need to determine exactly where to resume

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Read Current State

Read `{sidecarStatePath}` and extract deep-testing state:

- `currentZone`: {name, status, retryCount}
- `completedZones`: [{name, status, testsWritten}...]
- `remainingZones`: [...]
- `baselineGreen`: {true/false}

Also read `{deepTestingReportPath}` frontmatter for `stepsCompleted`.

### 2. Welcome Back & Present Status

"**Bon retour ! Voici l'état du deep testing :**

**Zones traitées : {N}/{total}**

| Zone | Statut | Tests écrits |
|------|--------|-------------|
{table of completedZones with status and test counts}

**Zones restantes :** {N}
{brief list of remaining zones}

**Zone interrompue :** {currentZone details or 'Aucune'}
**Baseline safety nets :** {green/unknown — will re-verify}"

### 3. Handle Interrupted Zone

**If `currentZone` is not null (interrupted mid-zone):**

Depending on `currentZone.status`:

- **spawning / testing:** The Deep Tester may have partially completed. Check if test files were written.
  - If partial test files exist → "⚠️ **Zone partiellement traitée :** `{currentZone.name}`. Des fichiers de test partiels peuvent exister. La zone sera re-traitée."
  - Reset `currentZone` to null
  - Move the interrupted zone back to the **front** of `remainingZones`

- **validating / awaiting-approval:** Tests were written but not yet validated/approved.
  - "⚠️ **Zone en attente :** `{currentZone.name}` — tests écrits, validation/approbation en attente."
  - Keep zone in `currentZone` for step-02 to resume validation

Update `{sidecarStatePath}` with cleaned state.

### 4. Verify Safety Nets Baseline

Before resuming, verify that safety nets are still green:
- Check `{safetyNetsReportPath}` baseline status
- If baseline not green → ERROR: "⚠️ Les safety nets ne sont plus vertes depuis la dernière session. Corrigez les échecs avant de reprendre le deep testing."
- If green → update `baselineGreen: true` in sidecar-state

### 5. Route to Correct Step

**Case A: Zones remaining (or interrupted zone to resume)**
IF `remainingZones` is non-empty OR `currentZone` is not null:
- "**{N} zones restantes.** Reprise du cycle de test."
- Load, read entirely, then execute `{step02File}`

**Case B: All zones processed, discoveries not yet integrated**
IF `remainingZones` is empty AND `currentZone` is null AND 'step-03-discovery-integration' NOT in stepsCompleted:
- "**Toutes les zones ont été traitées.** Intégration des découvertes."
- Load, read entirely, then execute `{step03File}`

**Case C: All zones processed, discoveries integrated**
IF `remainingZones` is empty AND `currentZone` is null AND 'step-03-discovery-integration' in stepsCompleted:
- "**Toutes les zones traitées et découvertes intégrées.** Finalisation du rapport."
- Load, read entirely, then execute `{step04File}`

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Sidecar state read and understood
- Clear status table presented to user
- Interrupted zone detected and handled appropriately
- Safety nets baseline re-verified
- Correct routing to step-02 (more zones), step-03 (discovery integration), or step-04 (report)

### ❌ SYSTEM FAILURE:

- Not detecting interrupted zone state
- Routing to the wrong step
- Skipping the status presentation
- Not re-verifying safety nets baseline
- Resuming with stale state

**Master Rule:** Always verify baseline and present clean state before resuming. Route based on actual progress, not assumptions.
