# CSPH GPL Tracking System

Système de Traçabilité du Gaz Hors Réseau pour la Caisse de Stabilisation des Prix des Hydrocarbures (CSPH).

## Quick Start

### Prerequisites

- [opencode](https://opencode.ai) installed
- [Bun](https://bun.sh) 1.x (backend runtime)
- Node.js 18+ (for tooling)
- Python 3.10+ (for data processing)
- Git

### Setup

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd workspace
   ```

2. **Install opencode** (if not already installed)
   ```bash
   npm install -g opencode
   ```

3. **Install Bun** (backend runtime)
   ```bash
   curl -fsSL https://bun.sh/install | bash
   ```

4. **Start opencode**
   ```bash
   opencode
   ```

That's it! opencode will automatically load:
- `AGENTS.md` - Project configuration
- `CONTEXT.md` - Domain language & glossary
- `.opencode/skills/` - 18 project-specific skills (IoT, RFID, API, dashboard, alerts, security, PDF, eraser)
- `.agents/skills/` - Global skills (architecture, devops, Playwright, etc.)
- MCP `eraser` for diagram-as-code

---

## How Our Documentation Works

This project uses a **structured documentation system** designed to function as an **online reference** where anyone can find internal information about the integrated system.

### Entry Point

**[`docs/00-INDEX.md`](docs/00-INDEX.md)** is the single entry point to all documentation. Start here to navigate the entire technical documentation.

### Documentation Architecture

```
docs/
├── 00-INDEX.md                 ← Master TOC (start here)
├── PARTIE_VI.md                ← Main technical document (~3200 lines)
├── SystemDesignDoc.md          ← Source design document
├── parties/                    ← Technical deliverables (PARTIEs)
│   ├── INDEX.md                ← Registry of all PARTIEs
│   ├── TEMPLATE.md             ← Reusable skeleton
│   ├── CONVENTIONS.md          ← Rules for creating a PARTIE
│   └── README.md               ← Overview
├── adr/                        ← Architecture Decision Records
│   ├── 0001-use-bun-runtime.md
│   ├── 0002-hybrid-diagrams-mermaid-eraser.md
│   └── 0003-gitflow-conventional-commits.md
├── diagrams/                   ← All system diagrams
│   ├── INDEX.md                ← Diagram index & selection guide
│   └── eraser/                 ← eraser.io diagrams + markdown docs
│       ├── architecture-cloud.eraser + .md
│       ├── kubernetes-topology.eraser + .md
│       ├── security-zones.eraser + .md
│       └── network.eraser + .md
├── agents/                     ← Agent configuration
└── superpowers/                ← Specs & implementation plans
```

### What is a PARTIE?

A **PARTIE** is a major technical deliverable (~500-3000 lines). Each PARTIE covers one complete aspect of the system:

| PARTIE | Title | Status |
|--------|-------|--------|
| **VI** | Conception du système (HLD & Backend) | Published |
| VII | Dimensionnement & Capacity Planning | Planned |
| VIII | Opérations, Observabilité & SRE | Planned |
| IX | Déploiement, CI/CD & Migration | Planned |
| X | Résilience, Reprise après sinistre & Tests | Planned |
| XI | Conformité, Réglementaire & Audit | Planned |
| XII | Coûts, TCO & Modèle économique | Planned |

Each PARTIE follows a strict template ([`TEMPLATE.md`](docs/parties/TEMPLATE.md)) with mandatory sections, cross-references, and frontmatter. See [`CONVENTIONS.md`](docs/parties/CONVENTIONS.md) for the full rules.

### Diagram System (Mermaid + eraser.io)

We use a **hybrid diagram strategy** (see [ADR-0002](docs/adr/0002-hybrid-diagrams-mermaid-eraser.md)):

#### Mermaid (inline in PARTIE_VI.md)

14 Mermaid diagrams are embedded directly in `PARTIE_VI.md` sections:

| # | Diagram | Section |
|---|---------|---------|
| 1 | HLD 5 layers | §13.1 |
| 2 | Flux 1 vs Flux 2 | §13.1 |
| 3 | EMQX Cluster Raft | §14.3.1 |
| 4 | MQTT Topics hierarchy | §14.2.1 |
| 5 | Microservices overview | §15.1.3 |
| 6 | Kafka Bus topology | §15.1.3 |
| 7 | ER PostgreSQL | §16.4 |
| 8 | TimescaleDB partitioning | §16.1 |
| 9 | Fraud detection pipeline | §17.3 |
| 10 | PKI X.509/mTLS flow | §18.1.2 |
| 11 | RBAC matrix | §18.2.1 |
| 12 | Audit hash chain | §18.4.3 |
| 13 | Delivery sequence (Flux 1) | §17.5 |
| 14 | Delivery sequence (Flux 2) | §17.5 |

**Rendering**: Mermaid diagrams render automatically in:
- opencode (inline)
- GitHub (native)
- VS Code (with Markdown Preview Mermaid Support extension)
- Any Mermaid-compatible viewer

#### eraser.io (cloud/K8s/security/network)

4 eraser.io diagrams for complex infrastructure visuals:

| Diagram | File | Documentation |
|---------|------|---------------|
| Architecture Cloud 5 layers | [`architecture-cloud.eraser`](docs/diagrams/eraser/architecture-cloud.eraser) | [`architecture-cloud.md`](docs/diagrams/eraser/architecture-cloud.md) |
| Kubernetes 3 zones HA | [`kubernetes-topology.eraser`](docs/diagrams/eraser/kubernetes-topology.eraser) | [`kubernetes-topology.md`](docs/diagrams/eraser/kubernetes-topology.md) |
| Security zones ATEX/ADR/mTLS | [`security-zones.eraser`](docs/diagrams/eraser/security-zones.eraser) | [`security-zones.md`](docs/diagrams/eraser/security-zones.md) |
| Network VPC/subnets | [`network.eraser`](docs/diagrams/eraser/network.eraser) | [`network.md`](docs/diagrams/eraser/network.md) |

**Rendering**: Open `.eraser` files on [app.eraser.io](https://app.eraser.io) or via the MCP eraser plugin configured in `opencode.json`. Each eraser diagram has a companion `.md` file with full description, component tables, and cross-references.

### How to Navigate

1. **General overview** → Start with [`docs/00-INDEX.md`](docs/00-INDEX.md)
2. **Technical deep-dive** → Read [`docs/PARTIE_VI.md`](docs/PARTIE_VI.md) (HLD, MQTT, Backend, DB, Dashboard, Security)
3. **Domain language** → Check [`CONTEXT.md`](CONTEXT.md) for glossary
4. **Decisions** → Browse [`docs/adr/`](docs/adr/) for architectural decisions
5. **Diagrams** → See [`docs/diagrams/INDEX.md`](docs/diagrams/INDEX.md) for selection guide
6. **How to contribute** → Read [`CONTRIBUTING.md`](CONTRIBUTING.md)

### Creating Documentation

To add a new PARTIE:
```bash
cp docs/parties/TEMPLATE.md docs/parties/PARTIE_VII_dimensionnement.md
# Follow the template and CONVENTIONS.md rules
```

To add a new diagram:
- **Mermaid**: Add inline in the relevant PARTIE section (§N)
- **eraser.io**: Create `.eraser` file in `docs/diagrams/eraser/` + companion `.md` doc

---

## Project Structure

```
workspace/
├── AGENTS.md                       # Agent configuration
├── CONTEXT.md                      # Domain language & glossary
├── CONTRIBUTING.md                 # Contributor guide
├── CHANGELOG.md                    # Version history
├── README.md                       # This file
├── opencode.json                   # opencode project config
├── docs/
│   ├── 00-INDEX.md                 # Master TOC
│   ├── SystemDesignDoc.md          # Source design document
│   ├── PARTIE_VI.md                # HLD & Backend (published, ~3200 lines)
│   ├── parties/                    # PARTIEs (current + future)
│   ├── adr/                        # Architecture Decision Records
│   ├── diagrams/                   # Diagrams (Mermaid + eraser.io)
│   ├── agents/                     # Agent config
│   └── superpowers/                # Specs & plans
├── .opencode/
│   └── skills/                     # 18 project-specific skills
└── .github/                        # GitHub config (CI, templates)
```

## Available Skills

### IoT & Embedded

| Skill | Purpose |
|-------|---------|
| `iot-development` | ADAM-6717, Node-RED, MQTT, sensor integration |
| `node-red-flows` | Modbus flows, data processing |
| `mqtt-protocol` | MQTT topics, message formats, QoS |
| `hardware-integration` | Sensor installation, ATEX/ADR compliance |

### Traceability & RFID

| Skill | Purpose |
|-------|---------|
| `rfid-pda-traceability` | Bottle tracking, PDA scanning |

### Cloud & API

| Skill | Purpose |
|-------|---------|
| `api-design-ciph` | REST endpoints, WebSocket, data ingestion |
| `dashboard-design` | Supervision UI, maps, charts |
| `data-validation` | Sensor data validation |

### Operations

| Skill | Purpose |
|-------|---------|
| `capacity-planning` | Dimensioning, scaling |
| `alert-system` | Anomaly detection, alerts |
| `security-compliance` | ATEX, ADR, encryption, compliance |

### Diagrams

| Skill | Purpose |
|-------|---------|
| `eraser-diagrams` | eraser.io diagram-as-code (cloud, K8s, network, security) |

### PDF & Document Generation

| Skill | Purpose |
|-------|---------|
| `pdf` | Read, create, merge, split, watermark, OCR, form fill |
| `pdf-generator` | Professional Kimi-quality PDFs |
| `html-to-pdf` | Pixel-perfect HTML to PDF (Puppeteer) |
| `markdown-to-pdf` | Markdown to PDF via Pandoc/LaTeX |
| `pdf-form-filler` | Fill PDF AcroForms |
| `pdf-review` | Validate, QA, accessibility check |

## Tech Stack

- **Backend** : [Bun 1.x](https://bun.sh) (TypeScript) — [ADR-0001](docs/adr/0001-use-bun-runtime.md)
- **MQTT** : EMQX 5.x (3-node Raft cluster)
- **Event bus** : Apache Kafka
- **Time-series DB** : TimescaleDB
- **DB métier** : PostgreSQL 16
- **Cache** : Redis
- **API Gateway** : Kong
- **Load Balancer** : HAProxy
- **Frontend** : Next.js 14 + TypeScript
- **Diagrams** : Mermaid (inline) + eraser.io (infrastructure) — [ADR-0002](docs/adr/0002-hybrid-diagrams-mermaid-eraser.md)
- **CI/CD** : GitHub Actions
- **Git Workflow** : Gitflow + Conventional Commits — [ADR-0003](docs/adr/0003-gitflow-conventional-commits.md)

## Usage Examples

### Working on IoT Firmware

```bash
opencode
# Then ask:
"Help me write a Node-RED flow for reading the Hall sensor"
```

### Designing API Endpoints

```bash
opencode
# Then ask:
"Design the /v1/ingest/frame endpoint for sensor data"
```

### Reading the Documentation

```bash
# Start with the master TOC
cat docs/00-INDEX.md

# Deep-dive into system design
cat docs/PARTIE_VI.md

# Check domain glossary
cat CONTEXT.md

# Browse architecture decisions
ls docs/adr/
```

### Generating Diagrams

```bash
# Mermaid — renders inline in PARTIE_VI.md
# No command needed, automatic in opencode/GitHub/VS Code

# eraser.io — open on https://app.eraser.io
# Or via MCP configured in opencode.json
# Markdown docs available in docs/diagrams/eraser/
```

## Issue Tracking

Issues are stored as markdown files in `.scratch/` directory.

```bash
mkdir -p .scratch/features
# Create .md file with frontmatter (status: needs-triage)
```

See [`docs/agents/issue-tracker.md`](docs/agents/issue-tracker.md) for details.

## Development Commands

```bash
# Backend (Bun)
bun run dev          # Start dev server
bun run build        # Build for production
bun test             # Run test suite
bun run lint         # Linter (eslint)
bun run typecheck    # TypeScript check

# Mermaid export
npx -p @mermaid-js/mermaid-cli mmdc -i diagram.md -o out.svg

# CI local
markdownlint '**/*.md' --ignore node_modules
lychee --offline docs/...
```

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for the full workflow (Git, commits, PR, ADR, PARTIEs).

## License

Proprietary — CSPH / DTI Solutions and Services.
