---
name: 'step-03-mcp-check'
description: 'Inventory available MCP tools and smoke test critical ones'

nextStepFile: './step-04-init-state.md'
mcpToolsRegistry: '../data/mcp-tools-registry.md'
---

# Step 3: MCP Tools Check

## STEP GOAL:

To inventory which MCP tools are available in the current environment, perform smoke tests on critical tools (Chrome DevTools, Playwright), and map missing tools to impacted LCS phases.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER assume a tool is available — verify each one
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- 🎯 Check every tool in the registry — no shortcuts
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — direct, methodical, reassuring
- ✅ Missing MCP tools are not errors — they reduce capabilities but don't block
- ✅ Be clear about what's impacted when tools are missing

### Step-Specific Rules:

- 🎯 Focus ONLY on MCP tool inventory and smoke testing
- 🚫 FORBIDDEN to write any files — that's step 04
- 🚫 FORBIDDEN to install or configure tools — just detect and report
- 💬 For each missing tool: explain which phase is impacted and what capability is lost
- 📋 Smoke tests are quick functional checks, not full test suites

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Store: tool inventory and smoke test results in memory for step 04
- 📖 Track each tool: name, status (available/missing/error), impact
- 🚫 Do NOT write any files — data persists in memory for step 04

## CONTEXT BOUNDARIES:

- Available: repo_path, site_url, environment capabilities, framework detection from steps 01-02
- Focus: MCP tool availability only
- Limits: do not attempt to use tools for analysis — just verify they respond
- Dependencies: none — MCP tools are independent of project state

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Load MCP Tools Registry

Load `{mcpToolsRegistry}` to get the complete list of recommended MCP tools with their detection methods and phase impact mapping.

### 2. Inventory MCP Tools

For each tool in the registry:

1. Attempt to detect if the MCP tool is available in the current environment
2. The detection method varies by tool — the registry specifies how to check each one
3. Record status: `available`, `missing`, or `error` (tool found but not responding)

**Detection approach:**
- MCP tools are typically available as configured MCP servers in the Claude Code environment
- Check if the tool responds to a basic capability query
- If the tool is not configured, mark as `missing`

### 3. Smoke Test Critical Tools

**For Chrome DevTools MCP** (if available):
- Attempt a minimal operation: request browser version or list targets
- Record: responds correctly / error / timeout

**For Playwright MCP** (if available):
- Attempt a minimal operation: request browser list or accessibility snapshot
- Record: responds correctly / error / timeout

**If smoke test fails for an available tool:**
- Mark status as `error` instead of `available`
- Note the specific error for the report

### 4. Site Access Verification (SSL Check)

**Condition:** Playwright MCP is `available` AND smoke test passed AND `{site_url}` is known.

If condition not met: skip this section entirely.

**Sequence:**

1. **Attempt navigation** to `{site_url}` using Playwright MCP
2. **Evaluate result:**

   **✅ Navigation succeeds:**
   - Record: `site_access = ok`
   - Continue to next section

   **⚠️ SSL certificate error detected** (self-signed, expired, CN mismatch — typical for DDEV local environments):

   a. Inform the user:
      > "⚠️ Playwright MCP cannot accéder à `{site_url}` — certificat SSL rejeté. C'est normal pour les environnements DDEV qui utilisent des certificats auto-signés."

   b. **Auto-fix:** Locate the Playwright MCP server configuration (in `.claude/settings.local.json`, `.claude/settings.json`, or `~/.claude/settings.json`) and add `--ignore-https-errors` to the Playwright MCP server `args` array.

   c. **Notify user:** Display the exact change made and inform them:
      > "✅ Configuration MCP Playwright mise à jour avec `--ignore-https-errors`. Un redémarrage de la session Claude Code est nécessaire pour appliquer le changement. Relancez la session puis ré-exécutez le pre-flight."

   d. **HALT** the workflow — the MCP server must be restarted for the change to take effect. Do NOT continue to step 04.

   **❌ Other navigation error** (site down, DNS failure, connection refused):
   - Record: `site_access = error`, note the specific error
   - Inform user: the site at `{site_url}` is not reachable — verify the local server (DDEV) is running
   - Do NOT halt — this may be resolved before the Audit phase

### 5. Map Missing Tools to Phase Impact

For each missing or errored tool, reference the registry to identify:
- Which LCS phase(s) depend on this tool
- What specific capability is lost
- Whether the phase can still proceed (degraded) or is blocked

### 6. Present MCP Inventory

Display a summary table:

```
| MCP Tool            | Status    | Smoke Test | Impacted Phase(s)         |
|---------------------|-----------|------------|---------------------------|
| Chrome DevTools     | available | OK         | —                         |
| Playwright          | available | OK         | —                         |
| MySQL               | missing   | —          | Audit (DB inspection)     |
| Semgrep             | missing   | —          | Audit (security analysis) |
| ESLint              | available | —          | Safety Nets               |
| Git MCP             | available | —          | Refactoring (worktree)    |
| Snyk                | missing   | —          | Dep. Update (vuln audit)  |
| Lighthouse          | missing   | —          | Audit (perf baselines)    |
| Context7            | available | —          | All (accurate API docs)   |
| WordPress MCP       | missing   | —          | Audit (WP-specific)       |
| Drupal Tools MCP    | missing   | —          | Audit (Drupal-specific)   |
```

Report totals:
- "**X** tools available, **Y** missing, **Z** errors"
- If critical tools (Chrome DevTools, Playwright) are missing: highlight this prominently

### 7. Proceed to State Initialization

Display: "**MCP inventory complete — Proceeding to state initialization...**"

#### Menu Handling Logic:

- After inventory is presented, immediately load, read entire file, then execute `{nextStepFile}`

#### EXECUTION RULES:

- This is an auto-proceed step with no user menu choices
- IF the user wants to discuss tool availability: respond, then proceed

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Every tool in the registry checked
- Smoke tests executed for critical tools (if available)
- Site access verified via Playwright when available (SSL check)
- SSL auto-fix applied if certificate error detected
- Missing tools mapped to impacted phases
- Inventory table presented clearly
- All data stored in memory for step 04

### ❌ SYSTEM FAILURE:

- Skipping tools from the registry
- Not performing smoke tests on critical tools
- Skipping SSL site access verification when Playwright is available
- Not applying auto-fix when SSL error is detected
- Not mapping missing tools to phase impacts
- Attempting to install or configure tools (EXCEPTION: SSL auto-fix on MCP config is allowed)
- Writing files before step 04 (EXCEPTION: MCP settings update for SSL fix is allowed)

**Master Rule:** Check every tool in the registry. Report facts, not opinions. Missing tools are normal — just document the impact. SSL certificate issues on DDEV are expected — auto-fix and inform.
