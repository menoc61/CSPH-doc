---
name: dashboard-design
description: Dashboard design for CSPH GPL tracking supervision. Use when creating real-time maps, charts, alert panels, or report views. Triggers: dashboard, UI, map, chart, supervision, monitoring, visualization, display.
---

# Dashboard Design - CSPH GPL Supervision

## Overview

Real-time dashboard for CSPH to monitor fleet, track deliveries, and manage alerts.

## User Roles & Views

| Role | Primary View | Key Features |
|------|--------------|--------------|
| Admin | Full dashboard | All features |
| Dispatcher | Map + alerts | Real-time tracking |
| Chauffeur | Mobile app | Own truck status |
| CSPH Auditor | Reports | Analytics only |

## Dashboard Layout

### Main Dashboard

```
┌─────────────────────────────────────────────────────────────┐
│  CSPH GPL TRACKING SYSTEM                    [Profile] [⚙] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐      │
│  │ 500      │ │ 487      │ │ 12       │ │ 1        │      │
│  │ Total    │ │ Online   │ │ Alerts   │ │ Critical │      │
│  │ Trucks   │ │ Now      │ │ Pending  │ │ Alerts   │      │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘      │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────┐ ┌─────────────────┐│
│  │                                     │ │ ALERTS          ││
│  │         LIVE MAP                    │ │                 ││
│  │                                     │ │ ⚠ TRK-042 Route ││
│  │    🚛 TRK-001                       │ │   deviation     ││
│  │         🚛 TRK-003                  │ │                 ││
│  │              🚛 TRK-007             │ │ 🔴 TRK-089 Low  ││
│  │    🚛 TRK-012                       │ │   pressure      ││
│  │                                     │ │                 ││
│  │         🚛 TRK-015                  │ │ ⚠ TRK-156 Speed ││
│  │                                     │ │   warning       ││
│  └─────────────────────────────────────┘ └─────────────────┘│
│                                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────┐ ┌─────────────────┐│
│  │  FUEL LEVEL TREND (24h)            │ │ DELIVERIES TODAY││
│  │  ▁▂▃▄▅▆▇█▇▆▅▄▃▂▁▂▃▄▅▆▇█▇▆▅      │ │                 ││
│  │                                     │ │ ✓ 12 delivered  ││
│  │  TRK-001: 78% → 45%               │ │ ✓ 8 returned    ││
│  │  TRK-003: 92% → 67%               │ │ ⏳ 3 in transit ││
│  │  TRK-007: 55% → 23%               │ │                 ││
│  └─────────────────────────────────────┘ └─────────────────┘│
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## Components

### 1. Live Map

**Technology:** Leaflet.js or Mapbox GL

```javascript
// Map configuration
const mapConfig = {
  center: [3.848, 11.502], // Yaoundé
  zoom: 7,
  maxZoom: 18,
  layers: {
    trucks: L.markerClusterGroup(),
    routes: L.polyline,
    geofences: L.polygon
  }
};

// Truck marker
function createTruckMarker(truck) {
  const statusColors = {
    moving: '#22c55e',
    idle: '#eab308',
    offline: '#6b7280',
    alert: '#ef4444'
  };
  
  return L.marker([truck.lat, truck.lng], {
    icon: L.divIcon({
      className: 'truck-marker',
      html: `<div style="background: ${statusColors[truck.status]}">
               🚛 ${truck.id}
             </div>`
    })
  });
}
```

### 2. KPI Cards

```html
<div class="kpi-card">
  <div class="kpi-value">500</div>
  <div class="kpi-label">Total Trucks</div>
  <div class="kpi-trend up">+12 this month</div>
</div>

<div class="kpi-card">
  <div class="kpi-value">487</div>
  <div class="kpi-label">Online Now</div>
  <div class="kpi-trend">97.4% availability</div>
</div>

<div class="kpi-card">
  <div class="kpi-value">12</div>
  <div class="kpi-label">Active Alerts</div>
  <div class="kpi-trend warning">3 critical</div>
