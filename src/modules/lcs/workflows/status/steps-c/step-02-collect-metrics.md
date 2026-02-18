---
name: 'step-02-collect-metrics'
description: 'Read artifact frontmatters for completed tasks, calculate per-phase metrics and global progression'

nextStepFile: './step-03-display.md'
artifactBasePath: '{output_folder}/lcs/'
---

# Step 2: Collect Metrics

## STEP GOAL:

To enrich the state data from step 01 by reading the frontmatter of completed task artifacts, calculating per-phase completion metrics, global progression, and identifying the next pending task for the "What's Next" indicator.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ This is a fully autonomous step — no user interaction needed
- ✅ You bring metrics collection expertise; extract only what's needed
- ✅ Be efficient — read frontmatters only, never full artifact content

### Step-Specific Rules:

- 🎯 Focus ONLY on reading artifact frontmatters and calculating metrics
- 🚫 FORBIDDEN to read full artifact content — frontmatter only (status + date fields)
- 🚫 FORBIDDEN to format any dashboard output — that's step 03
- 📋 If an artifact file is missing, record "N/A" for that task — do not fail

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store all calculated metrics in memory for step 03
- 📖 Track: per-phase done/total, global progression %, next pending task
- 🚫 Do NOT write any files — this is read-only

## CONTEXT BOUNDARIES:

- Available context: all sidecar-state data extracted in step 01
- Focus: artifact frontmatter reading and metric calculation only
- Limits: do not read full artifact content, do not format output
- Dependencies: step 01 must have completed with state data in memory

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise.

### 1. Read Artifact Frontmatters

For each phase in the tasks data (from step 01), iterate through all tasks.

**For each task with status `done` and a non-null `artifact` path:**

1. Construct the full path: `{artifactBasePath}` + the task's `artifact` value
2. Read ONLY the frontmatter of the artifact file (the YAML between `---` markers)
3. Extract: `status` and `date` from the frontmatter
4. Store the result for this task

**If the artifact file does not exist:**
- Record status as "N/A" and date as "N/A"
- This is normal early in the consolidation — do not error out

**If the task status is not `done` or artifact is null:**
- Skip reading — no artifact to check
- Record the task status as-is from sidecar-state

### 2. Calculate Per-Phase Metrics

For each of the 7 phases (pre-flight, audit, safety-nets, dependency-update, refactoring, deep-testing, guard):

- Count tasks with status `done` → done_count
- Count total tasks → total_count
- Calculate: `done_count / total_count` as phase completion ratio
- Determine phase status:
  - **completed** = all tasks done
  - **in_progress** = at least one task done or in_progress, but not all done
  - **pending** = no tasks started

### 3. Calculate Global Progression

- Count phases fully completed (all tasks done) → completed_phases
- Global progression: `completed_phases / 7`
- Identify the current phase (from `consolidation.current_phase` in step 01 data)

### 4. Identify Next Pending Task

In the current phase, find the first task with status `pending` or `in_progress`:

- Record its `id` as the "what's next" task
- If all tasks in current phase are done, the "what's next" is to advance to the next phase

If ALL phases are complete:
- Record "consolidation complete — guard mode active"

### 5. Proceed to Dashboard Display

Display: "**Proceeding to dashboard display...**"

#### Menu Handling Logic:

- After metrics calculation is complete, immediately load, read entire file, then execute `{nextStepFile}`

#### EXECUTION RULES:

- This is an auto-proceed step with no user menu choices
- Proceed directly to next step after metrics are calculated

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All artifact frontmatters read for done tasks (status + date only)
- Missing artifacts handled gracefully (N/A, not error)
- Per-phase metrics calculated (done/total for each phase)
- Global progression calculated (completed phases / 7)
- Next pending task identified
- All metrics stored in memory for step 03

### ❌ SYSTEM FAILURE:

- Reading full artifact content instead of frontmatter only
- Failing on missing artifact files instead of recording N/A
- Not calculating metrics for all 7 phases
- Formatting dashboard output in this step (that's step 03)
- Not identifying the "what's next" task

**Master Rule:** Read frontmatters ONLY. Calculate ALL metrics. Handle missing artifacts gracefully.
