---
name: 'step-01b-continue'
description: 'Handle workflow continuation from a previous session'

reportOutputPath: '{output_folder}/lcs/dependency-update-report.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
stepOptions:
  step-01-init-analyze: './step-01-init-analyze.md'
  step-02-plan: './step-02-plan.md'
  step-03-approve-execute: './step-03-approve-execute.md'
  step-04-report: './step-04-report.md'
---

# Step 1b: Continue Workflow

## STEP GOAL:

To resume the dependency update workflow from where it was left off in a previous session, by reading the sidecar-state and report progress, and routing to the correct next step.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip reading the current state
- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ You are resuming an interrupted dependency update session
- ✅ Be clear about what was completed and what remains

### Step-Specific Rules:

- 🎯 Focus ONLY on determining current state and routing
- 🚫 FORBIDDEN to re-execute completed steps
- 🚫 FORBIDDEN to skip incomplete steps
- 💬 Present a clear summary of progress before routing

## CONTEXT BOUNDARIES:

- User has run this workflow before
- Sidecar-state has dependency-update task statuses
- Report may exist with partial stepsCompleted
- Need to route to the correct next step

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly.

### 1. Welcome Back

"**Content de te revoir ! Voyons où on en était...**"

### 2. Read Current State

Load `{sidecarStatePath}` and read dependency-update task statuses.
Load `{reportOutputPath}` if it exists and read frontmatter `stepsCompleted`.

### 3. Present Progress Summary

"**État de la mise à jour des dépendances :**

| Tâche | Statut |
|-------|--------|
| Analyse | {done/in_progress/pending/failed} |
| Plan | {done/in_progress/pending/failed} |
| Approbation & Exécution | {done/in_progress/pending/failed} |
| Rapport | {done/in_progress/pending/failed} |

**Steps complétés :** {stepsCompleted from report or 'aucun'}
**Dernier step :** {last step name}"

### 4. Handle Failed Tasks

If any task has status `failed`:

"**⚠️ Tâche en échec détectée : {task_name}**

Souhaitez-vous :
- **[R]** Reprendre cette tâche depuis le début
- **[S]** Sauter et continuer au step suivant
- **[X]** Quitter pour investiguer"

**Wait for user response and handle accordingly.**

### 5. Determine and Route to Next Step

Based on sidecar-state task statuses, determine the next incomplete step:

- If `analysis` is `done` but `plan` is `pending` → route to `step-02-plan`
- If `plan` is `done` but `approval` is `pending` → route to `step-03-approve-execute`
- If `approval` is `in_progress` → route to `step-03-approve-execute` (resume mid-execution)
- If `execution` is `done` but `report` is `pending` → route to `step-04-report`

"**Prochaine étape : {step name}**

On reprend là où on s'est arrêté."

Load, read entirely, then execute the appropriate step from `{stepOptions}`.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Sidecar-state read and current progress identified
- Report frontmatter checked for stepsCompleted
- Clear progress summary presented to user
- Failed tasks handled with user input
- Routed to correct next step

### ❌ SYSTEM FAILURE:

- Not reading sidecar-state
- Re-executing completed steps
- Skipping incomplete steps without user consent
- Routing to wrong step

**Master Rule:** Resume from exactly where we left off. No re-work, no skipping.
