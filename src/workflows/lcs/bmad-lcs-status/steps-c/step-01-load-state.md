---
name: 'step-01-load-state'
description: 'Read sidecar-state.yaml, validate it exists, extract consolidation state data'

nextStepFile: './step-02-collect-metrics.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
---

# Step 1: Load State

## STEP GOAL:

To read the sidecar-state.yaml file, validate it exists, and extract all consolidation state data needed for the status dashboard: current phase, last session, all tasks with statuses, capabilities, and recent decisions.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ This is a fully autonomous step — no user interaction needed
- ✅ You bring state-reading expertise; the developer just needs the dashboard
- ✅ Be factual and concise — read, extract, move on

### Step-Specific Rules:

- 🎯 Focus ONLY on reading and parsing sidecar-state.yaml
- 🚫 FORBIDDEN to read artifact files — that's step 02
- 🚫 FORBIDDEN to format any dashboard output — that's step 03
- 📋 If sidecar-state.yaml does not exist, display error and STOP the workflow

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store all extracted data in memory for step 02 and step 03
- 📖 Track: current_phase, last_session, all tasks by phase, capabilities, decisions
- 🚫 Do NOT write any files in this step — this is read-only

## CONTEXT BOUNDARIES:

- Available context: workflow.md initialization, module config loaded
- Focus: sidecar-state.yaml reading only
- Limits: do not read artifacts, do not calculate metrics, do not format output
- Dependencies: none — this is the first step

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise.

### 1. Read Sidecar State File

Read `{sidecarStatePath}`.

**If the file does not exist:**

Display:
"**Sidecar state not found.**

The file `sidecar-state.yaml` does not exist yet. This means the pre-flight check has not been run.

**Run [PF] Pre-flight first to initialize the consolidation state.**"

**STOP the workflow. Do not proceed to step 02.**

### 2. Extract Consolidation Phase Data

From the sidecar-state, extract:

- `consolidation.current_phase` — the active phase name
- `consolidation.started_at` — when consolidation began
- `consolidation.last_session` — when the last session occurred

### 3. Extract All Tasks by Phase

For each phase in `tasks` (pre-flight, audit, safety-nets, dependency-update, refactoring, deep-testing, guard):

Extract the list of tasks with:
- `id` — task identifier
- `status` — pending / in_progress / done / failed / skipped
- `artifact` — path to output file (may be null)

### 4. Extract Capabilities

From `capabilities`, extract all entries. Note which capabilities are `false` — these will be displayed as warnings.

### 5. Extract Recent Decisions

From `decisions`, extract the last 3 entries (most recent first). For each:
- `date`
- `phase`
- `decision`
- `justification`

If the decisions array is empty, note that no decisions have been recorded.

### 6. Proceed to Metrics Collection

Display: "**Proceeding to metrics collection...**"

#### Menu Handling Logic:

- After state extraction is complete, immediately load, read entire file, then execute `{nextStepFile}`

#### EXECUTION RULES:

- This is an auto-proceed step with no user menu choices
- Proceed directly to next step after state extraction

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- sidecar-state.yaml read successfully
- current_phase extracted
- last_session extracted
- All tasks by phase extracted with statuses
- Capabilities extracted (false values noted)
- Recent decisions extracted (last 3)
- All data stored in memory for subsequent steps

### ❌ SYSTEM FAILURE:

- Not checking if sidecar-state.yaml exists
- Proceeding when sidecar-state.yaml is missing
- Reading artifact files in this step (that's step 02)
- Formatting dashboard output in this step (that's step 03)
- Not extracting all required data fields

**Master Rule:** Read the complete state file. Extract ALL fields. Do not skip any section of the sidecar-state.
