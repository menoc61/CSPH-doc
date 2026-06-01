# 0002. Stratégie hybride Mermaid + eraser.io

- **Statut** : accepted
- **Date** : 2026-06-01
- **Auteur** : Équipe DTI Solutions
- **Décideurs** : Lead doc DTI, Lead architecture DTI, CSPH (relecture présentation)

## Contexte et énoncé du problème

Le projet CSPH produit de nombreux diagrammes :
- 15+ diagrammes techniques (HLD, MQTT, microservices, ER, K8s, etc.)
- Diagrammes destinés à des **audiences mixtes** :
  - Équipe technique (devs, ops) : besoin de lisibilité inline dans le code/docs
  - CSPH, ministère, auditeurs : besoin de **diagrammes présentables**, imprimables
  - Sous-traitants / nouveaux membres : besoin d'**icônes standards** (cloud, K8s, etc.)

Il faut choisir entre :
- Un outil unique (simple, mais limité)
- Deux outils (puissant, mais risque de double source de vérité)

## Options considérées

### Option 1 — Mermaid uniquement

- ✅ Rendu natif opencode, GitHub, VS Code
- ✅ Markdown inline, pas de fichier séparé
- ✅ Zéro dépendance externe
- ❌ Pas d'icônes cloud/K8s natives
- ❌ Diagrammes complexes (> 15 nœuds) deviennent illisibles
- ❌ Qualité visuelle moyenne pour présentation CSPH
- ❌ Pas de notion de couches/zones riche

### Option 2 — eraser.io uniquement

- ✅ Diagram-as-code versionné (`.eraser`)
- ✅ Icônes cloud/K8s/architecture natives
- ✅ Excellente qualité visuelle
- ✅ MCP exposé (modification via outil)
- ❌ **Pas de rendu inline** dans opencode/GitHub → lecteurs cassés
- ❌ Dépendance externe (compte eraser.io requis)
- ❌ Migration des 15 Mermaid existants coûteux

### Option 3 — Hybride Mermaid + eraser.io (choisi)

- ✅ Mermaid pour les diagrammes simples (process, séquence, ER simple) → rendu inline
- ✅ eraser.io pour les diagrammes techniques riches (cloud, K8s, sécurité, réseau) → versionnés en `.eraser`
- ✅ Migration progressive possible
- ✅ Chaque diagramme au bon endroit
- ⚠️ Deux outils à maîtriser (mitigé par la skill `eraser-diagrams`)

## Décision

**Option 3 — Hybride** est retenu.

**Critère de choix (matrice)** :

| Type de diagramme | Outil | Raison |
|-------------------|-------|--------|
| Flowchart ≤ 10 nœuds | Mermaid | Suffisant, inline |
| Séquence ≤ 5 acteurs | Mermaid | Suffisant, inline |
| ER ≤ 10 tables | Mermaid | Suffisant, inline |
| Gantt, state machine | Mermaid | Natif |
| **Architecture cloud (AWS/Azure/GCP)** | **eraser.io** | Icônes natives |
| **Topologie Kubernetes multi-zones** | **eraser.io** | Richesse visuelle |
| **Périmètre sécurité / zones ATEX** | **eraser.io** | Couches + annotations |
| **Réseau (DMZ, subnets, flux)** | **eraser.io** | Visualisation réseau |
| **ER > 10 tables** | **eraser.io** | Meilleure lisibilité |
| **Présentation CSPH / ministère** | **eraser.io** | Qualité visuelle |
| **Diagramme de conformité** | **eraser.io** | Annotations réglementaires |

## Conséquences

### Positives
- Chaque diagramme au bon endroit.
- opencode/GitHub restent lisibles (Mermaid rendu).
- Qualité visuelle pour présentations externes (eraser.io).
- Possibilité de **migrer** un Mermaid vers eraser.io si besoin (cf. roadmap).

### Négatives
- L'équipe doit apprendre les deux syntaxes.
- Double source potentielle (mitigé par la matrice de choix + INDEX).
- eraser.io = dépendance externe (compte requis, MCP).

### Neutres
- La skill `eraser-diagrams` est créée (cf. `.opencode/skills/eraser-diagrams/SKILL.md`).
- L'INDEX des diagrammes est dans [`docs/diagrams/INDEX.md`](../diagrams/INDEX.md).

## Convention de nommage

| Format | Localisation | Exemple |
|--------|--------------|---------|
| Mermaid | Section dans `PARTIE_X_diagrams.md` ou inline dans `PARTIE_*.md` | `### §1 HLD 5 couches` |
| eraser.io | `docs/diagrams/eraser/<domaine>-<sous-domaine>.eraser` | `architecture-cloud.eraser` |

## Roadmap (non bloquante)

- **Court terme** : les 15 Mermaid existants restent en l'état, 4 nouveaux eraser.io ajoutés pour le volet technique.
- **Moyen terme** : si besoin, migrer les diagrammes Mermaid `complexes` (HLD, ER) vers eraser.io.
- **Long terme** : potentiel ajout d'**Excalidraw** pour wireframes UI (non-actif pour l'instant).

## Liens

- **PARTIES liées** : [`PARTIE_VI`](../PARTIE_VI.md) (référence tous les diagrammes)
- **Skill** : [`.opencode/skills/eraser-diagrams/SKILL.md`](../../.opencode/skills/eraser-diagrams/SKILL.md)
- **Index** : [`docs/diagrams/INDEX.md`](../diagrams/INDEX.md)
- **Config** : [`opencode.json`](../../opencode.json) (MCP `eraser`)
- **ADR liés** : [0001 (Bun)](./0001-use-bun-runtime.md), [0003 (Git)](./0003-gitflow-conventional-commits.md)
- **Documentation eraser.io** : https://docs.eraser.io
