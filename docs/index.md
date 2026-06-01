---
layout: default
title: CSPH GPL Tracking System
---

# CSPH GPL Tracking System

**Système de Traçabilité du Gaz Hors Réseau**

Documentation technique du système de traçabilité IoT pour la Caisse de Stabilisation des Prix des Hydrocarbures (CSPH) — Cameroun.

---

## Accès rapide

| Document | Description |
|----------|-------------|
| [**00-INDEX**]({{ site.baseurl }}/docs/00-INDEX) | Table des matières complète — point d'entrée unique |
| [**PARTIE VI — HLD & Backend**]({{ site.baseurl }}/docs/PARTIE_VI) | Document technique principal (~3200 lignes, 14 diagrammes Mermaid) |
| [**CONTEXT.md**]({{ site.baseurl }}/CONTEXT) | Glossaire et langue du domaine |
| [**SystemDesignDoc**]({{ site.baseurl }}/docs/SystemDesignDoc) | Mémoire technique source |

---

## Architecture du système

Le système repose sur **5 couches** séparées :

```
Terrain (IoT) → Transport (MQTT/Kafka) → Traitement (Microservices) → Stockage (DB) → Présentation (Dashboard)
```

### Flux de données

| Flux | Source | Destination | Protocole |
|------|--------|-------------|-----------|
| **Flux 1 — Gaz Vrac** | Camions-citernes ADAM-6717 | EMQX → Kafka → Backend | MQTT/TLS 1.3 |
| **Flux 2 — Bouteilles 50kg** | Terminaux PDA RFID | REST API → Backend | HTTPS/4G |

### Tech Stack

| Composant | Technologie |
|-----------|-------------|
| Backend | Bun 1.x (TypeScript) |
| MQTT Broker | EMQX 5.x (cluster 3 nœuds) |
| Event Bus | Apache Kafka |
| Time-Series DB | TimescaleDB |
| DB métier | PostgreSQL 16 |
| Cache | Redis |
| API Gateway | Kong |
| Load Balancer | HAProxy |
| Frontend | Next.js 14 + TypeScript |
| Diagrammes | Mermaid + eraser.io |

---

## Documentation technique

### PARTIEs du mémoire technique

| PARTIE | Titre | Statut |
|--------|-------|--------|
| [**VI**]({{ site.baseurl }}/docs/PARTIE_VI) | Conception du système (HLD & Backend) | ✅ Publié |
| VII | Dimensionnement & Capacity Planning | 📋 Planifié |
| VIII | Opérations, Observabilité & SRE | 📋 Planifié |
| IX | Déploiement, CI/CD & Migration | 📋 Planifié |
| X | Résilience, Reprise après sinistre & Tests | 📋 Planifié |
| XI | Conformité, Réglementaire & Audit | 📋 Planifié |
| XII | Coûts, TCO & Modèle économique | 📋 Planifié |

### Architecture Decision Records (ADR)

| ADR | Titre | Statut |
|-----|-------|--------|
| [**0001**]({{ site.baseurl }}/docs/adr/0001-use-bun-runtime) | Adopter Bun comme runtime backend | ✅ Accepted |
| [**0002**]({{ site.baseurl }}/docs/adr/0002-hybrid-diagrams-mermaid-eraser) | Stratégie hybride Mermaid + eraser.io | ✅ Accepted |
| [**0003**]({{ site.baseurl }}/docs/adr/0003-gitflow-conventional-commits) | Workflow Git (Gitflow + Conventional Commits) | ✅ Accepted |

### Diagrammes

| Catégorie | Nombre | Emplacement |
|-----------|--------|-------------|
| Mermaid (inline) | 14 | Dans [`PARTIE_VI.md`]({{ site.baseurl }}/docs/PARTIE_VI) (sections §13-§18) |
| eraser.io | 4 | [`docs/diagrams/eraser/`]({{ site.baseurl }}/docs/diagrams/eraser/) |

**Sélection du diagramme** : Voir [`diagrams/INDEX.md`]({{ site.baseurl }}/docs/diagrams/INDEX) pour le guide de choix Mermaid vs eraser.io.

---

## Navigation

### Par thème

| Thème | Sections dans PARTIE VI |
|-------|------------------------|
| **Architecture** | [§13 — HLD]({{ site.baseurl }}/docs/PARTIE_VI#13-high-level-design-hld) |
| **MQTT** | [§14 — Broker de messages]({{ site.baseurl }}/docs/PARTIE_VI#14-broker-de-messages-mqtt) |
| **Backend** | [§15 — Microservices & traitement]({{ site.baseurl }}/docs/PARTIE_VI#15-backend--traitement-des-données) |
| **Bases de données** | [§16 — TimescaleDB, PostgreSQL, Redis]({{ site.baseurl }}/docs/PARTIE_VI#16-bases-de-données) |
| **Dashboard** | [§17 — Reporting & détection fraude]({{ site.baseurl }}/docs/PARTIE_VI#17-tableau-de-bord--reporting-csph) |
| **Sécurité** | [§18 — PKI, RBAC, audit, DR]({{ site.baseurl }}/docs/PARTIE_VI#18-sécurité-du-système) |

### Par rôle

| Je suis... | Commencer par |
|------------|---------------|
| **Développeur backend** | [§15 — Backend]({{ site.baseurl }}/docs/PARTIE_VI#15-backend--traitement-des-données) |
| **Ingénieur IoT** | [§13 — HLD]({{ site.baseurl }}/docs/PARTIE_VI#13-high-level-design-hld) + [§14 — MQTT]({{ site.baseurl }}/docs/PARTIE_VI#14-broker-de-messages-mqtt) |
| **DevOps/SRE** | [§18.5 — DR]({{ site.baseurl }}/docs/PARTIE_VI#185-reprise-après-sinistre-dr) + [§18.6 — Observabilité]({{ site.baseurl }}/docs/PARTIE_VI#186-observabilité--sre) |
| **Chef de projet** | [00-INDEX]({{ site.baseurl }}/docs/00-INDEX) + [Conclusion]({{ site.baseurl }}/docs/PARTIE_VI#conclusion-de-la-partie-vi) |
| **Auditeur sécurité** | [§18 — Sécurité]({{ site.baseurl }}/docs/PARTIE_VI#18-sécurité-du-système) |

---

## Contribution

Voir [CONTRIBUTING.md](https://github.com/menoc61/CSPH-doc/blob/master/CONTRIBUTING.md) pour le workflow complet.

### Créer une nouvelle PARTIE

```bash
cp docs/parties/TEMPLATE.md docs/parties/PARTIE_VII_dimensionnement.md
# Suivre les conventions dans docs/parties/CONVENTIONS.md
```

### Ajouter un diagramme

- **Mermaid** : Ajouter inline dans la section correspondante de `PARTIE_VI.md`
- **eraser.io** : Créer un fichier `.eraser` dans `docs/diagrams/eraser/` + documentation `.md`

---

## Licence

Propriétaire — CSPH / DTI Solutions and Services.
