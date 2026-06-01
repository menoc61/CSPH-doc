---
name: data-validation
description: Data validation for CSPH GPL sensor data. Use when validating GPS coordinates, sensor readings, frame integrity, or data quality. Triggers: validation, data quality, sensor, GPS, checksum, integrity, verify, check.
---

# Data Validation - CSPH GPL Tracking

## Overview

Validation rules for sensor data, GPS coordinates, and frame integrity.

## Frame Validation Schema

### Complete Frame Schema

```json
{
  "truck_id": {
    "type": "string",
    "pattern": "^TRK-[0-9]{3}$",
    "required": true
  },
  "timestamp": {
    "type": "string",
    "format": "iso8601",
    "required": true
  },
  "position": {
    "type": "object",
    "required": true,
    "properties": {
      "lat": {
        "type": "number",
        "min": -90,
        "max": 90
      },
      "lng": {
        "type": "number",
        "min": -180,
        "max": 180
      },
      "speed": {
        "type": "number",
        "min": 0,
        "max": 150
      },
      "heading": {
        "type": "integer",
        "min": 0,
        "max": 360
      }
    }
  },
  "sensors": {
    "type": "object",
    "required": true
  }
}
```

## GPS Validation

### Coordinate Checks

```javascript
function validateGPS(position) {
  const errors = [];
  
  // Latitude range
  if (position.lat < -90 || position.lat > 90) {
    errors.push('Latitude out of range: ' + position.lat);
  }
  
  // Longitude range
  if (position.lng < -180 || position.lng > 180) {
    errors.push('Longitude out of range: ' + position.lng);
  }
  
  // Cameroon bounding box (approximate)
  const cameroonBounds = {
    lat: { min: 1.65, max: 13.08 },
    lng: { min: 8.49, max: 16.19 }
  };
  
  if (position.lat < cameroonBounds.lat.min || 
      position.lat > cameroonBounds.lat.max) {
    errors.push('Position outside Cameroon');
  }
  
  // Speed validation
  if (position.speed < 0 || position.speed > 150) {
    errors.push('Invalid speed: ' + position.speed);
  }
  
  // Zero position check
  if (position.lat === 0 && position.lng === 0) {
    errors.push('Invalid zero coordinates');
  }
  
  return {
    valid: errors.length === 0,
    errors: errors
  };
}
```

### Geofence Validation

```javascript
function checkGeofence(position, depot) {
  const R = 6371e3; // Earth radius in meters
  const φ1 = position.lat * Math.PI / 180;
  const φ2 = depot.lat * Math.PI / 180;
  const Δφ = (depot.lat - position.lat) * Math.PI / 180;
  const Δλ = (depot.lng - position.lng) * Math.PI / 180;
  
  const a = Math.sin(Δφ/2) * Math.sin(Δφ/2) +
            Math.cos(φ1) * Math.cos(φ2) *
            Math.sin(Δλ/2) * Math.sin(Δλ/2);
  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
  
  const distance = R * c;
  
  return {
    inGeofence: distance <= depot.radius,
    distance: Math.round(distance)
  };
}
```

## Sensor Validation

### Level Sensor

```javascript
function validateLevel(sensor) {
  const errors = [];
  
  // Range check (0-100%)
  if (sensor.value < 0 || sensor.value > 100) {
    errors.push('Level out of range: ' + sensor.value);
  }
  
  // Rate of change check (>10% in 30s = suspicious)
  const lastReading = getLastReading(sensor.truck_id, 'level');
  if (lastReading) {
    const change = Math.abs(sensor.value - lastReading.value);
    const timeDiff = (new Date(sensor.timestamp) - new Date(lastReading.timestamp)) / 1000;
    const ratePerMinute = (change / timeDiff) * 60;
    
    if (ratePerMinute > 10) {
      errors.push('Suspicious rate of change: ' + ratePerMinute.toFixed(2) + '%/min');
    }
  }
  
  // 4-20mA validation
  if (sensor.raw_ma < 4 || sensor.raw_ma > 20) {
    errors.push('mA signal out of range: ' + sensor.raw_ma);
  }
  
  return {
    valid: errors.length === 0,
    errors: errors
  };
}
```

### Temperature Sensor

```javascript
function validateTemperature(sensor) {
  const errors = [];
  
  // Reasonable range for LPG (-40°C to +70°C)
  if (sensor.value < -40 || sensor.value > 70) {
    errors.push('Temperature out of range: ' + sensor.value);
  }
  
  // Sudden change check (>5°C in 30s)
  const lastReading = getLastReading(sensor.truck_id, 'temperature');
  if (lastReading) {
    const change = Math.abs(sensor.value - lastReading.value);
    if (change > 5) {
      errors.push('Sudden temperature change: ' + change + '°C');
    }
  }
  
  return {
    valid: errors.length === 0,
    errors: errors
  };
}
```

### Pressure Sensor

