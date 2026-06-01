# Topologie Kubernetes CSPH — 3 zones haute disponibilité

> **Source :** [`PARTIE_VI.md`](../../PARTIE_VI.md) §13 (HLD) et §15 (Backend)
> **Fichier eraser :** [`kubernetes-topology.eraser`](./kubernetes-topology.eraser)

## Description

Architecture Kubernetes multi-zones pour garantir la haute disponibilité (SLA 99,5%). Le cluster est réparti sur 3 zones cloud (A primaire, B secondaire, C tertiaire) avec réplication synchrone/asynchrone des données.

## Topologie

### Edge

| Composant | Rôle |
|-----------|------|
| Cloudflare WAF | Protection DDoS + bot detection |
| CDN Statique | Cache des assets frontend |
| DDoS Protection | Mitigation attaques volumétriques |

### Zone A — Primaire

| Namespace | Composants |
|-----------|------------|
| ingress | HAProxy A, Kong A |
| mqtt | EMQX Node 1 |
| services | svc-auth, svc-ingestion-camions, svc-tracking |
| data | PostgreSQL Primary, TimescaleDB Primary, Redis Master, Kafka Broker 1 |

### Zone B — Secondaire

| Namespace | Composants |
|-----------|------------|
| ingress | HAProxy B, Kong B |
| mqtt | EMQX Node 2 |
| services | svc-rapprochement, svc-alertes, svc-rapports |
| data | PostgreSQL Replica, TimescaleDB Replica, Redis Replica, Kafka Broker 2 |

### Zone C — Tertiaire

| Namespace | Composants |
|-----------|------------|
| ingress | HAProxy C, Kong C |
| mqtt | EMQX Node 3 |
| services | svc-ingestion-bouteilles, svc-admin |
| data | PostgreSQL Replica, Kafka Broker 3, MinIO S3 |

## Réplications

| Composant | Mécanisme | Type |
|-----------|-----------|------|
| EMQX | Raft consensus | Actif-actif |
| Kafka | Replication Factor=3 | Actif-actif |
| PostgreSQL | Streaming replication | Actif-passif (1 sync + 1 async) |
| Redis | Async replication | Actif-passif |

## Procédure de bascule (N3 — perte zone A)

```bash
# 1. Promotion PostgreSQL replica B
patronictl -c /etc/patroni.yml failover --master pg-primary-a --candidate pg-replica-b

# 2. Bascule HAProxy (DNS update ou Consul)
consul-cli kv put service/postgres/leader pg-replica-b

# 3. Vérification health globale
./scripts/health-check-all-zones.sh
```

## Voir aussi

- [Diagramme eraser Architecture Cloud](./architecture-cloud.md)
- [Diagramme eraser Réseau](./network.md)
- [Section DR](../../PARTIE_VI.md#185-reprise-après-sinistre-dr)
