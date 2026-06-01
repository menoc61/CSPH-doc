---
name: capacity-planning
description: Capacity planning and dimensioning for CSPH GPL tracking system. Use when calculating storage, bandwidth, server resources, or scaling requirements. Triggers: capacity, dimensioning, scaling, storage, bandwidth, performance, calculation, fleet size.
---

# Capacity Planning - CSPH GPL Tracking

## Overview

Dimensioning calculations for the entire tracking system based on fleet size and operational parameters.

## Fleet Parameters

### Current Scale

| Parameter | Value |
|-----------|-------|
| Active tank trucks | ~500 |
| Depot locations | Multiple (Bipaga, etc.) |
| Operating hours | 24/7 |
| Average trips per day | 2-3 per truck |

### Assumptions

| Parameter | Value | Source |
|-----------|-------|--------|
| Trucks active | 500 | SNH/SCDP data |
| 4G frame interval | 30 seconds | Specification |
| Satellite frame interval | 5 minutes | Cost optimization |
| Satellite coverage | 15-20% of transit time | Route analysis |
| Sensor read interval | 10 seconds | Local processing |
| Data retention | 12 months | Regulatory requirement |

## Data Volume Calculations

### Per Truck (4G Mode)

```
Frame size: ~500 bytes
Frames per hour: 3600s / 30s = 120 frames
Data per hour: 120 × 500 bytes = 60 KB
Data per day: 60 KB × 24 = 1.44 MB
Data per month: 1.44 MB × 30 = 43.2 MB
Data per year: 43.2 MB × 12 = 518.4 MB
```

### Per Truck (Satellite Mode)

```
Frame size: ~200 bytes
Frames per hour: 60s / 5min = 12 frames
Data per hour: 12 × 200 bytes = 2.4 KB
Data per day: 2.4 KB × 24 = 57.6 KB
Data per month: 57.6 KB × 30 = 1.73 MB
Data per year: 1.73 MB × 12 = 20.7 MB
```

### Fleet Totals

```
4G Mode (80% of time):
- Trucks: 500
- Data per truck per year: 518.4 MB × 0.8 = 414.7 MB
- Total: 500 × 414.7 MB = 207.4 GB

Satellite Mode (20% of time):
- Trucks: 500
- Data per truck per year: 20.7 MB × 0.2 = 4.14 MB
- Total: 500 × 4.14 MB = 2.07 GB

Combined Total: ~210 GB per year (raw sensor data)
With indexing/metadata: ~300 GB per year
```

## Ingestion Rate

### Peak Load (4G)

```
Trucks sending every 30s:
- Messages per second: 500 / 30 = ~17 msg/s
- Bytes per second: 17 × 500 = 8.5 KB/s
- Megabytes per minute: 8.5 × 60 / 1024 = ~0.5 MB/min
```

### Server Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU cores | 4 | 8 |
| RAM | 16 GB | 32 GB |
| Storage | 500 GB SSD | 1 TB SSD |
| Network | 100 Mbps | 1 Gbps |
| Database | PostgreSQL 14+ | PostgreSQL 16 |

## Database Sizing

### Tables

| Table | Rows/Year | Size/Year |
|-------|-----------|-----------|
| positions | 500 × 120 × 24 × 365 × 0.8 = 420M | ~200 GB |
| sensor_readings | 500 × 120 × 24 × 365 = 525M | ~150 GB |
| alerts | 500 × 100/day × 365 = 18M | ~5 GB |
| deliveries | 500 × 10/day × 365 = 1.8M | ~1 GB |
| trucks | 500 | < 1 MB |
| **Total** | **~945M** | **~356 GB** |

### Indexes

```sql
-- Required indexes for query performance
CREATE INDEX idx_positions_truck_time ON positions(truck_id, timestamp);
CREATE INDEX idx_positions_location ON positions USING GIST (ll_to_earth(lat, lng));
CREATE INDEX idx_sensors_truck_time ON sensor_readings(truck_id, timestamp);
CREATE INDEX idx_alerts_truck_time ON alerts(truck_id, created_at);
CREATE INDEX idx_deliveries_time ON deliveries(timestamp);
```

