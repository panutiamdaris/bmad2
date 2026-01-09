[← Back to Index](./README.md)

# Core Workflows

## Workflow 1: User Registration and First-Time Setup

```mermaid
sequenceDiagram
    actor User
    participant WebApp
    participant ADB2C
    participant BackendAPI
    participant CosmosDB
    participant IndexDB
    User->>WebApp: Click "Sign Up"
    WebApp->>ADB2C: Redirect to registration
    ADB2C-->>WebApp: Redirect with auth token
    WebApp->>BackendAPI: POST /api/user/register
    BackendAPI->>CosmosDB: Create User record
    WebApp->>IndexDB: Initialize and store profile
```

## Workflow 2: Add Vehicle and First Service Entry (Offline)

```mermaid
sequenceDiagram
    actor User
    participant WebApp
    participant IndexDB
    participant SyncEngine
    WebApp->>IndexDB: INSERT Vehicle
    WebApp->>SyncEngine: Enqueue for sync
    WebApp->>IndexDB: INSERT ServiceEvent
    WebApp->>IndexDB: Update vehicle.currentOdometer
```

## Workflow 3: Background Sync - Push Local Changes

```mermaid
sequenceDiagram
    participant SyncEngine
    participant IndexDB
    participant APIM
    participant BackendAPI
    participant CosmosDB
    participant ServiceBus
    SyncEngine->>IndexDB: Query pending
    SyncEngine->>APIM: POST /api/sync/push
    BackendAPI->>CosmosDB: Batch upserts with version checks
    BackendAPI->>ServiceBus: Queue conflicts
    SyncEngine->>IndexDB: Update lastSyncedAt
```

## Workflow 4: Document Upload with Sync

```mermaid
sequenceDiagram
    actor User
    participant WebApp
    participant IndexDB
    participant APIM
    participant BackendAPI
    participant BlobStorage
    participant CosmosDB
    WebApp->>IndexDB: Store blobs + metadata
    WebApp->>APIM: POST /api/documents/upload
    BackendAPI->>BlobStorage: Upload file + thumbnail
    BackendAPI->>CosmosDB: Upsert Document metadata
    WebApp->>IndexDB: Update with cloud URLs
```

## Workflow 5: Fuel Economy Calculation

```mermaid
sequenceDiagram
    actor User
    participant WebApp
    participant IndexDB
    WebApp->>IndexDB: Query previous fill-up
    WebApp->>WebApp: Calculate MPG and averages
    WebApp->>IndexDB: INSERT FuelEntry
```
