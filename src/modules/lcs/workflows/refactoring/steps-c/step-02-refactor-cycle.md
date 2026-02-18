---
name: 'step-02-refactor-cycle'
description: 'Execute the per-target refactoring loop: worktree creation, Refactorer spawn, validation, user approval, merge/rollback'

nextStepFile: './step-03-report.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
refactoringReportPath: '{output_folder}/lcs/refactoring-report.md'
safetyNetsReportPath: '{output_folder}/lcs/safety-nets/safety-nets-report.md'
spawnPromptRefactorer: '../data/spawn-prompt-refactorer.md'
cleanupProtocol: '../data/cleanup-protocol.md'
cycleOutcomes: '../data/cycle-outcomes.md'
refactorerWorkflow: '../teammates/refactorer-workflow.md'
frameworksPath: '{project-root}/src/modules/lcs/data/frameworks/'
---

# Step 2: Refactoring Cycle

## STEP GOAL:

To execute the per-target refactoring loop: for each prioritized target, create an isolated worktree, spawn the Refactorer teammate, validate against safety nets, present results for user approval, and merge or rollback — one target at a time, atomically.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the baseline check before any cycle
- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — orchestrating surgical refactoring
- ✅ Atomic discipline: ONE target at a time, validate, get approval, merge or rollback
- ✅ The developer makes the final call on every merge — you present, they decide
- ✅ Be factual and concise — report clearly what happened and what the options are

### Step-Specific Rules:

