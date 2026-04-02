# Workflows Reference

LCS includes 11 workflows organized in 3 categories:

---

## Core Workflows (Main Journey)

These workflows form the progressive consolidation journey, executed in order.

### Phase 0: Pre-flight Check

**Workflow:** `pre-flight`
**Agent:** Conrad (Lead alone)

**Purpose:**
Verifies that all prerequisites are in place before starting consolidation: repo access, local site running, dev tools installed, MCP tools available. Detects the framework and loads the appropriate profile.

**Key Steps:**
1. Check environment (repo, site, tools)
2. Detect framework (WordPress/Drupal/Other)
3. Check MCP tool availability
4. Initialize sidecar state
5. Report readiness

---

### Phase 1: Audit

**Workflow:** `audit`
**Agents:** Conrad + Code Analyst + Site Explorer

**Purpose:**
Full analysis of the legacy project. Code Analyst performs static analysis while Site Explorer navigates the live site — in parallel via Agent Teams. Results are synthesized into a unified audit report, followed by a Risk Map and batched Q&A for ambiguous business logic.

**Key Steps:**
1. Spawn teammates (parallel or sequential)
2. Code analysis (structure, complexity, patterns)
3. Site exploration (pages, journeys, functional specs)
4. Dependency audit (vulnerabilities, outdated packages)
5. Synthesize into unified report
6. Batched Q&A for open questions
7. Generate Risk Map

---

### Phase 2: Safety Nets

**Workflow:** `safety-nets`
**Agents:** Conrad + Safety Net Builder + Static Guard

**Purpose:**
Deploy multi-layer safety nets to protect the codebase before any modifications. E2E tests, visual regression snapshots, smoke tests, linting, git hooks, and CI — all based on audit findings and risk prioritization.

**Key Steps:**
1. Prioritize from Risk Map
2. Spawn teammates
3. Write E2E tests (Playwright)
4. Create visual snapshots
5. Deploy smoke tests
6. Configure static guards (ESLint, CI)
7. Validate green baseline
8. Report

---

### Phase 2b: Dependency Update

**Workflow:** `dependency-update`
**Agent:** Conrad (Lead)

**Purpose:**
With safety nets in place, it's safe to update dependencies. Analyzes vulnerabilities via Snyk, proposes prioritized update plan, executes incrementally with safety net validation after each update.

**Key Steps:**
1. Analyze dependencies
2. Propose update plan
3. Get user approval
4. Execute updates (incremental + validated)
5. Report

---

### Phase 3: Refactoring

**Workflow:** `refactoring`
**Agents:** Conrad + Refactorer

**Purpose:**
Targeted refactoring to make legacy code testable. Works in a separate git worktree for safety. Each refactoring is validated against safety nets before merging.

**Key Steps:**
1. Identify targets from audit/risk map
2. Create git worktree
3. Spawn Refactorer
4. Execute surgical refactoring
5. Validate against safety nets
6. Merge or rollback
7. Report

---

### Phase 4: Deep Testing

**Workflow:** `deep-testing`
**Agents:** Conrad + Deep Tester

**Purpose:**
Build comprehensive test coverage on the now-refactored codebase. Integration tests, unit tests, and edge cases — prioritized by risk map.

**Key Steps:**
1. Analyze coverage gaps
2. Prioritize by risk
3. Spawn Deep Tester
4. Write integration, unit, and edge case tests
5. Validate full suite
6. Report coverage metrics

---

### Phase 5: Guard

**Workflow:** `guard`
**Agent:** Conrad (Lead)

**Purpose:**
Continuous protection post-consolidation. The daily-use companion. Before any modification, triggers Impact Analysis to identify affected zones. Runs relevant safety nets after changes.

**Key Steps:**
1. Activate guard mode
2. Pre-change impact analysis
3. Monitor changes
4. Post-change validation
5. Quick report

---

## Feature Workflows

### Risk Map

**Workflow:** `risk-map`
**Agent:** Conrad (Lead)

**Purpose:**
Synthesizes all audit findings into a prioritized risk map. Each zone gets a risk score based on complexity, coupling, coverage, vulnerabilities, and business criticality. Drives prioritization for all subsequent phases.

**When Used:** Auto-triggered after Audit phase.

---

### Framework Profile

**Workflow:** `framework-profile`
**Agent:** Conrad (Lead)

**Purpose:**
Loads or creates framework-specific knowledge for the target project. Provides patterns, known pitfalls, CLI tools, page builder awareness, and testing strategies specific to WordPress, Drupal, or other frameworks.

**When Used:** During Pre-flight Check (Phase 0).

---

### Impact Analysis

**Workflow:** `impact-analysis`
**Agents:** Conrad + Code Analyst (optional)

**Purpose:**
The killer feature for daily use. Before any modification, analyzes all potentially impacted zones: direct dependencies, indirect coupling, related tests, shared state, and framework-specific side effects. Central mechanism in Guard phase.

**When Used:** On-demand during Guard mode, before any code modification.

---

## Utility Workflows

### Status

**Workflow:** `status`
**Agent:** Conrad (Lead)

**Purpose:**
Displays the global consolidation state: current phase, safety nets in place, coverage metrics, and what's next. Available at any time via the `[ST]` menu command.

**When Used:** On-demand via `[ST]` menu command.

---

## Workflow Flow

```
Pre-flight Check --> Audit --> Risk Map
                      |          |
                      v          v
                Safety Nets <-- (priorities)
                      |
                      v
              Dependency Update
                      |
                      v
                Refactoring
                      |
                      v
                Deep Testing
                      |
                      v
                   Guard --> Impact Analysis (daily use)
                      |
                      v
                   Status
```
