# Architecture Réseau CSPH — VPC, sous-réseaux, flux

> **Source :** [`PARTIE_VI.md`](../../PARTIE_VI.md) §15 (Backend) et §18 (Sécurité)
> **Fichier eraser :** [`network.eraser`](./network.eraser)

## Description

Architecture réseau multi-VPC avec isolation par sous-réseaux, flux chiffrés TLS 1.3, et plan de reprise d'activité (DR) vers un VPC secondaire.

## VPC Principal (10.0.0.0/16)

| Subnet | CIDR | Composants |
|--------|------|------------|
| **Public** | 10.0.1.0/24 | NAT Gateway, Bastion Host |
| **DMZ** | 10.0.2.0/24 | HAProxy L4, Kong API Gateway, WAF |
| **MQTT Privé** | 10.0.10.0/24 | EMQX Cluster (3 nœuds) |
| **Services** | 10.0.20.0/24 | Service Mesh (9 microservices) |
| **Données** | 10.0.30.0/24 | PostgreSQL, TimescaleDB, Redis, MinIO S3 |
| **Sécurité** | 10.0.40.0/24 | HashiCorp Vault, PKI Engine, HSM, SIEM |
| **Observabilité** | 10.0.50.0/24 | Prometheus, Grafana, Loki, OpenTelemetry |

## VPC DR (10.1.0.0/16)

| Subnet | CIDR | Composants |
|--------|------|------------|
| **DR** | 10.1.30.0/24 | PostgreSQL Standby, MinIO S3 Replica |

## Flux réseau principaux

| Source | Destination | Protocole | Port |
|--------|-------------|-----------|------|
| Internet | HAProxy L4 | TCP | 443 |
| Internet | Kong API Gateway | HTTPS | 443 |
| HAProxy L4 | EMQX Cluster | MQTTS | 8883 |
| Kong API Gateway | Service Mesh | HTTPS | 8000-8100 |
| svc-ingestion-camions | EMQX Cluster | subscribe | — |
| PostgreSQL Primary | PostgreSQL Replica | streaming | 5432 |
| TimescaleDB Primary | TimescaleDB Replica | streaming | 5432 |
| Redis Master | Redis Replica | replication | 6379 |
| Service Mesh | HashiCorp Vault | HTTPS | 8200 |
| Service Mesh | OpenTelemetry Collector | OTLP | 4317 |
| PostgreSQL Primary | PostgreSQL Standby (DR) | WAL streaming | 5432 |
| MinIO S3 | MinIO S3 Replica (DR) | async replication | — |

## VPN Site-to-Site

| Tunnel | Destination |
|--------|-------------|
| VPN CSPH S2S | Bastion Host (IPsec) |
| VPN DTI S2S | Bastion Host (IPsec) |

## Sécurité réseau

| Mécanisme | Implémentation |
|-----------|----------------|
| Chiffrement en transit | TLS 1.3 partout (devices ↔ broker, clients ↔ API, services ↔ DB) |
| Segmentation | VLAN isolés par subnet, security groups stricts |
| Accès externe | Bastion SSH + VPN uniquement |
| WAF | Cloudflare (DDoS + bot detection) |
| mTLS interne | Service mesh (Istio) ou sidecars |

## Voir aussi

- [Diagramme eraser Architecture Cloud](./architecture-cloud.md)
- [Diagramme eraser Kubernetes](./kubernetes-topology.md)
- [Diagramme eraser Sécurité](./security-zones.md)
- [Section Backup/RPO/RTO](../../PARTIE_VI.md#166-backup-rétention--rpofto)
