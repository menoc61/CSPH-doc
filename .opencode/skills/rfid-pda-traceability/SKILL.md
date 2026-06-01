---
name: rfid-pda-traceability
description: RFID and PDA traceability system for CSPH 50kg bottle tracking. Use when working with RFID tags, PDA terminals, bottle lifecycle, or delivery recording. Triggers: RFID, PDA, bottle, bouteille, tag, scan, delivery, livraison, EPC, UHF.
---

# RFID/PDA Traceability - Bottle Tracking

## Overview

System for tracking 50kg LPG bottles using UHF RFID tags and 4G PDA terminals.

## System Components

### RFID Tags

| Parameter | Specification |
|-----------|---------------|
| Type | Passive UHF RFID |
| Standard | EPC Gen2 (ISO 18000-63) |
| Frequency | 860-960 MHz |
| Read Range | 1-3 meters |
| Memory | 96 bits EPC, 512 bits user |
| Encapsulation | Rugged, weather-resistant |
| Application | Adhesive or mechanical mount |

### Tag Data Structure

```
EPC (96 bits):
┌─────────────┬──────────────┬─────────────┐
│ Header (8)  │ Company (24) │ Item (36)   │
│             │              │             │
│ 0x30        │ CSPH (0xXX)  │ Serial (XX) │
└─────────────┴──────────────┴─────────────┘
│ Check (4)   │ Reserved (24)              │
└─────────────┴────────────────────────────┘
```

### PDA Terminal

| Parameter | Specification |
|-----------|---------------|
| Display | 5.5" HD touchscreen |
| OS | Android 12+ |
| RFID | Built-in UHF reader |
| Connectivity | 4G LTE, WiFi, Bluetooth |
| Battery | 5000mAh, 8h continuous |
| Ruggedness | IP67, 1.5m drop |
| Camera | 13MP (optional barcode) |

## Bottle Lifecycle

### States

```
[Filled] → [In Depot] → [In Transit] → [Delivered] → [Empty] → [Returned] → [Refilled]
```

### Data Model

```sql
-- Bottle master record
CREATE TABLE bottles (
  id UUID PRIMARY KEY,
  rfid_epc VARCHAR(24) UNIQUE NOT NULL,
  serial_number VARCHAR(20) UNIQUE NOT NULL,
  capacity_kg DECIMAL(5,2) DEFAULT 50.00,
  manufacture_date DATE,
  last_inspection DATE,
  status VARCHAR(20) DEFAULT 'active',
  current_depot_id UUID,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Delivery transactions
CREATE TABLE deliveries (
  id UUID PRIMARY KEY,
  bottle_id UUID REFERENCES bottles(id),
  pda_device_id VARCHAR(50),
  driver_id UUID,
  client_id UUID,
  depot_id UUID,
  delivery_type VARCHAR(20), -- 'delivery' or 'return'
  timestamp TIMESTAMP NOT NULL,
  gps_lat DECIMAL(10,7),
  gps_lng DECIMAL(10,7),
  scanned_by VARCHAR(100),
  created_at TIMESTAMP DEFAULT NOW()
);

-- Bottle status history
CREATE TABLE bottle_status_history (
  id BIGSERIAL PRIMARY KEY,
  bottle_id UUID REFERENCES bottles(id),
  status VARCHAR(20),
  changed_at TIMESTAMP DEFAULT NOW(),
  changed_by VARCHAR(100),
  notes TEXT
);
```

## PDA Application Flow

### 1. Delivery Scan

```
┌─────────────────────────────────────────┐
│  DELIVERY SCAN                          │
├─────────────────────────────────────────┤
│  Client: [Camerotel Restaurant    ▼]    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │  SCANNED: 12 bottles            │    │
│  │                                 │    │
│  │  EPC: E200 3411 B801 0160 1234 │    │
│  │  Serial: GPL-2024-001234        │    │
│  │  Status: OK ✓                   │    │
│  └─────────────────────────────────┘    │
│                                         │
│  [Scan Bottle]     [Complete Delivery]  │
└─────────────────────────────────────────┘
```

### 2. Return Scan

