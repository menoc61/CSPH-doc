# AGENTS.md - CSPH GPL Tracking System

## Project Overview

**Système de Traçabilité du Gaz Hors Réseau** - IoT-based monitoring solution for LPG distribution in Cameroon.

**Client:** Caisse de Stabilisation des Prix des Hydrocarbures (CSPH)
**Integrator:** DTI Solutions and Services

## Domain Context

This project implements a dual-track traceability system:

1. **Flux 1 - Gaz Vrac (Bulk Gas):** IoT prototype embedded on semi-trailer tank trucks
   - GPS tracking, volume sensors (Hall effect), 4G/LTE communication
   - Advantech ADAM-6717 gateway + Teltonika RUT241 router
   
2. **Flux 2 - Bouteilles 50kg:** RFID/PDA traceability for bottled gas
   - UHF RFID tags (EPC Gen2), 4G PDA terminals
   - Individual bottle lifecycle tracking

## Agent Skills

### Issue Tracker

Local markdown files under `.scratch/` directory. See `docs/agents/issue-tracker.md`.

### Triage Labels

- `needs-triage` — maintainer needs to evaluate
- `needs-info` — waiting on reporter
- `ready-for-agent` — fully specified, AFK-ready
- `ready-for-human` — needs human implementation
- `wontfix` — will not be actioned

See `docs/agents/triage-labels.md`.

### Domain Docs

Single-context layout. `CONTEXT.md` at root with domain language. See `docs/agents/domain.md`.

## Project-Specific Skills

### IoT & Embedded

| Skill | Purpose | Trigger Keywords |
|-------|---------|------------------|
| `iot-development` | ADAM-6717, Node-RED, MQTT, sensors | ADAM, Node-RED, MQTT, sensor, GPS, firmware |
| `node-red-flows` | Modbus flows, data processing | flow, Modbus, function node, debug |
| `mqtt-protocol` | MQTT topics, message formats | MQTT, topic, publish, subscribe, QoS |
| `hardware-integration` | Installation, ATEX/ADR compliance | hardware, sensor, calibration, ATEX, wiring |

### Traceability & RFID

| Skill | Purpose | Trigger Keywords |
|-------|---------|------------------|
| `rfid-pda-traceability` | Bottle tracking, PDA scanning | RFID, PDA, bottle, scan, delivery, EPC |

### Cloud & API

| Skill | Purpose | Trigger Keywords |
|-------|---------|------------------|
| `api-design-ciph` | REST endpoints, WebSocket | API, endpoint, REST, WebSocket, route |
| `dashboard-design` | Supervision UI, maps, charts | dashboard, UI, map, chart, visualization |
| `data-validation` | Sensor data validation | validation, data quality, checksum, verify |

### Operations

| Skill | Purpose | Trigger Keywords |
|-------|---------|------------------|
| `capacity-planning` | Dimensioning, scaling | capacity, dimensioning, scaling, storage |
| `alert-system` | Anomaly detection, alerts | alert, anomaly, detection, notification |
| `security-compliance` | ATEX, ADR, encryption | security, ATEX, ADR, encryption, compliance |

### Diagrams

| Skill | Purpose | Trigger Keywords |
|-------|---------|------------------|
| `eraser-diagrams` | eraser.io diagram-as-code (cloud, K8s, security, network) | eraser, diagram-as-code, cloud architecture, k8s topology, network diagram, .eraser file |

### PDF & Document Generation

| Skill | Purpose | Trigger Keywords |
|-------|---------|------------------|
| `pdf` | Read, create, merge, split, watermark, OCR, form fill | PDF, .pdf, extract, merge, split, OCR, fill form |
| `pdf-generator` | Professional Kimi-quality PDFs (reports, invoices) | generate PDF, professional PDF, invoice, certificate |
| `html-to-pdf` | Pixel-perfect HTML to PDF (Puppeteer) | HTML to PDF, convert HTML, headless Chrome, RTL |
| `markdown-to-pdf` | Markdown to PDF via Pandoc/LaTeX | markdown to PDF, pandoc, technical doc |
| `pdf-form-filler` | Fill PDF AcroForms from JSON/CSV/database | fill PDF form, form automation, AcroForm |
| `pdf-review` | Validate, QA, accessibility check | review PDF, validate, QA, render check |

## Architecture Decision Records (ADR)

