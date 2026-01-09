[← Back to Index](./README.md)

# Components

Based on the architectural patterns, tech stack, and data models, the following major logical components comprise the system. Given the monorepo structure, these components are organized as packages within the repository.

## Web Application (packages/web)
- Responsibility: PWA UI, offline-first
- Key Interfaces: Routes, Service Worker, IndexedDB, Backend API client
- Dependencies: packages/shared, packages/sync-engine
- Stack: Next.js 14, React 18, Zustand, Dexie, Zod

## Sync Engine (packages/sync-engine)
- Responsibility: Bidirectional sync, conflict detection and resolution
- Key Interfaces: SyncManager.sync/push/pull, ConflictResolver, SyncQueue
- Dependencies: packages/shared, Dexie, API
- Stack: TypeScript, RxJS, Dexie utilities

## Backend API (packages/api)
- Responsibility: Azure Functions HTTP endpoints (auth, sync, documents, export)
- Dependencies: packages/shared, Cosmos DB, Blob Storage, Service Bus, Azure AD B2C
- Stack: Node.js 20, Azure Functions 4.x, @azure SDKs, Zod

## Sync Worker (packages/sync-worker)
- Responsibility: Queue/Timer triggered background processing
- Dependencies: packages/shared, Cosmos DB, Service Bus
- Stack: Node.js 20, Azure Functions 4.x

## Shared Package (packages/shared)
- Responsibility: Shared types, schemas, utilities, constants
- Dependencies: None
- Stack: TypeScript, Zod, date-fns

## Infrastructure (packages/infrastructure)
- Responsibility: Azure Bicep IaC
- Interfaces: main.bicep and modular resources
- Stack: Bicep 0.24.24

## Component Interaction Diagram

```mermaid
graph TB
    subgraph "Client - Browser/PWA"
        WebApp[Web Application]\n        SyncEngine[Sync Engine]\n        IndexDB[(IndexedDB)]
    end
    subgraph "Azure Cloud"
        APIM[API Management]
        subgraph "Serverless Functions"
            BackendAPI[Backend API]\n            SyncWorker[Sync Worker]
        end
        ServiceBus[Service Bus]
        CosmosDB[(Cosmos DB)]
        BlobStorage[(Blob Storage)]
        ADB2C[Azure AD B2C]
    end
    subgraph "Shared Code"
        SharedPkg[Shared Package]
    end
    WebApp --> SyncEngine
    WebApp --> IndexDB
    SyncEngine --> IndexDB
    SyncEngine -->|HTTPS| APIM
    WebApp -->|Auth| ADB2C
    APIM --> BackendAPI
    BackendAPI --> ADB2C
    BackendAPI --> CosmosDB
    BackendAPI --> BlobStorage
    BackendAPI -->|Queue Messages| ServiceBus
    ServiceBus -->|Trigger| SyncWorker
    SyncWorker --> CosmosDB
    SyncWorker --> ServiceBus
    WebApp -.-> SharedPkg
    SyncEngine -.-> SharedPkg
    BackendAPI -.-> SharedPkg
    SyncWorker -.-> SharedPkg
```
