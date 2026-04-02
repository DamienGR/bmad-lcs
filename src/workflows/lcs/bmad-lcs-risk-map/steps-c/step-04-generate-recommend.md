---
name: 'step-04-generate-recommend'
description: 'Generate risk-map.md with section anchors, update sidecar-state, recommend next phase'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
riskMapOutputPath: '{output_folder}/lcs/risk-map.md'
---

# Step 4: Generate Risk Map & Recommend

## STEP GOAL:

To produce the final risk-map.md document with all section anchors for downstream consumers, update sidecar-state to mark the risk map phase complete, and recommend safety-nets as the next phase for the developer to launch.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER auto-trigger the next workflow — recommend only
- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — delivering the central LCS artifact
- ✅ The risk map drives ALL subsequent phases — quality matters
- ✅ Be direct and efficient — the data is validated, now generate cleanly
- ✅ End on a reassuring note — the developer has a clear roadmap

### Step-Specific Rules:

- 🎯 Focus ONLY on document generation, state update, and recommendation
- 🚫 FORBIDDEN to auto-trigger safety-nets or any next workflow
- 🚫 FORBIDDEN to modify validated scores — use exactly what step 03 confirmed
- 💬 Include section anchors (HTML comments) for downstream consumers
- 📋 Document MUST be written in `{document_output_language}`

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Write complete risk-map.md to `{riskMapOutputPath}`
- 📖 Update sidecar-state with phase completion
- 🚫 Do NOT auto-trigger next workflow — recommend only

## CONTEXT BOUNDARIES:

- Available: validated_zones, overrides, heat_map, top-10, UNCERTAIN resolutions from step 03
- Focus: document generation and completion only
- Limits: do not re-score, do not re-validate, do not trigger next workflow
- Dependencies: step 03 validation complete with confirmed top-10

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Generate Risk Map Document

Write `{riskMapOutputPath}` with the following structure. All content MUST be in `{document_output_language}`.

```markdown
---
project: {project_name}
generated: {current_date}
generator: Conrad (LCS Lead) — Risk Map Workflow
status: complete
zones_total: {N}
zones_critical: {N}
zones_high: {N}
zones_uncertain: {N}
overrides_applied: {N}
---

# Risk Map — {project_name}

<!-- SECTION:zone-inventory -->
## Zone Inventory

### Level 1 — Functional Areas

| Zone | Description | L2 Components | Avg Risk Score |
|------|-------------|---------------|---------------|
{L1 zones table}

### Level 2 — Components (Primary Scoring Unit)

| Zone | Parent L1 | Files | Risk Score | Tier | Vuln. Modifier |
|------|-----------|-------|-----------|------|---------------|
{L2 zones sorted by score descending}

### Level 3 — High-Risk Files (CRITICAL/HIGH Zones Only)

| File | Zone L2 | Primary Risk Factor | Notes |
|------|---------|-------------------|-------|
{L3 files for CRITICAL/HIGH zones only}

<!-- SECTION:scoring-matrix -->
## Scoring Matrix

### Methodology

5-dimension weighted sum with vulnerability modifier:
- Complexity (0.20) + Coupling (0.20) + Volatility (0.15) + Coverage Gap (0.20) + Business Criticality (0.25)
- Vulnerability modifier: ×1.0 / ×1.2 / ×1.5 based on critical CVE count

### Detailed Scores

| Zone L2 | Compl. | Coupl. | Volat. | Couv. | Crit. | Base | Vuln. | Final | Tier |
|---------|--------|--------|--------|-------|-------|------|-------|-------|------|
{all L2 zones with dimension scores — [f] for fallback values}

### Developer Overrides Applied

{table of overrides or "No overrides applied"}

<!-- SECTION:heat-map -->
## Risk Heat Map

CRITICAL ████████ {zones}
HIGH     ██████   {zones}
MEDIUM   ████     {zones}
LOW      ██       {zones}

<!-- SECTION:priority-ranking -->
## Priority Ranking — Top 10

| Priority | Zone | Score | Tier | Key Risk Factors |
|----------|------|-------|------|-----------------|
{top 10 with brief risk summary per zone}

<!-- SECTION:uncertain-zones -->
## UNCERTAIN Zones (ADR-005)

{table of zones that were marked UNCERTAIN with resolution status}
{or "No UNCERTAIN zones identified"}

<!-- SECTION:recommendations -->
## Recommendations per Phase

### Safety Nets (Phase 2) — Recommended Next

Priority order for safety net deployment:
1. {zone} — {reason}
2. {zone} — {reason}
...

### Refactoring (Phase 3)

Zones most in need of refactoring:
1. {zone} — {reason}
...

### Deep Testing (Phase 4)

Zones requiring comprehensive test coverage:
1. {zone} — {reason}
...
```

### 2. Verify Document Integrity

After writing, verify:
- All 6 section anchors present (`<!-- SECTION:... -->`)
- All L2 zones appear in scoring matrix (no zone without score)
- Top-10 matches validated ranking from step 03
- Frontmatter `status: complete` is set
- Document is written in `{document_output_language}`

### 3. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `risk_map.generation` task to `done`
- Set `consolidation.risk_map_complete` to `true`
- Set `consolidation.risk_map_date` to current date
- Store path: `risk_map_path: {riskMapOutputPath}`

### 4. Present Completion and Recommend

"**Risk map générée avec succès !**

**Document :** `{riskMapOutputPath}`
- **Zones totales :** {N} zones L2 scorées
- **Zones critiques :** {N} CRITICAL, {N} HIGH
- **Overrides appliqués :** {N}
- **Zones UNCERTAIN résolues :** {N}

**La risk map est l'artefact central du LCS.** Elle guide :
- 🛡️ **Safety Nets** (Phase 2) — priorité de déploiement des filets de sécurité
- 🔧 **Refactoring** (Phase 3) — zones à refactorer en premier
- 🧪 **Deep Testing** (Phase 4) — couverture de tests approfondie

**Prochaine étape recommandée :** Lancer le workflow **safety-nets** pour déployer les filets de sécurité sur les zones critiques.

Merci {user_name} pour votre validation — la risk map est prête. 🎯"

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- risk-map.md generated at `{riskMapOutputPath}` with all 6 section anchors
- All L2 zones present in document with complete scores
- Top-10 matches developer-validated ranking
- Overrides documented
- UNCERTAIN zones resolved and documented
- Document in `{document_output_language}`
- Sidecar-state updated (risk map complete)
- Safety-nets recommended as next phase (NOT auto-triggered)
- Workflow ends cleanly

### ❌ SYSTEM FAILURE:

- Missing section anchors in output
- Zones without scores in document
- Top-10 not matching validated ranking
- Modifying scores after validation
- Auto-triggering safety-nets workflow
- Document not in correct output language
- Not updating sidecar-state

**Master Rule:** The risk map document MUST contain all 6 section anchors, all scored zones, and the developer-validated top-10. Auto-triggering the next workflow is FORBIDDEN — recommend only.
