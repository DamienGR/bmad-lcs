---
name: 'step-03-discovery-integration'
description: 'Extract discovery findings from Deep Tester zone artifacts and integrate into risk map as supplementary observations'

nextStepFile: './step-04-report.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
deepTestingReportPath: '{output_folder}/lcs/deep-testing-report.md'
riskMapPath: '{output_folder}/lcs/risk-map.md'
artifactsFolder: '{output_folder}/lcs/deep-testing-artifacts/'
---

# Step 3: Discovery Integration

## STEP GOAL:

To extract discovery findings from all Deep Tester zone artifacts (bugs discovered, untestable code, surprising behaviors, fixture gaps) and integrate them into the risk map as supplementary observations from the deep-testing phase.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER discard discovery findings — every finding has value
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — synthesizing intelligence from deep testing
- ✅ Discovery findings are bonus value from the test-writing process — treat them seriously
- ✅ Enrich the risk map with real-world observations from the Deep Tester
- ✅ Be systematic — categorize, deduplicate, integrate

### Step-Specific Rules:

- 🎯 Focus on extracting and integrating discovery findings from Deep Tester artifacts
- 🎯 Use subprocess (Pattern 1) to scan all zone artifacts and extract discovery sections
- 🚫 FORBIDDEN to modify test files or production code
- 🚫 FORBIDDEN to re-run tests — that was step 02
- 💬 Present findings summary before integration
- 📋 Preserve original risk map content — ADD findings, don't replace

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update risk map with discovery appendix
- 💾 Update `{deepTestingReportPath}` with discovery summary
- 💾 Update `{sidecarStatePath}` with integration status
- 🚫 Do NOT delete or overwrite existing risk map content

## CONTEXT BOUNDARIES:

- Available: step-02 completed — all zone artifacts in `{artifactsFolder}`, sidecar-state with completedZones
- Focus: finding extraction and integration only
- Limits: do not re-analyze code, do not write tests, do not modify project files
- Dependencies: all zones processed in step-02

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load Completed Zones

Read `{sidecarStatePath}` and extract the list of `completedZones` with their statuses.

**For each zone with status `approved`:** its artifact file is at `{artifactsFolder}/zone-{zone_slug}.md`

**For skipped or spawn-failed zones:** no artifact to process — note them for completeness.

### 2. Extract Discovery Findings

Launch a subprocess that scans all zone artifact files in `{artifactsFolder}`, reads the "Discovery Findings" section from each, and returns a consolidated list of:
- **Bugs discovered:** bug description + zone + which test exposed it
- **Untestable code:** code that couldn't be tested despite refactoring + reason + zone
- **Surprising behaviors:** undocumented behaviors found during testing + zone
- **Fixture gaps:** where existing fixtures were insufficient + what was added + zone

If subprocess unavailable: read each artifact file individually in main thread and extract the same data.

### 3. Deduplicate and Categorize

**Merge findings into categories:**

| Catégorie | Zone | Trouvaille | Impact | Test exposant |
|-----------|------|-----------|--------|---------------|
| Bug découvert | {zone} | {description} | {high/medium/low} | {test_name} |
| Code non-testable | {zone} | {description} | {medium/low} | — |
| Comportement surprenant | {zone} | {description} | {medium/low} | {test_name} |
| Lacune fixture | {zone} | {description} | {low} | — |

**Deduplicate:** If the same issue appears across multiple zones, merge into one entry noting all affected zones.

**If NO discovery findings exist across all artifacts:**
- "**Aucune découverte supplémentaire par le Deep Tester.** Le risk map reste inchangé."
- Skip to section 6.

### 4. Present Findings Summary

"**Découvertes du Deep Tester pendant la phase de tests approfondis :**

**Bugs découverts : {count}**
{list with zone and brief description}

**Code non-testable identifié : {count}**
{list with zone and reason}

**Comportements surprenants : {count}**
{list with zone and brief description}

**Lacunes de fixtures : {count}**
{list with zone and what was added}

**Total : {total_count} découvertes à intégrer au risk map.**

Ces découvertes vont être intégrées au risk map comme observations complémentaires de la phase deep-testing."

### 5. Integrate into Risk Map

Read `{riskMapPath}`.

**Add a new section at the end of the risk map:**

```markdown
## Deep-Testing Discovery Findings

_Added by Conrad (LCS Lead) during deep-testing phase — {current_date}_

### Bugs Discovered During Deep Testing

| Bug | Zone | Test | Impact |
|-----|------|------|--------|
| {bug} | {zone} | {test_name} | {impact} |

### Untestable Code (Despite Refactoring)

| Code | Zone | Reason |
|------|------|--------|
| {code} | {zone} | {reason} |

### Surprising Behaviors

| Behavior | Zone | Test | Description |
|----------|------|------|-------------|
| {behavior} | {zone} | {test_name} | {description} |

### Fixture Gaps Identified

| Gap | Zone | Resolution |
|-----|------|------------|
| {gap} | {zone} | {what was added} |
```

**Important:**
- APPEND this section to the existing risk map — do NOT replace any existing content
- If a discovered bug affects a zone's risk score, add a note: `<!-- RISK_ADJUSTMENT: {zone} risk may be higher than estimated due to {finding} -->`
- If previous phases (safety-nets, refactoring) already added discovery sections, append AFTER those

### 6. Update Deep Testing Report

Append a "Discovery Findings" summary to `{deepTestingReportPath}`:
- Total findings by category
- Zones with most discoveries
- Risk map integration status

### 7. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `discovery-integration` status to `done`
- Store `discovery_count`: total findings integrated
- Store `risk_map_updated`: true/false

### 8. Auto-Proceed to Report

"**Intégration des découvertes terminée.**
- **Trouvailles intégrées :** {count}
- **Risk map mis à jour :** {oui/non}
- **Catégories :** {count} bugs, {count} code non-testable, {count} comportements surprenants, {count} lacunes fixtures

**Passage à la finalisation du rapport...**"

#### Menu Handling Logic:

- After integration complete, immediately load, read entire file, then execute `{nextStepFile}`

#### EXECUTION RULES:

- This is an auto-proceed step with no user choices
- Proceed directly to next step after integration

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All zone artifacts scanned for discovery findings
- Findings deduplicated and categorized across zones
- Summary presented clearly with counts and zone attribution
- Risk map updated with discovery appendix (preserving existing content — including prior phase discoveries)
- Deep testing report updated with discovery summary
- Sidecar-state updated with integration status
- Auto-proceeding to step 04

### ❌ SYSTEM FAILURE:

- Not reading discovery sections from all zone artifacts
- Overwriting existing risk map content (instead of appending)
- Skipping findings or ignoring a category
- Not updating sidecar-state
- Modifying test files or production code
- Not attributing findings to their source zone

**Master Rule:** Every discovery finding from the Deep Tester MUST be captured and integrated. The risk map is APPEND-ONLY during this step — existing content (including prior phase discoveries) is preserved.
