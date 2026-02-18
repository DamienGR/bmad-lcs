# LCS Workflow Contracts

## Producer/Consumer Matrix

| Producer | Artifact | Consumer(s) |
|----------|----------|-------------|
| Pre-flight (Lead) | `pre-flight/capability-matrix.md` | Audit (adapts tools), all workflows |
| Code Analyst | `audit/code-analysis.md` | Risk Map, Safety Nets (prioritization), Refactoring |
| Site Explorer | `audit/site-exploration.md` | Risk Map, Safety Nets (e2e routes), Deep Testing |
| Dependency scan | `audit/dependency-scan.md` | Risk Map, Dependency Update |
| Risk Map (Lead) | `audit/risk-map.md` | Safety Nets, Refactoring, Deep Testing (prioritization) |
| Safety Net Builder | `safety-nets/e2e-report.md` | Dependency Update (nets in place), Refactoring |
| Safety Net Builder | `safety-nets/snapshots-report.md` | Refactoring, Deep Testing |
| Safety Net Builder | `safety-nets/smoke-tests-report.md` | Dependency Update |
| Static Guard | `safety-nets/static-guard-report.md` | Refactoring, Deep Testing |
| Lead | `dependency-update/vulnerability-audit.md` | Dependency Update execution |
| Lead | `dependency-update/update-report.md` | Refactoring (updated deps context) |
| Refactorer | `refactoring/refactoring-plan.md` | Deep Testing (knows what changed) |
| Refactorer | `refactoring/refactoring-report.md` | Deep Testing, Status |
| Deep Tester | `deep-testing/coverage-report.md` | Guard, Status |
| Impact Analysis | `guard/impact-analyses/{date}-{desc}.md` | Developer (decision) |

## Phase Dependencies

```
Pre-flight ──→ Audit ──→ Risk Map
                │          │
                ▼          ▼
          Safety Nets ◄── (prioritization from Risk Map)
                │
                ▼
        Dependency Update
                │
                ▼
          Refactoring
                │
                ▼
          Deep Testing
                │
                ▼
             Guard ──→ Impact Analysis (daily use)
```

## Contract Rules

### Input Contract

Each workflow MUST:
1. Read `sidecar-state.yaml` to get current phase and task statuses
2. Verify its prerequisite artifacts exist (check `status: complete` in frontmatter)
3. If a prerequisite is missing or partial, report to Lead — do not proceed blind

### Output Contract

Each workflow MUST:
1. Produce artifacts following the frontmatter standard (see artifact-conventions.md)
2. Update its task status in `sidecar-state.yaml` (`pending` → `in_progress` → `done`)
3. Record the artifact path in the task's `artifact` field
4. If producing a decision, add an entry to the `decisions` array in sidecar-state

### Teammate Spawn Contract

When Lead spawns a teammate via Agent Teams:
1. Pass the workflow path to execute
2. Pass the framework profile path
3. Pass the sidecar-state.yaml path (for reading prerequisites and writing results)
4. The teammate reads its input artifacts from sidecar-state, executes its workflow, writes its output artifact, updates sidecar-state

### Batched Q&A Contract (ADR-005)

When a teammate encounters ambiguous business logic:
1. Mark the zone with confidence level in its artifact: `<!-- UNCERTAIN: {question} -->`
2. Add an entry to a `questions` array at the end of its artifact
3. Lead collects all questions post-phase and presents them in batch to the developer
4. Developer answers are recorded in sidecar-state under a `clarifications` key
5. Subsequent workflows can read clarifications before acting on uncertain zones
