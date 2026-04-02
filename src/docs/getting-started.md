# Getting Started with LCS: Legacy Consolidation Suite

Welcome to LCS! This guide will help you get up and running.

---

## What This Module Does

LCS takes the pain out of inheriting legacy web projects. When you pick up a client's existing WordPress or Drupal site — with unknown code, no tests, and minimal documentation — LCS provides a structured consolidation journey:

1. **Audit** — AI agents analyze the code AND navigate the live site
2. **Safety Nets** — E2E tests, snapshots, linting, and CI are deployed automatically
3. **Refactoring** — Targeted changes make the code testable
4. **Deep Testing** — Comprehensive test coverage is built
5. **Guard** — Permanent companion for daily development with impact analysis

The result: you go from "crossing your fingers" to deploying with confidence.

---

## Installation

If you haven't installed the module yet:

```bash
bmad install lcs
```

Follow the prompts to configure the module for your needs.

---

## Prerequisites

Before starting a consolidation, ensure you have:

- **Local development environment** — DDEV recommended for WordPress/Drupal
- **Local site running** — The site must be accessible locally
- **Repository access** — Git repo with the project code
- **Node.js** — Required for test frameworks and linting
- **MCP tools** (recommended) — Chrome DevTools MCP, Playwright MCP, Context7

The Pre-flight Check (Phase 0) will verify all prerequisites for you.

---

## First Steps

1. **Open your project** in Claude Code
2. **Launch LCS** — Conrad (🛡️) appears and asks about your project:
   - What framework? (WordPress, Drupal, Other)
   - Local site URL?
   - Any specific concerns?
3. **Pre-flight Check** runs automatically — verifies environment, detects framework, loads profile
4. **Start the Audit** with `[AU]` — Code Analyst and Site Explorer work in parallel
5. **Review the Risk Map** — Conrad presents prioritized findings and asks your input

From there, Conrad guides you through each phase progressively.

---

## Common Use Cases

### "I just inherited a WordPress site"

1. Launch LCS, tell Conrad it's WordPress
2. Run Audit — Conrad's team maps the code and navigates the site
3. Deploy Safety Nets — E2E tests for critical journeys, visual regression snapshots
4. Now you can add features or fix bugs with confidence

### "I need to update a Drupal site's dependencies"

1. Launch LCS, run through Audit and Safety Nets first
2. Use Dependency Update (Phase 2b) — safety nets catch any breakage
3. Each update is validated incrementally

### "I want ongoing protection for daily development"

1. Complete the full consolidation journey (Phases 0-4)
2. Enter Guard mode — Impact Analysis runs before every change
3. Relevant safety nets execute automatically after modifications

---

## What's Next?

- Check out the [Agents Reference](agents.md) to meet Conrad and the team
- Browse the [Workflows Reference](workflows.md) to see the full journey
- See [Examples](examples.md) for real-world usage

---

## Need Help?

If you run into issues:
1. Run `[ST]` Status to see your current consolidation state
2. Check the troubleshooting section in examples.md
3. Review your module configuration
4. Consult the broader BMAD documentation
