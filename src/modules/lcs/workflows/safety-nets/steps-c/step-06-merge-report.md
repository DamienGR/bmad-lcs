---
name: 'step-06-merge-report'
description: 'Merge safety branch, generate safety-nets report, update sidecar-state, recommend next workflow'

sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
safetyNetBuilderArtifact: '{output_folder}/lcs/safety-nets/safety-net-builder-report.md'
staticGuardArtifact: '{output_folder}/lcs/safety-nets/static-guard-report.md'
reportOutputPath: '{output_folder}/lcs/safety-nets-report.md'
---

# Step 6: Merge & Report

## STEP GOAL:

To merge the safety branch (`lcs/pre-safety-nets`) into the working branch, generate the final safety-nets deployment report, update sidecar-state to mark the safety-nets phase as complete, and recommend the next workflow in the LCS pipeline (dependency-update).

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER force-merge — resolve conflicts carefully
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — wrapping up the safety-nets phase
- ✅ The merge is the final gate — safety branch only merges with a green baseline
- ✅ The report is the deliverable — make it clear and useful
- ✅ Recommend next steps — guide the developer forward

### Step-Specific Rules:

- 🎯 Focus on merge, report, and phase completion
- 🚫 FORBIDDEN to modify test files or config
- 🚫 FORBIDDEN to re-run tests — that was step 04
- 💬 Report clearly — this is the last thing the developer sees before moving on
- 📋 Sidecar-state MUST be updated to mark phase complete

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Write final report at `{reportOutputPath}`
- 📖 Update sidecar-state: phase safety-nets → done
- 🚫 Do NOT skip the merge — it's the whole point of the safety branch

## CONTEXT BOUNDARIES:

- Available: validation results, discovery integration, teammate artifacts, sidecar-state with all task statuses
- Focus: merge + report + phase completion
- Limits: do not modify code, do not write new tests
- Dependencies: step 05 completed — discovery integration done

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Pre-Merge Check

Read `{sidecarStatePath}` to verify:
- `baseline_status` is `green` or `partial` (accepted by user in step 04)
- `return_branch` — the branch to merge into
- `safety_branch` — should be `lcs/pre-safety-nets`

**If baseline_status is NOT green or partial:**
- "⚠️ La baseline n'a pas été validée. Le merge ne peut pas procéder sans validation (step 04)."
- STOP — do not merge.

### 2. Merge Safety Branch

**Switch to return branch:**
- Run `git checkout {return_branch}`

**Merge safety branch:**
- Run `git merge lcs/pre-safety-nets --no-ff -m "LCS: merge safety-nets deployment (smoke, e2e, snapshots, lint, hooks, CI)"`

**If merge succeeds:**
- "**Merge réussi.** La branche `lcs/pre-safety-nets` a été fusionnée dans `{return_branch}`."

**If merge conflicts:**
- "**⚠️ Conflits de merge détectés.**"
- List conflicting files
- "**Options :**"
- "- **[R] Résoudre** — je tente de résoudre les conflits automatiquement (fichiers de config uniquement)"
- "- **[M] Manuel** — vous résolvez les conflits manuellement, puis tapez 'fait' quand c'est terminé"
- "- **[A] Abandonner** — annuler le merge (`git merge --abort`), conserver la branche séparée"

Wait for user response:
- If R: Attempt auto-resolution for non-code files (config, test files). If code conflicts exist, fall back to M.
- If M: Wait for user to signal completion. Verify merge is complete.
- If A: Abort merge. Note that safety-nets are on separate branch. Continue to report.

### 3. Optional: Clean Up Branch

**If merge succeeded:**
- "Souhaitez-vous supprimer la branche `lcs/pre-safety-nets` ? [O] Oui / [N] Non (conserver)"
- If O: Run `git branch -d lcs/pre-safety-nets`
- If N: Keep branch for reference

**If merge was aborted:**
- Keep branch — it contains all safety-net work

### 4. Generate Final Report

Write `{reportOutputPath}` with:

```yaml
---
phase: safety-nets
produced_by: conrad-lead
date: {current_date}
status: complete
baseline_status: {green / partial}
---
```

