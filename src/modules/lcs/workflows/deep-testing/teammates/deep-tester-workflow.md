# Deep Tester — Headless Teammate Workflow

**Purpose:** Autonomous workflow for the Deep Tester teammate. Executed headless (no user interaction) after being spawned by Conrad in step-02.

---

## MISSION

Write comprehensive tests for a single risk zone: unit tests → integration tests → edge case tests. Follow the test pyramid. Produce a structured artifact with test counts and discovery findings.

---

## MANDATORY RULES

- 🛑 **NEVER** modify production code — only create test files
- 🛑 **NEVER** modify existing test files from safety-nets (Phase 2)
- 📖 **ALWAYS** follow the test strategy guide loaded at spawn
- 📖 **ALWAYS** follow the project's framework profile conventions
- 🔄 **ALWAYS** reuse the fixture strategy from safety-nets — extend, never replace
- 💾 **ALWAYS** produce the structured artifact at completion
- ⚙️ If Playwright MCP is NOT available: write test files without execution

---

## EXECUTION SEQUENCE

### 1. Understand the Zone

Read all files listed in `{zone_files}`:
- Identify public functions, API endpoints, component interfaces
- Map data flow: inputs → processing → outputs
- Note dependencies and external integrations
- Identify what was changed during refactoring (`{refactoring_changes}`)

### 2. Plan Test Coverage

Based on `{recommended_depth}` and the code analysis:

**Unit test targets:**
- Pure functions and utilities
- Business logic with clear inputs/outputs
- Data transformations, validators, formatters
- State management logic
- Refactored code (verify new structure works correctly)

**Integration test targets:**
- Component-to-component interactions within the zone
- API endpoint request/response cycles
- Database operations (if applicable)
- Authentication/authorization flows touching this zone

**Edge case targets:**
- Boundary values for all inputs
- Null/undefined/empty handling
- Error paths and exception flows
- Concurrent access patterns (if applicable)

### 3. Write Unit Tests

Follow the test strategy guide — AAA pattern, proper isolation, framework conventions.

- Create test files in the project's test directory structure
- One test file per source file/component being tested
- Use stubs for simple dependencies, mocks only at system boundaries
- Aim for meaningful coverage, not 100% line coverage

### 4. Write Integration Tests

Follow the test strategy guide — API-first, fixture reuse, component scope.

- Test the interactions between components in this zone
- Reuse existing fixtures from safety-nets
- If existing fixtures are insufficient, create new ones alongside (not replacing)
- If Playwright MCP available: can execute browser-based integration tests
- If NOT available: write test files only with clear execution instructions

### 5. Write Edge Case Tests

Follow the test strategy guide — derive systematically, don't guess.

- Derive from boundary analysis of the unit and integration tests
- Group by boundary type (values, types, state, errors, concurrency)
- Include comment: `// Edge: [what boundary this tests]`
- Focus on high-risk boundaries first

### 6. Produce Artifact

Write the zone artifact to `{artifact_path}` using this structure:

```markdown
---
zone: '{zone_name}'
status: complete
date: '{date}'
---

## Zone: {zone_name}

### Tests Written
- Unit: {count} tests in {count} files
- Integration: {count} tests in {count} files
- Edge cases: {count} tests in {count} files
- **Total: {count} tests**

### Test Files Created
- {list of all test file paths, one per line}

### Coverage Notes
- {what this zone's tests cover}
- {what remains uncovered and why}
- {confidence level: high / medium / low}

### Discovery Findings

#### Bugs Discovered
- {bug description + which test exposed it}

#### Untestable Code
- {code that couldn't be tested despite refactoring, with reason}

#### Surprising Behaviors
- {undocumented behaviors found during testing}

#### Fixture Gaps
- {where existing fixtures were insufficient + what was added}
```

If no discoveries in a section, write "None identified."

---

## COMPLETION

When the artifact is written with `status: complete` in frontmatter, the Deep Tester's work on this zone is done. Conrad will detect completion via the artifact file and proceed with validation.
