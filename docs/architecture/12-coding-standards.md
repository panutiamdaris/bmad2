[← Back to Index](./README.md)

# Coding Standards

CRITICAL: These standards are MANDATORY for AI development agents.

## Core Standards
- Languages & Runtimes: TypeScript 5.3.3; Node.js 20.11.0 LTS; strict mode
- Style & Linting: ESLint 8.56.0; Prettier 3.2.4 (single quotes, 2-space, 100 cols)
- Tests: Colocated unit tests; E2E in tests/e2e; 80%+ coverage for business logic

## Naming Conventions
| Element | Convention | Example |
|---------|------------|---------|
| Files | kebab-case | sync-manager.ts |
| Classes | PascalCase | SyncManager |
| Interfaces/Types | PascalCase | Vehicle |
| Functions | camelCase | calculateMPG() |
| Constants | SCREAMING_SNAKE_CASE | MAX_RETRIES |
| React Components | PascalCase | VehicleCard |
| Hooks | camelCase with `use` | useVehicles() |

## Critical Rules
1. Never use console.log in production — use structured logger
2. All API responses use standardized types from `@shared/types`
3. Use repository pattern for all database access
4. Validate all inputs with Zod at API boundary (`@shared/schemas`)
5. No hardcoded error strings — use error code constants
6. Proper error handling for all async functions (try/catch)
7. UUIDs for all IDs (`crypto.randomUUID()`)
8. Dates in ISO 8601 (`new Date().toISOString()`)
9. Immutable updates only (no direct mutation)
10. Service worker must handle offline gracefully with fallbacks

## TypeScript Specifics
- No `any`. Prefer `unknown` + type guards or explicit types
- Explicit return types on public functions
- Use discriminated unions for variants
- Prefer `interface` for object shapes
- Use `readonly` for immutable props

## React Specifics
- Functional components only; no class components
- Explicitly typed props; define interfaces
- Extract reusable logic into custom hooks
- Use `useCallback` for callback props
- Destructure props in function signature

## Security Rules
- Sanitize user input before display (escape HTML)
- Parameterized queries only
- Validate file uploads (MIME, size)
- No secrets in code; use environment variables
- HTTPS only in production

Note: Corrected minor typo from source ("Core Standar" → "Core Standards").
