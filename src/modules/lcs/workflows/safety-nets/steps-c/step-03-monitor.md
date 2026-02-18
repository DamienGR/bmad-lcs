---
name: 'step-03-monitor'
description: 'Monitor Safety Net Builder and Static Guard teammate progress, route when both complete'

nextStepFile: './step-04-validate-triage.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
safetyNetBuilderArtifact: '{output_folder}/lcs/safety-nets/safety-net-builder-report.md'
staticGuardArtifact: '{output_folder}/lcs/safety-nets/static-guard-report.md'
---

# Step 3: Monitor Teammates

## STEP GOAL:

To monitor the progress of both spawned teammates (Safety Net Builder + Static Guard) by checking their artifact existence and frontmatter status. Auto-proceed to validation when both are complete. Offer user options if teammates are delayed.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER write to teammate artifacts — they produce their own
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — overseeing your safety-nets deployment team
- ✅ Your teammates are working autonomously — your role is oversight, not execution
- ✅ Monitor by checking artifact existence, not by interfering with teammate work
- ✅ Be patient but proactive — offer options if things stall

### Step-Specific Rules:

- 🎯 Focus ONLY on monitoring teammate completion
- 🚫 FORBIDDEN to write test files, config files, or artifacts — teammates do that
- 🚫 FORBIDDEN to start validation — that's step 04
- 🚫 FORBIDDEN to modify teammate artifacts
- 💬 Report teammate status clearly with a table
- 📋 Detect completion via artifact existence + frontmatter `status: complete`

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Update sidecar-state when teammates complete
- 📖 Auto-proceed when BOTH teammates are done
- 🚫 Do NOT proceed to step 04 until both artifacts exist with `status: complete`

## CONTEXT BOUNDARIES:

- Available: sidecar-state with branch info, spawn mode (parallel/sequential), task statuses
- Focus: teammate monitoring only
- Limits: do not analyze test content, do not write files, do not validate tests
- Dependencies: step 02 completed — teammates spawned (or running sequentially)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Read Spawn Mode

Read `{sidecarStatePath}` to determine:
- `spawn_mode` — parallel or sequential
- `safety-net-builder` task status
- `static-guard` task status

**If sequential mode** and both teammates already ran in step 02:
- Both artifacts should already exist
- Skip to section 3 (Check Teammate Artifacts)

**If parallel mode:**
- Teammates are running as independent agents
- Proceed to artifact checking

### 2. Check Teammate Artifacts

**Check Safety Net Builder artifact:**
- Does `{safetyNetBuilderArtifact}` exist?
- If exists: read frontmatter — is `status: complete`?
- If `status: failed`: flag as failed

**Check Static Guard artifact:**
- Does `{staticGuardArtifact}` exist?
- If exists: read frontmatter — is `status: complete`?
- If `status: failed`: flag as failed

### 3. Present Teammate Status

"**Statut des teammates :**

| Teammate | Artefact | Statut |
|----------|----------|--------|
| Safety Net Builder | safety-net-builder-report.md | {complete / en cours / absent / échoué} |
| Static Guard | static-guard-report.md | {complete / en cours / absent / échoué} |"

### 4. Route Based on Status

**Case A — BOTH complete:**

"**Tous les teammates ont terminé !** Les artefacts sont prêts pour la validation."

Update `{sidecarStatePath}`:
- Set `safety-net-builder` task status to `done`
- Set `static-guard` task status to `done`

Auto-proceed: load, read entire file, then execute `{nextStepFile}`

**Case B — One or both FAILED:**

"**⚠️ Échec détecté :**
- {teammate_name}: `status: failed`
- Raison probable: {read any error details from artifact frontmatter}

**Options :**
- **[R] Relancer** — re-spawn le teammate en échec
- **[S] Sauter** — continuer sans cet artefact (la validation sera partielle)
- **[Q] Quitter** — sauvegarder l'état et reprendre plus tard"

Wait for user response:
- If R: Re-read the appropriate spawn prompt, re-spawn the failed teammate. Return to section 2.
- If S: Mark the failed teammate as `skipped` in sidecar-state. If the other is `complete`, proceed to step 04. If both failed, warn user that validation will have no data.
- If Q: Inform user that resume is available via step-01b. End.

**Case C — One or both NOT READY (parallel mode):**

"**En attente des teammates.** Leurs artefacts apparaîtront dans `_bmad-output/lcs/safety-nets/`.

**Options :**
- **[V] Vérifier** — je revérifie maintenant
- **[L] Plus tard** — sauvegarder l'état et reprendre plus tard"

Wait for user response:
- If V or "check": Re-execute section 2 (Check Teammate Artifacts), then re-present status.
- If L or "plus tard": Inform user that resume is available via step-01b. End.
- If user waits and responds later: Re-check artifacts, redisplay status.
- If ~15 minutes pass with no progress: "Les teammates semblent prendre du temps. Vérifiez que les instances Agent Teams sont actives, ou redémarrez avec l'option séquentielle (step-01b → relancer en mode séquentiel)."

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Spawn mode correctly read from sidecar-state
- Both teammate artifacts checked for existence and frontmatter status
- Teammate status presented clearly to user
- Auto-proceed when both complete
- Failed teammates handled with user options (retry/skip/quit)
- Delayed teammates handled with user options (check/later)
- Sidecar-state updated when teammates complete
- Proceeding to step 04 with both artifacts ready

### ❌ SYSTEM FAILURE:

- Proceeding to step 04 without both artifacts confirmed complete (or explicitly skipped)
- Writing to teammate artifacts
- Writing test files or config files (that's for teammates)
- Not offering user options when teammates are delayed or failed
- Not reading frontmatter to verify `status: complete`
- Ignoring `status: failed` in artifact frontmatter

**Master Rule:** BOTH teammate artifacts MUST be confirmed `status: complete` (or explicitly skipped by user) before proceeding to step 04. Conrad monitors — he does NOT do the work himself.