</div>
```

### 3. Alert Panel

```javascript
// Alert component
const alertPanel = {
  columns: [
    { key: 'truck_id', label: 'Truck' },
    { key: 'type', label: 'Type' },
    { key: 'severity', label: 'Severity' },
    { key: 'time', label: 'Time' },
    { key: 'actions', label: 'Actions' }
  ],
  filters: {
    severity: ['all', 'critical', 'warning', 'info'],
    status: ['all', 'active', 'acknowledged'],
    timeRange: ['1h', '6h', '24h', '7d']
  }
};
```

### 4. Charts

#### Fuel Level Trend

```javascript
// Chart.js configuration
const fuelChart = {
  type: 'line',
  data: {
    labels: timeLabels,
    datasets: [
      {
        label: 'TRK-001',
        data: fuelLevels,
        borderColor: '#3b82f6',
        tension: 0.4
      },
      {
        label: 'TRK-003',
        data: fuelLevels2,
        borderColor: '#22c55e',
        tension: 0.4
      }
    ]
  },
  options: {
    responsive: true,
    scales: {
      y: {
        min: 0,
        max: 100,
        title: { display: true, text: 'Level %' }
      }
    }
  }
};
```

#### Delivery Statistics

```javascript
const deliveryChart = {
  type: 'bar',
  data: {
    labels: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
    datasets: [
      {
        label: 'Deliveries',
        data: [45, 52, 48, 61, 55, 32, 28],
        backgroundColor: '#3b82f6'
      },
      {
        label: 'Returns',
        data: [38, 45, 42, 55, 48, 28, 22],
        backgroundColor: '#22c55e'
      }
    ]
  }
};
```

## Real-time Updates

### WebSocket Connection

```javascript
const ws = new WebSocket('wss://api.ciph.cm/v1/tracking/stream');

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  
  switch (data.event) {
    case 'position_update':
      updateTruckPosition(data.truck_id, data.position);
      break;
    case 'alert_triggered':
      addAlert(data);
      break;
    case 'truck_offline':
      updateTruckStatus(data.truck_id, 'offline');
      break;
  }
};

// Ping/pong keepalive
setInterval(() => {
  ws.send(JSON.stringify({ event: 'ping' }));
}, 30000);
```

### State Management

```javascript
// React state example
const [trucks, setTrucks] = useState({});
const [alerts, setAlerts] = useState([]);

const handlePositionUpdate = (truckId, position) => {
  setTrucks(prev => ({
    ...prev,
    [truckId]: {
      ...prev[truckId],
      position,
      lastUpdate: new Date()
    }
  }));
};
```

## Mobile Responsive

### Breakpoints

```css
/* Desktop */
@media (min-width: 1024px) {
  .dashboard { grid-template-columns: 1fr 300px; }
  .map { height: 600px; }
}

/* Tablet */
@media (min-width: 768px) and (max-width: 1023px) {
  .dashboard { grid-template-columns: 1fr; }
  .map { height: 400px; }
}

/* Mobile */
@media (max-width: 767px) {
  .dashboard { flex-direction: column; }
  .map { height: 300px; }
  .alerts { max-height: 200px; }
}
```

## Performance Optimization

### Virtual Scrolling

```javascript
// For large truck lists
import { FixedSizeList } from 'react-window';

const TruckList = ({ trucks }) => (
  <FixedSizeList
    height={400}
    itemCount={trucks.length}
    itemSize={60}
  >
    {({ index, style }) => (
      <TruckRow
        truck={trucks[index]}
        style={style}
      />
    )}
  </FixedSizeList>
);
```

### Map Clustering

```javascript
// Cluster nearby markers
const markers = L.markerClusterGroup({
  maxClusterRadius: 50,
  spiderfyOnMaxZoom: true,
  showCoverageOnHover: false
});
```

## Export & Reports

### PDF Export

```javascript
const exportReport = async (dateRange) => {
  const response = await fetch('/api/reports/generate', {
    method: 'POST',
    body: JSON.stringify({
      type: 'monthly',
      dateRange,
      format: 'pdf'
    })
  });
  
  const blob = await response.blob();
  const url = window.URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `ciph-report-${dateRange}.pdf`;
  a.click();
};
```

## Accessibility

### Color Contrast

| Element | Text | Background | Ratio |
|---------|------|------------|-------|
| Critical alert | White | Red | 7:1 |
| Warning | Black | Yellow | 4.5:1 |
| Info | White | Blue | 7:1 |
| Normal text | Black | White | 15:1 |

### Keyboard Navigation

```javascript
// Tab order
const tabOrder = [
  'map',
  'alerts',
  'truck-list',
  'charts',
  'export'
];

// Keyboard shortcuts
document.addEventListener('keydown', (e) => {
  if (e.key === 'Escape') closeModals();
  if (e.key === 'r' && e.ctrlKey) refreshData();
});
```

## Dashboard Metrics

| Metric | Target |
|--------|--------|
| Initial load | < 2 seconds |
| Map render | < 500ms |
| Alert display | < 1 second |
| Data refresh | 30 seconds |
| Concurrent users | 50 |
| Mobile load | < 3 seconds |
