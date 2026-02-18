# LCS: Legacy Consolidation Suite

Progressive legacy consolidation by specialized AI agents.

Analyze, secure, refactor and test legacy web projects (WordPress, Drupal).

---

## Overview

LCS provides a structured, progressive approach to consolidating legacy web projects. Specialized AI agents analyze your codebase and live site, deploy multi-layer safety nets, perform targeted refactoring, and build deep test coverage — transforming a risky legacy project into a confident, well-protected codebase.

Designed for freelance developers who inherit client projects with unknown code, no tests, and minimal documentation. LCS takes you from "crossing your fingers at every deployment" to "I have an AI team that secured my terrain."

### Key Differentiators

- **Executing agents** — They analyze, navigate, code, and test (not just advisors)
- **Parallel orchestration** — Agent Teams for maximum efficiency at each phase
- **Contextual intelligence** — Understands your specific project (code + live site via MCP Chrome DevTools)
- **Measurable progression** — From 2/10 to 8/10 confidence, visible at every step
- **Framework expertise** — Specific WordPress and Drupal knowledge
- **Sequential fallback** — Works without Agent Teams active

---

## Installation

```bash
bmad install lcs
```

---

## Quick Start

1. Open your legacy project in Claude Code
2. Launch LCS — Conrad (the Lead) starts, asks about your stack, URL, and repo
3. Conrad runs the Pre-flight Check to verify your environment
4. Launch the Audit with `[AU]` — the team analyzes code and site in parallel
5. Deploy Safety Nets with `[FI]` — e2e tests, snapshots, linting, CI
6. Continue through Refactoring `[RE]` and Deep Testing `[PR]`
7. Enter Guard mode — your permanent companion for daily development

**For detailed documentation, see [docs/](docs/).**

---

## Components

### Agents

| Agent | Name | Icon | Role |
|-------|------|------|------|
| **LCS Lead** | Conrad | 🛡️ | Orchestrator — single entry point, coordinates phases, spawns teammates |

### Teammates (Headless — spawned by Lead)

| Teammate | Role | Phase |
|----------|------|-------|
| Code Analyst | Static analysis, structure, complexity, framework patterns | Audit |
| Site Explorer | Live site navigation, page/journey mapping | Audit |
| Safety Net Builder | E2E tests, snapshots, smoke tests | Safety Nets |
| Static Guard | Linting, type checking, git hooks, CI | Safety Nets |
| Refactorer | Targeted refactoring for testability | Refactoring |
| Deep Tester | Integration tests, unit tests, edge cases | Deep Testing |

### Workflows

#### Core (Main Journey)

| Phase | Workflow | Purpose |
|-------|----------|---------|
| 0 | Pre-flight Check | Verify environment and prerequisites |
| 1 | Audit | Full analysis: code + site + dependencies + business logic |
| 2 | Safety Nets | Deploy multi-layer safety nets |
| 2b | Dependency Update | Update vulnerable/outdated dependencies |
| 3 | Refactoring | Targeted refactoring for testability |
| 4 | Deep Testing | Increase test coverage and depth |
| 5 | Guard | Continuous protection post-consolidation |

#### Feature Workflows

| Workflow | Purpose |
|----------|---------|
| Risk Map | Prioritized risk map from audit findings |
| Framework Profile | Framework-specific knowledge (WordPress, Drupal) |
| Impact Analysis | Analyze impacted zones before any modification |

#### Utility

| Workflow | Purpose |
|----------|---------|
| Status | Global consolidation state dashboard |

---

## Configuration

The module uses Core configuration only (no custom variables). Conrad asks project-specific questions at runtime:
- Target framework (WordPress / Drupal / Other)
- Local site URL
- MCP tool preferences

---

## Module Structure

```
lcs/
├── module.yaml
├── README.md
├── TODO.md
├── docs/
│   ├── getting-started.md
│   ├── agents.md
│   ├── workflows.md
│   └── examples.md
├── agents/
│   └── lcs-lead.spec.md
├── workflows/
│   ├── pre-flight/
│   ├── audit/
│   ├── safety-nets/
│   ├── dependency-update/
│   ├── refactoring/
│   ├── deep-testing/
│   ├── guard/
│   ├── risk-map/
│   ├── framework-profile/
│   ├── impact-analysis/
│   └── status/
├── data/
│   ├── sidecar-state-template.yaml
│   └── frameworks/
│       ├── wordpress.md
│       └── drupal.md
└── templates/
```

---

## Documentation

For detailed user guides and documentation, see the **[docs/](docs/)** folder:
- [Getting Started](docs/getting-started.md)
- [Agents Reference](docs/agents.md)
- [Workflows Reference](docs/workflows.md)
- [Examples](docs/examples.md)

---

## Development Status

V1 complete:

- [x] Agent: LCS Lead — Conrad (with sidecar memory)
- [x] Workflows: 11 workflows (54 step files)
- [x] Data: WordPress and Drupal framework profiles
- [x] Sidecar state schema

See TODO.md for V2 backlog.

---

## Author

Created via BMAD Module workflow

---

## License

Part of the BMAD framework.
