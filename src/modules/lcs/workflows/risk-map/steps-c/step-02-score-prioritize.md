---
name: 'step-02-score-prioritize'
description: 'Auto-score all L2 zones on 5 dimensions, apply vulnerability modifier, sort by risk, produce ASCII heat map'

nextStepFile: './step-03-validate.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
scoringRubric: '../data/scoring-rubric.md'
---

# Step 2: Score & Prioritize Zones

## STEP GOAL:

To automatically score every Level 2 zone across 5 weighted dimensions using audit data, apply the vulnerability modifier from dependency mapping, sort zones by final risk score, and produce an ASCII heat map for immediate visual understanding.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER ask the developer to score zones manually — this is 90% autonomous
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — risk analyst, systematic scorer
- ✅ You score zones objectively from audit data — no subjective guesswork
- ✅ When data is missing for a dimension, use the documented fallback value
- ✅ Present results clearly — the developer validates in step 03

### Step-Specific Rules:

- 🎯 Focus ONLY on scoring and sorting — no business overrides yet (step 03)
- 🚫 FORBIDDEN to ask the developer to score zones — use audit data + fallbacks
- 🚫 FORBIDDEN to skip zones — every L2 zone MUST be scored
- 💬 Present scores transparently — show data source per dimension
- 🎯 Use subprocess to load scoring rubric (Pattern 3) — return rubric data only

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store all scores in memory: scored_zones[], heat_map, priority_ranking
- 📖 Track progress via sidecar-state
- 🚫 Do NOT ask user to validate scores yet — that's step 03

## CONTEXT BOUNDARIES:

- Available: zones from step 01 (L1, L2, L3 candidates), dep_zone_mapping, audit data in memory
- Focus: scoring and prioritization only
- Limits: do not modify zones, do not ask business criticality questions
- Dependencies: step 01 zone discovery complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Scoring Rubric

Launch a subprocess that loads `{scoringRubric}` and returns:
- 5 dimension definitions with weight table
- Scoring guide (1-5 per dimension)
- Vulnerability modifier table
- Fallback values per dimension

If subprocess unavailable: load `{scoringRubric}` in main thread.

### 2. Score All L2 Zones

For EACH Level 2 zone, score across 5 dimensions:

| Dimension | Weight | Data Source | Fallback |
|-----------|--------|-----------|----------|
| Complexity | 0.20 | Code analysis: file sizes, cyclomatic complexity | 3 |
| Coupling | 0.20 | Code analysis: import counts, shared state | 3 |
| Volatility | 0.15 | Code analysis: git history, commit frequency | 3 |
| Coverage Gap | 0.20 | Code analysis: test presence, coverage metrics | 4 |
| Business Criticality | 0.25 | Site exploration: journeys, forms, interactions | 3 |

**For each zone, calculate:**
```
base_score = (complexity × 0.20) + (coupling × 0.20) + (volatility × 0.15) + (coverage_gap × 0.20) + (business_criticality × 0.25)
```

**Document the data source used for each score.** If a dimension used a fallback, note it with `[fallback]`.

### 3. Apply Vulnerability Modifier

For each L2 zone, check the dependency-to-zone mapping from step 01:
- 0 critical CVEs → × 1.0
- 1-2 critical CVEs → × 1.2
- 3+ critical CVEs → × 1.5

```
final_score = base_score × vulnerability_modifier
```

### 4. Sort and Classify

Sort all L2 zones by `final_score` descending. Assign risk tiers:
- CRITICAL: ≥ 4.5
- HIGH: 3.5 – 4.49
- MEDIUM: 2.5 – 3.49
- LOW: < 2.5

### 5. Identify Level 3 Drill-Down

For zones classified CRITICAL or HIGH only:
- From L3 candidates tagged in step 01, list individual high-risk files
- Include: file name, primary risk factor (size, complexity, coupling)

LOW and MEDIUM zones: Level 2 summary only, no drill-down.

### 6. Roll Up to Level 1

For each Level 1 zone, calculate average `final_score` of its L2 children. This gives a functional-area overview.

### 7. Generate ASCII Heat Map

```
CRITICAL ████████ {zone1}, {zone2}
HIGH     ██████   {zone3}, {zone4}, {zone5}
MEDIUM   ████     {zone6}, {zone7}
LOW      ██       {zone8}, {zone9}
```

### 8. Present Scoring Results

"**Résultats du scoring automatique :**

**Vue d'ensemble (Niveau 1) :**

| Zone L1 | Score moyen | Catégorie | Zones L2 |
|---------|-------------|-----------|----------|
{table sorted by score descending}

**Détail par zone (Niveau 2) :**

| Zone L2 | Compl. | Coupl. | Volat. | Couv. | Crit. | Base | Vuln. | Final | Tier |
|---------|--------|--------|--------|-------|-------|------|-------|-------|------|
{table sorted by final_score descending — show [f] for fallback values}

**Zones CRITICAL + HIGH — Drill-down Niveau 3 :**
{for each CRITICAL/HIGH zone: list high-risk files with primary factor}

**Heat Map :**
{ASCII heat map from section 7}

**Top 10 zones critiques :**
{numbered list of top 10 by final_score}

**Zones avec données manquantes :** {N} dimensions utilisent des valeurs par défaut [f]

**Prochaine étape :** Validation par le développeur (ajustements business, zones UNCERTAIN)."

Display: **[C] Continuer vers la validation développeur**

#### Menu Handling Logic:

- IF C: Update `{sidecarStatePath}` (scoring task → done), then load, read entire file, then execute `{nextStepFile}`
- IF any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting scoring results
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All L2 zones scored across 5 dimensions with documented data sources
- Fallback values used (and noted) when data missing
- Vulnerability modifier applied from dependency mapping
- Zones sorted by final score and classified into 4 tiers
- Level 3 drill-down for CRITICAL/HIGH zones only
- Level 1 roll-up calculated
- ASCII heat map generated
- Top 10 critical zones identified
- Results presented clearly with scoring transparency

### ❌ SYSTEM FAILURE:

- Asking developer to manually score zones
- Skipping zones (every L2 MUST be scored)
- Not using fallback values when data is missing
- Not applying vulnerability modifier
- Drilling down to Level 3 for LOW/MEDIUM zones
- Not documenting data sources per score
- Presenting results without scoring transparency

**Master Rule:** Every L2 zone MUST be scored on all 5 dimensions using audit data or documented fallbacks. Scoring is 90% autonomous — the developer validates in step 03, not here.
