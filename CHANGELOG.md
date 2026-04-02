# Changelog

All notable changes to this project will be documented in this file.

## [2.0.1] - 2026-04-02

- Add CHANGELOG.md documenting full version history and v2.0.0 breaking changes

## [2.0.0] - 2026-04-02

### Breaking Changes

- **Module restructured for BMAD skills system** — The entire distribution layout has changed from `src/modules/lcs/` to `src/` with the new skills-based architecture. Projects using the old `1.x` format must reinstall the module.

### What changed and why

BMAD has migrated from the legacy `.claude/commands` system to a skills-based architecture (`.claude/skills/`). LCS v1.x was built on the old command format with agent YAML definitions and workflows without SKILL.md entry points. This made the module invisible to the new skill discovery and invocation system.

v2.0.0 aligns LCS with the pattern established by other BMAD modules (TEA, CIS, BMB):

- **Agent YAML replaced by SKILL.md** — `lcs-lead.agent.yaml` converted to `agents/bmad-lcs/SKILL.md` with capabilities table routing to named skills
- **11 workflow skills created** — Each workflow now has a `SKILL.md` wrapper and `bmad-skill-manifest.yaml`, making them independently invocable
- **Flat `src/` layout** — Removed the `src/modules/lcs/` nesting; module content is now directly under `src/` (agents, workflows, data, docs, sidecar)
- **Updated path references** — All `src/modules/lcs/data/` paths replaced with `_bmad/lcs/data/`
- **Sidecar instructions updated** — Auto-triggered workflow file paths replaced with skill name references
- **Spec files removed from distribution** — `.spec.md` and build plan files are development artifacts, no longer shipped

### Migration

Uninstall v1.x and reinstall v2.0.0. The BMAD installer will place files in the correct locations under `_bmad/lcs/` and `.claude/skills/`.

## [1.0.5] - 2026-02-20

- Package metadata fixes

## [1.0.4] - 2026-02-20

- Package version alignment

## [1.0.3] - 2026-02-18

- Package version alignment

## [1.0.1] - 2026-02-18

- Fix repository URLs in package.json

## [1.0.0] - 2026-02-18

- Initial release: Conrad agent with sidecar memory
- 11 workflows (54 step files) covering the full 7-phase consolidation journey
- WordPress and Drupal framework profiles
- Full documentation (getting-started, agents, workflows, examples)
