---
name: 'step-01-init-spawn'
description: 'Verify audit prerequisites, collect focus areas, adapt to MCP availability, and spawn teammates'

nextStepFile: './step-02-dep-scan-monitor.md'
continueFile: './step-01b-continue.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
capabilityMatrixPath: '{output_folder}/lcs/pre-flight/capability-matrix.md'
spawnPromptCodeAnalyst: '../data/spawn-prompt-code-analyst.md'
spawnPromptSiteExplorer: '../data/spawn-prompt-site-explorer.md'
mcpDegradationMatrix: '../data/mcp-degradation-matrix.md'
---

# Step 1: Initialize Audit & Spawn Teammates

## STEP GOAL:

To verify that audit prerequisites are met (pre-flight complete, site accessible), collect optional focus areas from the user, adapt the audit plan based on MCP tool availability, and spawn Code Analyst + Site Explorer teammates via Agent Teams (or execute sequentially as fallback).

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip any prerequisite check
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You are launching the audit team — orchestration is your expertise
- ✅ The developer brings their project context and optional focus areas
- ✅ Be factual and concise — this is technical orchestration, not a conversation

### Step-Specific Rules:

- 🎯 Focus ONLY on prerequisites, focus areas, MCP adaptation, and teammate spawning
- 🚫 FORBIDDEN to start any analysis — that's what teammates do
- 🚫 FORBIDDEN to write any audit artifacts — teammates and step 02+ do that
- 💬 Report each prerequisite check result clearly
- 📋 If audit tasks already show progress → route to step-01b (continuation)

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store all collected data in memory for spawn prompts
- 📖 Track: focus_areas, mcp_adaptation_plan, agent_teams_available, spawn_status
- 🚫 Do NOT write audit artifacts in this step

## CONTEXT BOUNDARIES:

- Available context: workflow.md initialization, module config loaded
- Focus: prerequisites + spawning only
- Limits: do not analyze code, navigate site, or scan dependencies
- Dependencies: pre-flight phase must be complete

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Check for Continuation

Read `{sidecarStatePath}`.

**Check audit tasks:**
- If ANY audit task has status `in_progress`, `done`, or `failed` → this is a continuation scenario
- Load, read entirely, then execute `{continueFile}`
- If ALL audit tasks are `pending` → this is a fresh audit, continue below

### 2. Verify Prerequisites

**Check pre-flight completion:**
- Read `{sidecarStatePath}` → verify `consolidation.current_phase` is `pre-flight` or later
- Verify all pre-flight tasks have status `done`
- If pre-flight NOT complete → ERROR: "Le pre-flight doit être complété avant de lancer l'audit. Utilisez le workflow pre-flight d'abord."

**Check site accessibility:**
- Read project `site_url` from sidecar-state
- Verify site is reachable (HTTP request)
- If site unreachable → ERROR: "Le site local n'est pas accessible à {site_url}. Vérifiez que votre environnement local est démarré."

### 3. Read Capability Matrix

Read `{capabilityMatrixPath}` to understand available MCP tools.

Load `{mcpDegradationMatrix}` to map each missing tool to its impact and fallback strategy.

**Present MCP adaptation summary:**

"**Outils MCP disponibles pour l'audit :**

| Outil | Status | Impact |
|-------|--------|--------|
| Chrome DevTools MCP | {OK/MISSING} | {impact} |
| Semgrep MCP | {OK/MISSING} | {impact} |
| MySQL MCP | {OK/MISSING} | {impact} |
| Snyk MCP | {OK/MISSING} | {impact} |
| Lighthouse MCP | {OK/MISSING} | {impact} |
| Context7 | {OK/MISSING} | {impact} |

{If any critical tool missing, note the fallback strategy}"

### 4. Collect Focus Areas

Ask the developer for optional focus areas:

"**Souhaitez-vous prioriser certaines zones pour l'audit ?**

Exemples :
- Un répertoire spécifique (ex: `wp-content/themes/custom-theme/`)
- Une fonctionnalité (ex: le checkout, le formulaire de contact)
- Un concern (ex: la sécurité, la performance)

Ou tapez **[S]** pour lancer l'audit complet sans focus particulier."

**Wait for user response.**

- If user provides focus areas → store in memory
- If user selects S or equivalent → no focus areas, full audit

### 5. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `consolidation.current_phase` to `audit`
- Set `consolidation.last_session` to current date
- Set all audit tasks to `in_progress`

### 6. Detect Agent Teams & Spawn Teammates

**Detect Agent Teams availability:**
- Check if Agent Teams / subagent spawning is available in the current environment

**Load spawn prompts:**
- Read `{spawnPromptCodeAnalyst}` — inject variables: repo_path, framework, framework_version, framework_profile_path, sidecar_state_path (read-only), focus_areas, mcp_tools_available, document_output_language
- Read `{spawnPromptSiteExplorer}` — inject variables: site_url, framework, framework_version, framework_profile_path, sidecar_state_path (read-only), focus_areas, mcp_tools_available, document_output_language

**If Agent Teams available:**
1. Spawn Code Analyst with injected spawn prompt
2. Spawn Site Explorer with injected spawn prompt
3. Verify BOTH spawns succeeded
4. If one spawn fails → cancel the other, switch to sequential fallback

**If Agent Teams NOT available (sequential fallback):**
1. Note: "Agent Teams non disponible. Exécution séquentielle."
2. Execute Code Analyst workflow first (as Conrad, loading `teammates/code-analyst-workflow.md`)
3. Then execute Site Explorer workflow (as Conrad, loading `teammates/site-explorer-workflow.md`)
4. After both complete → proceed to step 02

**Display spawn status:**

"**Audit lancé !**

- Code Analyst: {spawned / running / complete}
- Site Explorer: {spawned / running / complete}
- Mode: {parallel (Agent Teams) / séquentiel}

**Je passe au scan de dépendances pendant que l'équipe analyse...**"

### 7. Proceed to Dependency Scan

Display: "**Proceeding to dependency scan...**"

#### Menu Handling Logic:

- After spawn status is confirmed, immediately load, read entire file, then execute `{nextStepFile}`

#### EXECUTION RULES:

- This is an auto-proceed init step with no user menu choices at this point
- Proceed directly to next step after spawn confirmation
- IF the user wants to discuss results before proceeding: respond, then proceed

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Pre-flight completion verified
- Site accessibility confirmed
- Capability matrix read and MCP adaptation noted
- Focus areas collected (or full audit confirmed)
- Sidecar-state updated (current_phase: audit, tasks: in_progress)
- Teammates spawned (or sequential fallback initiated)
- Both spawns verified successful (or fallback activated)
- Proceeding to step 02

### ❌ SYSTEM FAILURE:

- Skipping prerequisite checks
- Not verifying spawn success before proceeding
- Starting analysis in this step (that's for teammates)
- Writing audit artifacts (that's for teammates and later steps)
- Not updating sidecar-state before spawning
- Proceeding with one failed spawn without fallback

**Master Rule:** All prerequisites MUST be verified and both spawns confirmed before proceeding. No shortcuts.
