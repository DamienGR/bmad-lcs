# Examples & Use Cases

This section provides practical examples for using LCS: Legacy Consolidation Suite.

---

## Example Workflows

### Full Consolidation of a WordPress Site

**Scenario:** You inherit a WordPress site with Elementor, WooCommerce, and custom theme code. No tests, no documentation, 15+ plugins.

**Journey:**
1. Launch LCS — Conrad detects WordPress, loads the WordPress profile
2. Pre-flight confirms DDEV is running, site is accessible, MCP tools ready
3. Audit runs in ~30 min — Code Analyst maps the theme, plugins, and custom code. Site Explorer navigates all pages and key journeys (checkout, account, forms)
4. Risk Map shows: WooCommerce checkout is highest risk (complex, no tests, business-critical)
5. Safety Nets focus on checkout first — Playwright E2E tests for the full purchase journey, visual snapshots of key pages
6. Dependencies updated safely — 3 plugins had known vulnerabilities
7. Refactoring targets `functions.php` — extracts testable functions from 2000-line file
8. Deep Testing adds unit tests for critical business logic
9. Guard mode activated — before any change, Impact Analysis runs

**Result:** Confidence goes from 2/10 to 8/10. Adding a new feature to checkout? Guard tells you exactly what's impacted and runs the relevant tests.

---

### Quick Safety Nets for a Drupal Site

**Scenario:** You need to make a quick fix on a Drupal site but want basic protection first.

**Journey:**
1. Launch LCS, run Pre-flight + Audit
2. Deploy Safety Nets — focus on the area you need to modify
3. Make your fix
4. Safety nets catch any regression immediately

**Time:** ~1-2 hours for basic coverage on the critical area.

---

### Daily Development with Guard Mode

**Scenario:** Consolidation is complete. You're adding a new feature to an ongoing project.

**Workflow:**
1. Tell Conrad what you're about to modify
2. Impact Analysis runs in < 2 minutes
3. Conrad shows: "These 5 files could be affected. Tests covering this area: 12 E2E, 3 integration, 8 unit."
4. You make your changes
5. Guard runs the relevant tests automatically
6. All green — ship with confidence

---

## Common Scenarios

### "I only have a few hours"

Focus on Phases 0-2 only:
- Pre-flight (5 min)
- Audit (30 min)
- Safety Nets for the highest-risk area (1 hour)

You'll have basic protection for the most critical parts.

### "The site has no local environment"

LCS requires a local site for full functionality (Site Explorer needs it). If you can't set up a local environment:
- Code analysis still works (Phase 1 partial)
- Static guards still work (linting, type checking)
- E2E tests and site exploration are limited

Recommendation: Set up DDEV first — it takes 15 minutes for WordPress/Drupal.

### "I'm not sure what framework this is"

Conrad's Pre-flight Check auto-detects the framework by looking for:
- `wp-config.php` → WordPress
- `settings.php` + `core/` → Drupal
- Other patterns → Custom/Other

If detection fails, Conrad asks you directly.

---

## Tips & Tricks

- **Start with Audit** — even if you don't plan to do full consolidation, the audit report alone is valuable
- **Use Status `[ST]` often** — it shows exactly where you are and what's next
- **Don't skip Safety Nets** — they're what makes everything else safe
- **Let Guard mode run** — it's lightweight and catches things you'd miss
- **Review the Risk Map with your client** — it's a great communication tool

---

## Troubleshooting

### Common Issues

**"Pre-flight says my site is not accessible"**
- Verify your local environment is running (`ddev start`)
- Check the URL you provided matches the actual local site URL
- Try opening the URL in your browser first

**"Audit is taking too long"**
- Large projects take longer — this is normal
- If Agent Teams is active, Code Analyst and Site Explorer run in parallel
- If sequential, expect ~2x the time

**"Safety net tests are failing on first run"**
- This usually means the baseline has a bug — that's actually valuable information
- Conrad will flag these as "pre-existing failures" vs "regressions"
- Decide with Conrad which ones to fix vs accept as known issues

**"Agent Teams is not available"**
- LCS works fine without Agent Teams — Conrad runs everything sequentially
- No configuration needed — fallback is automatic
- Same results, just slower execution

---

## Getting More Help

- Run `[ST]` Status to see your current state
- Review the main BMAD documentation
- Check module configuration in module.yaml
- Verify all agents and workflows are properly installed
