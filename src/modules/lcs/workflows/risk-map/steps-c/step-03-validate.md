---
name: 'step-03-validate'
description: 'Developer validates risk ranking, applies business overrides, classifies UNCERTAIN zones, confirms top-10'

nextStepFile: './step-04-generate-recommend.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
---

# Step 3: Developer Validation

## STEP GOAL:

To present the automated risk ranking for developer validation, allow business criticality overrides based on domain knowledge, classify UNCERTAIN zones, and confirm the final top-10 priority list that will drive all subsequent LCS phases.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER finalize ranking without developer confirmation
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — collaborative risk advisor
- ✅ This is the KEY collaborative moment — developer brings business knowledge you don't have
- ✅ Present data clearly, explain reasoning, accept overrides without pushback
- ✅ Be supportive — the developer knows their project better than audit data alone

### Step-Specific Rules:

- 🎯 Focus ONLY on validation, overrides, and UNCERTAIN zone classification
- 🚫 FORBIDDEN to override developer's business priority decisions
- 🚫 FORBIDDEN to proceed without explicit top-10 confirmation
- 💬 Explain impact of overrides: "Remonter la criticité business de 3→5 fait passer {zone} de MEDIUM à HIGH"
- 📋 Bound all scores to 1-5 — reject out-of-range values

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store validated scores and overrides: validated_zones[], overrides{}, uncertain_resolved[]
- 📖 Track progress via sidecar-state
- 🚫 Do NOT generate output document yet — that's step 04

## CONTEXT BOUNDARIES:

- Available: scored_zones from step 02, heat map, top-10, UNCERTAIN zones
- Focus: developer validation and business overrides only
- Limits: do not re-score from scratch, do not generate output document
- Dependencies: step 02 scoring complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Present Current Ranking for Review

"**Validation de la risk map — votre expertise business est essentielle.**

Le scoring automatique est basé sur les données d'audit. Vous connaissez votre projet mieux que les métriques — ajustons ensemble.

**Classement actuel :**

| # | Zone L2 | Score | Tier | Business Crit. | Source |
|---|---------|-------|------|----------------|--------|
{numbered table, sorted by final_score descending}

**Légende :** [f] = valeur par défaut utilisée (donnée manquante)"

### 2. Business Criticality Overrides

"**Ajustements de criticité business**

La dimension 'Business Criticality' (poids 0.25) peut être ajustée pour chaque zone. C'est la dimension que vous connaissez le mieux.

Pour chaque zone, la criticité actuelle est basée sur l'exploration du site. Souhaitez-vous ajuster certaines zones ?

**Exemples d'ajustements courants :**
- Zone de paiement sous-évaluée → remonter à 5
- Page admin peu utilisée → descendre à 2
- Fonctionnalité en cours de dépréciation → descendre à 1

Indiquez les ajustements au format : `{zone} → {new_score}`
Ou **[OK]** si le classement business est correct."

**Wait for user response.**

- If user provides overrides:
  - Validate score range (1-5, reject out-of-range)
  - Recalculate `base_score` and `final_score` for affected zones
  - Re-sort and re-classify tiers
  - Present updated ranking with changes highlighted:
    "**Zone {name} :** business criticality {old}→{new}, score {old_final}→{new_final}, tier {old_tier}→{new_tier}"
  - Ask: "D'autres ajustements ? Ou **[OK]** pour confirmer."
- If user confirms → proceed to section 3

### 3. Classify UNCERTAIN Zones

If UNCERTAIN zones exist (ADR-005):

"**Zones UNCERTAIN ({N} zones)**

Ces zones n'ont pas pu être classées avec certitude pendant l'audit :

| Zone | Raison de l'incertitude | Score actuel | Suggestion |
|------|------------------------|-------------|------------|
{table of UNCERTAIN zones}

Pour chaque zone, vous pouvez :
- **Confirmer** le score actuel
- **Ajuster** une ou plusieurs dimensions
- **Marquer comme hors-scope** (exclue de la risk map)

Indiquez vos décisions par zone."

**Wait for user response.**

- Apply decisions, recalculate if needed
- If no UNCERTAIN zones → "Aucune zone UNCERTAIN. Toutes les zones sont classées."

### 4. Confirm Final Top-10

Present the final ranking after all overrides:

"**Top 10 final (après vos ajustements) :**

| # | Zone L2 | Score final | Tier | Overrides appliqués |
|---|---------|-------------|------|---------------------|
{top 10 sorted by final_score}

**Changements par rapport au scoring automatique :**
{list changes if any, or 'Aucun changement'}

**Updated Heat Map :**
```
CRITICAL ████████ {zones}
HIGH     ██████   {zones}
MEDIUM   ████     {zones}
LOW      ██       {zones}
```

Confirmez-vous ce top 10 comme priorité pour les phases suivantes (safety-nets, refactoring, deep-testing) ?"

**Wait for explicit confirmation.**

- If user wants more changes → loop back to relevant section
- If confirmed → proceed

### 5. Update Sidecar State and Proceed

Update `{sidecarStatePath}`:
- Set `risk_map.validation` task to `done`
- Store `overrides_applied`: count
- Store `uncertain_resolved`: count

"**Validation terminée. Génération de la risk map...**"

Display: **[C] Continuer vers la génération du document**

#### Menu Handling Logic:

- IF C: Update sidecar-state, then load, read entire file, then execute `{nextStepFile}`
- IF any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for explicit top-10 confirmation
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Current ranking presented clearly with scoring transparency
- Business criticality overrides collected and applied (scores bounded 1-5)
- Affected zones recalculated and re-sorted after overrides
- UNCERTAIN zones classified by developer
- Final top-10 explicitly confirmed by developer
- Updated heat map reflects final scores
- Sidecar-state updated with validation results

### ❌ SYSTEM FAILURE:

- Proceeding without developer confirmation of top-10
- Rejecting or pushing back on developer's business overrides
- Allowing scores outside 1-5 range
- Not recalculating after overrides
- Skipping UNCERTAIN zone classification
- Generating output document (that's step 04)

**Master Rule:** The developer's business knowledge overrides automated scoring. Top-10 confirmation is MANDATORY before proceeding. No assumptions.
