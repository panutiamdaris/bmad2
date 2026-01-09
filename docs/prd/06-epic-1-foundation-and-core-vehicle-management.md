[ Back to Index](./README.md)

## Epic 1: Foundation & Core Vehicle Management

**Epic Goal:** Establish the complete project foundation including monorepo structure, build pipeline, authentication, database schema, and offline-first architecture with cloud sync engine, while delivering functional vehicle management (CRUD operations) that demonstrates the offline-first capability and provides immediate user value through the ability to add and manage vehicle records.

### Story 1.1: Project Setup & Repository Structure

**As a** developer,  
**I want** a structured monorepo with frontend, backend, shared types, and documentation folders with initial build configuration,  
**so that** I have a consistent development environment and can begin implementing features with proper separation of concerns.

**Acceptance Criteria:**

1. Monorepo created with `/client` (React + TypeScript), `/server` (Node.js + Express), `/shared` (TypeScript interfaces), `/docs` folders
2. Package.json workspaces configured for dependency management across packages
3. TypeScript configured with strict mode and shared tsconfig for type consistency
4. ESLint and Prettier configured with consistent rules across client and server
5. Git repository initialized with .gitignore for node_modules, build artifacts, environment files
6. README.md documents folder structure, setup instructions, and development commands
7. Basic npm scripts defined for install, build, dev, and lint operations

### Story 1.2: CI/CD Pipeline & Azure Deployment Setup

**As a** developer,  
**I want** automated build, test, and deployment pipeline using GitHub Actions with Azure infrastructure provisioned,  
**so that** code changes are automatically validated and deployed to staging/production environments without manual intervention.

**Acceptance Criteria:**

1. GitHub Actions workflow created for CI: lint, type-check, and build on every push/PR
2. Azure App Service provisioned for backend API (using free tier)
3. Azure Static Web Apps provisioned for frontend hosting (using free tier)
4. Azure Cosmos DB database provisioned with SQL API (using free tier)
5. Azure Blob Storage container created for file uploads (using free tier)
6. GitHub Actions workflow created for CD: deploy to staging on main branch merge, production on release tag
7. Environment variables configured in GitHub secrets for Azure connection strings and API keys
8. Deployment succeeds with \"Hello World\" endpoints verifying frontend and backend connectivity

### Story 1.3: Database Schema & Migrations

**As a** developer,  
**I want** a defined database schema for users, vehicles, entries, reminders, and documents with migration scripts,  
**so that** the data model supports all MVP features with referential integrity and can evolve over time through versioned migrations.

**Acceptance Criteria:**

1. Users table schema defined with email, password_hash, created_at, settings fields
2. Vehicles table schema defined with user_id FK, make, model, year, VIN, license_plate, current_odometer, photo_url, created_at
3. Service_entries, fuel_entries, insurance, documents, reminders table schemas defined per data model
4. Foreign key constraints established to maintain referential integrity (cascade deletes where appropriate)
5. Indexes created on frequently queried fields (user_id, vehicle_id, date fields)
6. Migration script created to initialize schema from scratch
7. Migration script successfully executes against Azure Cosmos DB using SQL API
8. Seed data script created for development/testing with sample records

### Story 1.4: User Authentication & Registration

**As a** vehicle owner,  
**I want** to register an account with email/password and log in securely,  
**so that** my vehicle data is private and accessible only to me across devices.

**Acceptance Criteria:**

1. POST /api/auth/register endpoint accepts email and password, creates user with bcrypt-hashed password
2. POST /api/auth/login endpoint validates credentials and returns JWT access token and refresh token
3. JWT access tokens expire after 15 minutes; refresh tokens stored in httpOnly secure cookies
4. POST /api/auth/refresh endpoint accepts refresh token and issues new access token
5. POST /api/auth/logout endpoint invalidates refresh token
6. Email validation enforces valid format; password requires minimum 8 characters
7. Registration prevents duplicate emails with clear error message
8. Authentication middleware validates JWT on protected routes and returns 401 for invalid/missing tokens
9. Unit tests cover registration, login, token refresh, and authentication middleware

### Story 1.5: Frontend Authentication Flow & Protected Routes

**As a** vehicle owner,  
**I want** a registration and login interface that redirects me to the app after authentication,  
**so that** I can access my account and begin tracking vehicles without friction.

**Acceptance Criteria:**

1. Login page component with email and password fields, \"Log In\" button, and \"Register\" link
2. Registration page component with email, password, confirm password fields, and \"Create Account\" button
3. Form validation displays inline errors for invalid email format, password mismatch, or weak passwords
4. Successful login stores JWT in memory and refresh token in cookie, redirects to dashboard
5. Successful registration automatically logs user in and redirects to dashboard
6. Protected route wrapper redirects unauthenticated users to login page
7. Axios interceptor automatically includes JWT in Authorization header for API requests
8. Axios interceptor handles 401 responses by attempting token refresh, retrying original request, or redirecting to login
9. Logout button clears tokens and redirects to login page