```markdown
# Safety Nets Deployment Report

## Summary

- **Phase:** Safety Nets (Phase 2)
- **Baseline status:** {green / partial}
- **Safety branch:** lcs/pre-safety-nets → {merged into {return_branch} / kept separate}
- **Teammates deployed:** Safety Net Builder + Static Guard
- **Execution mode:** {parallel (Agent Teams) / sequential}
- **MCP degradation:** {none / list degraded tools}

## Test Coverage

| Category | Files | Tests | Passed | Known Failures | Pending Fixes |
|----------|-------|-------|--------|----------------|---------------|
| Smoke tests | {n} | {n} | {n} | {n} | {n} |
| E2E tests | {n} | {n} | {n} | {n} | {n} |
| Visual snapshots | {n} | {n} | {n} | {n} | {n} |

### Zone Coverage

| Zone | Risk | Smoke | E2E | Snapshot | Tier |
|------|------|-------|-----|----------|------|
| {zone} | {critical/high/medium/low} | ✅/❌ | ✅/❌/TIER1 | ✅/❌ | {1/2} |

## Static Guards

| Guard | Status | Config File |
|-------|--------|-------------|
| ESLint / Linting | {configured/extended} | {path} |
| Type checking | {configured/existing/N/A} | {path} |
| Git hooks (pre-commit) | {installed} | {path} |
| Git hooks (pre-push) | {installed} | {path} |
| CI pipeline | {created/extended} | {path} |

## Fixture Strategy

- **Strategy:** {selected strategy}
- **Files:** {list fixture files}

## Known Failures

| Test | Zone | Classification | Description |
|------|------|---------------|-------------|
| {test} | {zone} | known_failure | {description} |

## Discovery Findings Integrated

- **Bugs discovered:** {count} → integrated into risk map
- **Unreachable paths:** {count} → integrated into risk map
- **Undocumented behaviors:** {count} → integrated into risk map
- **Lint baseline:** {count} errors documented

## Files Added to Repository

### Test Files
{list all test files}

### Configuration Files
{list all config files}

### Infrastructure
{list bootstrap, fixtures, CI workflow}

## Recommendations

1. **Next phase:** Run `dependency-update` workflow (Phase 2b) to address outdated dependencies identified during audit
2. **Known failures:** Address known_failure tests during refactoring phase
3. **Pending fixes:** {if any} — {count} tests need correction before full green baseline
4. **UNCERTAIN zones:** {if any} — run Q&A resolution, then upgrade from Tier 1 to Tier 2 coverage
```

### 5. Update Sidecar State — Phase Complete

Update `{sidecarStatePath}`:
- Set `phases.safety-nets.status` to `done`
- Set `phases.safety-nets.completed_date` to current date
- Set `phases.safety-nets.baseline_status` to `green` or `partial`
- Set `phases.safety-nets.report_path` to `{reportOutputPath}`
- Set `merge-report` task status to `done`

### 6. Present Final Summary and Recommend Next

"**Phase Safety Nets terminée ! ✅**

**Résumé :**
- **Baseline :** {verte / partielle}
- **Tests déployés :** {X} smoke + {Y} e2e + {Z} snapshots
- **Guards statiques :** ESLint + hooks + CI
- **Découvertes intégrées :** {N} trouvailles → risk map
- **Rapport :** `{reportOutputPath}`

---

**Prochaine étape recommandée :**

Le pipeline LCS continue avec **dependency-update** (Phase 2b) — mise à jour des dépendances obsolètes et vulnérables identifiées pendant l'audit.

Le filet de sécurité est maintenant en place : chaque modification future sera automatiquement vérifiée par les tests smoke, e2e, les snapshots visuels, et le linting. 🛡️

**Merci d'avoir déployé les filets de sécurité avec l'équipe LCS.**"

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Pre-merge check passed (baseline validated)
- Safety branch merged (or kept separate with user agreement)
- Final report written at `{reportOutputPath}` with complete frontmatter
- Report includes all sections (test coverage, static guards, known failures, discoveries, files, recommendations)
- Sidecar-state updated: phase safety-nets → done
- Next workflow recommended (dependency-update)

### ❌ SYSTEM FAILURE:

- Merging without baseline validation
- Force-merging with unresolved conflicts
- Not writing the final report
- Report missing sections or frontmatter
- Not updating sidecar-state to mark phase complete
- Not recommending next workflow
- Modifying test or project files

**Master Rule:** The safety-nets phase is NOT complete until the report is written AND sidecar-state is updated. The merge is critical but the report and state update are equally important. This is the final step — leave the developer with clear next steps.