```
┌─────────────────────────────────────────┐
│  RETURN SCAN                            │
├─────────────────────────────────────────┤
│  Client: [Camerotel Restaurant    ▼]    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │  RETURNS: 8 empty bottles       │    │
│  │                                 │    │
│  │  EPC: E200 3411 B801 0160 5678 │    │
│  │  Serial: GPL-2024-005678        │    │
│  │  Condition: Good ✓              │    │
│  └─────────────────────────────────┘    │
│                                         │
│  [Scan Return]    [Complete Return]     │
└─────────────────────────────────────────┘
```

## RFID Tag Encoding

### EPC Format for CSPH

```
Prefix: E200 (standard)
Company: 3411 (CSPH assigned)
Product: B8 (50kg bottle)
Serial: 01 0160 XXXX (unique per bottle)
```

### Write Tag Data

```javascript
// PDA RFID writer function
async function writeTag(epc, bottleData) {
  const tagData = {
    epc: epc,
    userMemory: {
      serial: bottleData.serialNumber,
      capacity: bottleData.capacityKg,
      manufactureDate: bottleData.manufactureDate,
      lastInspection: bottleData.lastInspection
    }
  };
  
  await rfidWriter.writeTag(tagData);
  return tagData;
}
```

## Delivery Recording API

### POST /v1/deliveries

```json
// Request
{
  "pda_device_id": "PDA-001",
  "driver_id": "DRV-001",
  "client_id": "CLI-001",
  "depot_id": "DEP-001",
  "delivery_type": "delivery",
  "bottles": [
    {
      "rfid_epc": "E2003411B80101601234",
      "serial_number": "GPL-2024-001234",
      "status": "filled"
    },
    {
      "rfid_epc": "E2003411B80101601235",
      "serial_number": "GPL-2024-001235",
      "status": "filled"
    }
  ],
  "position": {
    "lat": 3.8480000,
    "lng": 11.5020000
  },
  "timestamp": "2026-01-15T10:30:00Z"
}

// Response 201 Created
{
  "delivery_id": "DEL-2026-001234",
  "status": "recorded",
  "bottle_count": 2,
  "verified": true
}
```

### POST /v1/deliveries/verify

```json
// Request
{
  "delivery_id": "DEL-2026-001234",
  "bottle_epcs": [
    "E2003411B80101601234",
    "E2003411B80101601235"
  ]
}

// Response 200 OK
{
  "verified": true,
  "all_bottles_found": true,
  "status_matches": true
}
```

## Anomaly Detection

### Rules

| Rule | Description | Severity |
|------|-------------|----------|
| Duplicate Scan | Same bottle scanned twice in 24h | Warning |
| Wrong Location | Bottle scanned far from last location | Alert |
| Invalid State | Empty bottle marked as filled | Error |
| Missing Return | Bottle delivered but not returned in 30 days | Warning |
| Unknown Tag | RFID tag not in system | Alert |

### Alert Example

```json
{
  "alert_type": "duplicate_scan",
  "severity": "warning",
  "bottle_id": "GPL-2024-001234",
  "rfid_epc": "E2003411B80101601234",
  "first_scan": "2026-01-15T09:00:00Z",
  "second_scan": "2026-01-15T10:30:00Z",
  "location_first": {"lat": 3.85, "lng": 11.50},
  "location_second": {"lat": 3.86, "lng": 11.51}
}
```

## Offline Support

### Local Storage on PDA

```javascript
// Store delivery locally when offline
async function storeDelivery(delivery) {
  const db = await openDatabase('ciph_deliveries');
  await db.put('deliveries', {
    ...delivery,
    stored_offline: true,
    synced: false
  });
}

// Sync when back online
async function syncDeliveries() {
  const db = await openDatabase('ciph_deliveries');
  const pending = await db.getAllFromIndex('deliveries', 'synced', false);
  
  for (const delivery of pending) {
    try {
      await api.post('/v1/deliveries', delivery);
      await db.put('deliveries', { ...delivery, synced: true });
    } catch (error) {
      console.error('Sync failed:', error);
    }
  }
}
```

## Performance Metrics

| Metric | Target |
|--------|--------|
| RFID read time | < 1 second |
| PDA scan to server | < 5 seconds |
| Offline storage capacity | 1000 deliveries |
| Battery life (PDA) | 8 hours continuous |
| Read accuracy | > 99.5% |
| Batch scan rate | 20 bottles/minute |
