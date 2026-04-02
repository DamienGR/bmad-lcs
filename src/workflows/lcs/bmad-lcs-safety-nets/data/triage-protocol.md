# Failure Triage Protocol

**Purpose:** Classify test failures during safety-nets validation. Used by Conrad in step-04-validate-triage.

---

## Classifications

| Code | Name | Meaning | Action |
|------|------|---------|--------|
| **P** | Pass | Test is incorrect — observed behavior is normal (false positive) | Mark test as incorrect, adjust or remove |
| **K** | Known failure | Existing bug discovered — code has a real issue | Add `// KNOWN_FAILURE: {description}` comment, flag test, continue |
| **F** | Fix | Test has a defect — selector wrong, timing issue, bad assertion | List for teammate correction, test itself needs fixing |
| **E** | Environment | Environment issue — URL inaccessible, DB disconnected, service down | Document environment issue, not a test or code problem |

---

## Triage Presentation Template

For EACH failed test, present:

```
**Échec : {test_name}**
- **Fichier :** {test_file}
- **Zone :** {zone_name}
- **Erreur :** {error_message}
- **Contexte :** {relevant details — URL, selector, expected vs actual}

**Classification :**
- **[P] Pass** — le test est incorrect, le comportement est normal (faux positif)
- **[K] Known failure** — bug existant découvert, marquer comme `known_failure` et continuer
- **[F] Fix** — le test a un défaut, le noter pour correction par le teammate
- **[E] Environnement** — problème d'environnement (URL inaccessible, DB déconnectée, etc.)
```

Wait for user classification for each failure.

---

## Triage Results Table

Record all triage decisions:

| Test | Classification | Action |
|------|---------------|--------|
| {test} | pass / known_failure / fix / env | {action taken} |

---

## Green Baseline Definition

**Green baseline** = ALL tests pass OR all failures are triaged as `known_failure` or `pass`

- If `fix` tests remain → baseline is PARTIAL (user decides whether to re-spawn teammate for fixes or accept)
- If `env` tests remain → document environment issues, exclude from baseline assessment
