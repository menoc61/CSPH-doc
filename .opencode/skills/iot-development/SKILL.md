---
name: iot-development
description: IoT development for CSPH GPL tracking system. Use when working with ADAM-6717, Node-RED flows, MQTT protocols, sensor integration, or embedded firmware. Triggers: ADAM, Node-RED, MQTT, sensor, GPS, 4G, firmware, embedded, IoT, capteur, prototype.
---

# IoT Development - CSPH GPL Tracking

## Overview

Development guidelines for the IoT prototype embedded on LPG tank trucks.

## Hardware Stack

### Primary Components

| Component | Model | Role |
|-----------|-------|------|
| Gateway | Advantech ADAM-6717 | Data acquisition + local processing |
| Router | Teltonika RUT241 | 4G/LTE connectivity |
| Sensor | Rochester Hall Effect | Level measurement (4-20mA) |
| GPS | Integrated module | Position tracking |

### ADAM-6717 Specifications

- **I/O:** 8 channels (5 digital inputs, 3 analog inputs)
- **Protocols:** Modbus TCP/RTU, MQTT
- **Programming:** Node-RED (native support)
- **Storage:** Micro-SD for offline buffering
- **Temperature:** -40°C to +70°C operating

## Node-RED Development

### Flow Structure

```
[Hall Sensor] → [Modbus Read] → [JSON Format] → [MQTT Publish]
     ↓
[GPS Module] → [NMEA Parse] → [Position Update] → [MQTT Publish]
```

### MQTT Topics

```mqtt
cspH/{truck_id}/sensors/level      # Volume readings
cspH/{truck_id}/sensors/temperature # Temperature
cspH/{truck_id}/gps/position       # Lat/Lng/Speed
cspH/{truck_id}/status             # Device health
cspH/{truck_id}/alerts             # Critical alerts
```

### Data Format (JSON)

```json
{
  "truck_id": "TRK-001",
  "timestamp": "2026-01-15T10:30:00Z",
  "position": {
    "lat": 3.848,
    "lng": 11.502,
    "speed": 45.2,
    "heading": 180
  },
  "sensors": {
    "level_percent": 78.5,
    "level_liters": 39250,
    "temperature_c": 28.3,
    "pressure_bar": 8.2
  }
}
```

## Modbus Configuration

### Hall Sensor Registers

| Register | Address | Type | Description |
|----------|---------|------|-------------|
| Level | 40001 | Float32 | Level percentage (0-100%) |
| Raw | 40003 | Float32 | Raw mA signal (4-20mA) |
| Status | 40005 | UInt16 | Sensor status flags |

### GPS Module Registers

| Register | Address | Type | Description |
|----------|---------|------|-------------|
| Latitude | 40010 | Float32 | Decimal degrees |
| Longitude | 40012 | Float32 | Decimal degrees |
| Speed | 40014 | Float32 | km/h |
| Fix | 40016 | UInt16 | 0=no fix, 1=GPS, 2=DGPS |

## Offline Buffering (Store & Forward)

### Buffer Strategy

1. **Local Storage:** Micro-SD card on ADAM-6717
2. **Max Capacity:** 72 hours of data (~5000 frames)
3. **Priority Queue:** Alerts sent first when connectivity restored
4. **Deduplication:** Timestamp-based, no duplicate frames

### Reconnection Flow

```
[Offline] → [Buffer Write] → [4G Restore] → [Priority Send] → [Backfill] → [Resume Normal]
```

## Testing

### Unit Tests

```bash
npm run test:sensors    # Sensor data validation
npm run test:mqtt       # MQTT message format
npm run test:modbus     # Modbus register mapping
```

### Integration Tests

```bash
npm run test:device     # Full device simulation
npm run test:offline    # Store & forward scenarios
npm run test:alerts     # Alert generation logic
```

## Safety Considerations

### ATEX Zone Compliance

- **Zone 1/2:** No non-certified electronics
- **Cable routing:** Through certified cable glands
- **Power limits:** 12V DC max in hazardous areas
- **Intrinsically safe barriers:** Required for sensor connections

### Mechanical Installation

- **Mounting:** 4-point bolt on chassis
- **Vibration isolation:** Silent blocks required
- **Cable protection:** Braided loom + cable ties
- **Sealing:** IP67 minimum for enclosure
