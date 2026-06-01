# CSPH GPL Tracking System - Workspace

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
- `CONTEXT.md` - Domain language
- `.opencode/skills/` - 18 project-specific skills (IoT, RFID, API, dashboard, alerts, security, PDF, eraser)
- `.agents/skills/` - Global skills (architecture, devops, Playwright, etc.)
- MCP `eraser` for diagram-as-code

## Project Structure

```
workspace/
├── AGENTS.md                       # Agent configuration
├── CONTEXT.md                      # Domain language & glossary
├── CONTRIBUTING.md                 # Guide du contributeur
├── CHANGELOG.md                    # Historique des versions
├── README.md                       # Ce fichier
├── opencode.json                   # opencode project config
├── mcp.json                        # MCP server config
├── docs/
│   ├── 00-INDEX.md                 # Master TOC
│   ├── SystemDesignDoc.md          # Mémoire technique source
│   ├── PARTIE_VI.md                # HLD & Backend (publiée)
│   ├── parties/                    # PARTIES futures
│   │   ├── README.md
│   │   ├── TEMPLATE.md             # Squelette réutilisable
│   │   ├── CONVENTIONS.md          # Guide "comment créer une PARTIE"
│   │   └── INDEX.md                # TOC des PARTIES
│   ├── adr/                        # Architecture Decision Records
│   │   ├── README.md
│   │   ├── 0001-use-bun-runtime.md
│   │   ├── 0002-hybrid-diagrams-mermaid-eraser.md
│   │   └── 0003-gitflow-conventional-commits.md
│   ├── diagrams/
│   │   ├── INDEX.md
│   │   │   └── eraser/                 # 4 diagrammes eraser.io (.eraser) + docs markdown
│   │   │       ├── architecture-cloud.eraser
│   │   │       ├── architecture-cloud.md
│   │   │       ├── kubernetes-topology.eraser
│   │   │       ├── kubernetes-topology.md
│   │   │       ├── security-zones.eraser
│   │   │       ├── security-zones.md
│   │   │       ├── network.eraser
│   │   │       └── network.md
│   ├── agents/                     # Config agent (issues, triage, domain)
│   └── superpowers/                # Specs & plans
│       ├── specs/                  # Design docs
│       └── plans/                  # Implementation plans
├── .opencode/
│   └── skills/                     # 18 project-specific skills
│       ├── iot-development/
│       ├── node-red-flows/
│       ├── mqtt-protocol/
│       ├── hardware-integration/
│       ├── rfid-pda-traceability/
│       ├── api-design-ciph/
│       ├── dashboard-design/
│       ├── data-validation/
│       ├── capacity-planning/
│       ├── alert-system/
│       ├── security-compliance/
│       ├── eraser-diagrams/        # eraser.io skill
│       ├── pdf/
│       ├── pdf-generator/
│       ├── html-to-pdf/
│       ├── markdown-to-pdf/
│       ├── pdf-form-filler/
│       └── pdf-review/
├── .agents/
│   └── skills/                     # Global skills
└── .github/                        # GitHub config
    ├── CODEOWNERS
    ├── PULL_REQUEST_TEMPLATE.md
    ├── ISSUE_TEMPLATE/
    └── workflows/ci.yml
```

## Available Skills (18 project + global)

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

## Documentation

- **Master TOC** : [`docs/00-INDEX.md`](docs/00-INDEX.md) — porte d'entrée unique
- **Main technical doc** : [`docs/PARTIE_VI.md`](docs/PARTIE_VI.md) — HLD & Backend (~3200 lignes, 15 diagrammes Mermaid inline)
- **Mémoire technique source** : [`docs/SystemDesignDoc.md`](docs/SystemDesignDoc.md)
- **ADR** : [`docs/adr/`](docs/adr/) — décisions structurantes
- **Diagrammes** : [`docs/diagrams/`](docs/diagrams/) — Mermaid inline + eraser.io avec docs markdown
- **Domain language** : [`CONTEXT.md`](CONTEXT.md)

## Tech Stack

- **Backend** : [Bun 1.x](https://bun.sh) (TypeScript natif) — voir [ADR-0001](docs/adr/0001-use-bun-runtime.md)
- **MQTT** : EMQX 5.x (cluster 3 nœuds Raft)
- **Event bus** : Apache Kafka
- **Time-series DB** : TimescaleDB
- **DB métier** : PostgreSQL 16
- **Cache** : Redis
- **API Gateway** : Kong
- **Load Balancer** : HAProxy
- **Frontend** : Next.js 14 + TypeScript
- **Diagrammes** : Mermaid (inline dans PARTIE_VI.md) + eraser.io (techniques avec docs markdown) — voir [ADR-0002](docs/adr/0002-hybrid-diagrams-mermaid-eraser.md)
- **CI/CD** : GitHub Actions — voir [`.github/workflows/ci.yml`](.github/workflows/ci.yml)
- **Workflow Git** : Gitflow + Conventional Commits — voir [ADR-0003](docs/adr/0003-gitflow-conventional-commits.md)

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

### Creating a New PARTIE

```bash
# Copier le template
cp docs/parties/TEMPLATE.md docs/parties/PARTIE_VII_dimensionnement.md

# Suivre les conventions
code docs/parties/CONVENTIONS.md
```

### Generating Diagrams

```bash
# Mermaid (inline dans PARTIE_VI.md)
# Le rendu est automatique dans opencode, GitHub, VS Code

# eraser.io : ouvrir sur https://app.eraser.io
# ou via MCP configuré dans opencode.json
# Documentation markdown disponible dans docs/diagrams/eraser/
```

## Issue Tracking

Issues are stored as markdown files in `.scratch/` directory.

### Create an Issue

```bash
mkdir -p .scratch/features
# Créer un fichier .md avec frontmatter (status: needs-triage)
```

Voir [`docs/agents/issue-tracker.md`](docs/agents/issue-tracker.md) pour le détail.

### Templates GitHub

Si repo GitHub configuré, utiliser :
- [`.github/ISSUE_TEMPLATE/bug.md`](.github/ISSUE_TEMPLATE/bug.md)
- [`.github/ISSUE_TEMPLATE/feature.md`](.github/ISSUE_TEMPLATE/feature.md)
- [`.github/ISSUE_TEMPLATE/doc.md`](.github/ISSUE_TEMPLATE/doc.md)

## Development Commands

```bash
# Backend (Bun)
bun run dev          # Start dev server
bun run build        # Build for production
bun test             # Run test suite
bun run lint         # Linter (eslint)
bun run typecheck    # TypeScript check

# Diagrams
# Mermaid
npx -p @mermaid-js/mermaid-cli mmdc -i diagram.md -o out.svg
# eraser.io
# Voir https://app.eraser.io

# CI local
markdownlint '**/*.md' --ignore node_modules
lychee --offline docs/...
```

## Contributing

Voir [`CONTRIBUTING.md`](CONTRIBUTING.md) pour le workflow complet (Git, commits, PR, ADR, PARTIES).

## License

Proprietary — CSPH / DTI Solutions and Services.
