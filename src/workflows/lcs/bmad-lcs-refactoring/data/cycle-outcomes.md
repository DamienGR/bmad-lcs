# Cycle Outcomes — Protocols & Templates

## Purpose

Reference file for step-02 cycle outcomes. Contains presentation templates, outcome protocols, and retry logic extracted from the main step file to comply with size limits. Conrad loads these when reaching the corresponding sections.

---

## Results Presentation Template

Present the Refactorer's summary to the user in this format:

"**Resultat du refactoring : `{current_target}`**

| Aspect | Detail |
|--------|--------|
| **Cible** | `{current_target}` |
| **Type de refactoring** | {extract function / reduce complexity / decouple / etc.} |
| **Fichiers modifies** | {list from Refactorer summary} |
| **Safety nets** | {pass status} {retry info if applicable} |
| **Avant** | {brief description of original state} |
| **Apres** | {brief description of refactored state} |

**Souhaitez-vous merger ce refactoring ?**"

---

## Retry Correction Protocol

When safety nets FAIL in the worktree (first attempt):

1. "**Safety nets en echec dans le worktree.** {N} test(s) en echec."
2. Present failure details to understand what broke
3. Update `currentCycle.retryCount: 1`
4. Re-spawn or re-execute Refactorer with failure context (what tests failed, what needs fixing)
5. Re-run safety nets in worktree
   - **If PASS after retry:** "**Correction reussie.** Safety nets vertes apres retry." — proceed to results presentation
   - **If STILL FAILING:** "**Echec apres retry.** Le refactoring ne peut pas etre valide." — execute cleanup, flag as `failed`, go to non-merge state update

---

## Merge Protocol

When user selects [M] Merge:

1. Switch to main branch
2. Run `git merge {branch_name}` to merge the worktree's branch
3. Verify merge succeeded (no conflicts)
   - If conflict: present conflict details to user, assist with resolution
4. Execute cleanup: load cleanup-protocol.md, remove worktree + delete branch
5. Flag target as `merged`
6. Update sidecar-state: move target from remainingTargets to completedTargets with status `merged`, clear currentCycle
7. Append target result to refactoring report (Targets Treated section) with:
   - Risk score, complexity type, refactoring type applied
   - Status: merged
   - Before/after description (from Refactorer summary)
   - Safety nets result (pass, retry count)
   - Branch name used

---

## Alternative Refactoring Protocol

When user selects [R] Alternative refactoring:

1. "**Refactoring alternatif demande.** Le Refactorer va tenter une approche differente."
2. Ask user what they didn't like and what to try differently
3. Collect feedback as `alternative_instructions`
4. Keep the SAME worktree (reset changes with `git checkout .` in worktree)
5. Re-execute from Spawn Refactorer (section 4) with alternative instructions injected
6. Note: this counts as a new attempt, NOT as a retry (retryCount stays for safety net failures only)

---

## Skip/Rollback Protocol

When user selects [S] Skip:

1. Execute cleanup: load cleanup-protocol.md, remove worktree + delete branch
2. Flag target as `rejected`
3. Update sidecar-state: move target from remainingTargets to completedTargets with status `rejected`, clear currentCycle
4. Append target result to refactoring report (Targets Treated section) with:
   - Status: rejected
   - Reason: user declined merge

---

## Non-Merge State Update

For targets exiting the cycle without going through results/approval (failed, spawn-failed, obsolete):

1. Move target from `remainingTargets` to `completedTargets` with appropriate status flag
2. Clear `currentCycle` in sidecar-state
3. Append target result to refactoring report (Targets Treated section) with:
   - Status flag and reason
   - Any available context (failure details, obsolescence reason)
4. Update Metrics counters in report

---

## Progress Presentation Template

After each cycle completes, present progress:

"**Cycle termine pour `{current_target}` — Statut : {status flag}**

**Progression :** {completed}/{total} cibles traitees

| Merged | Failed | Rejected | Obsolete | Spawn-Failed |
|--------|--------|----------|----------|--------------|
| {n}    | {n}    | {n}      | {n}      | {n}          |

**Cibles restantes :** {N}"
