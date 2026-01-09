[← Back to Index](./README.md)

# External APIs

## Current MVP: No External APIs Required

Core features operate entirely on local and Azure infrastructure without third-party dependencies.

Rationale:
- Privacy-first approach minimizes data sharing with external services
- Offline-first architecture cannot depend on external API availability
- Cost control during MVP phase
- Simplifies GDPR compliance

## Future Integration: OBD-II Device APIs (Post-MVP)

Purpose: Automatic vehicle data capture (mileage, diagnostics, fuel, alerts).

Documentation:
- Web Bluetooth API: https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API
- Protocols: ISO 15031-5, ISO 14229, ISO 15765-4

Authentication: None (direct device connection with user permission)

Rate Limits: Device-dependent (poll every 100–500ms)

Key Endpoints Used (PIDs): 0x01 0x0C (RPM), 0x01 0x0D (Speed), 0x01 0x2F (Fuel Level), 0x01 0x31 (Distance), 0x01 0x03 (DTCs), 0x09 0x02 (VIN)

Integration Notes:
- Requires HTTPS and explicit user permission
- Chrome/Edge supported; Safari/Firefox limited
- Data stays local unless user enables cloud sync
- Automations: fuel entry creation, reminders via DTCs
- Manual entry always available
