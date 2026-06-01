---
name: mqtt-protocol
description: MQTT protocol configuration for CSPH GPL tracking. Use when setting up MQTT topics, message formats, QoS, or broker configuration. Triggers: MQTT, topic, publish, subscribe, QoS, broker, message format, payload.
---

# MQTT Protocol - CSPH GPL Tracking

## Overview

MQTT is the messaging protocol for IoT device communication with the cloud platform.

## Broker Configuration

### Connection Parameters

| Parameter | Value |
|-----------|-------|
| Broker | mqtt.ciph.cm |
| Port (TLS) | 8883 |
| Port (Plain) | 1883 |
| Protocol | MQTT v3.1.1 / v5.0 |
| Keep Alive | 60 seconds |
| Clean Session | true |
| Client ID Format | `truck_{truck_id}` or `pda_{device_id}` |

### Authentication

```javascript
// Device authentication
{
  "username": "truck_TRK-001",
  "password": "<device_secret>",
  "clientId": "truck_TRK-001"
}
```

## Topic Structure

### Hierarchy

```
cspH/
├── {truck_id}/
│   ├── sensors/
│   │   ├── level          # Fuel level readings
│   │   ├── temperature    # Temperature readings
│   │   └── pressure       # Pressure readings
│   ├── gps/
│   │   └── position       # GPS coordinates
│   ├── status/
│   │   ├── heartbeat      # Device health
│   │   └── connectivity   # Connection status
│   ├── alerts/
│   │   └── critical       # High-priority alerts
│   └── config/
│       └── update         # Configuration updates
└── system/
    ├── devices/
    │   └── online         # All online devices
    └── alerts/
        └── broadcast      # System-wide alerts
```

### Topic Definitions

| Topic Pattern | QoS | Retained | Description |
|---------------|-----|----------|-------------|
| `cspH/{truck_id}/sensors/level` | 1 | No | Level sensor data |
| `cspH/{truck_id}/sensors/temperature` | 1 | No | Temperature data |
| `cspH/{truck_id}/gps/position` | 1 | No | GPS position |
| `cspH/{truck_id}/status/heartbeat` | 1 | Yes | Device status |
| `cspH/{truck_id}/alerts/critical` | 2 | No | Critical alerts |
| `cspH/system/devices/online` | 1 | Yes | Online devices |

## Message Formats

### 1. Sensor Reading (Level)

```json
{
  "truck_id": "TRK-001",
  "sensor": "level",
  "value": 78.5,
  "unit": "%",
  "raw_ma": 16.52,
  "calibration": {
    "empty_ma": 4.0,
    "full_ma": 20.0
  },
  "timestamp": "2026-01-15T10:30:00Z",
  "sequence": 12345
}
```

### 2. GPS Position

```json
{
  "truck_id": "TRK-001",
  "position": {
    "lat": 3.8480000,
    "lng": 11.5020000,
    "altitude": 732.5,
    "speed": 45.2,
    "heading": 180,
    "satellites": 8,
    "hdop": 1.2,
    "fix_quality": "GPS"
  },
  "timestamp": "2026-01-15T10:30:00Z",
  "sequence": 12345
}
```

### 3. Aggregated Frame (30-second interval)

```json
{
  "truck_id": "TRK-001",
  "timestamp": "2026-01-15T10:30:00Z",
  "sequence": 12345,
  "position": {
    "lat": 3.8480000,
    "lng": 11.5020000,
    "speed": 45.2,
    "heading": 180
  },
  "sensors": {
    "level": {
      "value": 78.5,
      "unit": "%",
      "liters": 39250
    },
    "temperature": {
      "value": 28.3,
      "unit": "C"
    },
    "pressure": {
      "value": 8.2,
      "unit": "bar"
    }
  },
  "status": {
    "battery_voltage": 12.4,
    "signal_strength": -65,
    "uptime_hours": 48.5
  }
}
```

### 4. Critical Alert

