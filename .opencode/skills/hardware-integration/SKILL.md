---
name: hardware-integration
description: Hardware integration for CSPH GPL tracking. Use when working with sensor calibration, mechanical installation, electrical connections, or safety certifications. Triggers: hardware, sensor, calibration, installation, ATEX, ADR, mechanical, electrical, wiring, mount.
---

# Hardware Integration - CSPH GPL Tracking

## Overview

Physical installation and integration guidelines for the IoT prototype on LPG tank trucks.

## Sensor Integration

### Hall Effect Level Sensor

#### Principles

- Non-contact magnetic sensing through tank wall
- External float follows liquid level
- 4-20mA signal proportional to level (0-100%)

#### Installation Steps

1. **Mount Guide Tube**
   - Material: Stainless steel 316L, 25mm diameter
   - Position: Vertical on tank wall exterior
   - Fixation: Stainless steel clamps at 50cm intervals
   - Length: Equal to tank height

2. **Insert Float**
   - Material: Density-calibrated for LPG
   - Position: Inside guide tube
   - Function: Follows level via magnetic correlation

3. **Connect to ADAM-6717**
   - Cable: Shielded 2-conductor
   - Entry: Through ATEX-certified cable gland
   - Connection: Analog input AI0

4. **Calibration**
   - Empty tank: 4mA
   - Full tank: 20mA
   - Store parameters in Node-RED config

#### Wiring Diagram

```
Hall Sensor          ADAM-6717
+------------------+------------------+
| Red (Power +)    | AI0+ (24V)       |
| Black (Signal -) | AI0- (GND)       |
| Shield           | Frame Ground     |
+------------------+------------------+
```

### GPS Module

#### Connection

```
GPS Module           ADAM-6717
+------------------+------------------+
| VCC              | +12V (regulated) |
| GND              | GND              |
| TX               | RS-485 A+        |
| RX               | RS-485 B-        |
+------------------+------------------+
```

#### Configuration

- **Baud Rate:** 9600
- **Update Rate:** 1Hz (1 second)
- **NMEA Sentences:** GGA, RMC, VTG
- **Power Save:** Disabled (continuous tracking)

## Electrical Installation

### Power Distribution

```
Vehicle Battery (24V DC)
          |
    [Fuse 5A]
          |
    [DC-DC Isolated Converter]
          |
    +----+----+
    |         |
[ADAM-6717] [RUT241]
    |
[Battery LiFePO4]
(Backup power)
```

### Isolation Requirements

- **Galvanic isolation:** Required between vehicle and IoT electronics
- **Converter:** Isolated DC-DC (24V → 12V)
- **Protection:** Reverse polarity, transient suppression
- **Fusing:** 5A on main line, 2A on each device

### Cable Specifications

| Cable Type | Use | Rating |
|------------|-----|--------|
| Power | Main supply | 16 AWG, shielded |
| Sensor | 4-20mA signal | 18 AWG, twisted pair, shielded |
| Data | RS-485 | 22 AWG, twisted pair, shielded |
| Network | Ethernet | Cat6, UV-resistant |

## Mechanical Installation

### Mounting Locations

```
         Tank Truck Semi-Trailer
    +----------------------------------+
    |                                  |
    |  [ADAM-6717] ← Front chassis    |
    |  [RUT241]    ← Side panel       |
    |  [Battery]   ← Under chassis    |
    |                                  |
    |  [Hall Sensor] ← Tank wall      |
    |  [GPS]        ← Roof mount      |
    |                                  |
    +----------------------------------+
```

### Fixation Methods

| Component | Method | Points |
|-----------|--------|--------|
| ADAM-6717 | Bolt on chassis | 4x M8 bolts |
| RUT241 | DIN rail + bracket | 2x screws |
| Battery | Bracket + strap | 2x bolts + 1x strap |
| Hall sensor | Stainless clamps | Every 50cm |

### Vibration Protection

- **Frequency range:** 5-500 Hz
- **Acceleration:** 2G continuous, 5G shock
- **Isolation:** Silent blocks on all mounts
- **Cable routing:** Through braided loom
- **Connectors:** Push-lock or bayonet type

## Safety Compliance

### ATEX Zones

| Zone | Description | Equipment Required |
|------|-------------|-------------------|
| Zone 0 | Continuous explosive atmosphere | Intrinsically safe |
| Zone 1 | Occasional explosive atmosphere | EEx ia/ib certified |
| Zone 2 | Abnormal conditions only | Non-sparking, sealed |

### Installation Zones

```
Tank Truck Side View
+------------------------------------------+
|                                          |
|  [DANGER ZONE] ← Vents, valves, fittings|
|  No non-certified electronics            |
|                                          |
|  [SAFE ZONE] ← Chassis, side panels     |
|  IoT equipment installed here           |
|                                          |
+------------------------------------------+
```

### ADR Requirements

- **Cable glands:** ATEX certified, flameproof
- **Maximum voltage:** 12V DC in danger zone
- **Barrier:** Intrinsically safe for sensor connections
- **Marking:** Equipment must display CE marking

## Weatherproofing

### Enclosure Rating

- **Minimum:** IP67 (dust-tight, temporary immersion)
- **Recommended:** IP68 (continuous submersion)
- **Material:** Anodized aluminum or stainless steel
- **Seals:** Silicone gaskets, replaceable

### Thermal Management

- **Operating range:** -10°C to +70°C
- **Storage range:** -20°C to +85°C
- **Heat dissipation:** Aluminum enclosure acts as heatsink
- **Positioning:** Shaded area of chassis preferred

## Maintenance

### Preventive Schedule

| Interval | Task |
|----------|------|
| Weekly | Visual inspection, clean sensors |
| Monthly | Check connections, calibrate sensors |
| Quarterly | Inspect cables, test backup battery |
| Annually | Full system test, replace gaskets |

### Troubleshooting

| Symptom | Possible Cause | Action |
|---------|---------------|--------|
| No data | Power loss | Check fuses, battery voltage |
| Erratic readings | Cable damage | Inspect shield, replace cable |
| GPS drift | Antenna obstruction | Reposition GPS module |
| Low battery | Solar panel dirty | Clean panel, check connections |
