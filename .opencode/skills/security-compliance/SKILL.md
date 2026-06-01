---
name: security-compliance
description: Security and compliance for CSPH GPL tracking. Use when working with ATEX certification, ADR regulations, data encryption, or access control. Triggers: security, ATEX, ADR, encryption, authentication, compliance, certification, safety, regulatory.
---

# Security & Compliance - CSPH GPL Tracking

## Overview

Security measures and regulatory compliance for tracking LPG tank trucks and bottles.

## Regulatory Framework

### ATEX Certification

**ATmosphères EXplosibles** - EU directive for equipment in explosive atmospheres.

| Zone | Description | Equipment Class |
|------|-------------|-----------------|
| Zone 0 | Continuous explosive atmosphere | Ex ia (Intrinsically Safe) |
| Zone 1 | Occasional explosive atmosphere | Ex ib or Ex d (Flameproof) |
| Zone 2 | Abnormal conditions only | Ex nA (Non-sparking) |

### ADR Compliance

**Accord Européen relatif au transport international des marchandises Dangereuses par Route**

| Requirement | Implementation |
|-------------|----------------|
| No uncertified electronics in Zone 1/2 | IoT device in safe zone only |
| Cable glands ATEX certified | Flameproof or increased safety |
| Max voltage in danger zone | 12V DC |
| Intrinsically safe barriers | For sensor connections |
| Equipment marking | CE + Ex marking required |

### CEMAC Adoption

Cameroon adopted ADR in March 1999 via CEMAC regulation.

## Physical Security

### Installation Zones

```
Tank Truck Side View
┌──────────────────────────────────────────────┐
│                                              │
│  ╔══════════════════════════════════════════╗ │
│  ║  DANGER ZONE (Zone 1/2)                 ║ │
│  ║  - Vents, valves, fittings              ║ │
│  ║  - No non-certified electronics         ║ │
│  ║  - ATEX barriers for sensor cables      ║ │
│  ╚══════════════════════════════════════════╝ │
│                                              │
│  ╔══════════════════════════════════════════╗ │
│  ║  SAFE ZONE (Chassis)                    ║ │
│  ║  - ADAM-6717 gateway                    ║ │
│  ║  - Teltonika RUT241 router              ║ │
│  ║  - Battery and power supply             ║ │
│  ║  - GPS antenna                          ║ │
│  ╚══════════════════════════════════════════╝ │
│                                              │
└──────────────────────────────────────────────┘
```

### Tamper Protection

| Feature | Implementation |
|---------|----------------|
| Tamper-evident seal | On enclosure opening |
| Tamper detection | Magnetic reed switch |
| Alert on tamper | Immediate MQTT alert |
| Tamper log | Immutable audit trail |

```json
// Tamper alert payload
{
  "truck_id": "TRK-001",
  "alert_type": "tamper_detected",
  "severity": "critical",
  "details": {
    "enclosure_id": "ENC-001",
    "tamper_type": "opening_detected",
    "seal_status": "broken"
  },
  "timestamp": "2026-01-15T10:30:00Z",
  "requires_ack": true
}
```

## Data Security

### Encryption

| Layer | Protocol | Key Management |
|-------|----------|----------------|
| Transport | TLS 1.3 | Certificate-based |
| MQTT | TLS 8883 | Device certificates |
| Storage | AES-256 | Key vault |
| Backup | AES-256 | Offline keys |

### Authentication

| Method | Use Case |
|--------|----------|
| Device Certificate | IoT device → MQTT broker |
| JWT Token | API → Dashboard |
| API Key | External integrations |
| mTLS | Server-to-server |

### Access Control Matrix

| Resource | Chauffeur | Dispatcher | Admin |
|----------|-----------|------------|-------|
| Own truck data | Read | Read | Read/Write |
| All truck data | - | Read | Read/Write |
| Alerts | Read (own) | Read/All | Read/All/Ack |
| Configuration | - | - | Read/Write |
| User management | - | - | Read/Write |

