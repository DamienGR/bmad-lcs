# Test Strategy Guide

**Purpose:** Concrete testing patterns and strategies for the Deep Tester teammate. Loaded by the spawn prompt to guide test writing for each zone.

---

## Golden Rules

1. **Pyramid order within each zone:** Unit tests FIRST → Integration tests SECOND → Edge cases LAST
2. **Extend, don't replace** the fixture strategy established during safety-nets (Phase 2)
3. **Tests mirror actual usage patterns** — not theoretical coverage
4. **API tests are first-class citizens** alongside UI tests
5. **Prefer lower test levels** when possible (unit > integration > e2e)

---

## Unit Test Patterns

### When to Write Unit Tests
- Pure functions and utilities
- Business logic with clear inputs/outputs
- Data transformations and validators
- State management logic
- Critical calculations

### Conventions
- **Arrange-Act-Assert (AAA)** pattern for all unit tests
- **Naming:** `describe('[FunctionName]') → it('should [expected behavior] when [condition]')`
- **Isolation:** Each test is independent — no shared mutable state
- **Mocks vs Stubs:**
  - Use **stubs** for simple return values (database lookups, config reads)
  - Use **mocks** when verifying interaction behavior (API calls, event emissions)
  - Prefer stubs over mocks — they're simpler and less brittle
- **One assertion per test** when practical (multiple related assertions OK)

### Anti-Patterns to Avoid
- Testing implementation details (internal method calls)
- Mocking everything — only mock at system boundaries
- Fragile assertions on exact strings/formats
- Tests that depend on execution order

---

## Integration Test Patterns

### When to Write Integration Tests
- Component-to-component interactions
- API endpoint request/response cycles
- Database read/write operations
- Authentication/authorization flows
- Form submission → processing → result chains

### API-First Testing
- Test API endpoints BEFORE testing UI that calls them
- Verify request validation, response format, error codes
- Test authentication/authorization at API level
- Use direct HTTP calls (not browser-based)

### Fixture Strategy
- **REUSE the fixture strategy from safety-nets Phase 2** — check the safety-nets report for the chosen approach
- If fixtures are insufficient for deeper testing, **extend** the existing strategy:
  - Add new seed data for untested scenarios
  - Add new mock configurations for edge cases
  - Never modify existing fixtures that safety nets depend on
- **Database state:** Reset between test suites (not between individual tests within a suite)
- **External services:** Mock at the network boundary

### Scope
- Integration tests cover 2-3 components interacting
- NOT end-to-end (that's the safety nets' job)
- Focus on the "seams" between components — where data flows from one to another

---

## Edge Case Patterns

### Deriving Edge Cases Systematically
Don't guess — derive edge cases from:

1. **Boundary values:** Min, max, zero, negative, empty, null, undefined
2. **Type boundaries:** String where number expected, array where object expected
3. **State boundaries:** Empty state, full state, transitional state
4. **Error paths:** Network failure, timeout, invalid response, permission denied
5. **Concurrency:** Rapid double-submit, parallel requests, stale data

### Priority Order
1. Edge cases for HIGH RISK zones (from risk map)
2. Edge cases discovered while writing unit/integration tests
3. Edge cases from known bug patterns in the codebase

### Documentation
- Each edge case test should include a comment: `// Edge: [what boundary/condition this tests]`
- Group edge cases by the boundary type they test

---

## Test File Organization

Follow the project's existing test file conventions (from framework profile). If none exist:

```
tests/
├── unit/
│   └── [zone-name]/
│       └── [component].test.[ext]
├── integration/
│   └── [zone-name]/
│       └── [interaction].test.[ext]
└── edge-cases/
    └── [zone-name]/
        └── [boundary-type].test.[ext]
```

---

## Deep Tester Artifact Structure

After completing a zone, the Deep Tester MUST produce a structured artifact:

```markdown
## Zone: [zone-name]

### Tests Written
- Unit: [count] tests in [count] files
- Integration: [count] tests in [count] files
- Edge cases: [count] tests in [count] files

### Test Files Created
- [list of all test file paths]

### Coverage Notes
- [what this zone's tests cover]
- [what remains uncovered and why]

### Discovery Findings
- **Bugs discovered:** [list with test that exposed them]
- **Untestable code:** [code that couldn't be tested despite refactoring, with reason]
- **Surprising behaviors:** [undocumented behaviors found during testing]
- **Fixture gaps:** [where existing fixtures were insufficient]
```
