# CONTEXT.md - CSPH GPL Tracking System

## Domain Language

### Core Entities

| Term | Definition |
|------|------------|
| **CSPH** | Caisse de Stabilisation des Prix des Hydrocarbures - State regulatory body |
| **GPL** | Gaz de Pétrole Liquéfié (Liquefied Petroleum Gas) |
| **Marketer** | Licensed distributor of LPG products |
| **Péréquation** | Price equalization mechanism / subsidy system |
| **Dépôt** | Storage depot where tank trucks are loaded |
| **Bouteille 50kg** | 50kg LPG cylinder for commercial/industrial use |

### Technical Terms

| Term | Definition |
|------|------------|
| **ADAM-6717** | Advantech IoT gateway with 8 I/O channels, Node-RED support |
| **RUT241** | Teltonika 4G LTE industrial router |
| **Hall Effect Sensor** | Non-contact magnetic level sensor, 4-20mA output |
| **UHF RFID** | Ultra High Frequency RFID (EPC Gen2 standard) |
| **PDA** | Personal Digital Assistant - handheld scanner for deliveries |
| **Modbus TCP/RTU** | Industrial communication protocol |
| **MQTT** | Message Queuing Telemetry Transport - lightweight IoT protocol |
| **Store & Forward** | Offline data buffering with later transmission |

### Regulatory Terms

| Term | Definition |
|------|------------|
| **ADR** | European Agreement concerning the International Carriage of Dangerous Goods by Road |
| **ATEX** | ATmosphères EXplosibles - EU certification for explosive environments |
| **CEMAC** | Communauté Économique et Monétaire de l'Afrique Centrale |

### System Components

| Component | Role |
|-----------|------|
| **Capteur de niveau** | Level sensor on tank truck (Hall effect, external mount) |
| **Unité embarquée** | Embedded unit - the complete IoT device on truck |
| **Gateway** | ADAM-6717 - data acquisition and local processing |
| **Routeur** | Teltonika RUT241 - 4G/LTE connectivity |
| **Plateforme cloud** | Central server for data aggregation and dashboard |
| **Terminal PDA** | Handheld device for bottle delivery scanning |

### Data Flow

```
Tank Truck → Hall Sensor → ADAM-6717 → MQTT → Cloud API → Dashboard
                                                      ↓
Bottle PDA → RFID Scan → 4G → Cloud API → Alert System
```

### Business Rules

1. **Subsidy Eligibility:** Only residential use qualifies for government subsidy
2. **Volume Verification:** System must detect discrepancies between declared and actual volumes
3. **Real-time Tracking:** GPS position must be available within 30 seconds
4. **Offline Resilience:** 72-hour battery backup, local data buffering
5. **Safety Compliance:** ATEX Zone 1/2 restrictions on electronics

### Metrics & KPIs

| Metric | Target |
|--------|--------|
| Sensor accuracy | ±1% of nominal volume |
| GPS precision | <5m CEP50 |
| Battery autonomy | 72h minimum |
| Data latency | <30 seconds |
| Platform availability | >99.5% SLA |

### Stakeholder Roles

| Role | Responsibility |
|------|----------------|
| CSPH | Regulatory oversight, receives statistics |
| Marketers | Distribution operations, data transmission |
| Dépôts | Loading and storage sites |
| Chauffeurs | Drivers, operate embedded units |
| Clients | End recipients (enterprises) |
| DTI Solutions | System integrator, platform administrator |

### Common Abbreviations

- **LPG** = Gaz de Pétrole Liquéfié
- **GPS** = Global Positioning System
- **4G/LTE** = Fourth Generation / Long Term Evolution
- **RFID** = Radio Frequency Identification
- **PDA** = Personal Digital Assistant
- **SLA** = Service Level Agreement
- **CEP50** = Circular Error Probable 50%
