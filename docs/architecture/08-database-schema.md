[← Back to Index](./README.md)

# Database Schema

This section transforms the conceptual data models into concrete database schemas for both IndexedDB (client-side) and Azure Cosmos DB (cloud-side).

## IndexedDB Schema (Client-Side)

Database Name: vehicle-tracker  
Version: 1

```typescript
// Dexie.js schema definition
const db = new Dexie('vehicle-tracker');

db.version(1).stores({
  users: 'id, email, lastSyncAt',
  vehicles: 'id, userId, isActive, currentOdometer, [userId+isActive]',
  serviceEvents: 'id, vehicleId, date, odometer, serviceType, [vehicleId+date]',
  fuelEntries: 'id, vehicleId, date, odometer, isFillUp, [vehicleId+date], [vehicleId+isFillUp]',
  insurancePolicies: 'id, vehicleId, endDate, [vehicleId+endDate]',
  documents: 'id, vehicleId, category, date, linkedEntityType, linkedEntityId, [vehicleId+category]',
  reminders: 'id, vehicleId, status, odometerThreshold, dateThreshold, [vehicleId+status]',
  syncConflicts: 'id, userId, entityType, entityId, resolvedAt',
  syncQueue: '++autoId, entityType, entityId, operation, timestamp'
});
```

Index Rationale:
- Primary Keys: `id` UUID
- Foreign Keys: `userId`, `vehicleId`
- Compound Indexes: `[userId+isActive]`
- Query Optimization: date and status fields
- Sync Queue: Auto-increment for FIFO

Storage Estimates (per typical MVP user): ~10–15 MB (dominated by documents/thumbnails)

## Azure Cosmos DB Schema (Cloud-Side)

Database: vehicle-tracker-db  
API: Core (SQL)  
Partition Strategy: `/userId`

Container Configuration:
- Partition Key: `/userId`
- Consistency: Session
- Change Feed: Enabled (except sync-conflicts)
- TTL: 30 days on resolved sync-conflicts

Containers: users, vehicles, service-events, fuel-entries, insurance-policies, documents, reminders, sync-conflicts

Indexing Policy:
```json
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [{ "path": "/*" }],
  "excludedPaths": [
    { "path": "/localVersion/*" },
    { "path": "/cloudVersion/*" },
    { "path": "/_etag/?" }
  ]
}
```

Throughput Configuration:
- MVP: 400 RU/s shared
- Production: Autoscale 400–4000 RU/s per container

Note: The source document contains a duplicated Database Schema section; this shard preserves the first occurrence to avoid redundancy.
