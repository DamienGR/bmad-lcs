# Audit Data Extraction Guide

This file defines input contracts for the 3 audit artifacts consumed by the risk-map workflow, zone extraction methodology, and dependency-to-zone mapping. Protects against drift between audit and risk-map workflows.

---

## Input Contracts

### 1. Code Analysis (`code-analysis.md`)

**Producer:** Audit workflow → Code Analyst teammate
**Expected sections (extract from):**

| Section | Data to Extract | Used For |
|---------|----------------|----------|
| File Inventory | File paths, sizes, types | Zone identification (L2/L3) |
| Complexity Metrics | Cyclomatic complexity, nesting depth, line counts | Complexity dimension |
| Dependency Graph | Import counts, shared state, coupling indicators | Coupling dimension |
| Git History Summary | Commit frequency, recent changes, contributors | Volatility dimension |
| Test Coverage | Test file presence, coverage %, documented behaviors | Coverage Gap dimension |
| Architecture Notes | Patterns, anti-patterns, god files, dead code | Zone grouping hints |

**Validation:** Check frontmatter for `status: complete`. If absent → "Complete audit first."

### 2. Site Exploration (`site-exploration.md`)

**Producer:** Audit workflow → Site Explorer teammate
**Expected sections (extract from):**

| Section | Data to Extract | Used For |
|---------|----------------|----------|
| Page Inventory | URLs, page types, navigation structure | Zone identification (L1) |
| User Journeys | Journey names, steps, critical paths | Business Criticality + L1 zones |
| Interactive Elements | Forms, buttons, dynamic content, AJAX calls | Business Criticality scoring |
| Error States | Broken pages, 404s, JS errors | Coverage Gap indicators |
| Authentication Flows | Login, registration, session handling | Business Criticality (security) |
| Content Structure | Templates, layouts, reusable components | Zone grouping (L2) |

**Validation:** Check frontmatter for `status: complete`. If absent → "Complete audit first."

### 3. Dependency Scan (`dependency-scan.md`)

**Producer:** Audit workflow → dependency scan step
**Expected sections (extract from):**

| Section | Data to Extract | Used For |
|---------|----------------|----------|
| Dependency List | Package names, versions, types (prod/dev) | Dependency-to-zone mapping |
| Vulnerability Report | CVE IDs, severity (critical/high/medium/low), affected packages | Vulnerability modifier |
| Outdated Packages | Current vs latest version, breaking changes | Risk context |
| License Issues | Problematic licenses, compliance flags | Business Criticality context |

**Validation:** Check frontmatter for `status: complete`. If absent → "Complete audit first."

---

## Zone Extraction Methodology

### Phase 1: Identify Level 1 Zones (Functional)

**Source:** Site exploration report → User Journeys + Page Inventory

1. List all user journeys from site exploration
2. Group pages by journey/functional area
3. Each journey = one Level 1 zone
4. Name format: lowercase-kebab-case (e.g., `checkout-flow`, `user-auth`)

**Fallback:** If no user journeys defined, derive from page URL structure (e.g., `/admin/*` → `admin`, `/shop/*` → `shop`)

### Phase 2: Identify Level 2 Zones (Component)

**Source:** Code analysis report → File Inventory + Architecture Notes

1. Group files by logical unit (directory structure, shared prefix, related functionality)
2. Cross-reference with Level 1 zones — each L2 zone belongs to one L1 parent
3. Each logical group = one Level 2 zone
4. Name format: lowercase-kebab-case (e.g., `payment-gateway`, `cart-logic`)

**Rules:**
- Minimum 2 files per L2 zone (single-file zones get merged into nearest neighbor)
- Maximum ~15 L2 zones total (if more, merge similar zones)
- Every file from code analysis must belong to exactly one L2 zone
- Orphan files (no clear group) → create `misc-utilities` zone

### Phase 3: Tag Level 3 Files (Individual)

**Source:** Code analysis report → Complexity Metrics

1. Within each L2 zone, identify files with extreme indicators:
   - Lines > 500
   - Cyclomatic complexity > 20
   - Import count > 10
   - Explicitly flagged in code analysis
2. Tag these as Level 3 entries (for drill-down in CRITICAL/HIGH zones only)

**Rule:** Identify L3 candidates during zone discovery (step 01). Only include in drill-down output after scoring (step 02) determines the zone is CRITICAL or HIGH.

---

## Dependency-to-Zone Mapping

### Purpose

CVE data in the dependency scan is per-dependency, not per-zone. The vulnerability modifier requires knowing which zones are affected by which vulnerabilities.

### Mapping Process

1. **Extract vulnerable dependencies** from dependency scan (those with critical CVEs)
2. **For each vulnerable dependency:**
   - Search code analysis file inventory for `import`/`require`/`use` of that dependency
   - List all files that reference it
   - Map those files to their L2 zones
3. **Build mapping table:**

```markdown
| Dependency | CVE(s) | Severity | Files Using It | L2 Zones Affected |
|-----------|--------|----------|---------------|-------------------|
| {package} | {CVE-ID} | {critical/high} | {file1, file2} | {zone1, zone2} |
```

4. **Aggregate per zone:** Count critical CVEs per L2 zone for vulnerability modifier

### Edge Cases

- **Dependency used everywhere** (e.g., jQuery): Map to all zones that import it. Each affected zone gets the modifier.
- **Dependency not in any file** (transitive dependency): Map to the zone containing the parent dependency that pulls it in. If unknown → flag as UNCERTAIN.
- **No vulnerable dependencies:** All zones get modifier × 1.0. Skip mapping table.

---

## UNCERTAIN Zone Handling (ADR-005)

During zone extraction, some files or components may not clearly belong to any zone, or their risk profile may be ambiguous. Mark these as UNCERTAIN:

```markdown
<!-- UNCERTAIN: {zone-name} — {reason} -->
```

**Reasons for UNCERTAIN:**
- File purpose unclear from code analysis
- No matching user journey in site exploration
- Conflicting indicators (high complexity but low criticality)
- Insufficient data to score a dimension

**Rule:** UNCERTAIN zones are presented to the developer in step 03 for classification. They appear in the risk map with a `?` marker.
