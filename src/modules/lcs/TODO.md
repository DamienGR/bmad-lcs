# TODO: LCS — Legacy Consolidation Suite

Development roadmap for lcs module.

---

## Agents to Build

- [x] LCS Lead — Conrad 🛡️ (Legacy Consolidation Lead)
  - Agent: `agents/lcs-lead.agent.yaml`
  - Sidecar: `agents/lcs-lead-sidecar/`
  - Validation: PASS

---

## Workflows to Build

### Core Workflows

- [x] pre-flight (Phase 0 — Environment verification)
  - Spec: `workflows/pre-flight/pre-flight.spec.md`
  - Steps: 5 steps + 2 data files

- [x] audit (Phase 1 — Full project analysis)
  - Spec: `workflows/audit/audit.spec.md`
  - Steps: 6 steps + 3 data files + 2 teammates

- [x] safety-nets (Phase 2 — Deploy safety nets)
  - Spec: `workflows/safety-nets/safety-nets.spec.md`
  - Steps: 7 steps + 5 data files + 2 teammates

- [x] dependency-update (Phase 2b — Update dependencies)
  - Spec: `workflows/dependency-update/dependency-update.spec.md`
  - Steps: 5 steps + 1 data file + 1 template

- [x] refactoring (Phase 3 — Targeted refactoring)
  - Spec: `workflows/refactoring/refactoring.spec.md`
  - Steps: 4 steps + 3 data files + 1 template + 1 teammate

- [x] deep-testing (Phase 4 — Increase coverage)
  - Spec: `workflows/deep-testing/deep-testing.spec.md`
  - Steps: 5 steps + 3 data files + 1 template + 1 teammate

- [x] guard (Phase 5 — Continuous protection)
  - Spec: `workflows/guard/guard.spec.md`
  - Steps: 6 steps + 1 data file + 1 template

### Feature Workflows

- [x] risk-map (Prioritized risk map)
  - Spec: `workflows/risk-map/risk-map.spec.md`
  - Steps: 4 steps + 2 data files

- [x] framework-profile (Framework-specific knowledge)
  - Spec: `workflows/framework-profile/framework-profile.spec.md`
  - Steps: 3 steps + 1 data file + 1 template

- [x] impact-analysis (Pre-modification impact analysis)
  - Spec: `workflows/impact-analysis/impact-analysis.spec.md`
  - Steps: 6 steps + 2 data files + 1 template

### Utility Workflows

- [x] status (Consolidation state dashboard)
  - Spec: `workflows/status/status.spec.md`
  - Steps: 3 steps + 1 data file

---

## Data Files to Create

- [x] `data/sidecar-state-schema.yaml` — Sidecar state schema and template (138 lines)
- [x] `data/frameworks/wordpress.md` — WordPress framework profile (detailed)
- [x] `data/frameworks/drupal.md` — Drupal framework profile (detailed)

---

## Documentation

- [x] Complete README.md with usage examples
- [x] `docs/getting-started.md` — Installation and first steps
- [x] `docs/agents.md` — Agent reference (Conrad + 6 teammates)
- [x] `docs/workflows.md` — Full workflow reference (11 workflows)
- [x] `docs/examples.md` — Practical usage examples

---

## Installation Testing

- [ ] Test installation with `bmad install`
- [ ] Verify module.yaml prompts work correctly
- [ ] Verify all agents and workflows are discoverable

---

## V2 Backlog

- [ ] Confidence Score workflow (composite score: coverage + nets + residual risks)
- [ ] Report workflow (generate consolidation report for client)
- [ ] AST MCP Server integration (deep structural analysis)
- [ ] GitHub MCP integration (create issues/PRs from Lead)
- [ ] Docker MCP integration (DDEV container monitoring)

---

## Next Steps

1. ~~Build Conrad (LCS Lead) agent using create-agent workflow~~
2. ~~Build core workflows (Phase 0-5) using create-workflow workflow~~
3. ~~Build feature and utility workflows~~
4. ~~Create framework profile data files~~
5. Test installation and full consolidation journey
6. Iterate based on testing

---

_Last updated: 2026-02-18_
