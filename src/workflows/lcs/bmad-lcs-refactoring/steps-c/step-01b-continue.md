---
name: 'step-01b-continue'
description: 'Resume interrupted refactoring workflow by reading sidecar state, cleaning orphan worktrees, and routing to the correct step'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
refactoringReportPath: '{output_folder}/lcs/refactoring-report.md'
safetyNetsReportPath: '{output_folder}/lcs/safety-nets/safety-nets-report.md'
cleanupProtocol: '../data/cleanup-protocol.md'
step02File: './step-02-refactor-cycle.md'
step03File: './step-03-report.md'
---

# Step 1b: Resume Refactoring Workflow

## STEP GOAL:

To resume an interrupted refactoring workflow by reading task statuses from sidecar-state.yaml, detecting and cleaning orphan worktrees, presenting the current state to the user, and routing to the correct step.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the status assessment
- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — resuming an interrupted refactoring session
- ✅ Be clear about what was completed and what needs to be done
- ✅ The developer needs to understand the current state quickly

### Step-Specific Rules:

- 🎯 Focus ONLY on status assessment, orphan cleanup, and routing
- 🚫 FORBIDDEN to start any refactoring, worktree creation, or code modification
- 💬 Present a clear status table before routing
- 📋 Detect and clean orphan worktrees before resuming

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update sidecar-state with cleaned statuses
- 📖 Archive any partial artifacts before resetting

## CONTEXT BOUNDARIES:

- Previous refactoring session was interrupted
- sidecar-state.yaml contains cycle state from the previous session
- An orphan worktree may exist from an interrupted cycle
- Need to determine exactly where to resume

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Read Current State

Read `{sidecarStatePath}` and extract refactoring state:

- `currentCycle`: {target, worktreePath, branchName, status, retryCount}
- `completedTargets`: [{target, status}...]
- `remainingTargets`: [...]
- `baselineGreen`: {true/false}

Also read `{refactoringReportPath}` frontmatter for `stepsCompleted`.

### 2. Welcome Back & Present Status

"**Bon retour ! Voici l'état du refactoring :**

**Cibles traitées : {N}/{total}**

| Cible | Statut |
|-------|--------|
{table of completedTargets with status flags}

**Cibles restantes :** {N}
{brief list of remaining targets}

**Cycle interrompu :** {currentCycle details or 'Aucun'}
**Baseline safety nets :** {green/unknown — will re-verify}"

### 3. Detect and Clean Orphan Worktrees

**If `currentCycle` is not null (interrupted mid-cycle):**

Load `{cleanupProtocol}` for cleanup instructions.

1. Check if worktree at `currentCycle.worktreePath` still exists (via Git MCP or filesystem check)
2. Check if branch `currentCycle.branchName` still exists

**If orphan worktree found:**
- "⚠️ **Worktree orphelin détecté :** `{currentCycle.worktreePath}` (branche: `{currentCycle.branchName}`)"
- "Le cycle sur `{currentCycle.target}` a été interrompu au statut `{currentCycle.status}`."
- Execute cleanup: remove worktree, delete temporary branch
- Reset `currentCycle` to null in sidecar-state
- Move the interrupted target back to the **front** of `remainingTargets`
- "✅ Nettoyage effectué. La cible `{target}` sera re-traitée en priorité."

**If no orphan worktree:**
- "✅ Pas de worktree orphelin détecté."

Update `{sidecarStatePath}` with cleaned state.

### 4. Verify Safety Nets Baseline

Before resuming, verify that safety nets are still green:
- Check `{safetyNetsReportPath}` baseline status
- If baseline not green → ERROR: "⚠️ Les safety nets ne sont plus vertes depuis la dernière session. Corrigez les échecs avant de reprendre le refactoring."
- If green → update `baselineGreen: true` in sidecar-state

### 5. Route to Correct Step

**Case A: Targets remaining**
IF `remainingTargets` is non-empty:
- "**{N} cibles restantes.** Reprise du cycle de refactoring."
- Load, read entirely, then execute `{step02File}`

**Case B: All targets processed**
IF `remainingTargets` is empty AND `currentCycle` is null:
- "**Toutes les cibles ont été traitées.** Génération du rapport final."
- Load, read entirely, then execute `{step03File}`

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Sidecar state read and understood
- Clear status table presented to user
- Orphan worktrees detected and cleaned
- Interrupted target moved back to front of remaining list
- Safety nets baseline re-verified
- Correct routing to step-02 (more targets) or step-03 (all done)

### ❌ SYSTEM FAILURE:

- Not detecting orphan worktrees
- Not cleaning up orphan worktrees and branches
- Routing to the wrong step
- Skipping the status presentation
- Not re-verifying safety nets baseline
- Resuming with stale/dirty state

**Master Rule:** Always clean orphan state and re-verify baseline before resuming. Route based on the CLEANED state, not the raw state.