### Story 1.6: Offline-First Architecture & IndexedDB Setup

**As a** vehicle owner,  
**I want** the application to store my data locally and function fully without internet connectivity,  
**so that** I can log entries in the field (gas station, mechanic parking lot) regardless of signal availability.

**Acceptance Criteria:**

1. Dexie.js configured as IndexedDB wrapper with schema mirroring server database structure
2. Local database tables created for vehicles, service_entries, fuel_entries, insurance, documents, reminders
3. Service Worker registered using Workbox for offline asset caching (HTML, CSS, JS, images)
4. Service Worker caches API responses for offline read access using Cache-First strategy
5. All CRUD operations write to IndexedDB first, then queue for sync when online
6. Application displays sync status indicator in UI (online, offline, syncing, sync error)
7. Manual sync trigger button available in settings/profile page
8. Application detects online/offline status changes using browser navigator.onLine events
9. IndexedDB operations wrapped in try/catch with user-friendly error messages for quota exceeded scenarios

### Story 1.7: Cloud Sync Engine - Upload Queue & Conflict Resolution

**As a** vehicle owner,  
**I want** my local data changes to automatically synchronize to the cloud when connectivity is available,  
**so that** my data is backed up and accessible across multiple devices without manual export/import.

**Acceptance Criteria:**

1. Sync queue table in IndexedDB tracks pending operations (create, update, delete) with timestamp, entity type, entity ID, payload
2. Background sync process runs every 30 seconds when online, processing queue operations sequentially
3. Successful sync operations remove items from queue and update synced_at timestamp on local records
4. Failed sync operations increment retry count and remain in queue, with exponential backoff for retries
5. Conflict detection compares local modified_at timestamp with server updated_at timestamp
6. Last-write-wins conflict resolution: server timestamp newer overwrites local; local timestamp newer uploads to server
7. Sync status indicator updates in real-time showing \"Syncing (X items remaining)\" during active sync
8. User settings include toggle to enable/disable cloud sync (default: enabled for new users)
9. When sync disabled, local data persists but no upload/download occurs; manual export/import remains available
10. Unit tests cover queue processing, conflict resolution, and retry logic

### Story 1.8: Vehicle CRUD API Endpoints

**As a** developer,  
**I want** RESTful API endpoints for creating, reading, updating, and deleting vehicle records,  
**so that** the frontend can manage vehicles with proper validation and authorization.

**Acceptance Criteria:**

1. POST /api/vehicles endpoint creates vehicle record with user_id from JWT, validates required fields (make, model, year), returns created vehicle with generated ID
2. GET /api/vehicles endpoint returns all vehicles for authenticated user, sorted by created_at descending
3. GET /api/vehicles/:id endpoint returns single vehicle if owned by authenticated user, returns 404 if not found or 403 if not owned
4. PUT /api/vehicles/:id endpoint updates vehicle fields, validates ownership, returns updated vehicle
5. DELETE /api/vehicles/:id endpoint soft-deletes vehicle and cascades to related entries/reminders, validates ownership
6. Photo upload handled via separate POST /api/vehicles/:id/photo endpoint returning Blob Storage SAS URL for direct upload
7. Validation enforces: make/model/year required, year between 1900-2027, VIN optional but unique if provided, current_odometer >= 0
8. Integration tests cover CRUD operations with authentication and authorization scenarios
9. Error responses follow consistent format with HTTP status codes and descriptive messages

### Story 1.9: Vehicle Management UI

**As a** vehicle owner,  
**I want** to add, view, edit, and delete my vehicles through an intuitive interface,  
**so that** I can manage my vehicle inventory and set the foundation for tracking lifecycle data.

**Acceptance Criteria:**

1. Dashboard displays vehicle list with cards showing vehicle name/label, make, model, year, and current odometer
2. \"Add Vehicle\" button opens modal/page with form fields: name (optional), make, model, year, VIN (optional), license plate (optional), current odometer, photo upload
3. Form validation displays inline errors matching API validation rules
4. Successful vehicle creation displays success message, adds vehicle to list, and closes modal/returns to dashboard
5. Each vehicle card includes \"Edit\" and \"Delete\" actions
6. Edit action opens populated form, successful update refreshes vehicle display
7. Delete action prompts confirmation (\"Delete [Vehicle Name]? This will also delete all associated data.\"), removes vehicle on confirmation
8. Empty state message displayed when no vehicles exist: \"Add your first vehicle to start tracking\"
9. Vehicle photo displays as thumbnail; clicking opens larger preview; upload supports JPG/PNG with 5MB limit
10. All operations work offline with sync status indicator showing \"Pending sync\" for unsync'd changes

---