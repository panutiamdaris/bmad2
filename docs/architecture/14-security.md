[← Back to Index](./README.md)

# Security

## Input Validation
- Library: Zod 3.22.4 at API boundary before processing
- Whitelist approach; reject invalid input with 400

## Authentication & Authorization
- Azure AD B2C with JWT tokens
- Stateless sessions (1h access, 7d refresh)
- Validate JWT on all protected endpoints; userId in claims; enforce userId filter on queries

## Secrets Management
- Dev: .env (gitignored)
- Prod: Azure Key Vault with managed identities
- Never hardcode secrets; use process.env; no secrets in logs; rotate quarterly

## API Security
- Rate Limiting: APIM policies (auth 100 req/min, unauth 20 req/min, uploads 10/min)
- CORS: Whitelist specific origins
- Security Headers: HSTS, X-Content-Type-Options, X-Frame-Options, CSP
- HTTPS enforced in production

## Data Protection
- Encryption at Rest: Cosmos (MMK), Blob (AES-256), IndexedDB (browser-level)
- TLS 1.2+ in transit
- PII Handling: VIN/license optional; email only for auth; GDPR export/delete supported
- Logging: never log passwords/tokens; sanitize VIN/plates; emails only for auth events

## Dependency Security
- Tools: npm audit + Dependabot; patch highs/criticals within 7 days

## Security Testing
- SAST: ESLint security plugins
- Pre-Deployment Checks: no hardcoded secrets, auth on endpoints, correct CORS, rate limiting active
