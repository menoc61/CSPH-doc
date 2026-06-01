---
name: alert-system
description: Alert system for CSPH GPL tracking anomaly detection. Use when creating alert rules, notification workflows, or incident response. Triggers: alert, anomaly, detection, notification, incident, threshold, warning, critical.
---

# Alert System - CSPH GPL Tracking

## Overview

Automated anomaly detection and alerting for fleet monitoring.

## Alert Types

### Critical Alerts (QoS 2, Immediate)

| Alert | Condition | Action |
|-------|-----------|--------|
| Gas Leak | Pressure drop > 2 bar/min | Emergency services |
| Tamper Detected | Enclosure opened | Physical inspection |
| Fire/Heat | Temperature > 70°C | Emergency services |
| Crash | Sudden deceleration > 5G | Emergency services |

### Warning Alerts (QoS 1)

| Alert | Condition | Action |
|-------|-----------|--------|
| Route Deviation | 500m from planned route | Dispatcher review |
| Speed Warning | > 80 km/h | Driver notification |
| Low Fuel | Battery < 20% | Maintenance scheduled |
| Signal Lost | No update > 5 minutes | Connectivity check |

### Info Alerts (QoS 1)

| Alert | Condition | Action |
|-------|-----------|--------|
| Geofence Entry | Truck enters depot | Log event |
| Geofence Exit | Truck leaves depot | Log event |
| Delivery Complete | Delivery recorded | Log event |
| Maintenance Due | 30 days since last service | Schedule maintenance |

## Alert Schema

```json
{
  "alert_id": "ALT-2026-001234",
  "truck_id": "TRK-001",
  "alert_type": "leak_detected",
  "severity": "critical",
  "message": "Rapid pressure drop detected - possible gas leak",
  "sensor_data": {
    "pressure_before": 8.5,
    "pressure_after": 6.2,
    "drop_rate": 2.3,
    "unit": "bar/min"
  },
  "position": {
    "lat": 3.8480000,
    "lng": 11.5020000
  },
  "timestamp": "2026-01-15T10:30:00Z",
  "requires_ack": true,
  "acknowledged": false,
  "acknowledged_by": null,
  "acknowledged_at": null,
  "notes": null
}
```

## Detection Rules

### 1. Pressure Drop (Leak Detection)

```javascript
function detectPressureDrop(readings) {
  if (readings.length < 2) return null;
  
  const recent = readings.slice(-5);
  const timeSpan = (new Date(recent[4].timestamp) - new Date(recent[0].timestamp)) / 1000;
  const pressureDrop = recent[0].value - recent[4].value;
  const dropPerMinute = (pressureDrop / timeSpan) * 60;
  
  if (dropPerMinute > 2) {
    return {
      type: 'leak_detected',
      severity: 'critical',
      data: {
        pressure_before: recent[0].value,
        pressure_after: recent[4].value,
        drop_rate: dropPerMinute.toFixed(2)
      }
    };
  }
  
  return null;
}
```

### 2. Route Deviation

```javascript
function detectRouteDeviation(position, plannedRoute) {
  const minDistance = Math.min(
    ...plannedRoute.map(point => 
      calculateDistance(position, point)
    )
  );
  
  if (minDistance > 500) { // 500 meters
    return {
      type: 'route_deviation',
      severity: 'warning',
      data: {
        deviation_meters: Math.round(minDistance),
        planned_route_id: plannedRoute.id
      }
    };
  }
  
  return null;
}
```

### 3. Speed Warning

```javascript
function detectSpeedWarning(position, speedLimit = 80) {
  if (position.speed > speedLimit) {
    return {
      type: 'speed_warning',
      severity: 'warning',
      data: {
        speed: position.speed,
        limit: speedLimit,
        excess: position.speed - speedLimit
      }
    };
  }
  
  return null;
}
```

### 4. Geofence Alert

```javascript
function detectGeofenceViolation(position, geofences) {
  for (const fence of geofences) {
    const inside = pointInPolygon(position, fence.polygon);
    
    if (fence.type === 'restricted' && inside) {
      return {
        type: 'geofence_violation',
        severity: 'warning',
        data: {
          fence_id: fence.id,
          fence_name: fence.name
        }
      };
    }
  }
  
  return null;
}
```

### 5. Offline Detection

```javascript
function detectOffline(truck) {
  const lastUpdate = new Date(truck.last_update);
  const now = new Date();
  const minutesSinceUpdate = (now - lastUpdate) / 1000 / 60;
  
  if (minutesSinceUpdate > 5) {
    return {
      type: 'truck_offline',
      severity: 'info',
      data: {
        last_seen: truck.last_update,
        minutes_offline: Math.round(minutesSinceUpdate)
      }
    };
  }
  
  return null;
}
```

## Notification Channels

### MQTT (Real-time)

```javascript
// Critical alerts - immediate broadcast
function publishCriticalAlert(alert) {
  mqttClient.publish(
    `cspH/alerts/critical`,
    JSON.stringify(alert),
    { qos: 2, retain: false }
  );
}

// Truck-specific alerts
function publishTruckAlert(alert) {
  mqttClient.publish(
    `cspH/${alert.truck_id}/alerts`,
    JSON.stringify(alert),
    { qos: 1, retain: false }
  );
}
```

### SMS (Emergency)

