---
framework: ""           # wordpress, drupal
version: ""             # 6.x, 5.x, 10, 7
---

# Framework Profile: {Framework} {Version}

## Architecture

- Standard file structure
- Core design patterns (hooks, plugins, modules, etc.)
- Template/theme system
- Routing and URL structure

## Common Patterns

- Theme/module customization approaches
- Content types and data modeling
- Page builders / layout systems (if applicable)
- Custom code entry points (functions.php, custom modules, etc.)

## Known Pitfalls

- Common sources of side effects
- Performance traps
- Security anti-patterns specific to this framework/version
- Dependency conflicts and plugin/module incompatibilities

## CLI Tools

- Primary CLI tool and key commands
- DDEV integration commands
- Database inspection commands
- Cache and configuration management

## Test Patterns

- Critical pages/routes to cover with E2E tests
- Components that benefit from snapshot testing
- Integration points to monitor
- Framework-specific test utilities or helpers

## Database

- Key tables and their purposes
- Important relationships to understand
- Configuration storage patterns
- Content storage patterns
