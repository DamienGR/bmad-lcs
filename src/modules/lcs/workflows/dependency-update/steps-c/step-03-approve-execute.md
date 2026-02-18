---
name: 'step-03-approve-execute'
description: 'Get developer approval for update plan, then execute updates atomically with safety net validation and circuit breaker'

nextStepFile: './step-04-report.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
reportOutputPath: '{output_folder}/lcs/dependency-update-report.md'
rollbackStrategies: '../data/rollback-strategies.md'
---

# Step 3: Approve & Execute Updates

## STEP GOAL:

To present the update plan for developer approval, then execute approved updates atomically — one package at a time with git commit, safety net validation, and surgical rollback on failure. Stop automatically after 3 consecutive failures (circuit breaker).

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER execute updates without developer approval
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You are executing the most critical phase — precision and safety are paramount
- ✅ The developer has final authority on approval and rollback decisions
- ✅ Be transparent about every success and failure

### Step-Specific Rules:

- 🎯 APPROVAL GATE: Do not execute until developer explicitly approves
- 🎯 ATOMIC EXECUTION: One package at a time — commit, update, validate, commit or revert
- 🛑 CIRCUIT BREAKER: After 3 consecutive failures → STOP and consult developer
- 🚫 FORBIDDEN to skip safety net validation after any update
- 🚫 FORBIDDEN to batch-update multiple packages in a single operation
- 💬 Report each update result immediately (success or failure)

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update `{reportOutputPath}` tables after each package update
- 📖 Update `{sidecarStatePath}` after each package update
- 🛑 HALT on circuit breaker trigger — wait for developer decision

## CONTEXT BOUNDARIES:

- Available: complete update plan with batches, rollback strategy, risk assessments (from step 02)
- Focus: approval + atomic execution + validation
- Limits: one package at a time, never skip validation
- Dependencies: step-02 plan must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Update Sidecar State and Present Approval

"**Le plan de mise à jour est prêt. Comment souhaitez-vous procéder ?**

**Options d'approbation :**

**[A] Approuver tout** — Exécuter tous les batches dans l'ordre (critique → haute → moyenne → maintenance)

**[B] Sélectionner par batch** — Choisir quels batches exécuter
- Batch 1 (critique) : {N} packages
- Batch 2 (haute) : {N} packages
- Batch 3 (moyenne) : {N} packages
- Batch 4 (maintenance) : {N} packages

**[S] Sélectionner individuellement** — Choisir package par package

**[X] Annuler** — Ne rien mettre à jour

Votre choix ?"

**Wait for developer response.**

#### Approval Handling:

- **IF A:** Mark all packages as approved, proceed to execution
- **IF B:** Present batch selection, mark selected batches as approved, proceed to execution
- **IF S:** Present individual package list with checkboxes, mark selected as approved, proceed to execution
- **IF X:** Mark all as skipped, update sidecar-state, skip to step summary

### 3. Confirm Before Execution

"**Récapitulatif de l'approbation :**

- **Packages approuvés :** {N}
- **Packages exclus/ignorés :** {N}
- **Stratégie :** Git commit par update, `git revert` si échec
- **Circuit breaker :** Arrêt après 3 échecs consécutifs

**⚠️ L'exécution va commencer. Chaque mise à jour sera validée par les safety nets.**

**Confirmer ?** [OK] Lancer / [X] Annuler"

**Wait for confirmation.** If cancelled, return to approval options.

### 4. Update Sidecar State for Execution

Update `{sidecarStatePath}`:
- Set dependency-update task `approval` to `done`
- Set dependency-update task `execution` to `in_progress`

### 5. Execute Updates — Atomic Loop

**Initialize counters:**
- `consecutive_failures = 0`
- `total_applied = 0`
- `total_failed = 0`
- `total_skipped = 0`

**For each approved package (in priority order):**

#### 5a. Pre-Update Commit