### Partitioning Strategy

```sql
-- Partition positions by month
CREATE TABLE positions_2026_01 PARTITION OF positions
  FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');

-- Auto-create partitions
SELECT create_monthly_partition('positions', '2026-02');
```

## Bandwidth Requirements

### Cloud Platform

```
Inbound:
- MQTT messages: ~17 msg/s × 500 bytes = 8.5 KB/s
- REST API: ~10 req/s × 1 KB = 10 KB/s
- Total inbound: ~20 KB/s = 1.6 Mbps

Outbound:
- WebSocket updates: ~17 msg/s × 200 bytes = 3.4 KB/s
- Dashboard queries: ~50 req/s × 10 KB = 500 KB/s
- Total outbound: ~503 KB/s = 4 Mbps
```

### MQTT Broker

| Metric | Value |
|--------|-------|
| Connections | 500 (one per truck) |
| Messages/sec | ~17 |
| Throughput | ~10 KB/s |
| Message rate limit | 1000 msg/s recommended |

## Storage Tiers

### Hot Storage (0-30 days)

```
- All real-time data
- Current positions
- Recent alerts
- SSD storage
- Fast query access
```

### Warm Storage (1-6 months)

```
- Aggregated data (1-hour intervals)
- Historical positions (sampled)
- HDD storage
- Moderate query speed
```

### Cold Storage (6-12 months)

```
- Raw data archived
- Monthly aggregates only
- Tape or cloud archive
- Slow query access
```

## Scaling Thresholds

### Current (500 trucks)

```
- Ingestion: 17 msg/s
- Storage: 300 GB/year
- Queries: 100 req/s
- Users: 50 concurrent
```

### Phase 2 (2000 trucks)

```
- Ingestion: 67 msg/s
- Storage: 1.2 TB/year
- Queries: 400 req/s
- Users: 200 concurrent
```

### Full Scale (5000 trucks)

```
- Ingestion: 167 msg/s
- Storage: 3 TB/year
- Queries: 1000 req/s
- Users: 500 concurrent
```

### Scaling Actions

| Threshold | Action |
|-----------|--------|
| CPU > 70% | Add application server |
| Storage > 80% | Add data volume |
| Connections > 1000 | Add MQTT broker node |
| Query latency > 200ms | Add read replicas |

## Cost Estimation

### Cloud Resources (Monthly)

| Component | 500 Trucks | 2000 Trucks |
|-----------|------------|-------------|
| Compute (8 vCPU) | $200 | $800 |
| Database (PostgreSQL) | $150 | $600 |
| Storage (SSD 1TB) | $100 | $400 |
| MQTT Broker | $50 | $200 |
| Bandwidth (100GB) | $20 | $80 |
| **Total** | **$520** | **$2,080** |

### Cellular Data Costs

```
Per truck per month:
- 4G data: 43.2 MB
- Satellite: 1.73 MB
- Total: ~45 MB/month

Fleet (500 trucks):
- 500 × 45 MB = 22.5 GB/month
- Estimated cost: $200-500/month (carrier dependent)
```

## Monitoring Metrics

### System Health

```javascript
// Key metrics to monitor
const metrics = {
  ingestion_rate: 'messages per second',
  storage_used_gb: 'total storage',
  query_latency_ms: 'p95 query time',
  active_connections: 'MQTT connections',
  error_rate: 'failed messages per hour',
  data_freshness: 'seconds since last update'
};
```

### Alert Thresholds

| Metric | Warning | Critical |
|--------|---------|----------|
| Ingestion rate | < 10 msg/s | < 5 msg/s |
| Storage usage | > 80% | > 90% |
| Query latency | > 200ms | > 500ms |
| Error rate | > 1% | > 5% |
| Data freshness | > 60s | > 300s |
