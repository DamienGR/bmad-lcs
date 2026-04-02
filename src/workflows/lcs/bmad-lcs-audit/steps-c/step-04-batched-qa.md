---
name: 'step-04-batched-qa'
description: 'Extract open questions from teammate artifacts, present to user in batch, record answers'

nextStepFile: './step-05-risk-map-trigger.md'
sidecarStatePath: '{output_folder}/lcs/sidecar-state.yaml'
codeAnalysisPath: '{output_folder}/lcs/audit/code-analysis.md'
siteExplorationPath: '{output_folder}/lcs/audit/site-exploration.md'
openQuestionsPath: '{output_folder}/lcs/audit/open-questions.md'
---

# Step 4: Batched Q&A

## STEP GOAL:

To extract all open questions from teammate artifacts (ADR-005), present them to the developer grouped by theme, collect answers, and record them in sidecar-state and the open-questions artifact.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 🛑 NEVER skip question extraction — even if you think there are none
- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ✅ YOU MUST ALWAYS SPEAK OUTPUT in your Agent communication style with the config `{communication_language}`

### Role Reinforcement:

- ✅ You are Conrad, the LCS Lead — translating technical uncertainty into clear questions
- ✅ Questions should be understandable by a developer, not phrased in analysis jargon
- ✅ Group questions logically to minimize back-and-forth
- ✅ Some questions may have been partially answered by the audit report context

### Step-Specific Rules:

- 🎯 Focus ONLY on question extraction, presentation, and answer collection
- 🚫 FORBIDDEN to re-analyze code or site — use teammate findings as-is
- 💬 Present questions in plain language, grouped by theme
- 📋 Record ALL answers in sidecar-state clarifications

## EXECUTION PROTOCOLS:

- 🎯 Follow the MANDATORY SEQUENCE exactly
- 💾 Write open-questions.md artifact with Q&A pairs
- 📖 Update sidecar-state: batched-qa → done, add clarifications
- 🚫 Do NOT modify teammate artifacts

## CONTEXT BOUNDARIES:

- Available: teammate artifacts with UNCERTAIN markers and questions arrays
- Focus: user-facing Q&A only
- Limits: do not re-analyze or expand on teammate findings
- Dependencies: synthesis complete (step 03)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Extract Questions from Artifacts

**From `{siteExplorationPath}`:**
- Search for `<!-- UNCERTAIN: {question} -->` markers
- Read the `questions` array at the end of the artifact (ADR-005)
- Note the context (which page/journey the question relates to)

**From `{codeAnalysisPath}`:**
- Search for `<!-- UNCERTAIN: {question} -->` markers
- Read any questions array if present
- Note the context (which file/function the question relates to)

### 2. Check If Questions Exist

**If NO questions found in either artifact:**
- "**Aucune ambiguïté détectée par l'équipe d'audit.** Les analyses sont suffisamment claires pour continuer."
- Write minimal open-questions.md (no questions)
- Update sidecar-state: batched-qa → done
- Auto-proceed to step 05

**If questions found:** Continue to step 3.

### 3. Group Questions by Theme

Organize questions into logical groups:

- **Business Logic:** Questions about how features should work
- **Data:** Questions about data relationships, ownership, or flows
- **Navigation:** Questions about page relationships or user journeys
- **Security:** Questions about authentication, authorization, or data handling
- **Framework:** Questions about framework-specific patterns or configurations

Remove duplicates (same question from different teammates).

### 4. Present Questions to User

"**L'équipe d'audit a rencontré {count} questions nécessitant votre expertise.**

Je les ai regroupées par thème. Prenez votre temps pour répondre — vos réponses guideront les phases suivantes."

**For each theme group:**

"**{Theme} ({count} questions)**

**Q{N}.** {Question in plain language}
*Contexte : {where this was encountered — page, file, function}*

**Q{N+1}.** {Next question}
*Contexte : {context}*

..."

"Répondez à chaque question numérotée. Vous pouvez aussi répondre 'je ne sais pas' — c'est une information utile."

**Wait for user responses.**

### 5. Collect and Validate Answers

For each answer:
- Match to question number
- Validate: is the answer clear enough to inform subsequent phases?
- If unclear: ask a brief follow-up for clarification
- If "je ne sais pas": record as uncertain — subsequent phases will treat this zone with extra caution

### 6. Write Open Questions Artifact

Write `{openQuestionsPath}` with standard frontmatter:

```yaml
---
phase: audit
task_id: batched-qa
produced_by: conrad-lead
date: {current_date}
status: complete
total_questions: {count}
answered: {count}
uncertain: {count}
---
```

**Content structure:**

```markdown
# Open Questions — Audit Phase

## Summary
- Total questions: {count}
- Answered: {count}
- Uncertain (user doesn't know): {count}

## Questions & Answers

### {Theme 1}

**Q1.** {Question}
**Context:** {where encountered}
**Answer:** {user's answer}

**Q2.** {Question}
**Context:** {where encountered}
**Answer:** {user's answer}

### {Theme 2}
...
```

### 7. Update Sidecar State

Update `{sidecarStatePath}`:
- Set `tasks.audit.batched-qa.status` to `done`
- Add `clarifications` section with Q&A pairs:

```yaml
clarifications:
  - question: "{question text}"
    answer: "{answer text}"
    theme: "{theme}"
    source: "{code-analyst|site-explorer}"
    status: "answered|uncertain"
```

### 8. Proceed to Risk Map Trigger

Display: "**Q&A terminé. {answered} réponses enregistrées. Passage à la finalisation de l'audit...**"

#### Menu Handling Logic:

- After Q&A is complete and recorded, load, read entirely, then execute `{nextStepFile}`

#### EXECUTION RULES:

- Auto-proceed after Q&A completion
- IF user wants to discuss before proceeding: respond, then proceed

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All UNCERTAIN markers and questions arrays extracted from both artifacts
- Questions grouped by theme and deduplicated
- Questions presented in plain language (not analysis jargon)
- All answers collected and recorded
- open-questions.md written with standard frontmatter
- Sidecar-state updated with clarifications
- Proceeding to step 05

### ❌ SYSTEM FAILURE:

- Not checking BOTH artifacts for questions
- Presenting questions in technical jargon
- Not recording "uncertain" answers (they're information too)
- Modifying teammate artifacts
- Skipping the extraction even if none are found (must verify)

**Master Rule:** Every UNCERTAIN marker and questions array entry must be extracted and presented. Missing a question means missing context for subsequent phases.