- 🎯 Focus ONLY on the refactoring cycle — do not generate the final report (that's step 03)
- 🚫 FORBIDDEN to merge without explicit user approval
- 🚫 FORBIDDEN to skip baseline check or cleanup
- 💬 Present refactoring results using the template from `{cycleOutcomes}`
- 📋 Update sidecar state and report after EVERY cycle outcome
- 🎯 Use subprocess for baseline check (Pattern 1) — run full safety net suite, return pass/fail

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly — it is a loop
- 💾 Update `{sidecarStatePath}` after every cycle outcome
- 💾 Append target result to `{refactoringReportPath}` after every cycle outcome
- 📖 Track: currentCycle, completedTargets, remainingTargets, baselineGreen
- 🚫 NEVER leave an orphan worktree — cleanup is mandatory on every exit path

## CONTEXT BOUNDARIES:

- Available: step-01 completed — prioritized targets in sidecar-state, framework profile loaded
- Focus: per-target refactoring cycle only
- Limits: do not generate final report, do not re-prioritize targets
- Dependencies: sidecar-state has remainingTargets, safety nets are green

## MANDATORY SEQUENCE

**CRITICAL:** This is a LOOP. Execute sections 1-10 for each target. After all targets processed, proceed to section 11.

---

### 0. Read Current State

Read `{sidecarStatePath}` and load: `remainingTargets`, `completedTargets`, `baselineGreen`.

**If `remainingTargets` is empty:** "**Toutes les cibles ont été traitées.**" → Load, read entirely, then execute `{nextStepFile}`

**Otherwise:** "**Cycle de refactoring — {N} cible(s) restante(s)**" — take the FIRST target as current.

---

### ── BEGIN LOOP (per target) ──

### 1. Baseline Check

**Run the FULL safety net suite** (as defined in `{safetyNetsReportPath}`) to verify green baseline.

Launch a subprocess that executes the test suite and returns pass/fail. If subprocess unavailable: execute in main thread.

- **GREEN:** "✅ **Baseline verte.** Cycle pour `{current_target}` autorisé." → Update `baselineGreen: true` in `{sidecarStatePath}`
- **RED:** "🛑 **STOP — Baseline rouge.**" → Present failure details. **HALT** — user must fix and restart.

### 2. Mini Re-Assessment

Check if current target is still relevant after previous refactorings (modified by prior merge? complexity changed?).

- **Still relevant:** "✅ `{current_target}` — toujours pertinent. Complexité : {complexity}."
- **Obsolete:** "⚠️ `{current_target}` — impacté par un refactoring précédent."
  - "[C] Continuer quand même / [S] Skip" — if Skip: flag `obsolete`, update sidecar + report, **go to section 9**

### 3. Create Worktree

Create a fresh git worktree: branch `lcs-refactor/{target-slug}`, path `{repo_path}/.lcs-worktrees/refactor-{target-slug}`.

- Run `git worktree add {worktree_path} -b {branch_name}`
- If branch already exists (from interrupted session): ask user to reuse or recreate
- Update `{sidecarStatePath}`: set `currentCycle` with target, worktreePath, branchName, status: `worktree-created`, retryCount: 0

"**Worktree créé :** `{worktree_path}` (branche: `{branch_name}`)"

### 4. Spawn Refactorer

Read `{spawnPromptRefactorer}` and inject: target path, complexity, risk score, recommended refactoring, framework profile from `{frameworksPath}`, worktree path. Update sidecar: `status: "spawning"`.

**If sub-agent available:** Spawn Refactorer. If spawn fails → retry once. If retry fails → cleanup (load `{cleanupProtocol}`), flag `spawn-failed`, **go to section 9**.

**If sub-agent NOT available:** "Exécution séquentielle." → Load and execute `{refactorerWorkflow}` with injected context.

Update sidecar: `status: "refactoring"`. "**Refactorer lancé** sur `{current_target}`."

### 5. Wait for Completion

**Sub-agent:** Monitor for Refactorer's output artifact (summary file in worktree).
**Sequential:** Completion immediate after section 4.

Update sidecar: `status: "validating"`

### 6. Run Safety Nets in Worktree

**Execute the FULL safety net suite** (as defined in `{safetyNetsReportPath}`) **in the worktree context** (not main).

Launch subprocess from worktree directory, returns: pass/fail, failure details, baseline comparison. If subprocess unavailable: execute in main thread.

- **ALL PASS:** "✅ **Safety nets vertes dans le worktree.**" → Proceed to section 7
- **FAILURES:** Follow the Retry Correction Protocol from `{cycleOutcomes}`: retry once with failure context. If PASS after retry → section 7. If STILL FAILING → cleanup (load `{cleanupProtocol}`), flag `failed`, **go to section 9**.

### 7. Present Results for Approval

Load the Results Presentation Template from `{cycleOutcomes}`. Present the Refactorer's summary with before/after context, refactoring type, files modified, safety net status.

Display: **[M] Merger / [R] Refactoring alternatif / [S] Skip (rollback)**

#### Menu Handling Logic:

- IF M: Proceed to section 8a (Merge)
- IF R: Proceed to section 8b (Alternative refactoring)
- IF S: Proceed to section 8c (Skip/Rollback)
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input
- NEVER merge without explicit 'M' from user

### 8a. Merge

Follow the Merge Protocol from `{cycleOutcomes}`: switch to main, merge branch, verify no conflicts (assist user if any), cleanup (load `{cleanupProtocol}`), flag `merged`, update `{sidecarStatePath}` + `{refactoringReportPath}`.

"✅ **Merge effectué.** `{current_target}` refactorisé et intégré." → **Go to section 10**

### 8b. Alternative Refactoring

Follow the Alternative Refactoring Protocol from `{cycleOutcomes}`: collect user feedback, reset worktree (`git checkout .`), re-execute from section 4 with alternative instructions. This is a new attempt, NOT a retry.

### 8c. Skip (Rollback)

Follow the Skip Protocol from `{cycleOutcomes}`: cleanup (load `{cleanupProtocol}`), flag `rejected`, update `{sidecarStatePath}` + `{refactoringReportPath}`.

"⏭️ **Skip.** `{current_target}` — rollback effectué." → **Go to section 10**

### 9. Update State (non-merge outcomes)

Follow the Non-Merge State Update from `{cycleOutcomes}`: move target to completedTargets with status flag, clear currentCycle, append to `{refactoringReportPath}`, update Metrics. **Go to section 10**

### 10. Next Target

Load the Progress Presentation Template from `{cycleOutcomes}`. Present cycle status with metrics table.

**If more targets in `remainingTargets`:**

Display: **[C] Continuer vers la prochaine cible / [X] Arrêter et reprendre plus tard**

- IF C: **Return to section 1** (Baseline Check) with next target
- IF X: Update sidecar-state + `{refactoringReportPath}` frontmatter. "**Session sauvegardée.**" → **HALT**

**If `remainingTargets` is empty:** "**Toutes les cibles ont été traitées !**" → **Go to section 11**

### ── END LOOP ──

---

### 11. Proceed to Report

Update `{refactoringReportPath}` frontmatter: append `step-02-refactor-cycle` to `stepsCompleted`, set `lastStep`.

"**Tous les cycles terminés. Passage au rapport final.**"

Load, read entirely, then execute `{nextStepFile}`

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Baseline verified GREEN before every cycle
- Target relevance re-assessed before every cycle
- Fresh worktree created and cleaned for every target
- Refactorer spawned with full injected context
- Safety nets run in worktree (not on main)
- Retry mechanism applied correctly (1 attempt)
- Results presented clearly with before/after
- User approval obtained before every merge
- Cleanup executed on EVERY exit path (no orphans)
- Sidecar state updated after every cycle outcome
- Report updated progressively after every cycle outcome
- Session pause/resume supported via [X] option

### ❌ SYSTEM FAILURE:

- Skipping baseline check
- Merging without user approval
- Leaving orphan worktrees or branches
- Running safety nets on main instead of worktree
- Not updating sidecar state after a cycle
- Not appending to report after a cycle
- Allowing more than 1 retry before rollback
- Modifying code directly (that's the Refactorer's job)
- Not presenting clear before/after results

**Master Rule:** ATOMIC discipline. One target, one worktree, one approval, one merge. Cleanup on EVERY exit path. No exceptions.
