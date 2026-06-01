# Index des diagrammes

> Tous les diagrammes du projet, classés par type et outil.

## Légende outils

| Outil | Usage | Localisation | Rendu |
|-------|-------|--------------|-------|
| **Mermaid** | Flow, séquence, ER simple, gantt | Inline dans [`PARTIE_VI.md`](../PARTIE_VI.md) | Natif opencode / GitHub / VS Code |
| **eraser.io** | Architecture cloud, K8s, sécurité, réseau, ER complexe | [`eraser/`](./eraser/) + MCP [eraser](https://app.eraser.io) | Natif eraser.io + export PNG/SVG + docs markdown |

## Critère de choix

| Type de diagramme | Outil recommandé | Pourquoi |
|-------------------|------------------|----------|
| Flowchart simple (≤ 10 nœuds) | Mermaid | Suffisant, rendu inline |
| Séquence (≤ 5 acteurs) | Mermaid | Suffisant |
| Diagramme de classes / ER (≤ 10 tables) | Mermaid | Suffisant |
| Architecture cloud (AWS/Azure/GCP) | **eraser.io** | Icônes natives, riche |
| Topologie Kubernetes | **eraser.io** | Visualisation multi-zones |
| Périmètre sécurité / zones ATEX | **eraser.io** | Couches + annotations |
| Réseau (DMZ, subnets, flux) | **eraser.io** | Visualisation réseau |
| ER complexe (> 10 tables) | **eraser.io** | Meilleure lisibilité |
| Diagramme « présentation CSPH / ministère » | **eraser.io** | Qualité visuelle |

Voir [ADR-0002](../adr/0002-hybrid-diagrams-mermaid-eraser.md) pour la justification complète.

## Mermaid — Inline dans `PARTIE_VI.md` (15 diagrammes)

| # | Diagramme | Type | Section |
|---|-----------|------|---------|
| 1 | HLD 5 couches | flowchart | §13.1 |
| 2 | Flux 1 vs Flux 2 | flowchart | §13.1 |
| 3 | Cluster EMQX Raft 3 nœuds | flowchart | §14.3.1 |
| 4 | Hiérarchie topics MQTT | graph TD | §14.2.1 |
| 5 | Microservices backend | flowchart | §15.1.3 |
| 6 | Bus Kafka — topologie & topics | flowchart | §15.1.3 |
| 7 | ER PostgreSQL métier | erDiagram | §16.4 |
| 8 | Partitionnement TimescaleDB | graph TB | §16.1 |
| 9 | Pipeline détection de fraude | flowchart | §17.3 |
| 10 | Flux PKI X.509 / mTLS | sequence | §18.1.2 |
| 11 | Matrice RBAC | flowchart | §18.2.1 |
| 12 | Chaîne de hash audit | flowchart | §18.4.3 |
| 13 | Séquence livraison Flux 1 | sequence | §17.5 |
| 14 | Séquence livraison Flux 2 | sequence | §17.5 |

## eraser.io — `docs/diagrams/eraser/` (4 diagrammes)

| Fichier | Contenu | Doc markdown | Status |
|---------|---------|--------------|--------|
| [`architecture-cloud.eraser`](./eraser/architecture-cloud.eraser) | HLD 5 couches avec icônes cloud | [`architecture-cloud.md`](./eraser/architecture-cloud.md) | ✅ Créé |
| [`kubernetes-topology.eraser`](./eraser/kubernetes-topology.eraser) | Cluster K8s 3 zones, StatefulSets | [`kubernetes-topology.md`](./eraser/kubernetes-topology.md) | ✅ Créé |
| [`security-zones.eraser`](./eraser/security-zones.eraser) | Périmètre sécurité, zones ATEX | [`security-zones.md`](./eraser/security-zones.md) | ✅ Créé |
| [`network.eraser`](./eraser/network.eraser) | DMZ, sous-réseaux, flux | [`network.md`](./eraser/network.md) | ✅ Créé |

## Convention de nommage

| Type | Format | Exemple |
|------|--------|---------|
| Mermaid | Inline dans `PARTIE_X.md` | Section `### 13.1` |
| eraser.io | `<domaine>-<sous-domaine>.eraser` + `.md` | `architecture-cloud.eraser` + `architecture-cloud.md` |

## Rendu & export

```bash
# Mermaid → PNG/SVG (depuis PARTIE_VI.md)
npx -p @mermaid-js/mermaid-cli mmdc -i PARTIE_VI.md -o out.svg

# eraser.io → PNG/SVG (via MCP ou web UI)
# Se connecter sur https://app.eraser.io, ouvrir le fichier, exporter
```

## Validation CI

La CI GitHub Actions (`.github/workflows/ci.yml`) vérifie :

- Tous les fichiers `.eraser` sont parsables (syntax check)
- Tous les blocs Mermaid se rendent sans erreur
- Tous les chemins référencés depuis les PARTIES existent
