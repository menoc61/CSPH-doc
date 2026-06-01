---
name: api-design-ciph
description: API design for CSPH GPL tracking platform. Use when designing REST endpoints, WebSocket streams, authentication, or data ingestion. Triggers: API, endpoint, REST, WebSocket, route, authentication, ingestion, tracking, alerts, fleet.
---

# API Design - CSPH GPL Tracking Platform

## Overview

RESTful API design for the cloud platform receiving IoT data and serving the dashboard.

## Base Configuration

```yaml
Base URL: https://api.ciph.cm/v1
Authentication: Bearer JWT
Content-Type: application/json
Rate Limit: 1000 req/min per client
```

## Endpoint Categories

### 1. Data Ingestion (IoT Devices)

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/v1/ingest/frame` | Single sensor frame | Device Token |
| POST | `/v1/ingest/batch` | Batch of frames (max 500) | Device Token |

### 2. Real-time Tracking

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/v1/tracking/live` | Last position of all trucks | dispatcher, admin |
| GET | `/v1/tracking/:truck_id` | Position history | dispatcher, admin |
| WS | `/v1/tracking/stream` | WebSocket position feed | dispatcher, admin |

### 3. Fleet Management

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/v1/trucks` | List all trucks | dispatcher, admin |
| GET | `/v1/trucks/:id` | Truck details + last sensors | dispatcher, admin |
| PATCH | `/v1/trucks/:id` | Update truck status | admin |
| GET | `/v1/trucks/:id/sensors` | Sensor history | dispatcher, admin |

### 4. Alerts

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/v1/alerts` | List alerts with filters | dispatcher, admin |
| GET | `/v1/alerts/:id` | Alert details | dispatcher, admin |
| POST | `/v1/alerts/:id/ack` | Acknowledge alert | dispatcher, admin |

### 5. Route Planning

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/v1/trips` | List trips | dispatcher, admin |
| POST | `/v1/trips` | Create trip | dispatcher |
| GET | `/v1/trips/:id` | Trip details | all |
| GET | `/v1/trips/:id/replay` | Replay GPS trace | dispatcher, admin |

### 6. Administration

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/v1/health` | System health | system |
| GET | `/v1/metrics` | System metrics | admin |
| GET | `/v1/warehouses` | List warehouses | admin |
| GET | `/v1/warehouses/:id/trucks` | Trucks at warehouse | dispatcher, admin |

## Request/Response Examples

### POST /v1/ingest/frame

```json
// Request
{
  "truck_id": "TRK-001",
  "lat": 3.848,
  "lng": 11.502,
  "speed": 45.2,
  "canal": 0,
  "sensors": [
    {"name": "level", "value": 78.5, "unit": "%"},
    {"name": "temperature", "value": 28.3, "unit": "C"}
  ],
  "ts": "2026-01-15T10:30:00Z"
}

// Response 202 Accepted
{
  "status": "accepted",
  "frame_id": "f_abc123",
  "processed_at": "2026-01-15T10:30:01Z"
}
```

### GET /v1/tracking/live

```json
// Response 200 OK
{
  "trucks": [
    {
      "truck_id": "TRK-001",
      "status": "moving",
      "position": {
        "lat": 3.848,
        "lng": 11.502,
        "speed": 45.2,
        "heading": 180,
        "timestamp": "2026-01-15T10:30:00Z"
      },
      "last_alert": null,
      "warehouse_id": "DEP-001"
    }
  ],
  "count": 1,
  "filters": {}
}
```

## WebSocket Protocol

### Connection

```javascript
const ws = new WebSocket('wss://api.ciph.cm/v1/tracking/stream', {
  headers: { Authorization: 'Bearer <token>' }
});
```

### Events

```json
// Position Update
{
  "event": "position_update",
  "truck_id": "TRK-001",
  "data": { "lat": 3.848, "lng": 11.502, "speed": 45.2 }
}

// Alert Triggered
{
  "event": "alert_triggered",
  "truck_id": "TRK-001",
  "data": { "type": "deviation", "severity": "high" }
}

// Truck Offline
{
  "event": "truck_offline",
  "truck_id": "TRK-001",
  "data": { "last_seen": "2026-01-15T10:25:00Z" }
}
```

### Ping/Pong

- Server sends ping every 30 seconds
- Client must respond with pong
- Connection closed if no pong within 10 seconds

## Error Responses

```json
// 400 Bad Request
{
  "error": "validation_error",
  "message": "Invalid truck_id format",
  "details": [
    {"field": "truck_id", "issue": "must match pattern TRK-XXX"}
  ]
}

// 401 Unauthorized
{
  "error": "authentication_error",
  "message": "Invalid or expired token"
}

// 429 Rate Limited
{
  "error": "rate_limit_exceeded",
  "message": "Too many requests",
  "retry_after": 60
}
```

## Database Schema

### Core Tables

```sql
-- Trucks
CREATE TABLE trucks (
  id UUID PRIMARY KEY,
  truck_id VARCHAR(10) UNIQUE NOT NULL,
  capacity_liters INTEGER,
  warehouse_id UUID REFERENCES warehouses(id),
  status VARCHAR(20) DEFAULT 'active',
  created_at TIMESTAMP DEFAULT NOW()
);

-- Positions
CREATE TABLE positions (
  id BIGSERIAL PRIMARY KEY,
  truck_id VARCHAR(10) REFERENCES trucks(truck_id),
  lat DECIMAL(10,7),
  lng DECIMAL(10,7),
  speed DECIMAL(5,2),
  heading INTEGER,
  timestamp TIMESTAMP NOT NULL
);

-- Sensors
CREATE TABLE sensor_readings (
  id BIGSERIAL PRIMARY KEY,
  truck_id VARCHAR(10) REFERENCES trucks(truck_id),
  sensor_name VARCHAR(50),
  value DECIMAL(10,3),
  unit VARCHAR(20),
  timestamp TIMESTAMP NOT NULL
);

-- Alerts
CREATE TABLE alerts (
  id UUID PRIMARY KEY,
  truck_id VARCHAR(10) REFERENCES trucks(truck_id),
  type VARCHAR(50),
  severity VARCHAR(20),
  message TEXT,
  acknowledged BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW()
);
```

## Performance Targets

| Metric | Target |
|--------|--------|
| API response time (p95) | <200ms |
| WebSocket latency (4G) | <2s |
| WebSocket latency (satellite) | <10s |
| Ingestion throughput | 500 frames/sec |
| Data retention | 12 months |
