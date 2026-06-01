---
name: node-red-flows
description: Node-RED flow development for ADAM-6717 on CSPH GPL tracking. Use when creating Modbus flows, sensor reading, MQTT publishing, or data processing on the IoT gateway. Triggers: Node-RED, flow, Modbus, ADAM, sensor reading, data processing, function node, debug.
---

# Node-RED Flows - ADAM-6717 Gateway

## Overview

Node-RED is the programming environment for the Advantech ADAM-6717 gateway. All data acquisition and local processing happens here.

## Architecture

```
[Modbus Read] → [JSON Format] → [Local Store] → [MQTT Publish]
      ↓
[GPS NMEA] → [Parse] → [Position Update] → [MQTT Publish]
      ↓
[Alert Logic] → [Threshold Check] → [Priority Queue]
```

## Core Flows

### 1. Hall Sensor Reading Flow

```json
[
  {
    "id": "modbus_read",
    "type": "modbus-read",
    "name": "Read Hall Sensor",
    "showStatusMessages": true,
    "unitid": 1,
    "fc": "FC3",
    "coilsAddress": 0,
    "coilsQuantity": 0,
    "holdingAddress": 40001,
    "holdingQuantity": 2,
    "pollRate": "10",
    "connection": {
      "type": "modbus-client",
      "clienttype": "TCP",
      "hostname": "192.168.1.100",
      "port": "502",
      "unit_id": "1",
      "clienttype": "TCP",
      "tcpType": "DEFAULT"
    }
  },
  {
    "id": "parse_level",
    "type": "function",
    "name": "Parse Level Value",
    "func": "// Parse float32 from Modbus registers\nvar raw = msg.payload;\nvar levelPercent = raw[0];\nvar levelMa = raw[1];\n\n// Validate range\nif (levelPercent < 0 || levelPercent > 100) {\n    node.error('Invalid level: ' + levelPercent);\n    return null;\n}\n\nmsg.payload = {\n    sensor: 'level',\n    value: levelPercent,\n    unit: '%',\n    raw_ma: levelMa,\n    timestamp: new Date().toISOString(),\n    truck_id: flow.get('truck_id') || 'TRK-000'\n};\n\nreturn msg;"
  },
  {
    "id": "mqtt_publish",
    "type": "mqtt out",
    "name": "Publish to MQTT",
    "topic": "cspH/{{truck_id}}/sensors/level",
    "qos": "1",
    "retain": "false",
    "broker": "mqtt_broker"
  }
]
```

### 2. GPS Position Flow

```json
[
  {
    "id": "gps_serial",
    "type": "serial in",
    "name": "GPS NMEA Input",
    "serial": "gps_port",
    "dataType": "string",
    "split": "\\n"
  },
  {
    "id": "parse_nmea",
    "type": "function",
    "name": "Parse NMEA GGA",
    "func": "// Parse $GPGGA sentence\nvar sentence = msg.payload;\nif (!sentence.startsWith('$GPGGA')) return null;\n\nvar parts = sentence.split(',');\nif (parts[6] === '0') return null; // No fix\n\nvar lat = parseFloat(parts[2]);\nvar latDir = parts[3];\nvar lng = parseFloat(parts[4]);\nvar lngDir = parts[5];\n\n// Convert to decimal degrees\nlat = Math.floor(lat / 100) + (lat % 100) / 60;\nif (latDir === 'S') lat = -lat;\n\nlng = Math.floor(lng / 100) + (lng % 100) / 60;\nif (lngDir === 'W') lng = -lng;\n\nmsg.payload = {\n    sensor: 'gps',\n    lat: lat,\n    lng: lng,\n    satellites: parseInt(parts[7]),\n    hdop: parseFloat(parts[8]),\n    altitude: parseFloat(parts[9]),\n    timestamp: new Date().toISOString(),\n    truck_id: flow.get('truck_id') || 'TRK-000'\n};\n\nreturn msg;"
  },
  {
    "id": "mqtt_gps",
    "type": "mqtt out",
    "name": "Publish GPS",
    "topic": "cspH/{{truck_id}}/gps/position",
    "qos": "1",
    "broker": "mqtt_broker"
  }
]
```

### 3. Sensor Aggregation Flow

```json
[
  {
    "id": "aggregate",
    "type": "function",
    "name": "Aggregate All Sensors",
    "func": "// Buffer sensor readings\nvar context = flow.get('sensorBuffer') || {};\nvar sensor = msg.payload.sensor;\ncontext[sensor] = msg.payload;\n\n// Update last activity\ncontext.lastUpdate = new Date().toISOString();\nflow.set('sensorBuffer', context);\n\n// Send aggregated frame every 30 seconds\nvar lastSend = flow.get('lastSend') || 0;\nvar now = Date.now();\n\nif (now - lastSend >= 30000) {\n    flow.set('lastSend', now);\n    \n    var frame = {\n        truck_id: flow.get('truck_id'),\n        timestamp: new Date().toISOString(),\n        position: context.gps || null,\n        sensors: {\n            level: context.level || null,\n            temperature: context.temperature || null,\n            pressure: context.pressure || null\n        },\n        battery: context.battery || null\n    };\n    \n    msg.payload = frame;\n    return msg;\n}\n\nreturn null;"
  }
]
```

