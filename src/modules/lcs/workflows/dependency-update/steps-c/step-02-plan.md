---
name: 'step-02-plan'
description: 'Propose a prioritized dependency update plan with risk assessment and batch grouping'

nextStepFile: './step-03-approve-execute.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
reportOutputPath: '{output_folder}/lcs/dependency-update-report.md'
rollbackStrategies: '../data/rollback-strategies.md'
---

# Step 2: Propose Update Plan

## STEP GOAL:

To sort dependency updates by severity, assess breaking change risk per package, group into logical batches, adapt rollback strategy to the detected package manager, and present the complete prioritized update plan to the developer.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER execute any updates — this step is planning only
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You are building the update plan — prioritization and risk assessment are key
- ✅ The developer will review and approve this plan in the next step
- ✅ Be factual, precise, and transparent about risks

### Step-Specific Rules:

- 🎯 Focus ONLY on planning and risk assessment
- 🚫 FORBIDDEN to execute any package updates — that's step 03
- 🚫 FORBIDDEN to ask for approval — that's step 03
- 💬 Present risks clearly so the developer can make informed decisions
- 📋 Load rollback strategies to adapt to the detected package manager

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store the complete plan in memory for step 03: prioritized_batches, rollback_strategy, risk_assessments
- 📖 Update sidecar-state task `plan` to `in_progress` at start, `done` at completion
- 🚫 Do NOT execute updates or ask for approval in this step

## CONTEXT BOUNDARIES:

- Available: dependency scan results, vulnerability data, outdated packages, package manager detected, exclusion list (from step 01)
- Focus: planning and risk assessment only
- Limits: do not update, do not ask for approval
- Dependencies: step-01 analysis must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Update Sidecar State

Update `{sidecarStatePath}`:
- Set dependency-update task `plan` to `in_progress`

### 2. Load Rollback Strategy

Load `{rollbackStrategies}` and identify the strategy matching the detected package manager (from step 01).

"**Stratégie de rollback sélectionnée pour {package_manager} :**
- Commande update : `{update_command}`
- Fichiers versionnés : `{manifest} + {lockfile}`
- Rollback : git commit par update → `git revert` chirurgical si échec
- Validation : `{validation_command}` + safety nets Playwright"

### 3. Sort by Severity

From the dependency data collected in step 01, sort all updates:

**Priority order:**
1. **Critical vulnerabilities** (CVSS ≥ 9.0) — security emergency
2. **High vulnerabilities** (CVSS 7.0-8.9) — security priority
3. **Medium vulnerabilities** (CVSS 4.0-6.9) — security improvement
4. **Low vulnerabilities** (CVSS < 4.0) — security housekeeping
5. **Outdated (no vulnerability)** — maintenance updates

### 4. Assess Breaking Change Risk

For each package, evaluate breaking change risk:

**Risk Factors:**
- **Major version bump** (e.g., 2.x → 3.x) → HIGH risk
- **Minor version bump** (e.g., 2.1 → 2.3) → MEDIUM risk
- **Patch version bump** (e.g., 2.1.0 → 2.1.5) → LOW risk
- **Known breaking changes** in changelog → flag explicitly
- **Deep dependency** (many packages depend on it) → higher risk
- **Framework core package** → HIGHEST risk (test thoroughly)

"**Évaluation des risques de breaking changes :**

| Package | Update | Type | Risque BC | Raison |
|---------|--------|------|-----------|--------|
{table for each package with risk assessment}"

### 5. Group into Batches

Group updates into logical batches for execution:

**Batch 1 — Sécurité critique** (CVSS ≥ 9.0)
- These MUST be updated — security emergency
- Execute first, one at a time
- {list packages}

**Batch 2 — Sécurité haute** (CVSS 7.0-8.9)
- Strong recommendation to update
- Execute one at a time
- {list packages}

**Batch 3 — Sécurité moyenne** (CVSS 4.0-6.9)
- Recommended but lower urgency
- Can be batched if low breaking change risk
- {list packages}

**Batch 4 — Maintenance** (low vuln + outdated, no vuln)
- Optional — developer decides
- Can be batched for low-risk patches
- {list packages}

**Excluded** (from step 01 exclusions)
- {list excluded packages with reasons}

### 6. Estimate Execution

"**Estimation de l'exécution :**

| Batch | Packages | Risque BC moyen | Temps estimé |
|-------|----------|-----------------|--------------|
| 1 — Critique | {N} | {risk} | {time} |
| 2 — Haute | {N} | {risk} | {time} |
| 3 — Moyenne | {N} | {risk} | {time} |
| 4 — Maintenance | {N} | {risk} | {time} |

**Total :** {N} packages à mettre à jour
**Circuit breaker :** Arrêt automatique après 3 échecs consécutifs"

### 7. Present Complete Plan

"**Plan de mise à jour des dépendances :**

---

**Package manager :** {detected}
**Stratégie rollback :** Git commit par update, `git revert` chirurgical
**Validation :** Safety nets après chaque update
**Circuit breaker :** 3 échecs consécutifs → STOP

---

**Batch 1 — Sécurité critique ({N} packages)**

| # | Package | De | Vers | CVSS | Risque BC |
|---|---------|----|----- |------|-----------|
{detailed table}

**Batch 2 — Sécurité haute ({N} packages)**

| # | Package | De | Vers | CVSS | Risque BC |
|---|---------|----|----- |------|-----------|
{detailed table}

**Batch 3 — Sécurité moyenne ({N} packages)**

| # | Package | De | Vers | CVSS | Risque BC |
|---|---------|----|----- |------|-----------|
{detailed table}

**Batch 4 — Maintenance ({N} packages)**

| # | Package | De | Vers | Retard | Risque BC |
|---|---------|----|----- |--------|-----------|
{detailed table}

**Exclus ({N} packages)**

| # | Package | Raison |
|---|---------|--------|
{table}

---

**Ce plan sera présenté pour approbation à l'étape suivante.**"

### 8. Update Sidecar State and Report

Update `{sidecarStatePath}`:
- Set dependency-update task `plan` to `done`

Update `{reportOutputPath}` frontmatter:
- Append `'step-02-plan'` to `stepsCompleted`

### 9. Proceed

Display: **[C] Continuer vers l'approbation et l'exécution**

#### Menu Handling Logic:

- IF C: Update `{sidecarStatePath}` plan task to done, update `{reportOutputPath}` frontmatter with step-02-plan in stepsCompleted, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Rollback strategy loaded and adapted to detected package manager
- All packages sorted by severity (CVSS descending)
- Breaking change risk assessed for each package
- Packages grouped into logical batches (critical → high → medium → maintenance)
- Excluded packages listed with reasons
- Execution estimate provided
- Complete plan presented clearly
- Sidecar-state updated (plan: done)
- Report frontmatter updated (stepsCompleted)
- Proceeding to step 03

### ❌ SYSTEM FAILURE:

- Executing any updates in this step (forbidden)
- Asking for approval in this step (forbidden)
- Not loading rollback strategies
- Not assessing breaking change risk
- Not grouping into batches
- Not presenting the complete plan
- Not updating sidecar-state

**Master Rule:** This is PLANNING ONLY. Present a complete, transparent plan with risk assessments. Do not execute anything. Do not ask for approval — that's step 03.
