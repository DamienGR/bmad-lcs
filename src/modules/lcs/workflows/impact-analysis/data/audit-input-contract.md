# Audit Input Contract

**Purpose:** Defines the expected format and structure of audit workflow output consumed by the impact-analysis workflow.

---

## Required Fields

The audit data must provide:

### Code Structure
- File inventory with paths and types (PHP, JS, template, config, etc.)
- Function/method inventory per file
- Class hierarchy and inheritance chains

### Dependency Graph
- Import/require/include relationships between files
- Function call graph (caller → callee)
- Class instantiation and usage patterns

### Framework Profile
- Framework type (WordPress, Drupal, Laravel, custom, etc.)
- Hook/filter registration points
- Action chains and event listeners
- Plugin/module activation dependencies

---

## Expected File Patterns

The init step will search for audit data in:

1. `{output_folder}/lcs/audit/` — Primary location
2. `{output_folder}/lcs/` — Fallback location

### Filename Patterns
- `audit-report-*.md` — Full audit report
- `dependency-graph-*.md` — Dependency graph data
- `framework-profile-*.md` — Framework-specific profile

---

## Validation Rules

1. **Freshness:** Warn if audit data is older than 7 days
2. **Completeness:** Require at minimum: file inventory + dependency graph
3. **Framework match:** Warn if framework profile doesn't match project type

---

## Missing Data Handling

If audit data is missing or incomplete:
- **No audit data found:** Error — "Run the audit workflow first to generate codebase analysis"
- **Partial data (no dependency graph):** Warning — "Impact analysis will be limited without dependency graph. Proceed anyway?"
- **Stale data (>7 days):** Warning — "Audit data is {N} days old. Results may not reflect recent changes. Proceed anyway?"