> Toute décision structurante est tracée dans [`docs/adr/`](./docs/adr/README.md).

| ADR | Titre | Statut |
|-----|-------|--------|
| [ADR-0001](./docs/adr/0001-use-bun-runtime.md) | Adopter Bun comme runtime backend | ✅ Accepted |
| [ADR-0002](./docs/adr/0002-hybrid-diagrams-mermaid-eraser.md) | Stratégie hybride Mermaid + eraser.io | ✅ Accepted |
| [ADR-0003](./docs/adr/0003-gitflow-conventional-commits.md) | Workflow Git (Gitflow + Conventional Commits) | ✅ Accepted |

## Documentation Structure

| Répertoire | Rôle |
|------------|------|
| [`docs/00-INDEX.md`](./docs/00-INDEX.md) | Master TOC du projet |
| [`docs/PARTIE_*.md`](./docs/) | Livrables techniques principaux |
| [`docs/parties/`](./docs/parties/) | PARTIES futures (TEMPLATE, CONVENTIONS, INDEX) |
| [`docs/adr/`](./docs/adr/) | Architecture Decision Records |
| [`docs/diagrams/`](./docs/diagrams/) | Mermaid (inline) + eraser.io (`.eraser`) |
| [`docs/agents/`](./docs/agents/) | Config agent (issues, triage, domaine) |
| [`docs/superpowers/specs/`](./docs/superpowers/specs/) | Specs design (brainstorming) |
| [`docs/superpowers/plans/`](./docs/superpowers/plans/) | Plans d'implémentation (writing-plans) |
| [`CONTRIBUTING.md`](./CONTRIBUTING.md) | Guide du contributeur |
| [`CHANGELOG.md`](./CHANGELOG.md) | Historique des versions |
| [`.github/`](./.github/) | Templates issues, PR, CI |

## Technical Stack

- **Embedded:** Advantech ADAM-6717, Node-RED, Modbus TCP/RTU
- **Sensors:** Hall effect (4-20mA), GPS, temperature, pressure
- **Communication:** 4G/LTE, MQTT, WebSocket, Satellite (Iridium/Orbcomm)
- **Cloud:** REST API, PostgreSQL, real-time dashboard
- **Security:** ATEX certification, ADR compliance, E2E encryption

## API Endpoints

Key routes defined in `docs/SystemDesignDoc.md`:
- `/v1/ingest/frame` - Sensor data ingestion (single frame)
- `/v1/ingest/batch` - Batch frame ingestion (store & forward)
- `/v1/tracking/live` - Real-time GPS tracking
- `/v1/tracking/:truck_id` - Position history
- `/v1/tracking/stream` - WebSocket position feed
- `/v1/trucks` - Fleet management
- `/v1/trucks/:id` - Truck details
- `/v1/trucks/:id/sensors` - Sensor history
- `/v1/alerts` - Alert management
- `/v1/alerts/:id/ack` - Alert acknowledgment
- `/v1/trips` - Route planning
- `/v1/trips/:id/replay` - GPS trace replay

## Data Flow

```
Tank Truck → Hall Sensor → ADAM-6717 → MQTT → Cloud API → Dashboard
                                                      ↓
Bottle PDA → RFID Scan → 4G → Cloud API → Alert System
```

## Code Conventions

- French technical documentation (industry standard for Cameroon)
- Metric units throughout
- ISO 8601 timestamps
- UTC timezone for all server operations
- JSON format for all API payloads

## Commands

```bash
# Development
npm run dev          # Start dev server
npm run build        # Build for production
npm run test         # Run test suite
npm run lint         # Run linter

# IoT Testing
npm run test:sensors    # Sensor integration tests
npm run test:mqtt       # MQTT broker tests
npm run test:api        # API endpoint tests

# Data Processing
python scripts/process_frames.py       # Process sensor data
python scripts/validate_deliveries.py  # Validate deliveries
```

## Quick Reference

- **Main Document:** `docs/SystemDesignDoc.md`
- **API Specification:** See section 3.5 in SystemDesignDoc
- **Hardware Specs:** Advantech ADAM-6717, Teltonika RUT241
- **Sensor:** Hall effect, 4-20mA, Rochester model
- **Fleet Size:** ~500 active trucks
- **Data Retention:** 12 months (regulatory requirement)
