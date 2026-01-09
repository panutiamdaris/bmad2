[ Back to Index](./README.md)

## Epic 2: Lifecycle Data Capture & History

**Epic Goal:** Enable users to capture comprehensive vehicle lifecycle data through intuitive logging interfaces for service events, fuel entries, insurance records, and document uploads. Provide complete history views with search, filter, and sort capabilities so users can quickly find any recorded information. This epic transforms the application from basic vehicle management into a functional tracking system that addresses the core value proposition of maintaining verifiable, searchable vehicle records for ownership decisions and resale documentation.

### Story 2.1: Service Event Logging API

**As a** developer,  
**I want** RESTful API endpoints for creating, reading, updating, and deleting service event records,  
**so that** users can track all maintenance activities with proper validation and association to their vehicles.

**Acceptance Criteria:**

1. POST /api/vehicles/:vehicleId/services endpoint creates service entry with date, odometer, service_type, cost, location, notes fields, validates vehicle ownership
2. GET /api/vehicles/:vehicleId/services endpoint returns all service entries for vehicle, sorted by date descending
3. GET /api/services/:id endpoint returns single service entry, validates user owns associated vehicle
4. PUT /api/services/:id endpoint updates service entry fields, validates ownership
5. DELETE /api/services/:id endpoint soft-deletes service entry, validates ownership
6. Validation enforces: date required (not future), odometer >= 0, service_type from predefined list (oil_change, tire_rotation, brake_service, general_maintenance, repair, inspection, other), cost >= 0
7. Service entry automatically updates vehicle's current_odometer if entry odometer > current vehicle odometer
8. POST /api/services/:id/receipt endpoint returns Blob Storage SAS URL for receipt document upload
9. Integration tests cover CRUD operations with edge cases (future dates rejected, negative costs rejected, non-owner access denied)


### Story 2.2: Service Event Logging UI

**As a** vehicle owner,  
**I want** to quickly log service events with all relevant details from my phone or desktop,  
**so that** I can maintain a complete maintenance history for warranty, resale, and personal records.

**Acceptance Criteria:**

1. Vehicle detail page includes "Log Service" button that opens service entry form
2. Service form includes fields: date (defaults to today), odometer (pre-filled from vehicle current_odometer), service type dropdown, cost, location/shop, notes (optional), receipt upload
3. Form validation displays inline errors matching API validation rules, highlights invalid fields
4. Successful submission displays success message, adds entry to service history, updates vehicle current_odometer if applicable, clears form
5. Service history tab shows chronological list of entries with date, service type, odometer, cost; clicking entry expands to show full details
6. Each service entry includes "Edit" and "Delete" actions; edit opens populated form, delete prompts confirmation
7. Receipt upload supports PDF/JPG/PNG with 10MB limit; uploaded receipts display as clickable thumbnails that open full view
8. Form adapts to mobile with large tap targets and thumb-zone positioning for one-handed entry
9. Offline operations queue for sync with visual indicator showing "Pending sync" status
10. Quick-add shortcut on dashboard allows service logging without navigating to vehicle detail first

### Story 2.3: Fuel Entry Logging API

**As a** developer,  
**I want** RESTful API endpoints for creating, reading, updating, and deleting fuel entries with automatic MPG calculation,  
**so that** users can track fuel consumption patterns and compare efficiency across vehicles.

**Acceptance Criteria:**

1. POST /api/vehicles/:vehicleId/fuel endpoint creates fuel entry with date, odometer, gallons (or liters), cost_per_unit, total_cost, location fields
2. Automatic MPG calculation: retrieve previous fuel entry for vehicle, calculate (current_odometer - previous_odometer) / gallons, store as calculated_mpg
3. GET /api/vehicles/:vehicleId/fuel endpoint returns all fuel entries for vehicle with calculated_mpg, sorted by date descending
4. GET /api/fuel/:id endpoint returns single fuel entry, validates ownership
5. PUT /api/fuel/:id endpoint updates fuel entry, recalculates MPG if odometer/gallons changed
6. DELETE /api/fuel/:id endpoint soft-deletes fuel entry, validates ownership
7. Validation enforces: date required (not future), odometer >= vehicle creation odometer, gallons > 0, cost_per_unit >= 0, total_cost >= 0
8. First fuel entry for vehicle skips MPG calculation (no previous baseline); subsequent entries calculate MPG
9. Fuel entry updates vehicle current_odometer if entry odometer > current vehicle odometer
10. Integration tests cover MPG calculation edge cases (first entry, single entry after deletion, out-of-order dates)

### Story 2.4: Fuel Entry Logging UI & Fuel History

**As a** vehicle owner,  
**I want** to quickly log fuel fill-ups and view my fuel consumption history with calculated MPG,  
**so that** I can track efficiency trends and identify potential vehicle issues through MPG changes.

**Acceptance Criteria:**

1. Vehicle detail page includes "Log Fuel" button that opens fuel entry form
2. Fuel form includes fields: date (defaults to today), odometer (pre-filled), gallons or liters (unit toggle), cost per gallon/liter, total cost (auto-calculated from gallons  cost_per_unit), location (optional)
3. Gallons/liters toggle persists per vehicle (remembers user preference for that vehicle)
4. Form auto-calculates total cost when gallons and cost_per_unit both entered; allows manual override
5. Successful submission displays calculated MPG (if available) in success message, adds entry to fuel history
6. Fuel history tab shows entries with date, gallons, total cost, MPG (if calculated), location; clicking expands details
7. Empty state for first fuel entry explains "MPG will be calculated starting with your second fuel entry"
8. Fuel history displays average MPG across all entries at top of list
9. Each entry includes "Edit" and "Delete" actions; editing recalculates MPG on save
10. Mobile-optimized form with numeric keyboards for number fields, large buttons for quick logging