Stage and commit manifest + lock files as rollback point: `git commit -m "pre-update: {package_name}"`
If nothing to commit (clean state), the rollback will use the previous commit.

#### 5b. Execute Package Update

Run the update command from `{rollbackStrategies}` for the detected package manager.

**If update command fails:** revert files, mark "failed — install error", increment `consecutive_failures`, skip to 5e.

#### 5c. Run Safety Nets

Execute the safety net test suite (Playwright MCP if available, otherwise CLI).

**If ALL GREEN:** commit updated files with `git commit -m "update: {package_name} {old} → {new}"`, mark "applied", reset `consecutive_failures = 0`.
- Increment `total_applied`

"✅ **{package_name}** {old_version} → {new_version} — Safety nets vertes"

**If ANY RED:**

```
git revert HEAD --no-edit
```

- Mark package as "failed — safety net breakage"
- Increment `consecutive_failures`
- Increment `total_failed`

"❌ **{package_name}** {old_version} → {new_version} — Safety nets rouges → Rollback effectué
- Tests échoués : {list failed tests}"

#### 5d. Update Report

Append the update result to `{reportOutputPath}`:
- Add row to "Updates Applied" table (if success)
- Add row to "Failures & Rollbacks" table (if failure)

#### 5e. Circuit Breaker Check

**If `consecutive_failures >= 3`:**

"🛑 **CIRCUIT BREAKER — 3 échecs consécutifs détectés.**

**Packages échoués :**
1. {package_1} — {reason}
2. {package_2} — {reason}
3. {package_3} — {reason}

**Options :**
- **[R] Reprendre** — Continuer avec les packages restants (reset du compteur)
- **[S] Sauter le batch** — Passer au batch suivant
- **[X] Arrêter** — Terminer l'exécution et générer le rapport

Votre choix ?"

**Wait for developer decision.**

- **IF R:** Reset `consecutive_failures = 0`, continue loop
- **IF S:** Skip remaining packages in current batch, move to next batch, reset counter
- **IF X:** Exit loop, proceed to summary

#### 5f. Progress Report (After Each Batch)

After completing a batch, present progress:

"**Batch {N} terminé :**
- Appliqués : {count} ✅
- Échoués : {count} ❌
- Restants : {count} packages dans les batches suivants

**Continuer avec le batch suivant ?** [C] Continuer / [X] Arrêter"

**Wait for developer response.**

### 6. Execution Summary

"**Exécution terminée.**

**Résultats :**
- ✅ Appliqués : {total_applied}
- ❌ Échoués (rollback) : {total_failed}
- ⏭️ Ignorés : {total_skipped}

**Safety nets finales :** {GREEN/RED — run final check}

{If any failures: 'Les packages échoués sont documentés dans le rapport pour review manuelle.'}
{If all green: '✅ Toutes les mises à jour ont été appliquées avec succès. Safety nets vertes.'}"

### 7. Update Sidecar State and Report

Update `{sidecarStatePath}`:
- Set dependency-update task `execution` to `done`

Update `{reportOutputPath}` frontmatter:
- Append `'step-03-approve-execute'` to `stepsCompleted`
- Update `updatedCount`, `failedCount`, `skippedCount`

### 8. Proceed to Report

Display: **[C] Continuer vers la génération du rapport**

#### Menu Handling Logic:

- IF C: Update `{sidecarStatePath}` execution task to done, update `{reportOutputPath}` frontmatter with step-03-approve-execute in stepsCompleted, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Approval obtained, atomic execution (one package per cycle), safety nets after each, rollback on failure, circuit breaker on 3 failures, report updated in real-time

### ❌ SYSTEM FAILURE:

- Executing without approval, batch-updating, skipping validation, not rolling back, ignoring circuit breaker

**Master Rule:** ATOMIC EXECUTION — one package, one commit, one validation. Roll back on failure. Stop on 3 consecutive failures.
