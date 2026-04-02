---
workflowType: 'guard'
stepsCompleted: []
lastStep: ''
date: ''
user_name: ''
project_name: ''
changeScope: ''
verdict: ''
---

# Guard Impact Report

## Change Scope

**Declared Scope:**
[Files, functions, or features the developer plans to modify]

**Date:** {{date}}

---

## Impact Analysis

### Direct Dependencies

[Callers, importers, includers of the changed code]

### Indirect Coupling

[Shared state, hooks, filters, framework-specific side effects]

### Impacted Zones

| Zone | Risk Score | Impact Level | Safety Nets Available |
|------|-----------|--------------|----------------------|
| | | | |

### Existing Test Coverage

[Tests that currently cover the impacted zones]

---

## Actual Changes

**Git Diff Summary:**
[What was actually modified vs declared scope]

**Scope Drift:**
[Any changes outside the declared scope — flagged for additional validation]

---

## Validation Results

**Safety Net Selection Protocol Applied:**

| Safety Net | Zone | Risk Level | Result | Details |
|-----------|------|-----------|--------|---------|
| | | | | |

**Execution Order:** lint → smoke → e2e → visual

---

## Verdict

**Overall:** {{verdict}}

- **BLOCKED**: At least 1 FAIL on CRITICAL zone
- **WARNING**: FAILs only on HIGH or below zones
- **CLEAR**: All PASS

---

## Recommendations

[Actions recommended based on the validation results]