### Story 2.5: Insurance & Document Storage API

**As a** developer,  
**I want** API endpoints for managing insurance records and vehicle documents with secure file storage,  
**so that** users can keep all vehicle-related paperwork organized and accessible.

**Acceptance Criteria:**

1. POST /api/vehicles/:vehicleId/insurance endpoint creates insurance record with provider, policy_number, renewal_date, premium fields
2. GET /api/vehicles/:vehicleId/insurance endpoint returns current insurance record for vehicle (most recent by renewal_date)
3. PUT /api/insurance/:id endpoint updates insurance record, validates ownership
4. POST /api/vehicles/:vehicleId/documents endpoint creates document record with document_type, name, file upload, validates ownership
5. Document types supported: registration, warranty, manual, receipt, other
6. GET /api/vehicles/:vehicleId/documents endpoint returns all documents for vehicle, grouped by document_type
7. DELETE /api/documents/:id endpoint deletes document record and removes file from Blob Storage, validates ownership
8. File upload generates Blob Storage SAS URL for direct upload, stores blob URL in document record
9. GET /api/documents/:id/download endpoint generates time-limited download URL for document file
10. Validation enforces: insurance renewal_date not in past (at creation), premium >= 0, document file size <= 10MB, supported file types (PDF, JPG, PNG, DOCX)

### Story 2.6: Insurance & Document Management UI

**As a** vehicle owner,  
**I want** to store my insurance policy details and upload important vehicle documents,  
**so that** I have quick access to policy information and organized digital copies of all paperwork.

**Acceptance Criteria:**

1. Vehicle detail page includes "Insurance" tab showing current policy with provider, policy number, renewal date, premium, and "Update" button
2. Insurance form allows creating or updating policy details; displays renewal date prominently with visual indicator if within 30 days
3. Vehicle detail page includes "Documents" tab with "Upload Document" button
4. Document upload form includes document type dropdown, name field (auto-populated from filename, editable), file picker
5. Documents tab displays documents grouped by type (Registration, Warranty, Manuals, Receipts, Other) with thumbnails for images/PDFs
6. Clicking document opens preview modal (for images/PDFs) or initiates download (for other types)
7. Each document includes "Delete" action with confirmation prompt
8. Insurance renewal date reminder appears on dashboard when within 30 days or past due
9. Offline document uploads queue file for upload when connectivity restored, with progress indicator
10. Documents support drag-and-drop upload on desktop for faster bulk uploads

### Story 2.7: Search & Filter Functionality

**As a** vehicle owner,  
**I want** to search across all my entries and filter by date, vehicle, type, or keyword,  
**so that** I can quickly find specific records like "that brake service last summer" or "all 2024 expenses."

**Acceptance Criteria:**

1. Global search bar in header allows keyword search across service events, fuel entries, insurance, documents (searches service_type, location, notes, document names)
2. Search results page displays matched entries grouped by type (Services, Fuel, Documents) with highlighting of matched terms
3. Filter panel includes: date range picker (start/end dates), vehicle multi-select, entry type checkboxes (Service, Fuel, Insurance, Document)
4. Applied filters display as removable chips above results with "Clear All" option
5. Sort options: Date (newest/oldest), Cost (highest/lowest), Odometer (highest/lowest)
6. Empty state message for no results: "No entries found. Try adjusting your filters."
7. Search/filter executes on local IndexedDB data for instant offline results
8. Results include quick actions: "View Details", "Edit", "Delete" without leaving search page
9. Export button on search results allows exporting filtered subset to CSV
10. Search and filter state persists in URL query params for bookmarkable/shareable search links

### Story 2.8: Vehicle History View & Timeline

**As a** vehicle owner,  
**I want** a comprehensive chronological timeline of all activity for each vehicle,  
**so that** I can see the complete maintenance and usage history at a glance.

**Acceptance Criteria:**

1. Vehicle detail page includes "History" tab as default view showing unified timeline of all entries (services, fuel, insurance updates, document uploads)
2. Timeline displays entries chronologically descending with date, entry type icon, brief description, and cost (if applicable)
3. Entry descriptions: "Oil Change - Bob's Auto" (service), "Fuel: 12.5 gal, 28.3 MPG" (fuel), "Uploaded registration" (document), "Insurance renewed" (insurance)
4. Timeline supports infinite scroll/pagination for vehicles with extensive history (load 20 entries per page)
5. Each timeline entry expandable to show full details inline without navigating away
6. Timeline includes visual milestones for significant odometer marks (10K, 25K, 50K, 100K miles)
7. Empty state for new vehicles: "No entries yet. Start by logging a service or fuel fill-up."
8. Timeline filters available: Show All, Services Only, Fuel Only, Documents Only
9. Quick stats summary at top of timeline: Total entries, Total cost (all time), Average monthly cost, Current odometer
10. Print-friendly view available for generating paper copy of complete vehicle history

---