[← Back to Index](./README.md)

# Source Tree

The following project structure reflects the monorepo organization with clear separation between frontend, backend, shared code, and infrastructure.

```text
vehicle-tracker/
 .github/
  workflows/
   ci.yml
   deploy-dev.yml
   deploy-prod.yml
 packages/
  web/
   src/app/(auth|dashboard)/ ...
   public/ (manifest.json, service-worker.js)
   tests/
  sync-engine/
   src/(sync-manager.ts, conflict-resolver.ts, ...)
  api/
   src/functions/(auth|sync|documents|export|user)/
   tests/
  sync-worker/
   src/functions/(process-sync-queue.ts, change-feed-handler.ts, ...)
  shared/
   src/(types|schemas|constants|utils)/
  infrastructure/
   main.bicep, modules/, parameters/
 scripts/ (setup.sh, build-all.sh, deploy.sh)
 docs/ (architecture.md, prd.md, brief.md)
 turbo.json, pnpm-workspace.yaml, package.json, tsconfig.json
```

Key Decisions:
1. Monorepo with pnpm workspaces
2. Next.js App Directory
3. Separate Sync Engine package
4. Two Function Apps (api vs sync-worker)
5. Shared Package for types/utilities
6. Modular Bicep for Azure resources

Package Dependencies:
- web → shared, sync-engine
- sync-engine → shared
- api → shared
- sync-worker → shared
- shared → none
