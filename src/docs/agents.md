# Agents Reference

LCS includes 1 BMAD agent (the Lead) and 6 headless teammates:

---

## Conrad 🛡️ — Legacy Consolidation Lead

**ID:** `_bmad/lcs/agents/lcs-lead.md`
**Icon:** 🛡️
**Memory:** Sidecar (retains consolidation state between sessions)

**Role:**
Orchestrator and single entry point for the LCS module. Coordinates consolidation phases, spawns headless teammates via Agent Teams (or executes sequentially as fallback), synthesizes results, and manages the consolidation state.

**When to Use:**
Conrad is your primary contact for all LCS operations. You interact with him directly — he manages the team behind the scenes.

**Key Capabilities:**
- Full project audit orchestration
- Safety net deployment coordination
- Refactoring and testing oversight
- Impact analysis for daily development
- Intelligent calibration (adapts parallelism to project size)
- Framework-specific expertise (via loaded profiles)

**Menu Commands:**

| Trigger | Command | Description |
|---------|---------|-------------|
| [AU] | Audit | Launch full project audit (code + site + dependencies) |
| [FI] | Safety Nets | Deploy multi-layer safety nets |
| [RE] | Refactoring | Targeted refactoring for testability |
| [PR] | Deep Testing | Increase test coverage and depth |
| [ST] | Status | Global consolidation state dashboard |
| [DA] | Dismiss Agent | Exit LCS Lead |

---

## Headless Teammates

Teammates are NOT BMAD agents — they are Claude Code instances spawned by Conrad via Agent Teams. They execute structured BMAD workflows without menus or user interaction. You don't interact with them directly; Conrad manages them.

### Code Analyst

**Spawned During:** Audit (Phase 1)
**Tools:** Code reading, AST, framework profile, Semgrep MCP, MySQL MCP
**Purpose:** Static analysis, structure mapping, complexity assessment, framework pattern identification, business logic extraction.

### Site Explorer

**Spawned During:** Audit (Phase 1)
**Tools:** MCP Chrome DevTools, Lighthouse MCP
**Purpose:** Live site navigation, page/journey mapping, functional spec extraction, performance baselines. Collects "open questions" for batched Q&A (ADR-005).

### Safety Net Builder

**Spawned During:** Safety Nets (Phase 2)
**Tools:** Playwright MCP, test framework
**Purpose:** Writes E2E tests for critical journeys, creates visual regression snapshots, deploys smoke tests.

### Static Guard

**Spawned During:** Safety Nets (Phase 2)
**Tools:** ESLint MCP, CI config
**Purpose:** Configures linting rules, type checking, git hooks, and CI pipeline for automated quality gates.

### Refactorer

**Spawned During:** Refactoring (Phase 3)
**Tools:** Code read/write, Git MCP (worktree management)
**Purpose:** Surgical refactoring to make code testable — extract functions, reduce complexity, decouple. Works in a separate git worktree for safety.

### Deep Tester

**Spawned During:** Deep Testing (Phase 4)
**Tools:** Test framework, Playwright MCP
**Purpose:** Writes integration tests, unit tests, and edge case tests. Prioritized by risk map.

---

## Sequential Fallback

If Agent Teams is not available, Conrad executes all teammate workflows sequentially himself. Same results, just slower. No configuration needed — Conrad detects Agent Teams availability automatically.
