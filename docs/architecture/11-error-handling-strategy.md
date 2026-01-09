[← Back to Index](./README.md)

# Error Handling Strategy

## General Approach
- Error Model: Structured objects with codes, messages, context
- Exception Hierarchy: Custom errors extend `AppError`
- Propagation: Bubble up with context enrichment
- Principles: Fail fast, preserve context, user-friendly messages, security, observability

## Logging Standards
- Library: Winston (backend), structured console (frontend)
- Format: JSON logs; Levels: ERROR, WARN, INFO, DEBUG
- Required Context: Correlation ID, User Context, Service Context, Timestamp, Environment

## Error Handling Patterns
### External API Errors
- Retry: Exponential backoff (3 retries, base 1s)
- Circuit Breaker: Open after 5 consecutive failures, 1m cooldown
- Timeouts: API 30s; Sync 2m; Uploads 5m; Reports 3m
- Translation: Map to user-friendly messages

### Business Logic Errors
- Custom Exceptions: VehicleNotFoundError, InvalidOdometerError, SyncConflictError, AuthenticationError
- UX: Actionable user-facing messages

### Data Consistency
- Transactions: Cosmos DB batch operations
- Compensation: Roll back on partial failures
- Idempotency: Operation IDs for dedupe

## Security Considerations
- Never log: passwords, tokens, full PAN, PII without consent, VIN/license plates (except targeted debugging)
- Sanitize: Remove sensitive data from messages

## Monitoring and Alerting
- Application Insights: error tracking, custom metrics, distributed tracing
- Alerts: Error rate > 5%, Sync failure > 10%, p95 latency > 2s
