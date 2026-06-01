# Périmètre Sécurité CSPH — ATEX, ADR, mTLS

> **Source :** [`PARTIE_VI.md`](../../PARTIE_VI.md) §18 (Sécurité du système)
> **Fichier eraser :** [`security-zones.eraser`](./security-zones.eraser)

## Description

Périmètre de sécurité du système CSPH couvrant les zones physiques ATEX (atmosphères explosives), le transport de matières dangereuses (ADR), et l'authentification mutuelle mTLS pour tous les devices IoT.

## Zones physiques — Camion-citerne

| Zone | Description | Exigence |
|------|-------------|----------|
| **ATEX 0** | Gaz permanent (intérieur citerne) | Aucun électronique |
| **ATEX 1** | Gaz probable en fonctionnement normal (soupapes, évêts) | Électronique ATEX certifié |
| **ATEX 2** | Gaz peu probable (pourtour citerne) | Électronique ATEX recommandé |
| **Zone Sure** | Boîtier IoT, capteurs | Électronique non-ATEX tolérée |

## Zones réseau

| Zone | Composants | Accès |
|------|------------|-------|
| **Réseau WAN** | 4G LTE, Satellite, VPN MPLS | Chiffré TLS 1.3 |
| **DMZ** | HAProxy L4, Kong API Gateway, WAF, Bastion SSH | Public (HTTPS) |
| **Réseau Interne** | EMQX Cluster, Services Backend, Bases de Données, Vault, PKI | mTLS uniquement |
| **Audit & SIEM** | Audit Log, SIEM, Monitoring, WORM Storage | Append-only |

## Chaîne de trust mTLS

```
Device (ADAM-6717/PDA)
  → X.509 client cert (Vault PKI)
  → TLS 1.3 (4G LTE)
  → HAProxy L4
  → Kong API Gateway
  → EMQX Cluster
  → Services Backend
  → Bases de Données
```

## Composants sécurité

| Composant | Rôle |
|-----------|------|
| HashiCorp Vault | Gestion secrets + PKI Engine |
| PKI Engine | Émission/rotation certificats X.509 |
| HSM | Stockage clé racine CA (FIPS 140-2 Level 3) |
| SIEM | Corrélation événements sécurité |
| WORM Storage | Stockage immuable audit trail (12 ans) |

## Conformité

| Norme | Périmètre |
|-------|-----------|
| **ATEX** | Zones explosives GPL (Zone 0/1/2) |
| **ADR** | Transport matières dangereuses |
| **CEMAC** | Réglementation financière (rétention 12 ans) |
| **RGPD** | Données personnelles chauffeurs |

## Voir aussi

- [Diagramme eraser Architecture Cloud](./architecture-cloud.md)
- [Diagramme eraser Réseau](./network.md)
- [Section Sécurité](../../PARTIE_VI.md#18-sécurité-du-système)
- [Diagramme Mermaid PKI](../../PARTIE_VI.md#181-authentification-des-devices-certificats-x509)