## Audit Trail

### Logged Events

| Event | Data Captured |
|-------|---------------|
| Tamper detection | Device ID, timestamp, location |
| Configuration change | Old/new values, user, timestamp |
| Alert acknowledgment | User, note, timestamp |
| Data export | User, date range, format |
| Login/logout | User, IP, timestamp |

### Audit Log Schema

```sql
CREATE TABLE audit_logs (
  id BIGSERIAL PRIMARY KEY,
  event_type VARCHAR(50) NOT NULL,
  entity_type VARCHAR(50),
  entity_id UUID,
  user_id UUID,
  old_values JSONB,
  new_values JSONB,
  ip_address INET,
  user_agent TEXT,
  timestamp TIMESTAMP DEFAULT NOW()
);

-- Immutable append-only
CREATE RULE audit_no_update AS ON UPDATE TO audit_logs DO INSTEAD NOTHING;
CREATE RULE audit_no_delete AS ON DELETE TO audit_logs DO INSTEAD NOTHING;
```

## Intrusion Detection

### Physical Sensors

| Sensor | Location | Trigger |
|--------|----------|---------|
| Magnetic reed switch | Enclosure | Opening |
| Accelerometer | Chassis | Impact/vibration |
| Temperature sensor | Inside enclosure | Fire/heat |
| Light sensor | Inside enclosure | Opening in dark |

### Network Security

| Measure | Implementation |
|---------|----------------|
| Firewall | Allow only MQTT/API |
| IDS | Monitor for anomalies |
| Rate limiting | 1000 req/min per device |
| Geo-fencing | Alert on unexpected locations |

## Data Integrity

### Checksum Verification

```javascript
// Frame integrity check
function verifyFrame(frame) {
  const payload = JSON.stringify(frame.data);
  const calculated = crypto
    .createHmac('sha256', deviceSecret)
    .update(payload)
    .digest('hex');
  
  return calculated === frame.signature;
}
```

### Immutable Data

```sql
-- Blockchain-like hash chain
CREATE TABLE data_chain (
  id BIGSERIAL PRIMARY KEY,
  frame_id UUID,
  data_hash VARCHAR(64),
  previous_hash VARCHAR(64),
  created_at TIMESTAMP DEFAULT NOW()
);

-- Verify chain integrity
SELECT verify_chain_integrity();
```

## Compliance Reports

### Monthly Reports

```json
{
  "report_type": "monthly_compliance",
  "period": "2026-01",
  "fleet_size": 500,
  "metrics": {
    "data_completeness": 99.2,
    "tamper_incidents": 0,
    "alert_response_time_avg": 45,
    "system_availability": 99.7
  },
  "compliance_status": "COMPLIANT"
}
```

### Audit Export

```bash
# Export audit logs for period
curl -X GET "https://api.ciph.cm/v1/audit/export?from=2026-01-01&to=2026-01-31" \
  -H "Authorization: Bearer <token>" \
  -o audit_2026_01.json
```

## Emergency Procedures

### Gas Leak Detection

```
1. Alert triggered (pressure drop > 2 bar/min)
2. Immediate MQTT message (QoS 2)
3. Dispatcher notified (< 30 seconds)
4. Emergency services contacted
5. Truck location shared with responders
6. Incident logged immutably
```

### Device Compromise

```
1. Tamper alert triggered
2. Device quarantined (stop data transmission)
3. Admin notified immediately
4. Physical inspection scheduled
5. Device re-certified or replaced
6. Incident documented
```

## Security Metrics

| Metric | Target |
|--------|--------|
| Tamper detection rate | 100% |
| Alert response time | < 30 seconds |
| Data encryption | 100% (at rest + in transit) |
| Audit log completeness | 100% |
| System availability | > 99.5% |
| Failed authentication attempts | < 10/day |
