---
name: 'step-05-discovery-integration'
description: 'Integrate teammate discovery findings into risk map and audit report'

nextStepFile: './step-06-merge-report.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
safetyNetBuilderArtifact: '{output_folder}/lcs/safety-nets/safety-net-builder-report.md'
staticGuardArtifact: '{output_folder}/lcs/safety-nets/static-guard-report.md'
riskMapPath: '{output_folder}/lcs/risk-map.md'
---

# Step 5: Discovery Integration

## STEP GOAL:

To extract discovery findings from both teammate artifacts (existing bugs, unreachable code paths, undocumented behaviors, improvement opportunities) and integrate them into the risk map and audit report as supplementary findings from the safety-nets phase.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER discard discovery findings — every finding has value
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — synthesizing intelligence from your team
- ✅ Discovery findings are bonus value from safety-nets deployment — treat them seriously
- ✅ Enrich the risk map with real-world observations from test writing
- ✅ Be systematic — categorize, deduplicate, integrate

### Step-Specific Rules:

- 🎯 Focus on extracting and integrating discovery findings
- 🚫 FORBIDDEN to modify test files or config
- 🚫 FORBIDDEN to re-run tests — that was step 04
- 💬 Present findings summary before integration
- 📋 Preserve original risk map content — ADD findings, don't replace

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update risk map with discovery appendix
- 📖 Update sidecar-state with integration status
- 🚫 Do NOT delete or overwrite existing risk map content

## CONTEXT BOUNDARIES:

- Available: teammate artifacts with discovery sections, risk map, audit report, validation results
- Focus: finding extraction and integration only
- Limits: do not re-analyze code, do not write tests, do not modify project files
- Dependencies: step 04 completed — validation done

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Extract Discovery Findings

**From Safety Net Builder artifact (`{safetyNetBuilderArtifact}`):**

Read the "Discovery Findings" section. Extract:
- **Existing bugs:** Pages returning errors, broken links, JS errors, broken forms
- **Unreachable code paths:** Unexpected redirects, disabled features, 404 URLs
- **Undocumented behaviors:** Hidden pages, non-obvious flows, unexpected redirects

**From Static Guard artifact (`{staticGuardArtifact}`):**

Read the "Discovery Findings" section. Extract:
- **Existing issues:** Lint errors count and categories, missing dependencies, incompatible versions, CI conflicts
- **Improvement opportunities:** Files needing stricter typing, directories with no coverage, dead config

### 2. Deduplicate and Categorize

**Merge findings into categories:**

| Catégorie | Source | Trouvaille | Impact | Zone |
|-----------|--------|-----------|--------|------|
| Bug existant | Safety Net Builder | {description} | {high/medium/low} | {zone} |
| Code inaccessible | Safety Net Builder | {description} | {medium/low} | {zone} |
| Comportement non documenté | Safety Net Builder | {description} | {medium/low} | {zone} |
| Problème existant | Static Guard | {description} | {high/medium/low} | — |
| Opportunité d'amélioration | Static Guard | {description} | {low} | — |

**Deduplicate:** If the same issue appears in both artifacts, merge into one entry noting both sources.

**If NO discovery findings exist in either artifact:**
- "Aucune découverte supplémentaire par les teammates. Le risk map reste inchangé."
- Skip to section 5.

### 3. Present Findings Summary

"**Découvertes des teammates pendant le déploiement des filets de sécurité :**

**Bugs existants découverts : {count}**
{list with brief description}

**Code inaccessible détecté : {count}**
{list with brief description}

**Comportements non documentés : {count}**
{list with brief description}

**Problèmes existants (linting/config) : {count}**
{list with brief description}

**Opportunités d'amélioration : {count}**
{list with brief description}

Ces découvertes vont être intégrées au risk map comme observations complémentaires de la phase safety-nets."

### 4. Integrate into Risk Map

Read `{riskMapPath}`.

**Add a new section at the end of the risk map:**

```markdown
## Safety-Nets Discovery Findings

_Added by Conrad (LCS Lead) during safety-nets phase — {current_date}_

### Existing Bugs Discovered During Test Writing

| Bug | Zone | Source | Impact |
|-----|------|--------|--------|
| {bug} | {zone} | {Safety Net Builder / Static Guard} | {impact} |

### Unreachable Code Paths

| Path | Zone | Description |
|------|------|-------------|
| {path} | {zone} | {description} |

### Undocumented Behaviors

| Behavior | Zone | Description |
|----------|------|-------------|
| {behavior} | {zone} | {description} |

### Static Analysis Baseline

- **Lint errors:** {count} existing errors ({categories})
- **Improvement opportunities:** {list}
```

**Important:**
- APPEND this section to the existing risk map — do NOT replace any existing content
- If a discovered bug affects a zone's risk score, add a note: `<!-- RISK_ADJUSTMENT: {zone} risk may be higher than estimated due to {finding} -->`

### 5. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `discovery-integration` task status to `done`
- Store `discovery_count`: total findings integrated
- Store `risk_map_updated`: true/false

### 6. Auto-Proceed to Merge & Report

"**Intégration des découvertes terminée.**
- **Trouvailles intégrées :** {count}
- **Risk map mis à jour :** {oui/non}

**Passage au merge et rapport final...**"

#### Menu Handling Logic:

- After integration complete, immediately load, read entire file, then execute `{nextStepFile}`

#### EXECUTION RULES:

- This is an auto-proceed step with no user choices
- Proceed directly to next step after integration

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Discovery findings extracted from both teammate artifacts
- Findings deduplicated and categorized
- Summary presented clearly
- Risk map updated with discovery appendix (preserving existing content)
- Sidecar-state updated with integration status
- Auto-proceeding to step 06

### ❌ SYSTEM FAILURE:

- Not reading discovery sections from teammate artifacts
- Overwriting existing risk map content (instead of appending)
- Skipping findings or ignoring a category
- Not updating sidecar-state
- Modifying test files or project code

**Master Rule:** Every discovery finding from teammates MUST be captured and integrated. The risk map is APPEND-ONLY during this step — existing content is preserved.
