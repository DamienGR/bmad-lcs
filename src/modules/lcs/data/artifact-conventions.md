# LCS Artifact Conventions

## Root Location

All LCS artifacts are stored in `{project-root}/_bmad-output/lcs/`.

## Directory Structure

```
_bmad-output/lcs/
├── sidecar-state.yaml                  # Central consolidation state
├── pre-flight/
│   └── capability-matrix.md            # Environment capabilities detected
├── audit/
│   ├── code-analysis.md                # Code Analyst output
│   ├── site-exploration.md             # Site Explorer output
│   ├── dependency-scan.md              # Snyk/Semgrep output
│   └── risk-map.md                     # Risk Map synthesis
├── safety-nets/
│   ├── e2e-report.md                   # E2E test creation report
│   ├── snapshots-report.md             # Snapshot test creation report
│   ├── smoke-tests-report.md           # Smoke test creation report
│   └── static-guard-report.md          # Linting/type checking/CI config report
├── dependency-update/
│   ├── vulnerability-audit.md          # Vulnerability findings
│   └── update-report.md               # Update execution results
├── refactoring/
│   ├── refactoring-plan.md             # Planned refactoring targets
│   └── refactoring-report.md           # Refactoring execution results
├── deep-testing/
│   ├── integration-report.md           # Integration test results
│   ├── unit-report.md                  # Unit test results
│   ├── edge-cases-report.md            # Edge case test results
│   └── coverage-report.md             # Coverage summary
└── guard/
    └── impact-analyses/
        └── {date}-{description}.md     # One file per impact analysis
```

## Naming Convention

- **Directories:** `{phase}/` — matches phase name in sidecar-state.yaml
- **Files:** `{task-id}.md` — matches task id in sidecar-state.yaml
- **Impact analyses:** `{YYYY-MM-DD}-{kebab-description}.md`

## Artifact Frontmatter

Every artifact MUST include a YAML frontmatter header:

```yaml
---
phase: audit
task_id: code-analysis
produced_by: code-analyst     # Teammate or Lead
date: 2026-02-09
status: complete              # complete|partial|failed
---
```

This allows consumers to verify freshness and completeness without parsing the full file.

## Rules

1. **One task = one artifact.** No multi-task files.
2. **sidecar-state.yaml is the registry.** Every artifact path is recorded in the `artifact` field of the corresponding task.
3. **Consumers read artifacts by path from sidecar-state.** Never hardcode artifact paths in workflows — read from state.
4. **Append, don't overwrite.** If a task is re-run, archive the previous artifact (rename with timestamp suffix) before writing the new one.
5. **Frontmatter is mandatory.** Every artifact starts with the standard frontmatter.
