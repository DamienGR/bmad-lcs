# Fixture Strategies for Legacy Safety Nets

**Purpose:** Reference guide for test data management strategies when deploying safety nets on legacy web projects.

---

## Strategy A: Database Snapshot

**Approach:** Create a SQL dump of the current database state as the test baseline.

**Setup:**
1. Export full database: `mysqldump -u {db_user} -p {db_name} > fixtures/baseline-dump.sql`
2. Create a restore script for test setup
3. Run before each test suite to ensure clean state

**Pros:**
- Real production-like data
- Captures actual edge cases and data relationships
- Authentic behavior testing

**Cons:**
- May contain sensitive/personal data (GDPR risk)
- Large file size, slow restore
- Data may change over time (drift)

**Best for:** Projects with complex data relationships, when data sensitivity is not a concern.

**Mitigation:** Use data anonymization tools before committing snapshots.

---

## Strategy B: Seed Data

**Approach:** Create minimal scripts that populate the database with synthetic test data.

**Setup:**
1. Create seed scripts per entity (users, posts, products, etc.)
2. Use framework-specific tools:
   - WordPress: `wp-cli` fixtures or custom PHP script
   - Drupal: `drush` migrations or content fixtures
3. Run seed scripts before test suite

**Pros:**
- Lightweight and fast
- No sensitive data risk
- Fully reproducible across environments
- Version-controlled

**Cons:**
- May miss real-world edge cases
- Requires manual creation effort
- May not cover all data combinations

**Best for:** Projects with simple data models, CI/CD environments, when data privacy is important.

---

## Strategy C: Mock External Services

**Approach:** Intercept and mock all external API calls (payment gateways, email services, CRM, etc.).

**Setup:**
1. Identify all external service integration points (from audit report)
2. Create mock responses for each endpoint
3. Use Playwright's `route()` API to intercept network requests
4. Store mock responses in `fixtures/mocks/`

**Pros:**
- Tests isolated from external dependencies
- Fast execution (no network calls)
- Deterministic results

**Cons:**
- Does not validate internal data
- Mocks may drift from real API responses
- Setup effort per service

**Best for:** Projects with many external integrations (payment, email, shipping, etc.).

---

## Strategy D: Hybrid (Recommended)

**Approach:** Combine database snapshot (or seed) with external service mocking.

**Setup:**
1. Choose database strategy (A or B) based on data sensitivity
2. Apply Strategy C for all external services
3. Create a test bootstrap script that:
   - Restores/seeds database
   - Activates mock routes
   - Verifies baseline state

**Pros:**
- Comprehensive coverage
- Real data + isolated services
- Reproducible

**Cons:**
- Most complex setup
- Requires maintenance of both data and mocks

**Best for:** Most legacy projects — provides the best balance of authenticity and isolation.

---

## Framework-Specific Notes

### WordPress
- Use `wp db export` / `wp db import` for snapshots
- `WP_TESTS_DIR` for test environment isolation
- Consider `wp-env` for containerized test environments
- Multisite: snapshot must include all sites

### Drupal
- Use `drush sql:dump` / `drush sql:cli < dump.sql` for snapshots
- Content fixtures via migrations or default_content module
- Configuration export/import for test settings

### Generic PHP
- PDO-based seed scripts
- PHP migration tools (Phinx, Doctrine Migrations)
- .env.testing for test database configuration

---

## Test Data Organization

```
project-root/
├── tests/
│   ├── fixtures/
│   │   ├── baseline-dump.sql      # Strategy A
│   │   ├── seeds/                 # Strategy B
│   │   │   ├── users.php
│   │   │   ├── products.php
│   │   │   └── orders.php
│   │   └── mocks/                 # Strategy C
│   │       ├── payment-gateway.json
│   │       ├── email-service.json
│   │       └── crm-api.json
│   └── bootstrap.php              # Test setup script
```
