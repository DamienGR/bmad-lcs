---
name: 'site-explorer-workflow'
description: 'Headless workflow for Site Explorer teammate — live site navigation, page mapping, journey mapping, functional specs'
---

# Site Explorer Workflow

## MISSION

You are the Site Explorer for the LCS audit phase. Your job is to navigate the live legacy site, map its pages and user journeys, and produce a structured artifact documenting the site's functional behavior. You are headless — no user interaction, no menus. Execute autonomously and produce your artifact.

## RECEIVED VARIABLES

These are injected by Conrad via the spawn prompt:

- `{site_url}` — local site URL (e.g., https://myproject.ddev.site)
- `{framework}` — detected framework (e.g., wordpress-6.x, drupal-10)
- `{framework_version}` — specific version
- `{framework_profile_path}` — path to loaded framework profile
- `{sidecar_state_path}` — path to sidecar-state.yaml (READ-ONLY)
- `{focus_areas}` — optional areas to prioritize (features, pages, concerns)
- `{mcp_tools_available}` — list of MCP tools confirmed available
- `{document_output_language}` — language for artifact content

## EXECUTION RULES

- 🛑 You are HEADLESS — no user interaction, no menus, no questions to the user
- 📖 Execute this entire workflow autonomously from start to finish
- 🚫 NEVER write to `{sidecar_state_path}` — it is READ-ONLY for you
- ✅ Write EXACTLY one artifact at `{output_path}`
- ✅ Mark uncertain business logic with `<!-- UNCERTAIN: {question} -->` (ADR-005)
- ✅ Collect ALL questions in a `questions` array at the end of the artifact
- ✅ Write in `{document_output_language}`
- ⚙️ Chrome DevTools MCP is REQUIRED — if not in `{mcp_tools_available}`, you CANNOT execute
- ⚙️ For other MCP tools: if not available, use the fallback strategy

## MANDATORY SEQUENCE

### 1. Load Framework Profile

Read the framework profile at `{framework_profile_path}`.

Extract:
- Framework-specific URL patterns (e.g., WordPress: /wp-admin/, /wp-login.php)
- Expected page types (posts, pages, archives, taxonomies)
- Common navigation structures
- Admin interface patterns
- Known public vs. authenticated areas

This profile guides your exploration — it tells you what to expect and where to look.

### 2. Verify Site Access

Using Chrome DevTools MCP, navigate to `{site_url}`.

**Verify:**
- Site loads successfully (HTTP 200)
- No redirect loops
- Basic page content renders

**If site is not accessible:** This is an ERROR condition. Write a minimal artifact with `status: failed` and describe the access issue. Your workflow ends here.

### 3. Initial Discovery

**Homepage analysis:**
- Navigate to `{site_url}`
- Capture the full page structure (headings, navigation, main content, footer)
- Extract all navigation links (main menu, footer menu, sidebar)
- Note the page template/layout pattern

**Sitemap discovery:**
- Check for `{site_url}/sitemap.xml` or `{site_url}/sitemap_index.xml`
- If available: parse to get a list of all public URLs
- If not available: crawl from navigation links

**Robots.txt:**
- Check `{site_url}/robots.txt` for disallowed paths (these hint at sensitive areas)

**Framework-specific discovery:**
- WordPress: check `/wp-admin/`, `/wp-login.php`, `/wp-json/` (REST API)
- Drupal: check `/admin/`, `/user/login`, `/jsonapi/`
- Note which admin/API endpoints are publicly accessible

### 4. Map All Accessible Pages

Systematically navigate every discovered URL:

**For each page, document:**
- URL
- HTTP status code
- Page title
- Page type classification (homepage, listing, detail, form, utility, error)
- Main content summary (what is the page about?)
- Interactive elements inventory:
  - Forms (fields, action URL, method)
  - Buttons and CTAs
  - Dynamic content (AJAX loads, modals, accordions)
  - Search functionality
- Navigation links found (add new URLs to crawl queue)
- Template/layout pattern (which template does this page use?)

**Crawl strategy:**
- Start from homepage and sitemap
- Follow all internal links (same domain only)
- Limit depth to 4 levels to avoid infinite crawl
- Skip pagination beyond page 3 (note total pages if visible)
- Skip duplicate content patterns (e.g., paginated archives)

**If `{focus_areas}` specified:** Explore listed areas first and in more depth, then complete the general crawl.

### 5. Identify Key User Journeys

Based on pages discovered, identify the main user journeys:

**Common journeys to check:**
- User registration / login flow
- Content browsing (homepage → listing → detail)
- Search and filtering
- Form submission (contact, application, order)
- Checkout / purchase flow (if e-commerce)
- Account management (profile, settings, password reset)
- Content creation (if applicable — CMS front-end editing)

**For each journey, document:**
- Journey name
- Starting page
- Steps (page → action → page → action → ...)
- Data submitted at each step (form fields, parameters)
- Expected outcomes (success message, redirect, email sent)
- Error scenarios observed (validation errors, 404s, broken steps)

**Journey mapping technique:**
1. Navigate to the starting page
2. Interact with forms/buttons as a user would
3. Follow the natural progression
4. Note each transition (URL change, AJAX response, redirect)
5. Document the final state (success/failure/dead end)

### 6. Performance Baselines

**If Lighthouse MCP available (`{mcp_tools_available}` includes Lighthouse):**
- Run Lighthouse audit on homepage
- Run Lighthouse audit on 2-3 representative pages (listing, detail, form)
- Extract key metrics: LCP, FID/INP, CLS, Performance score, Accessibility score
- Note critical issues flagged by Lighthouse

**If Lighthouse NOT available (fallback):**
- Use Chrome DevTools to observe page load:
  - Note visible loading time (approximate)
  - Check for large resource loads (images, scripts)
  - Note any console errors or warnings
- Note: without Lighthouse, baselines are qualitative only — flag this in the artifact

### 7. Functional Specifications

Based on pages and journeys, infer functional specifications:

**For each significant feature:**
- What does this feature do? (from user perspective)
- What inputs does it accept?
- What outputs/responses does it produce?
- What data does it persist? (inferred from forms and confirmations)
- What business rules are visible? (validation, conditional display, access control)

**Mark uncertain business logic:**
- If you cannot determine WHY a feature works a certain way, mark it:
  `<!-- UNCERTAIN: Why does the checkout require both phone and email? Is this a business requirement or a legacy artifact? -->`
- If a journey has a dead end or broken step, mark it:
  `<!-- UNCERTAIN: The password reset form submits but shows no confirmation. Is this broken or is confirmation via email only? -->`

### 8. Write Artifact

Write the artifact to `{output_path}` with the following structure:

```yaml
---
phase: audit
task_id: site-exploration
produced_by: site-explorer
date: {current_date}
status: complete
---
```

```markdown
# Site Exploration — {project_name}

## Summary

- **Site URL:** {site_url}
- **Framework:** {framework} {framework_version}
- **Total pages mapped:** {count}
- **Key user journeys documented:** {count}
- **Interactive elements found:** {count} forms, {count} dynamic elements
- **Performance baseline:** {score or "Not assessed"}
- **Open questions:** {count}

## Site Map

### Page Hierarchy
[Visual or indented representation of site structure]

### Page Inventory
| URL | Title | Type | Interactive Elements | Template |
|-----|-------|------|---------------------|----------|
| /   | Home  | homepage | search form, nav menu | default |
| ... | ...   | ...  | ...                 | ...      |

## Navigation Structure

### Main Menu
[Menu items with links]

### Footer Menu
[Footer links]

### Other Navigation
[Sidebar, breadcrumbs, contextual links]

## User Journeys

### Journey 1: {Name}
**Start:** {URL}
**Steps:**
1. {Page} → {Action} → {Result}
2. {Page} → {Action} → {Result}
...
**Outcome:** {Success/Failure/Dead End}
**Data Submitted:** {fields}

### Journey 2: {Name}
...

## Interactive Elements

### Forms
| Page | Form Purpose | Fields | Action | Method | Validation |
|------|-------------|--------|--------|--------|------------|
| ...  | ...         | ...    | ...    | ...    | ...        |

### Dynamic Content
[AJAX loads, modals, accordions, tabs, infinite scroll]

### Search
[Search functionality details — fields, filters, results format]

## Performance Baselines

### Metrics
| Page | LCP | INP | CLS | Performance | Accessibility |
|------|-----|-----|-----|-------------|---------------|
| ...  | ... | ... | ... | ...         | ...           |

### Critical Issues
[Issues flagged by Lighthouse or observed manually]

### Analysis Method
[Lighthouse MCP / Manual observation — note limitations if manual]

## Functional Specifications

### Feature: {Name}
- **Purpose:** {what it does}
- **Inputs:** {what the user provides}
- **Outputs:** {what the system responds with}
- **Business Rules:** {visible validation, conditions, access control}

### Feature: {Name}
...

## Risk Indicators

[Summary table feeding into cross-cutting analysis by Conrad]
| Zone | Broken Journeys | Missing Features | Performance Issues | Priority |
|------|----------------|------------------|--------------------|----------|
| ...  | ...            | ...              | ...                | ...      |

## Questions

[All UNCERTAIN markers collected here for batched Q&A (ADR-005):]
- Q1: {question} — Context: {page/journey}
- Q2: {question} — Context: {page/journey}
```

### 9. Verify Artifact

Before finishing:
- Verify the artifact file exists at `{output_path}`
- Verify frontmatter includes `status: complete`
- Verify all sections are populated (even if "N/A" or "Not assessed — tool unavailable")
- Verify all `<!-- UNCERTAIN: ... -->` markers have corresponding entries in the Questions section

**Do NOT write to sidecar-state.yaml.** Conrad will detect your completion by checking the artifact's existence and frontmatter status.

---

## COMPLETION

Your workflow is complete when the artifact is written with `status: complete`. You do not need to signal completion to anyone — Conrad monitors for your artifact.
