# Architecture Cloud CSPH — HLD 5 couches

> **Source :** [`PARTIE_VI.md`](../../PARTIE_VI.md) §13 (High Level Design)
> **ADR lié :** [ADR-0001 — Adopter Bun comme runtime backend](../../adr/0001-use-bun-runtime.md)
> **Fichier eraser :** [`architecture-cloud.eraser`](./architecture-cloud.eraser)

## Description

Vue d'ensemble de l'architecture du système de traçabilité CSPH, organisée en 5 couches séparées :

1. **Acquisition (Terrain)** — Capteurs IoT (camions-citernes ADAM-6717 + terminaux PDA RFID)
2. **Transport** — Réseau 4G/LTE, satellite Iridium, broker MQTT EMQX, bus Kafka
3. **Traitement** — HAProxy, Kong API Gateway, 9 microservices Bun
4. **Stockage** — TimescaleDB, PostgreSQL 16, Redis Cluster, MinIO S3, HashiCorp Vault
5. **Présentation** — Dashboard Web CSPH, app mobile chauffeur, app auditeur CSPH

## Composants clés

### Couche 1 — Acquisition

| Composant | Rôle |
|-----------|------|
| ADAM-6717 | Gateway IoT avec Node-RED, 8 canaux I/O |
| Capteur Hall | Mesure de niveau gaz 4-20mA |
| Module GPS | Géolocalisation temps réel |
| Teltonika RUT241 | Routeur 4G LTE industriel |
| Batterie LiFePO4 | Autonomie 72h minimum |
| Terminal PDA | Scanner RFID UHF pour bouteilles 50kg |

### Couche 2 — Transport

| Composant | Rôle |
|-----------|------|
| EMQX Cluster | Broker MQTT HA (3 nœuds Raft) |
| Apache Kafka | Bus d'événements (3 brokers) |
| Réseau 4G LTE | Communication principale terrain |
| Satellite Iridium | Fallback zones blanches |

### Couche 3 — Traitement

| Composant | Rôle |
|-----------|------|
| HAProxy | Load balancer L4/L7 |
| Kong API Gateway | API management, auth, rate-limiting |
| svc-auth | Authentification JWT |
| svc-ingestion-camions | Réception MQTT trames camions |
| svc-ingestion-bouteilles | Réception REST scans RFID |
| svc-tracking | Positions temps réel WebSocket |
| svc-rapprochement | Croisement volumes déclarés/mesurés |
| svc-alertes | Notification multi-canal |
| svc-rapports | Génération rapports péréquation |
| svc-admin | CRUD entités métier |

### Couche 4 — Stockage

| Composant | Rôle |
|-----------|------|
| TimescaleDB | Données capteurs (hypertables) |
| PostgreSQL 16 | Entités métier (relations) |
| Redis Cluster | Cache, sessions, rate-limiting |
| MinIO S3 | Archives WORM (audit trail) |
| HashiCorp Vault | Secrets, PKI, chiffrement |

### Couche 5 — Présentation

| Composant | Rôle |
|-----------|------|
| Dashboard Web CSPH | Supervision temps réel (Next.js 14) |
| Dashboard Marketer | Vue par marketer |
| App Mobile Chauffeur | Statut mission, scan RFID fallback |
| App Mobile Auditeur CSPH | Audit terrain |

## Flux de données

```
Terrain → MQTT/TLS 1.3 → EMQX → Kafka → Microservices → Bases de données → Dashboard
```

## Voir aussi

- [Diagramme Mermaid HLD](../../PARTIE_VI.md#131-diagramme-darchitecture-globale)
- [Diagramme eraser Kubernetes](./kubernetes-topology.md)
- [Diagramme eraser Sécurité](./security-zones.md)
- [Diagramme eraser Réseau](./network.md)