```json
{
  "truck_id": "TRK-001",
  "alert_type": "leak_detected",
  "severity": "critical",
  "message": "Potential leak detected - pressure drop > 2 bar/min",
  "sensor_data": {
    "pressure_before": 8.5,
    "pressure_after": 6.2,
    "drop_rate": 2.3
  },
  "position": {
    "lat": 3.8480000,
    "lng": 11.5020000
  },
  "timestamp": "2026-01-15T10:30:00Z",
  "requires_ack": true
}
```

### 5. Device Heartbeat

```json
{
  "truck_id": "TRK-001",
  "status": "online",
  "uptime_seconds": 174600,
  "battery_voltage": 12.4,
  "signal_dbm": -65,
  "signal_percent": 75,
  "storage_free_mb": 1024,
  "last_sensor_read": "2026-01-15T10:29:50Z",
  "firmware_version": "1.2.3",
  "timestamp": "2026-01-15T10:30:00Z"
}
```

### 6. Batch Upload (Store & Forward)

```json
{
  "type": "batch",
  "truck_id": "TRK-001",
  "frame_count": 120,
  "frames": [
    {
      "timestamp": "2026-01-15T09:00:00Z",
      "position": {"lat": 3.85, "lng": 11.50},
      "sensors": {"level": 80.0}
    },
    {
      "timestamp": "2026-01-15T09:00:30Z",
      "position": {"lat": 3.85, "lng": 11.50},
      "sensors": {"level": 79.8}
    }
  ],
  "upload_timestamp": "2026-01-15T10:30:00Z"
}
```

## QoS Levels

| QoS | Use Case | Description |
|-----|----------|-------------|
| 0 | Status updates | Fire and forget |
| 1 | Sensor data | At least once delivery |
| 2 | Critical alerts | Exactly once delivery |

### Recommendations

- **QoS 0:** Heartbeat, status (can miss if offline)
- **QoS 1:** GPS, sensor readings (most data)
- **QoS 2:** Critical alerts (leak, pressure, emergency)

## Retained Messages

### Status Topics (Retained = true)

```json
// Last known status persists
{
  "truck_id": "TRK-001",
  "status": "online",
  "timestamp": "2026-01-15T10:30:00Z"
}
```

### Data Topics (Retained = false)

```json
// Real-time data, no retention
{
  "truck_id": "TRK-001",
  "value": 78.5,
  "timestamp": "2026-01-15T10:30:00Z"
}
```

## Wildcard Subscriptions

### Monitor All Trucks

```javascript
// Subscribe to all sensor data
client.subscribe('cspH/+/sensors/#');

// Subscribe to all GPS positions
client.subscribe('cspH/+/gps/#');

// Subscribe to all alerts
client.subscribe('cspH/+/alerts/#');
```

### Monitor Single Truck

```javascript
// Subscribe to everything for one truck
client.subscribe('cspH/TRK-001/#');
```

## Security

### TLS Configuration

```javascript
{
  "broker": "mqtt.ciph.cm",
  "port": 8883,
  "tls": {
    "ca": "/certs/ca.crt",
    "cert": "/certs/client.crt",
    "key": "/certs/client.key"
  }
}
```

### Access Control

| Topic Pattern | Publish | Subscribe |
|---------------|---------|-----------|
| `cspH/{truck_id}/#` | Device only | Dispatcher, Admin |
| `cspH/system/#` | Admin only | All authenticated |
| `cspH/{truck_id}/config/#` | Admin only | Device only |

## Error Handling

### Connection Lost

```javascript
client.on('offline', function() {
  console.log('MQTT offline - switching to store & forward');
  flow.set('mqttConnected', false);
});

client.on('reconnect', function() {
  console.log('MQTT reconnecting...');
});
```

### Message Expiry

```javascript
// Set message expiry (MQTT 5.0 only)
{
  "payloadFormatIndicator": 1,
  "messageExpiryInterval": 300, // 5 minutes
  "responseTopic": "cspH/TRK-001/response"
}
```

## Performance Metrics

| Metric | Target |
|--------|--------|
| Message latency (4G) | < 2 seconds |
| Message latency (satellite) | < 10 seconds |
| Message size (normal) | ~500 bytes |
| Message size (batch) | ~50KB max |
| Messages per second (fleet) | ~17 (500 trucks / 30s) |
| Bandwidth per truck | ~1 KB/min |
