[ Back to Index](./README.md)

## Epic 3: Proactive Maintenance & Reminders

**Epic Goal:** Transform the application from passive tracking to proactive vehicle management by implementing a comprehensive reminder system that notifies users of upcoming and overdue maintenance based on odometer readings and calendar dates. This epic directly addresses the "cognitive burden" problem statement by automating the mental juggling of maintenance schedules, reducing anxiety about forgotten service intervals, and ensuring users stay on top of vehicle care without constant manual checking. The reminder system integrates with logged service events to automatically update intervals, creating a closed-loop maintenance workflow.

### Story 3.1: Reminder Data Model & API

**As a** developer,  
**I want** RESTful API endpoints for creating, reading, updating, and deleting maintenance reminders with support for both odometer-based and date-based triggers,  
**so that** users can configure automated notifications for all vehicle maintenance needs.

**Acceptance Criteria:**

1. POST /api/vehicles/:vehicleId/reminders endpoint creates reminder with type (odometer_based or date_based), title, description, interval_miles (for odometer), interval_days (for date), next_due_odometer, next_due_date
2. GET /api/vehicles/:vehicleId/reminders endpoint returns all reminders for vehicle with status (upcoming, due_soon, overdue) calculated dynamically
3. GET /api/reminders/:id endpoint returns single reminder with status, validates ownership
4. PUT /api/reminders/:id endpoint updates reminder configuration, validates ownership
5. DELETE /api/reminders/:id endpoint soft-deletes reminder, validates ownership
6. POST /api/reminders/:id/complete endpoint marks reminder complete, calculates next due (adds interval to current odometer/date), creates completion log entry
7. POST /api/reminders/:id/snooze endpoint delays reminder by specified days or miles, updates next_due values
8. Status calculation: overdue (past due_date or current_odometer > due_odometer), due_soon (within 7 days or 500 miles), upcoming (future)
9. Validation enforces: title required, type required, interval > 0 for applicable type, next_due values required
10. Integration tests cover reminder creation, status calculation edge cases, completion workflow, snooze logic

### Story 3.2: Reminder Management UI

**As a** vehicle owner,  
**I want** to create and manage maintenance reminders for each vehicle with customizable intervals,  
**so that** I never miss an oil change, tire rotation, or other scheduled maintenance.

**Acceptance Criteria:**

1. Vehicle detail page includes "Reminders" tab showing list of all reminders grouped by status (Overdue, Due Soon, Upcoming)
2. "Add Reminder" button opens reminder creation form
3. Reminder form includes: title, description (optional), reminder type radio buttons (Odometer-Based / Date-Based)
4. For odometer-based: interval_miles field, current odometer display, calculated next_due_odometer shown
5. For date-based: interval_days field or specific date picker, calculated next_due_date shown
6. Common reminder templates available as quick-add: "Oil Change (every 5,000 mi)", "Tire Rotation (every 7,500 mi)", "Annual Inspection (yearly)", "Insurance Renewal (custom date)"
7. Reminder list displays: title, next due (date or odometer), status badge (color-coded: red=overdue, yellow=due soon, green=upcoming)
8. Each reminder includes "Complete", "Snooze", "Edit", "Delete" actions
9. "Complete" button marks reminder done, shows success message with new next due value, optionally prompts to log associated service entry
10. "Snooze" button opens modal with snooze duration options (1 week, 2 weeks, 500 miles, 1,000 miles, custom)

### Story 3.3: Dashboard Reminder Notifications

**As a** vehicle owner,  
**I want** to see all overdue and due-soon reminders prominently on my dashboard when I open the app,  
**so that** critical maintenance never slips through the cracks across my multiple vehicles.

**Acceptance Criteria:**

1. Dashboard displays "Reminders" widget at top showing all overdue and due-soon reminders across all vehicles
2. Overdue reminders displayed first with red badge, due-soon reminders below with yellow badge
3. Each reminder shows: vehicle name, reminder title, how overdue or how soon (e.g., "300 miles overdue" or "Due in 3 days")
4. Clicking reminder navigates to vehicle reminders tab with that reminder highlighted
5. "Mark Complete" quick action available directly from dashboard widget
6. Widget shows count: "3 Overdue  2 Due Soon" when collapsed; expand/collapse toggle for full list
7. Empty state when no reminders due: "All caught up! No maintenance needed right now." with green checkmark
8. Dashboard widget updates in real-time as reminders completed or new ones become due
9. Widget includes "Add Reminder" quick action that prompts for vehicle selection
10. Reminder count badge appears on dashboard navigation/header when overdue reminders exist

### Story 3.4: Automatic Reminder Updates from Service Logs

**As a** vehicle owner,  
**I want** reminders to automatically update when I log related service events,  
**so that** I don't have to manually mark reminders complete after every oil change or maintenance.

**Acceptance Criteria:**

