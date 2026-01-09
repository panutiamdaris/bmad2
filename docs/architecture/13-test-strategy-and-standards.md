[← Back to Index](./README.md)

# Test Strategy and Standards

## Philosophy
- Test-After Development; focus on quality, reliability, maintainability
- Coverage goals: Unit 80%+, Integration (core interactions), E2E (critical flows)
- Test Pyramid: 70% Unit, 20% Integration, 10% E2E

## Unit Tests
- Framework: Vitest 1.2.0; colocated `{filename}.test.ts`
- Requirements: Cover public methods, edge cases, AAA pattern, mock externals

## Integration Tests
- Location: packages/{package}/tests/integration/
- Infra: Testcontainers (Cosmos emulator), in-memory Service Bus, WireMock

## End-to-End Tests
- Framework: Playwright 1.41.0
- Scenarios: registration/login, add vehicle, offline service entry + sync, fuel entry + MPG, upload document, reminders, export PDF/CSV, conflict resolution

## Test Data
- Factories with Faker.js; fixtures in tests/fixtures/; teardown after each test

## Continuous Testing
- Run all tests on every PR; E2E on main branch
- Upload coverage to CI; fail build < 80%
