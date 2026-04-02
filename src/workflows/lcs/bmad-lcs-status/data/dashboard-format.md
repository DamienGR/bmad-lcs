# Status Dashboard Format

## Layout Reference

The status dashboard displays in the following order. Each section is displayed only if relevant data exists.

---

### Section 1: Header

```
══════════════════════════════════════════════
  LCS STATUS — {project_name}
  Last session: {relative_time} ({date})
══════════════════════════════════════════════
```

---

### Section 2: Phase Progression

Display all 7 phases in order. Use icons to indicate status:

- ✅ = phase fully completed (all tasks done)
- 🔄 = current phase (at least one task in_progress or pending)
- ⬚ = future phase (not yet started)

```
  ✅ Pre-flight → ✅ Audit → 🔄 Safety-Nets → ⬚ Dep-Update → ⬚ Refactoring → ⬚ Deep-Testing → ⬚ Guard
```

Global progression: `{completed_phases} / 7 phases`

---

### Section 3: Current Phase Detail

Show all tasks in the current phase with their status:

```
  Current Phase: {phase_name}
  ─────────────────────────────
  ✅ {task_id}    — done ({date})
  🔄 {task_id}    — in_progress
  ⬚ {task_id}     — pending
  ─────────────────────────────
  Progress: {done}/{total} tasks
```

---

### Section 4: Capabilities Warnings (only if any capability is false)

```
  ⚠️  Capabilities Warnings:
  - {capability_name}: not available
  - {capability_name}: not available
```

If all capabilities are true, skip this section entirely.

---

### Section 5: Safety Nets Summary

Show safety-nets phase tasks regardless of current phase:

```
  Safety Nets:
  ✅ E2E Tests        — done
  ✅ Snapshots        — done
  ⬚ Smoke Tests      — pending
  ⬚ Static Guard     — pending
  Coverage: {done}/{total}
```

If safety-nets phase has not been reached yet, display:

```
  Safety Nets: not yet started
```

---

### Section 6: Recent Decisions (last 3, only if decisions exist)

```
  Recent Decisions:
  • [{date}] {phase}: {decision} — {justification}
  • [{date}] {phase}: {decision} — {justification}
  • [{date}] {phase}: {decision} — {justification}
```

If no decisions recorded, skip this section entirely.

---

### Section 7: What's Next

Identify the first pending task in the current phase:

```
  ➡️  What's Next: {task_id} in {phase_name}
     {brief guidance on what this task involves}
```

If current phase is fully completed:

```
  ➡️  What's Next: Advance to {next_phase_name}
```

If all phases are completed:

```
  ✅ Consolidation Complete — Guard mode active
```

---

### Footer

```
══════════════════════════════════════════════
```