```javascript
async function sendSMSAlert(alert) {
  if (alert.severity === 'critical') {
    await smsService.send({
      to: process.env.EMERGENCY_PHONE,
      message: `CRITICAL ALERT: ${alert.message}\nTruck: ${alert.truck_id}\nLocation: ${alert.position.lat}, ${alert.position.lng}`
    });
  }
}
```

### Email (Daily Summary)

```javascript
async function sendDailySummary(alerts) {
  const critical = alerts.filter(a => a.severity === 'critical');
  const warnings = alerts.filter(a => a.severity === 'warning');
  
  await emailService.send({
    to: process.env.ADMIN_EMAIL,
    subject: `CIPH Daily Alert Summary - ${new Date().toISOString().split('T')[0]}`,
    html: `
      <h2>Daily Alert Summary</h2>
      <p>Critical: ${critical.length}</p>
      <p>Warnings: ${warnings.length}</p>
      <table>
        <tr><th>Truck</th><th>Type</th><th>Time</th></tr>
        ${alerts.map(a => `
          <tr>
            <td>${a.truck_id}</td>
            <td>${a.alert_type}</td>
            <td>${a.timestamp}</td>
          </tr>
        `).join('')}
      </table>
    `
  });
}
```

## Alert Acknowledgment

### ACK Flow

```javascript
async function acknowledgeAlert(alertId, userId, note) {
  const alert = await getAlert(alertId);
  
  if (!alert) {
    throw new Error('Alert not found');
  }
  
  if (alert.acknowledged) {
    throw new Error('Alert already acknowledged');
  }
  
  // Validate note for critical alerts
  if (alert.severity === 'critical' && !note) {
    throw new Error('Note required for critical alert acknowledgment');
  }
  
  // Update alert
  await updateAlert(alertId, {
    acknowledged: true,
    acknowledged_by: userId,
    acknowledged_at: new Date().toISOString(),
    notes: note
  });
  
  // Create audit log
  await createAuditLog({
    event_type: 'alert_acknowledged',
    entity_id: alertId,
    user_id: userId,
    new_values: { note }
  });
  
  return { success: true };
}
```

## Alert Escalation

### Escalation Rules

| Level | Time | Action |
|-------|------|--------|
| 0 | 0-5 min | Dispatcher notified |
| 1 | 5-15 min | Supervisor notified |
| 2 | 15-30 min | Manager notified |
| 3 | 30+ min | Emergency services (if critical) |

```javascript
function escalateAlert(alert) {
  const elapsed = (new Date() - new Date(alert.timestamp)) / 1000 / 60;
  
  if (alert.severity === 'critical') {
    if (elapsed > 30 && !alert.emergency_notified) {
      notifyEmergencyServices(alert);
      updateAlert(alert.id, { emergency_notified: true });
    } else if (elapsed > 15) {
      notifyManager(alert);
    } else if (elapsed > 5) {
      notifySupervisor(alert);
    }
  }
}
```

## Alert Analytics

### Metrics

```javascript
const alertMetrics = {
  total: 'COUNT(*)',
  bySeverity: 'GROUP BY severity',
  byType: 'GROUP BY alert_type',
  avgResponseTime: 'AVG(acknowledged_at - created_at)',
  unresolved: 'WHERE acknowledged = false'
};
```

### Dashboard Queries

```sql
-- Alerts by severity (last 24h)
SELECT severity, COUNT(*) 
FROM alerts 
WHERE created_at > NOW() - INTERVAL '24 hours'
GROUP BY severity;

-- Average response time
SELECT 
  alert_type,
  AVG(EXTRACT(EPOCH FROM (acknowledged_at - created_at))) as avg_seconds
FROM alerts 
WHERE acknowledged = true
GROUP BY alert_type;

-- Most active trucks
SELECT truck_id, COUNT(*) as alert_count
FROM alerts
WHERE created_at > NOW() - INTERVAL '7 days'
GROUP BY truck_id
ORDER BY alert_count DESC
LIMIT 10;
```

## Alert Templates

### Critical Alert Email

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    .critical { background: #fee2e2; border-left: 4px solid #ef4444; }
    .warning { background: #fef3c7; border-left: 4px solid #f59e0b; }
    .info { background: #dbeafe; border-left: 4px solid #3b82f6; }
  </style>
</head>
<body>
  <div class="{{severity}}">
    <h2>🚨 {{alert_type}} Alert</h2>
    <p><strong>Truck:</strong> {{truck_id}}</p>
    <p><strong>Time:</strong> {{timestamp}}</p>
    <p><strong>Location:</strong> {{position.lat}}, {{position.position.lng}}</p>
    <p><strong>Details:</strong> {{message}}</p>
    {{#if sensor_data}}
    <h3>Sensor Data</h3>
    <pre>{{json sensor_data}}</pre>
    {{/if}}
    <a href="https://dashboard.ciph.cm/alerts/{{alert_id}}">
      View in Dashboard
    </a>
  </div>
</body>
</html>
```

## Performance Metrics

| Metric | Target |
|--------|--------|
| Detection latency | < 10 seconds |
| Alert delivery (4G) | < 2 seconds |
| Alert delivery (satellite) | < 30 seconds |
| ACK processing | < 1 second |
| Escalation accuracy | 100% |
| False positive rate | < 5% |