### 4. Store & Forward Flow

```json
[
  {
    "id": "check_connection",
    "type": "function",
    "name": "Check 4G Connection",
    "func": "// Check if MQTT is connected\nvar mqttStatus = flow.get('mqttConnected') || false;\n\nif (!mqttStatus) {\n    // Store to local buffer\n    var buffer = flow.get('offlineBuffer') || [];\n    buffer.push({\n        frame: msg.payload,\n        stored_at: new Date().toISOString()\n    });\n    \n    // Limit buffer to 72 hours (~5000 frames)\n    if (buffer.length > 5000) {\n        buffer = buffer.slice(-5000);\n    }\n    \n    flow.set('offlineBuffer', buffer);\n    node.warn('Offline: Frame buffered (' + buffer.length + ')');\n    return null;\n}\n\nreturn msg;"
  },
  {
    "id": "send_buffered",
    "type": "function",
    "name": "Send Buffered Frames",
    "func": "// Send all buffered frames on reconnection\nvar buffer = flow.get('offlineBuffer') || [];\nif (buffer.length === 0) return null;\n\nvar frames = buffer.map(function(item) {\n    return item.frame;\n});\n\n// Send as batch\nmsg.payload = {\n    type: 'batch',\n    frames: frames,\n    count: frames.length\n};\n\nflow.set('offlineBuffer', []);\nnode.warn('Sending ' + frames.length + ' buffered frames');\n\nreturn msg;"
  }
]
```

## Node Configuration

### Modbus TCP Client

```json
{
  "id": "modbus-client",
  "type": "modbus-client",
  "name": "ADAM-6717",
  "clienttype": "TCP",
  "hostname": "192.168.1.100",
  "port": "502",
  "unit_id": "1",
  "commandDelay": "50",
  "clientTimeout": "1000",
  "reconnectOnTimeout": true,
  "reconnectTimeout": 5000,
  "parallelUnitIdsAllowed": true
}
```

### MQTT Broker

```json
{
  "id": "mqtt_broker",
  "type": "mqtt-broker",
  "name": "CSPH Cloud",
  "broker": "mqtt.ciph.cm",
  "port": "8883",
  "clientid": "truck_{{truck_id}}",
  "autoConnect": true,
  "usetls": true,
  "protocolVersion": "4",
  "keepalive": "60",
  "cleansession": true,
  "autoUnsubscribe": true,
  "z": ""
}
```

### Serial Port (GPS)

```json
{
  "id": "gps_port",
  "type": "serial-port",
  "name": "GPS Module",
  "serialtype": "usb",
  "usb": "/dev/ttyUSB0",
  "baudrate": "9600",
  "databits": "8",
  "parity": "none",
  "stopbits": "1",
  "waitfor": "",
  "spliton": "\\n",
  "x": 140,
  "y": 220,
  "wires": ["parse_nmea"]
}
```

## Flow Deployment

### Export Format

1. Open Node-RED admin interface
2. Go to Menu → Import → Clipboard
3. Paste the flow JSON
4. Click Deploy

### Environment Variables

Set in Node-RED settings.js:

```javascript
// /etc/node-red/settings.js
process.env.TRUCK_ID = 'TRK-001';
process.env.MQTT_BROKER = 'mqtt.ciph.cm';
process.env.MQTT_PORT = '8883';
```

## Testing

### Debug Nodes

Add debug nodes to inspect data:

```json
{
  "id": "debug_level",
  "type": "debug",
  "name": "Level Value",
  "active": true,
  "tosidebar": true,
  "console": false,
  "tostatus": false,
  "complete": "payload",
  "targetType": "msg",
  "statusVal": "",
  "statusType": "auto"
}
```

### Test MQTT Messages

```bash
# Subscribe to test
mosquitto_sub -h mqtt.ciph.cm -t "cspH/TRK-001/#" -v

# Publish test message
mosquitto_pub -h mqtt.ciph.cm -t "cspH/TRK-001/sensors/level" \
  -m '{"sensor":"level","value":75.5,"unit":"%"}'
```

## Performance Notes

| Metric | Target |
|--------|--------|
| Sensor read rate | 10 seconds |
| GPS update rate | 1 second |
| MQTT publish rate | 30 seconds |
| Offline buffer | 72 hours |
| Max frames buffered | 5000 |
