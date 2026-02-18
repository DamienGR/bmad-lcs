---
name: 'step-02-spawn'
description: 'Create git safety branch and spawn Safety Net Builder + Static Guard teammates'

nextStepFile: './step-03-monitor.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
spawnPromptSafetyNetBuilder: '../data/spawn-prompt-safety-net-builder.md'
spawnPromptStaticGuard: '../data/spawn-prompt-static-guard.md'
mcpDegradationMatrix: '../data/mcp-degradation-matrix.md'
---

# Step 2: Create Safety Branch & Spawn Teammates

## STEP GOAL:

To create a git safety branch isolating all safety-net changes, load the MCP degradation matrix, prepare spawn prompts with injected variables, and spawn Safety Net Builder + Static Guard teammates in parallel (or execute sequentially as fallback).

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the safety branch creation
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — launching the safety net deployment team
- ✅ Orchestration is your expertise — spawn, monitor, synthesize
- ✅ The developer trusts you to manage the branch and team safely
- ✅ Be factual and concise — this is technical orchestration

### Step-Specific Rules:

- 🎯 Focus ONLY on branch creation and teammate spawning
- 🚫 FORBIDDEN to write any test files or config — that's what teammates do
- 🚫 FORBIDDEN to start validation — that's step 04
- 💬 Report each action result clearly
- 📋 Verify both spawns succeed before proceeding

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update sidecar-state with branch name and spawn statuses
- 📖 Track: branch_name, spawn_mode (parallel/sequential), spawn_status per teammate
- 🚫 Do NOT write test artifacts or config in this step

## CONTEXT BOUNDARIES:

- Available context: step-01 completed — priorities confirmed, fixture strategy selected, sidecar-state updated
- Focus: branch creation + teammate spawning only
- Limits: do not analyze, write tests, or validate
- Dependencies: step-01 must be complete with priorities and fixture strategy stored in sidecar-state

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Create Safety Branch

**Record current branch:**
- Run `git rev-parse --abbrev-ref HEAD` to capture the current working branch
- Store as `return_branch` in memory

**Create isolation branch:**
- Run `git checkout -b lcs/pre-safety-nets`
- Verify branch creation succeeded

**If branch already exists** (from a previous interrupted session):
- Ask user: "La branche `lcs/pre-safety-nets` existe déjà. Souhaitez-vous : [R] Réutiliser la branche existante / [D] La supprimer et en créer une nouvelle ?"
- Wait for user response and act accordingly

"**Branche de sécurité créée :** `lcs/pre-safety-nets`
Tous les fichiers de tests et configs seront écrits sur cette branche. Si la validation échoue, la branche peut être abandonnée sans impact sur `{return_branch}`."

### 2. Load MCP Degradation Matrix

Read `{mcpDegradationMatrix}` to understand fallback strategies for each MCP tool.

**Map available tools from sidecar-state capability matrix to degradation levels:**

| Tool | Status | Degradation Level | Fallback |
|------|--------|-------------------|----------|
| Playwright MCP | {OK/MISSING} | {full/degraded/manual} | {fallback description} |
| ESLint MCP | {OK/MISSING} | {full/degraded/manual} | {fallback description} |

Store degradation plan in memory for spawn prompt injection.

### 3. Prepare Spawn Prompts

**Read spawn prompts:**
- Read `{spawnPromptSafetyNetBuilder}`
- Read `{spawnPromptStaticGuard}`

**Read context from sidecar-state:**
- `repo_path` — absolute path to the project repository
- `framework` — detected framework
- `framework_version` — specific version
- `framework_profile_path` — path to framework profile
- `sidecar_state_path` — path to sidecar-state (READ-ONLY for teammates)
- `site_url` — local site URL
- `document_output_language` — language for artifacts
- `prioritized_zones` — from step-01
- `fixture_strategy` — from step-01
- `uncertain_zones` — zones with Tier 1 treatment
- `mcp_tools_available` — from capability matrix
- `mcp_degradation_plan` — from degradation matrix
- `output_path` — where to write the artifact

**Inject variables into each spawn prompt.**

### 4. Spawn Teammates

**Detect Agent Teams availability:**
- Check if Agent Teams / subagent spawning is available in the current environment

**If Agent Teams available (parallel mode):**
1. Spawn Safety Net Builder with injected spawn prompt
2. Spawn Static Guard with injected spawn prompt
3. Verify BOTH spawns succeeded
4. If one spawn fails → cancel the other, switch to sequential fallback

**If Agent Teams NOT available (sequential fallback):**
1. Note: "Agent Teams non disponible. Exécution séquentielle."
2. Execute Safety Net Builder workflow first (as Conrad, loading `teammates/safety-net-builder-workflow.md`)
3. Then execute Static Guard workflow (as Conrad, loading `teammates/static-guard-workflow.md`)
4. After both complete → proceed to step 03

### 5. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `safety_branch` to `lcs/pre-safety-nets`
- Set `return_branch` to original branch name
- Set `spawn_mode` to `parallel` or `sequential`
- Set `safety-net-builder` task status to `in_progress`
- Set `static-guard` task status to `in_progress`

### 6. Present Spawn Status and Proceed

"**Déploiement lancé !**

- **Branche de sécurité :** `lcs/pre-safety-nets` (depuis `{return_branch}`)
- **Safety Net Builder :** {spawned/running} — Fixtures → Smoke → E2E → Snapshots
- **Static Guard :** {spawned/running} — ESLint → Type check → Hooks → CI
- **Mode :** {parallèle (Agent Teams) / séquentiel}
- **Dégradation MCP :** {summary of any degraded tools}

**Je passe au monitoring des teammates...**"

Display: **[C] Continuer vers le monitoring**

#### Menu Handling Logic:

- IF C: Update `{sidecarStatePath}`, then load, read entire file, then execute `{nextStepFile}`
- IF Any other: help user respond, then redisplay menu

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed to next step when user selects 'C'

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Safety branch created successfully (lcs/pre-safety-nets)
- Return branch recorded
- MCP degradation matrix loaded and mapped
- Spawn prompts prepared with all injected variables
- Both teammates spawned (or sequential fallback initiated)
- Both spawns verified successful (or fallback activated)
- Sidecar-state updated with branch info and task statuses
- Proceeding to step 03

### ❌ SYSTEM FAILURE:

- Not creating safety branch before spawning
- Not recording the return branch
- Not loading MCP degradation matrix
- Spawning with missing variables in prompts
- Not verifying spawn success before proceeding
- Proceeding with one failed spawn without fallback
- Writing test files or config (that's for teammates)

**Master Rule:** Safety branch MUST exist before ANY teammate writes files. Both spawns MUST be confirmed before proceeding. No shortcuts.