```javascript
function validatePressure(sensor) {
  const errors = [];
  
  // LPG pressure range (5-15 bar typically)
  if (sensor.value < 0 || sensor.value > 20) {
    errors.push('Pressure out of range: ' + sensor.value);
  }
  
  // Drop detection (>2 bar/min = leak alert)
  const lastReading = getLastReading(sensor.truck_id, 'pressure');
  if (lastReading) {
    const drop = lastReading.value - sensor.value;
    const timeDiff = (new Date(sensor.timestamp) - new Date(lastReading.timestamp)) / 1000;
    const dropPerMinute = (drop / timeDiff) * 60;
    
    if (dropPerMinute > 2) {
      errors.push('Rapid pressure drop: ' + dropPerMinute.toFixed(2) + ' bar/min');
    }
  }
  
  return {
    valid: errors.length === 0,
    errors: errors
  };
}
```

## Frame Integrity

### Checksum Verification

```javascript
function calculateChecksum(frame) {
  const data = JSON.stringify({
    truck_id: frame.truck_id,
    timestamp: frame.timestamp,
    position: frame.position,
    sensors: frame.sensors
  });
  
  return crypto
    .createHmac('sha256', process.env.FRAME_SECRET)
    .update(data)
    .digest('hex');
}

function verifyFrameIntegrity(frame) {
  const calculated = calculateChecksum(frame);
  return calculated === frame.checksum;
}
```

### Sequence Validation

```javascript
function validateSequence(frame) {
  const lastSequence = getLastSequence(frame.truck_id);
  
  if (!lastSequence) {
    return { valid: true, gap: false };
  }
  
  const expected = lastSequence + 1;
  const gap = frame.sequence - expected;
  
  return {
    valid: frame.sequence >= expected,
    gap: gap > 0,
    missingFrames: gap
  };
}
```

## Time Validation

### Timestamp Checks

```javascript
function validateTimestamp(frame) {
  const errors = [];
  const frameTime = new Date(frame.timestamp);
  const now = new Date();
  
  // Future timestamp check
  if (frameTime > now) {
    errors.push('Future timestamp');
  }
  
  // Too old (>5 minutes)
  const ageSeconds = (now - frameTime) / 1000;
  if (ageSeconds > 300) {
    errors.push('Frame too old: ' + ageSeconds + ' seconds');
  }
  
  // Clock drift check (>30 seconds difference from expected)
  const expectedTime = new Date(lastFrame.timestamp);
  expectedTime.setSeconds(expectedTime.getSeconds() + 30);
  const drift = Math.abs(frameTime - expectedTime) / 1000;
  
  if (drift > 30) {
    errors.push('Clock drift detected: ' + drift + ' seconds');
  }
  
  return {
    valid: errors.length === 0,
    errors: errors
  };
}
```

## Batch Validation

### Store & Forward Validation

```javascript
function validateBatch(batch) {
  const errors = [];
  
  // Batch size check
  if (batch.frames.length > 500) {
    errors.push('Batch too large: ' + batch.frames.length);
  }
  
  // Sequential order check
  for (let i = 1; i < batch.frames.length; i++) {
    const prevTime = new Date(batch.frames[i-1].timestamp);
    const currTime = new Date(batch.frames[i].timestamp);
    
    if (currTime < prevTime) {
      errors.push('Out of order frame at index ' + i);
    }
  }
  
  // Duplicate check
  const timestamps = batch.frames.map(f => f.timestamp);
  const duplicates = timestamps.filter((t, i) => timestamps.indexOf(t) !== i);
  
  if (duplicates.length > 0) {
    errors.push('Duplicate timestamps found');
  }
  
  return {
    valid: errors.length === 0,
    errors: errors,
    frameCount: batch.frames.length
  };
}
```

## Validation Pipeline

### Complete Pipeline

```javascript
async function validateFrame(frame) {
  const results = {
    timestamp: validateTimestamp(frame),
    gps: validateGPS(frame.position),
    level: validateLevel(frame.sensors.level),
    temperature: validateTemperature(frame.sensors.temperature),
    pressure: validatePressure(frame.sensors.pressure),
    checksum: verifyFrameIntegrity(frame),
    sequence: validateSequence(frame)
  };
  
  const allValid = Object.values(results).every(r => r.valid);
  
  // Log validation result
  await logValidation({
    truck_id: frame.truck_id,
    frame_id: frame.id,
    valid: allValid,
    errors: Object.entries(results)
      .filter(([_, r]) => !r.valid)
      .map(([key, r]) => ({ field: key, errors: r.errors }))
  });
  
  return {
    valid: allValid,
    results: results
  };
}
```

### Error Handling

```javascript
function handleValidationError(frame, errors) {
  // Log to monitoring
  metrics.increment('validation.errors');
  
  // Alert if critical
  if (errors.some(e => e.severity === 'critical')) {
    sendAlert({
      type: 'validation_critical',
      frame_id: frame.id,
      truck_id: frame.truck_id,
      errors: errors
    });
  }
  
  // Store invalid frame for analysis
  storeInvalidFrame(frame, errors);
  
  // Don't process invalid data
  return false;
}
```

## Validation Metrics

| Metric | Target |
|--------|--------|
| Valid frames | > 99% |
| GPS accuracy | < 5m CEP50 |
| Sensor accuracy | ±1% |
| Checksum verification | 100% |
| Sequence gaps | < 0.1% |
| Timestamp accuracy | < 1 second drift |
