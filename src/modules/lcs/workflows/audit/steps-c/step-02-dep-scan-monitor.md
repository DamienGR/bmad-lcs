---
name: 'step-02-dep-scan-monitor'
description: 'Run dependency scan while monitoring teammate artifact completion'

nextStepFile: './step-03-synthesize.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
depScanOutputPath: '{output_folder}/lcs/audit/dependency-scan.md'
codeAnalysisPath: '{output_folder}/lcs/audit/code-analysis.md'
siteExplorationPath: '{output_folder}/lcs/audit/site-exploration.md'
---

# Step 2: Dependency Scan & Monitor Teammates

## STEP GOAL:

To run the dependency scan on the project (composer.json / package.json) while monitoring teammate artifact completion. Proceed to synthesis only when all 3 inputs are ready.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip the dependency scan
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess, subagent, or tool you do not have access to, you MUST still achieve the outcome in your main context thread
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — running the dep scan yourself while your team works
- ✅ Be efficient — this runs in parallel with teammates
- ✅ Monitor teammates by checking artifact existence, not by polling them

### Step-Specific Rules:

- 🎯 Focus on dependency scan + teammate monitoring
- 🚫 FORBIDDEN to start synthesis — that's step 03
- 🚫 FORBIDDEN to write to teammate artifacts — they produce their own
- 💬 Report dep scan results clearly with severity classification
- 📋 Use subprocess Pattern 3 for parsing dependency files if available

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Write dependency-scan.md artifact when scan complete
- 📖 Update sidecar-state: dependency-scan → done
- 🚫 Do NOT proceed to step 03 until all 3 artifacts exist with status: complete

## CONTEXT BOUNDARIES:

- Available: sidecar-state with project info, teammates spawned (or running sequentially)
- Focus: dependency scan + monitoring
- Limits: do not analyze code structure or navigate site
- Dependencies: step 01 completed, teammates spawned

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Run Dependency Scan

Read project info from `{sidecarStatePath}` (repo_path, framework).

**Locate dependency files:**
- `composer.json` / `composer.lock` (PHP projects)
- `package.json` / `package-lock.json` (Node.js projects)
- Both may exist in the same project

**If Snyk MCP available:**
- Run Snyk vulnerability analysis on each dependency file
- Extract: vulnerability name, severity (critical/high/medium/low), CVSS score, affected package, fix recommendation

**If Snyk MCP NOT available (fallback):**
- Run `composer audit` (for composer.json)
- Run `npm audit` (for package.json)
- Parse output for vulnerabilities and outdated packages

**Also check for outdated packages:**
- Run `composer outdated` or `npm outdated`
- Note major version gaps (security risk indicator)

### 2. Write Dependency Scan Artifact

Write `{depScanOutputPath}` with standard frontmatter:

```yaml
---
phase: audit
task_id: dependency-scan
produced_by: conrad-lead
date: {current_date}
status: complete
---
```

**Content structure:**
- **Summary:** Total dependencies, total vulnerabilities by severity, total outdated
- **Vulnerabilities:** Table with package, version, vulnerability, severity, CVSS, fix
- **Outdated Packages:** Table with package, current version, latest version, major gap flag
- **Recommendations:** Prioritized list of updates (critical vulnerabilities first)

### 3. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `tasks.audit.dependency-scan.status` to `done`

### 4. Present Dependency Scan Summary

"**Scan de dépendances terminé.**

| Métrique | Valeur |
|----------|--------|
| Dépendances totales | {count} |
| Vulnérabilités critiques | {count} |
| Vulnérabilités hautes | {count} |
| Vulnérabilités moyennes | {count} |
| Packages obsolètes | {count} |

Détails complets dans `audit/dependency-scan.md`."

### 5. Check Teammate Artifacts

**Check Code Analyst artifact:**
- Does `{codeAnalysisPath}` exist?
- If exists: read frontmatter — is `status: complete`?

**Check Site Explorer artifact:**
- Does `{siteExplorationPath}` exist?
- If exists: read frontmatter — is `status: complete`?

**Present teammate status:**

"**Statut des teammates :**

| Teammate | Artefact | Statut |
|----------|----------|--------|
| Code Analyst | code-analysis.md | {complete / en cours / absent} |
| Site Explorer | site-exploration.md | {complete / en cours / absent} |"

### 6. Route Based on Teammate Status

**If BOTH artifacts exist with status: complete:**
- "Tous les inputs sont prêts. Passage à la synthèse."
- Load, read entirely, then execute `{nextStepFile}`

**If one or both artifacts NOT ready:**
- "En attente des teammates. Leurs artefacts apparaîtront dans `_bmad-output/lcs/audit/`."
- "Vous pouvez :"
- "- **Attendre** — je vérifierai à nouveau dans quelques minutes"
- "- **Continuer plus tard** — l'audit reprendra où il en est (tapez 'plus tard')"
- "- **Vérifier** — tapez 'check' pour que je revérifie maintenant"

**Wait for user response.**

- If user says "check" or similar → re-check artifacts, redisplay status
- If user says "plus tard" or wants to stop → inform that resume is available via step-01b
- If user waits and artifacts become ready → proceed to step 03
- If ~15 minutes pass with no progress → "Les teammates semblent prendre du temps. Vérifiez que les instances Agent Teams sont actives, ou relancez l'audit pour un fallback séquentiel."

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Dependency scan completed and artifact written
- Sidecar-state updated (dependency-scan: done)
- Both teammate artifacts verified (exist + status: complete)
- Proceeding to synthesis with all 3 inputs ready

### ❌ SYSTEM FAILURE:

- Skipping the dependency scan
- Not writing the artifact with proper frontmatter
- Proceeding to synthesis without all 3 inputs ready
- Writing to teammate artifacts
- Not offering the user options when teammates are delayed

**Master Rule:** All 3 artifacts (code-analysis, site-exploration, dependency-scan) MUST exist with status: complete before proceeding to step 03.