1. Service entry form includes optional "Link to Reminder" dropdown showing relevant reminders for that vehicle
2. Relevant reminders suggested based on service_type match (oil_change service suggests "Oil Change" reminders)
3. When service entry saved with linked reminder, reminder automatically marked complete and next due calculated
4. Completion creates audit trail linking service entry to reminder completion in reminder history
5. If service logged without linking reminder, app displays toast notification: "Did this service complete any reminders?" with quick-link options
6. Reminder completion workflow from service entry shows same success message as manual completion
7. Service history entry displays linked reminder badge/icon when associated with reminder completion
8. If multiple reminders match service type, user can select which one(s) to complete from service form
9. Unlinked service entries can retroactively link to reminders via "Link to Reminder" action in service history
10. Reminder history shows all completions with timestamps and linked service entry references (if available)

### Story 3.5: Reminder Templates & Smart Suggestions

**As a** vehicle owner,  
**I want** pre-configured reminder templates and smart suggestions based on my vehicle and usage patterns,  
**so that** I can quickly set up comprehensive maintenance schedules without researching intervals.

**Acceptance Criteria:**

1. "Quick Setup" wizard available on first reminder creation offering vehicle-specific maintenance schedule
2. Wizard asks: "How often do you drive?" (Daily, Weekly, Occasionally) to estimate annual mileage
3. Based on vehicle year and mileage patterns, suggests common reminders: Oil change, tire rotation, air filter, cabin filter, coolant flush, brake inspection, annual inspection
4. User can check/uncheck suggested reminders and adjust intervals before batch creation
5. Reminder templates library includes 20+ common maintenance items with industry-standard intervals
6. Template search allows finding reminders by keyword: "brake", "filter", "fluid"
7. Creating reminder from template pre-fills title, description, and recommended interval; user can modify before saving
8. Smart suggestions appear when adding reminders: "Based on your last oil change, consider adding a tire rotation reminder"
9. Reminders for known vehicle-specific requirements (e.g., timing belt at 100K miles for certain models) suggested proactively
10. Templates support both metric and imperial units based on user preference (km vs miles)

### Story 3.6: Reminder History & Compliance Tracking

**As a** vehicle owner,  
**I want** to view the complete history of all reminder completions and track my maintenance compliance rate,  
**so that** I can see how well I'm staying on top of vehicle care and identify patterns of neglect or over-servicing.

**Acceptance Criteria:**

1. Reminders tab includes "History" sub-tab showing all completed reminders chronologically
2. Each history entry shows: reminder title, completion date, actual odometer at completion, linked service entry (if exists), days early/late
3. Compliance metrics displayed at top: On-Time Rate (% completed within due window), Average Days Late, Average Miles Overdue
4. Visual compliance indicator: green badge 90% on-time, yellow 70-89%, red <70%
5. Filter history by reminder type, date range, or specific reminder
6. Export reminder history to CSV for external analysis or record-keeping
7. Compliance report shows per-reminder breakdown: "Oil Change: 4/5 on-time (80%)", "Tire Rotation: 2/3 on-time (67%)"
8. History chart visualizes completion timeline with markers for on-time (green) vs late (red) completions
9. Deleted reminders remain in history with "(Deleted)" label for audit purposes
10. Reminder history contributes to vehicle resale documentation showing maintenance diligence

### Story 3.7: Odometer Update Triggers & Sync

**As a** vehicle owner,  
**I want** the app to track my vehicle odometer automatically based on entries and recalculate reminder statuses,  
**so that** odometer-based reminders accurately reflect how close I am to needing service without manual odometer updates.

**Acceptance Criteria:**

1. Service entries and fuel entries that include odometer values automatically update vehicle's current_odometer if higher than existing value
2. Vehicle detail page displays current odometer prominently with "Last Updated" timestamp
3. Manual "Update Odometer" action available on vehicle card and detail page for updating without logging entry
4. Odometer update triggers immediate recalculation of all odometer-based reminder statuses for that vehicle
5. Reminder status changes propagate to dashboard widget in real-time
6. Odometer update validates new value is greater than current odometer (prevents accidental rollback)
7. Odometer history tracked in vehicle audit log showing all updates with source (service entry, fuel entry, manual)
8. If odometer updated manually and reminders become overdue, dashboard notification alerts user
9. Sync ensures odometer updates propagate across devices when cloud sync enabled
10. Vehicle statistics show average miles per month based on odometer change over time

### Story 3.8: Bulk Reminder Actions & Management

**As a** vehicle owner,  
**I want** to perform bulk operations on multiple reminders at once,  
**so that** I can efficiently manage maintenance schedules when circumstances change (e.g., selling vehicle, changing oil type).

**Acceptance Criteria:**

1. Reminders tab includes checkbox selection mode for multi-select reminders
2. "Select All" option available for current filter/view (all overdue, all due soon, all for vehicle)
3. Bulk actions available when 2 reminders selected: Complete All, Snooze All, Delete All, Adjust Intervals
4. "Complete All" prompts for confirmation, marks all selected reminders complete, shows summary ("5 reminders marked complete")
5. "Snooze All" opens modal for unified snooze duration, applies to all selected reminders
6. "Adjust Intervals" allows percentage-based adjustment (e.g., "Increase all intervals by 20%") for changed oil type or driving habits
7. Bulk delete prompts for confirmation with warning, soft-deletes all selected reminders
8. Bulk operations execute as single transaction; all succeed or all fail (no partial completion)
9. Undo option available for 10 seconds after bulk operation with toast notification
10. Bulk operation results logged in reminder history for audit trail

---