[ Back to Index](./README.md)

## Technical Assumptions

### Repository Structure

**Monorepo** - Single repository containing frontend (/client), backend (/server), shared types (/shared), database migrations (/migrations), and documentation (/docs). This structure simplifies coordination for solo development, enables atomic commits across stack layers, and reduces overhead of managing multiple repos.

### Service Architecture

**Monolith with Serverless Functions (Hybrid)** - Core API deployed as Express.js monolith on Azure App Service for standard CRUD operations and sync logic. Computationally intensive or infrequent operations (PDF generation, data exports, batch processing) deployed as separate Azure Functions.

### Testing Requirements

**Unit + Integration Testing with Manual E2E** - Implement unit tests for business logic (sync engine, conflict resolution, cost calculations) and integration tests for API endpoints. Manual end-to-end testing for critical user workflows due to time constraints. Focus testing effort on offline-sync reliability (highest risk area).

### Additional Technical Assumptions and Requests

- **Frontend Framework:** React with TypeScript for type safety and mature ecosystem (offline-first libraries like Workbox, IndexedDB wrappers)
- **State Management:** Redux Toolkit for predictable state management across offline/online modes and sync queue tracking
- **Local Storage:** Dexie.js wrapper around IndexedDB for cleaner API and better transaction handling
- **Offline/PWA:** Workbox for Service Worker management, enabling offline functionality and background sync
- **Backend Language:** Node.js with Express.js (JavaScript ecosystem consistency with frontend reduces context switching)
- **Database:** Azure Cosmos DB with SQL API for built-in conflict resolution and global distribution capabilities (supports future multi-region sync)
- **Authentication:** JWT with refresh tokens stored in httpOnly cookies (simpler than Azure AD B2C for MVP; can migrate later)
- **File Storage:** Azure Blob Storage with SAS tokens for secure direct upload of receipts and vehicle photos
- **API Design:** RESTful with resource-based routes; sync endpoint uses delta/diff protocol to minimize bandwidth
- **Conflict Resolution:** Last-write-wins (LWW) with timestamp comparison for MVP; flag conflicts for user review in Phase 2
- **Deployment:** Azure App Service for API, Azure Static Web Apps for frontend (free tier includes CI/CD from GitHub)
- **CI/CD:** GitHub Actions for automated build/test/deploy pipeline with separate staging and production environments
- **Monitoring:** Application Insights for error tracking and performance monitoring within Azure free tier limits
- **Development Tools:** VS Code, ESLint/Prettier for code quality, Git for version control, Azure CLI for infrastructure management
- **Data Migration:** Versioned schema migrations using custom migration scripts (no heavy ORM overhead for small schema)

---