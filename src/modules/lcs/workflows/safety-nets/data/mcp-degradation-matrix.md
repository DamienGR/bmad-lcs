# MCP Degradation Matrix — Safety Nets

**Purpose:** Define fallback strategies when MCP tools are unavailable during safety-net deployment.

---

## Degradation Levels

| Level | Description |
|-------|-------------|
| **Full** | MCP tool available — use all capabilities |
| **Degraded** | MCP tool missing — use CLI/manual fallback with reduced capability |
| **Manual** | No automated alternative — write files only, user validates manually |

---

## Tool Matrix

### Playwright MCP

| Aspect | Full (MCP Available) | Degraded/Manual (MCP Missing) |
|--------|---------------------|-------------------------------|
| **E2E tests** | Navigate live site, discover elements, write tests with real selectors | Write tests based on audit report code analysis. Mark: `// NEEDS_MANUAL_VERIFICATION` |
| **Smoke tests** | Navigate to URLs, verify responses, check console errors | Write test files with URL list from audit. First run establishes actual results |
| **Visual snapshots** | Capture screenshots via MCP, establish baselines automatically | Write snapshot test files. Mark: `// BASELINE_PENDING — first run establishes baseline` |
| **Element discovery** | MCP reveals actual page structure and interactive elements | Infer from code analysis — selectors may need manual adjustment |
| **Impact** | High — tests are validated against live site during creation | Tests are "best effort" — may need fixes on first manual run |

**Recommendation when missing:** Write all test files with clear markers. Provide a `first-run-guide.md` explaining how to run tests manually and fix any selector issues.

---

### ESLint MCP

| Aspect | Full (MCP Available) | Degraded/Manual (MCP Missing) |
|--------|---------------------|-------------------------------|
| **Config validation** | Run lint on sample files, verify config works | Write config files only. Mark: `// LINT_BASELINE_PENDING` |
| **Baseline capture** | Capture existing violation count automatically | User must run lint manually to see baseline |
| **Rule testing** | Verify each added rule doesn't cause excessive false positives | Rules written based on best practices — may need tuning |
| **Impact** | Low — ESLint config is mostly static. CLI fallback works well |

**Recommendation when missing:** Write config files. Include a `lint-setup-guide.md` with commands to run lint manually.

---

## Degradation Summary Table

| Tool | Used By | Full Capability | Missing Impact | Fallback Quality |
|------|---------|-----------------|----------------|-----------------|
| Playwright MCP | Safety Net Builder | Live site interaction during test creation | HIGH — tests are unverified | Medium — write-only, manual verification needed |
| ESLint MCP | Static Guard | Lint validation during config | LOW — config is static | High — CLI commands work fine |

---

## Fallback Files to Generate

When operating in degraded mode, teammates should generate additional helper files:

### If Playwright MCP Missing:
- `tests/FIRST-RUN-GUIDE.md` — Instructions for first manual test run
- Expected: some tests may fail due to selector mismatches
- How to fix common issues (wrong selectors, timing, etc.)

### If ESLint MCP Missing:
- `LINT-SETUP-GUIDE.md` — Instructions to run lint and establish baseline
- Expected violation count estimate
- How to address initial violations
