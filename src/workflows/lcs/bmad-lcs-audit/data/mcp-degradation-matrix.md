# MCP Degradation Matrix — Audit Phase

## Tool Impact Assessment

| MCP Tool | Teammate | If Absent | Severity | Fallback Strategy |
|----------|----------|-----------|----------|-------------------|
| Chrome DevTools MCP | Site Explorer | Cannot navigate live site | **HARD BLOCKER** | ERROR — site exploration impossible without Chrome DevTools. Pre-flight should have flagged this. |
| Semgrep MCP | Code Analyst | No automated security analysis | Soft | Manual code review for common vulnerabilities (SQLi, XSS, CSRF). Less thorough but functional. |
| MySQL MCP | Code Analyst | Cannot inspect DB architecture | Medium | Infer data architecture from code (ORM models, schema files, wp_options references). Partial coverage. |
| Snyk MCP | Lead (dep scan) | No automated vulnerability scan | Soft | Use `npm audit` / `composer audit` CLI commands. Same data, different format. |
| Lighthouse MCP | Site Explorer | No performance/accessibility baselines | Soft | Skip performance baselines. Note in audit report that baselines are unavailable. |
| Context7 | Code Analyst | No up-to-date API documentation | Soft | Rely on built-in knowledge. Risk of outdated API references for recent framework versions. |

## Usage Instructions

**In step-01-init-spawn.md:**
1. Read capability-matrix.md to get MCP tool availability
2. Load this file to map each missing tool to its impact
3. If Chrome DevTools MCP is absent → stop and report error
4. For all other missing tools → note the fallback in spawn prompts so teammates adapt
5. Present the adaptation summary to the user

**In spawn prompts:**
- Include the list of available MCP tools
- Include fallback instructions for missing tools
- Teammates must check tool availability before attempting to use them
