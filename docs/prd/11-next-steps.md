[ Back to Index](./README.md)

## Next Steps

### UX Expert Prompt

Once this PRD is reviewed and approved, please engage the UX Expert agent with the following prompt:

> "Please review the Vehicle Lifecycle Tracking Application PRD (docs/prd.md) and create a comprehensive UX/UI design document. Focus on the user flows, wireframes, and interaction patterns defined in the 'User Interface Design Goals' section. Ensure the design supports offline-first architecture with clear sync status indicators, mobile-first responsive layouts, and accessibility (WCAG AA compliance). Deliver wireframes for the core screens: Dashboard, Vehicle Detail (with tabs for History/Reminders/Documents/Analytics), entry forms (Service/Fuel), and the reminder management interface."

### Architect Prompt

After UX design is complete, engage the Architecture agent with:

> "Please review the Vehicle Lifecycle Tracking Application PRD (docs/prd.md) and UX design document to create the technical architecture. Focus on the offline-first sync engine implementation (IndexedDB + Azure Cosmos DB with last-write-wins conflict resolution), monorepo structure with React/TypeScript frontend and Node.js/Express backend, Azure deployment architecture (App Service, Static Web Apps, Blob Storage), and authentication flow (JWT with refresh tokens). Define the API specification, database schema migrations, sync protocol, and CI/CD pipeline configuration. Address the technical risks identified in the brief, particularly around sync complexity and Azure free-tier cost management."

---

**Document Status:** Complete - Ready for Checklist Validation  
**Total Epics:** 4  
**Total Stories:** 34  
**Last Updated:** January 6, 2026