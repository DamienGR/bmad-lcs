# LCS: Legacy Consolidation Suite

**A BMAD Module** — Progressive legacy consolidation by specialized AI agents.

Analyze, secure, refactor and test legacy web projects (WordPress, Drupal).

---

## What is LCS?

LCS is a module for the [BMAD Framework](https://github.com/bmad-code-org/BMAD-METHOD) that provides a structured, AI-driven consolidation journey for legacy web projects. Specialized agents work together to progressively secure, refactor, and test codebases — transforming risky inherited projects into confident, well-protected ones.

**Designed for** freelance developers who inherit client projects with unknown code, no tests, and minimal documentation.

### Key Features

- **Executing agents** — They analyze, navigate, code, and test (not just advisors)
- **Parallel orchestration** — Agent Teams for maximum efficiency at each phase
- **Contextual intelligence** — Understands your specific project (code + live site via MCP Chrome DevTools)
- **Measurable progression** — From 2/10 to 8/10 confidence, visible at every step
- **Framework expertise** — Built-in WordPress and Drupal knowledge profiles
- **Sequential fallback** — Works without Agent Teams active

---

## Requirements

- [BMAD Framework](https://github.com/bmad-code-org/BMAD-METHOD) v6.0.0-Beta.7 or later
- Claude Code

---

## Installation

```bash
bmad install lcs
```

LCS uses BMAD Core configuration only — no additional prompts during installation.

---

## Quick Start

1. Open your legacy project in Claude Code
2. Launch LCS — Conrad (the Lead) starts, asks about your stack, URL, and repo
3. Conrad runs the **Pre-flight Check** to verify your environment
4. Launch the **Audit** with `[AU]` — the team analyzes code and site in parallel
5. Deploy **Safety Nets** with `[FI]` — E2E tests, snapshots, linting, CI
6. Continue through **Refactoring** `[RE]` and **Deep Testing** `[PR]`
7. Enter **Guard** mode — your permanent companion for daily development

---

## The Consolidation Journey

```
Pre-flight ──> Audit ──> Risk Map
                │          │
                v          v
          Safety Nets <── (prioritization)
                │
                v
        Dependency Update
                │
                v
          Refactoring
                │
                v
          Deep Testing
                │
                v
             Guard ──> Impact Analysis (daily use)
```

### Phases

| Phase | Workflow | What It Does |
|-------|----------|-------------|
| 0 | **Pre-flight Check** | Verify environment, detect framework, check MCP tools |
| 1 | **Audit** | Full analysis: code structure + live site navigation + dependency scan |
| 2 | **Safety Nets** | Deploy E2E tests, snapshots, smoke tests, linting, CI config |
| 2b | **Dependency Update** | Update vulnerable/outdated dependencies safely |
| 3 | **Refactoring** | Targeted refactoring to make code testable |
| 4 | **Deep Testing** | Integration tests, unit tests, edge cases |
| 5 | **Guard** | Continuous protection with impact analysis before every change |

### Feature Workflows (available anytime)

| Workflow | What It Does |
|----------|-------------|
| **Risk Map** | Generate prioritized risk map from audit findings |
| **Framework Profile** | Load WordPress/Drupal-specific knowledge |
| **Impact Analysis** | Analyze impacted zones before any modification |
| **Status** | Display consolidation state dashboard |

---

## Agent: Conrad (LCS Lead)

Conrad is the single entry point for all LCS operations. He orchestrates phases, spawns headless teammates, manages the sidecar memory, and communicates progress.

### Teammates (spawned by Conrad)

| Teammate | Phase | Role |
|----------|-------|------|
| Code Analyst | Audit | Static analysis, structure, complexity, framework patterns |
| Site Explorer | Audit | Live site navigation, page/journey mapping via MCP |
| Safety Net Builder | Safety Nets | E2E tests, snapshots, smoke tests |
| Static Guard | Safety Nets | Linting, type checking, git hooks, CI config |
| Refactorer | Refactoring | Targeted refactoring for testability |
| Deep Tester | Deep Testing | Integration tests, unit tests, edge cases |

---

## Supported Frameworks

LCS ships with detailed framework profiles for:

- **WordPress** 5.x / 6.x — Architecture, hooks, page builders (Elementor, DIVI, WPBakery), ACF, WP-CLI, database schema, test patterns
- **Drupal** 7 / 9 / 10 — Hook system, plugin system, config management, Paragraphs, Layout Builder, Drush, entity API, database schema

New frameworks can be added by creating a profile in `data/frameworks/` using the included template.

---

## MCP Tools (Optional but Recommended)

LCS works best with these MCP tools available:

| Tool | Purpose |
|------|---------|
| Chrome DevTools MCP | Live site exploration and visual testing |
| Playwright MCP | E2E test generation and execution |
| MySQL MCP | Direct database inspection |
| Semgrep | Security pattern analysis |
| ESLint | JavaScript/PHP linting |
| Snyk | Dependency vulnerability scanning |
| Lighthouse | Performance auditing |

LCS degrades gracefully — if a tool is unavailable, Conrad adapts the strategy.

---

## Module Structure

```
src/modules/lcs/
├── module.yaml                  # Module configuration
├── module-help.csv              # Workflow/agent discovery registry
├── README.md                    # Module documentation
├── agents/
│   ├── lcs-lead.agent.yaml      # Conrad — Lead agent definition
│   ├── lcs-lead.spec.md         # Agent specification
│   └── lcs-lead-sidecar/        # Persistent memory system
├── workflows/
│   ├── pre-flight/              # Phase 0 — 5 steps
│   ├── audit/                   # Phase 1 — 6 steps + 2 teammates
│   ├── safety-nets/             # Phase 2 — 7 steps + 2 teammates
│   ├── dependency-update/       # Phase 2b — 5 steps
│   ├── refactoring/             # Phase 3 — 4 steps + 1 teammate
│   ├── deep-testing/            # Phase 4 — 5 steps + 1 teammate
│   ├── guard/                   # Phase 5 — 6 steps
│   ├── risk-map/                # Feature — 4 steps
│   ├── framework-profile/       # Feature — 3 steps
│   ├── impact-analysis/         # Feature — 6 steps
│   └── status/                  # Utility — 3 steps
├── data/
│   ├── sidecar-state-schema.yaml
│   ├── artifact-conventions.md
│   ├── workflow-contracts.md
│   └── frameworks/
│       ├── _template.md
│       ├── wordpress.md
│       └── drupal.md
└── docs/
    ├── getting-started.md
    ├── agents.md
    ├── workflows.md
    └── examples.md
```

**54 step files** across 11 workflows. ~18,000 lines of documentation and specifications.

---

## Documentation

Detailed guides are available in the `docs/` folder after installation:

- [Getting Started](src/modules/lcs/docs/getting-started.md) — Installation, prerequisites, first steps
- [Agents Reference](src/modules/lcs/docs/agents.md) — Conrad and all teammates
- [Workflows Reference](src/modules/lcs/docs/workflows.md) — All 11 workflows detailed
- [Examples](src/modules/lcs/docs/examples.md) — Practical usage scenarios

---

## License

MIT — See [LICENSE](LICENSE) for details.

---

Part of the [BMAD Framework](https://github.com/bmad-code-org/BMAD-METHOD) ecosystem.
