[← Back to Index](./README.md)

# Infrastructure and Deployment

## Infrastructure as Code
- Tool: Azure Bicep 0.24.24
- Location: packages/infrastructure/
- Modules: cosmos-db, function-app, storage, service-bus, api-management, monitoring

## Deployment Strategy
- Blue-Green with Azure Function deployment slots (staging/production)
- Zero-downtime, instant rollback via slot swap
- Smoke tests on staging before traffic switch

## CI/CD Platform
- GitHub Actions (.github/workflows/)
- Workflows: ci.yml, deploy-dev.yml, deploy-prod.yml

## Environments
- Development (dev): shared Cosmos (400 RU/s), consumption Functions
- Production (prod): dedicated Cosmos (1000 RU/s), premium Functions

## Promotion Flow
Developer Push → CI → Auto Deploy to Dev → Manual Testing → Approval → Deploy to Prod (slot) → Smoke Tests → Traffic Switch

## Rollback Strategy
- Primary: Slot swap
- Triggers: Failed smoke tests, error rate > 5% in 15m, manual command
- RTO: ~2 minutes
