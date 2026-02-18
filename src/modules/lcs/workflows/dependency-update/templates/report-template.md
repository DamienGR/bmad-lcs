---
workflowType: 'dependency-update'
stepsCompleted: []
lastStep: ''
date: ''
project_name: ''
user_name: ''
nextWorkflow: 'refactoring'
packageManager: ''
totalPackages: 0
updatedCount: 0
failedCount: 0
skippedCount: 0
---

# Dependency Update Report

## Summary

**Project:** {{project_name}}
**Date:** {{date}}
**Package Manager:** {{packageManager}}

**Results:**
- Total packages analyzed: {{totalPackages}}
- Successfully updated: {{updatedCount}}
- Failed (rolled back): {{failedCount}}
- Skipped/excluded: {{skippedCount}}

**Safety Nets Status:** [GREEN/YELLOW — details below]

---

## Updates Applied

| # | Package | From | To | CVSS | Status | Notes |
|---|---------|------|----|------|--------|-------|
[Populated by step-03-approve-execute]

---

## Failures & Rollbacks

| # | Package | From | To | Failure Reason | Rollback | Safety Net Detail |
|---|---------|------|----|----------------|----------|-------------------|
[Populated by step-03-approve-execute — empty if no failures]

---

## Remaining Packages

| # | Package | Current | Latest | CVSS | Reason Not Updated |
|---|---------|---------|--------|------|--------------------|
[Populated by step-04-report — packages excluded or deferred]

---

## Recommendations

[Populated by step-04-report — next steps, manual review items, Phase 3 readiness]
