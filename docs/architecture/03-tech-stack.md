[← Back to Index](./README.md)

# Tech Stack

This is the DEFINITIVE technology selection section for the entire project. These choices are the single source of truth that all other documents and development work must reference.

## Cloud Infrastructure

- Provider: Microsoft Azure
- Key Services:
  - Azure Functions (Serverless Compute)
  - Azure Cosmos DB (NoSQL Database)
  - Azure Blob Storage (Document Storage)
  - Azure Service Bus (Message Queue)
  - Azure API Management (API Gateway) - Consumption tier
  - Azure AD B2C (Identity)
  - Azure Application Insights (Monitoring)
- Deployment Regions: Single region initially (e.g., East US 2 or West Europe), multi-region ready via Cosmos DB global distribution

## Technology Stack Table

| Category | Technology | Version | Purpose | Rationale |
|----------|-----------|---------|---------|-----------|
| Language | TypeScript | 5.3.3 | Primary development language | Strong typing prevents sync bugs, shared types across stack |
| Runtime | Node.js | 20.11.0 LTS | JavaScript runtime | Long-term support, stable, excellent Azure Functions support |
| Frontend Framework | React | 18.2.0 | UI library | Large ecosystem, PWA support, potential React Native path |
| Frontend Meta-Framework | Next.js | 14.1.0 | React framework | Built-in routing, SSR capability, optimized builds, PWA support |
| Backend Framework | Azure Functions | 4.x | Serverless functions | Native Azure integration, cost-effective, scales to zero |
| Client Storage | Dexie.js | 3.2.4 | IndexedDB wrapper | Powerful queries, sync utilities, promise-based API |
| Database | Azure Cosmos DB | (Managed Service) | Cloud NoSQL database | Flexible schema, change feed, global distribution ready |
| Blob Storage | Azure Blob Storage | (Managed Service) | Document/image storage | Cost-effective, integrates with Functions, supports large files |
| Message Queue | Azure Service Bus | (Managed Service) | Async sync processing | Reliable messaging, dead-letter queues, Azure integration |
| API Gateway | Azure API Management | Consumption Tier | API gateway | Rate limiting, authentication, monitoring, cost-effective |
| Authentication | Azure AD B2C | (Managed Service) | Identity management | GDPR compliant, social logins, user management |
| Package Manager | pnpm | 8.15.0 | Dependency management | Fast, disk efficient, monorepo workspaces |
| Build Tool | Vite | 5.0.12 | Frontend build tool | Fast HMR, modern ESM, excellent PWA plugin |
| Monorepo Tool | Turborepo | 1.12.0 | Monorepo task runner | Build caching, parallel execution, simple config |
| IaC | Azure Bicep | 0.24.24 | Infrastructure as Code | Clean syntax, native Azure, type checking |
| Testing - Unit | Vitest | 1.2.0 | Unit test runner | Fast, Vite-native, Jest-compatible API |
| Testing - E2E | Playwright | 1.41.0 | End-to-end testing | Cross-browser, reliable, good mobile emulation |
| Linting | ESLint | 8.56.0 | Code quality | Standard linting, TypeScript support |
| Formatting | Prettier | 3.2.4 | Code formatting | Consistent style, integrates with ESLint |
| State Management | Zustand | 4.5.0 | Client state | Simple API, TypeScript support, minimal boilerplate |
| Data Validation | Zod | 3.22.4 | Schema validation | TypeScript-first, runtime validation, type inference |
| Date/Time | date-fns | 3.2.0 | Date manipulation | Tree-shakeable, immutable, comprehensive |
| HTTP Client | Fetch API | Native | HTTP requests | Built-in, no dependencies, modern standard |
| Monitoring | Azure Application Insights | (Managed Service) | APM & logging | Native Azure, tracks Functions, user analytics |
| CI/CD | GitHub Actions | - | Continuous integration | Free for public repos, Azure integration, matrix builds |
