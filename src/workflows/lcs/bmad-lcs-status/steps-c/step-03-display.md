---
name: 'step-03-display'
description: 'Format and display the consolidation status dashboard'

dashboardFormat: '../data/dashboard-format.md'
---

# Step 3: Display Dashboard

## STEP GOAL:

To format all collected state data and metrics into a clear, concise consolidation status dashboard and display it directly to the user. This is the final step — the workflow ends after display.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 Load the dashboard format reference before formatting
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ This is a fully autonomous step — no user interaction needed
- ✅ Present information clearly and concisely — the developer needs an instant snapshot
- ✅ Be factual — no commentary, just data

### Step-Specific Rules:

- 🎯 Focus ONLY on formatting and displaying the dashboard
- 🚫 FORBIDDEN to read state or artifact files — all data must already be in memory from steps 01 and 02 (loading the dashboard format reference is allowed)
- 🚫 FORBIDDEN to write any output files — display is console only
- 📋 Follow the dashboard format reference exactly
- 📋 Skip sections that have no data (empty decisions, all capabilities true, etc.)

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 No file output — console display only
- 📖 This is the final step — workflow ends after display
- 🚫 Do NOT write any files

## CONTEXT BOUNDARIES:

- Available context: all state data (step 01) and calculated metrics (step 02)
- Focus: formatting and display only
- Limits: do not read state/artifact files (dashboard format reference is OK), do not calculate, do not write files
- Dependencies: steps 01 and 02 must have completed with all data in memory

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise.

### 1. Load Dashboard Format

Load `{dashboardFormat}` for the layout reference.

### 2. Display Header

Display the header section with:
- Project name (from `project.name` in sidecar-state)
- Last session relative time indicator (e.g., "3 days ago") calculated from `consolidation.last_session`
- Last session date

### 3. Display Phase Progression

Display all 7 phases in a single line using the icon convention:
- ✅ = phase fully completed
- 🔄 = current phase (in progress)
- ⬚ = future phase (not started)

Display global progression count: `{completed_phases} / 7 phases`

### 4. Display Current Phase Detail

Show all tasks in the current phase with their individual statuses:
- ✅ for done tasks (include date from artifact frontmatter if available)
- 🔄 for in_progress tasks
- ⬚ for pending tasks

Display phase progress: `{done}/{total} tasks`

### 5. Display Capabilities Warnings (Conditional)

**Only if any capability is `false`:**

Display each false capability as a warning line.

**If all capabilities are `true`:** skip this section entirely.

### 6. Display Safety Nets Summary

Show safety-nets phase tasks with their statuses regardless of current phase.

**If safety-nets phase has not been reached yet:**
Display: "Safety Nets: not yet started"

**Otherwise:** Display each safety-net task with status and coverage count.

### 7. Display Recent Decisions (Conditional)

**Only if the decisions array is not empty:**

Display the last 3 decisions with date, phase, decision text, and justification.

**If no decisions recorded:** skip this section entirely.

### 8. Display What's Next

Based on step 02's "next pending task" identification:

- **If there is a next pending task:** Display its id and the phase it belongs to
- **If current phase is complete:** Display guidance to advance to next phase
- **If all phases complete:** Display "Consolidation Complete — Guard mode active"

### 9. Display Footer

Display the closing separator line.

### 10. Workflow Complete

This is the final step. The workflow is now complete. No further action needed.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Dashboard format reference loaded and followed
- Header displayed with project name and last session
- Phase progression bar displayed with correct icons
- Current phase detail displayed with all task statuses
- Capabilities warnings displayed only if relevant
- Safety nets summary displayed
- Recent decisions displayed only if relevant
- "What's Next" guidance displayed clearly
- Dashboard is concise and instantly readable
- No files written — console output only

### ❌ SYSTEM FAILURE:

- Not loading the dashboard format reference
- Reading files during this step (all data should be in memory)
- Writing output files (this is console-only)
- Displaying sections with no data (should be skipped)
- Missing any dashboard section that has data
- Verbose or unclear formatting

**Master Rule:** Follow the dashboard format exactly. Display ALL sections with data. Skip ALL sections without data. Keep it concise and instant.
