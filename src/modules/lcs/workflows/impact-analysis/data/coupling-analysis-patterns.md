# Coupling Analysis Patterns

**Purpose:** Defines the three coupling dimensions, their analysis checkpoints, and expected subprocess return structures for step-03.

---

## Dimension A: Code Coupling

**Analysis checkpoints:**
1. **Shared state:** Global variables read or written by target code
2. **Shared objects:** Singleton instances, static class properties, registries
3. **Event systems:** Custom event dispatchers, observer patterns, pub/sub
4. **Configuration dependencies:** Shared config keys, environment variables
5. **Session/request state:** Session variables, request globals modified by target code

**Return structure:**
```json
{
  "dimension": "code",
  "findings": [
    {
      "type": "shared_state",
      "name": "$global_user_cache",
      "affectedFiles": ["file1.php", "file2.php"],
      "risk": "HIGH",
      "surprising": true,
      "explanation": "Global cache modified by authenticate() but read by 12 other files"
    }
  ],
  "totalFindings": 5,
  "surprisingCount": 2
}
```

---

## Dimension B: Data Coupling

**Analysis checkpoints:**
1. **Database triggers:** Triggers on tables modified by target code
2. **Stored procedures:** Procedures that reference the same tables
3. **SQL queries:** Other code that queries the same tables with assumptions about schema/data
4. **Migrations:** Pending or recent migrations affecting referenced tables
5. **Cache invalidation:** Cache keys that depend on data modified by target code
6. **Search indexes:** Indexes that need rebuilding if data changes

**Return structure:**
```json
{
  "dimension": "data",
  "findings": [
    {
      "type": "db_trigger",
      "name": "after_user_update",
      "table": "wp_users",
      "risk": "HIGH",
      "surprising": true,
      "explanation": "Trigger sends notification email on every user update — modifying authenticate() could trigger unexpected emails"
    }
  ],
  "totalFindings": 3,
  "surprisingCount": 1
}
```

---

## Dimension C: Framework Coupling

**Analysis checkpoints by framework:**

**WordPress:**
- `add_action` / `do_action` chains touching target functions
- `add_filter` / `apply_filters` chains on data modified by target
- Plugin activation/deactivation hooks
- Shortcode registrations referencing target functions

**Drupal:**
- `hook_` implementations for target modules
- Event subscribers
- Plugin/module dependencies

**Laravel:**
- Service provider bindings
- Event listeners
- Middleware chains
- Facade bindings

**General:**
- Dependency injection registrations
- Middleware/interceptor chains
- Route-to-controller mappings

**Return structure:**
```json
{
  "dimension": "framework",
  "findings": [
    {
      "type": "action_hook",
      "name": "wp_authenticate",
      "priority": 10,
      "registeredBy": "plugin-two-factor/two-factor.php",
      "risk": "MEDIUM",
      "surprising": false,
      "explanation": "Two-factor plugin hooks into authentication — expected coupling"
    }
  ],
  "totalFindings": 8,
  "surprisingCount": 3
}
```
