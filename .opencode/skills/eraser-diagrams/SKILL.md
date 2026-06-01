---
name: eraser-diagrams
description: Generate and edit architecture, ER, sequence, and cloud-architecture diagrams using eraser.io diagram-as-code syntax (.eraser files). Use when the user asks to create, edit, or visualize a technical diagram (cloud, K8s, network, security perimeter, complex ER) that benefits from icons and rich visualization, when editing files in docs/diagrams/eraser/*.eraser, or when calling the eraser MCP tools. Triggers: eraser, diagram-as-code, architecture diagram, cloud architecture, k8s topology, network diagram, security zones, ERD, .eraser file, eraser MCP.
---

# Eraser.io Diagram-as-Code

> Skill pour générer et éditer des diagrammes eraser.io (`.eraser`).
> Voir ADR-0002 pour la justification du choix hybride Mermaid + eraser.io.

## Quand utiliser eraser.io (vs Mermaid)

| Type de diagramme | Outil |
|-------------------|-------|
| Flow simple (≤ 10 nœuds), séquence, ER simple, gantt | **Mermaid** (inline) |
| Architecture cloud (AWS/Azure/GCP) | **eraser.io** |
| Topologie K8s multi-zones | **eraser.io** |
| Périmètre sécurité, zones ATEX | **eraser.io** |
| Réseau (DMZ, subnets, flux) | **eraser.io** |
| ER complexe (> 10 tables) | **eraser.io** |
| Présentation CSPH / ministère | **eraser.io** |

Voir [`docs/diagrams/INDEX.md`](../../../docs/diagrams/INDEX.md) pour la matrice complète.

## 4 types de diagrammes eraser.io

### 1. Architecture (cloud, K8s, réseau) — `node [icon: ...]` + groups

**Syntaxe** :
```
title: Mon diagramme
colorMode bold
typeface clean

MonGroupe [icon: aws-vpc, color: blue] {
  Node1 [icon: aws-ec2]
  Node2 [icon: aws-rds]
}

Node1 > Node2: label [color: green]
```

**Icônes disponibles** : voir https://docs.eraser.io/docs/icons
- AWS : `aws-ec2`, `aws-rds`, `aws-s3`, `aws-lambda`, `aws-vpc`, etc.
- K8s : `kubernetes-pod`, `kubernetes-service`, `kubernetes-deployment`
- Tech : `postgres`, `redis`, `kafka`, `mqtt`, `vault`, `prometheus`
- General : `server`, `database`, `firewall`, `shield`, `lock`, `globe`, `cloud`

**Patterns clés** :
- `icon:` : icône attachée
- `color:` : couleur du nœud (nom ou hex entre guillemets)
- `label:` : libellé distinct du nom (pour noms techniques)
- `link:` : URL associée au nœud
- `colorMode:` : `pastel` (défaut), `bold`, `outline`
- `styleMode:` : `shadow` (défaut), `plain`, `watercolor`
- `typeface:` : `rough` (défaut), `clean`, `mono`

### 2. Entity Relationship Diagram (ERD) — `entity { attr type }`

**Syntaxe** :
```
users [icon: user, color: blue] {
  id string pk
  email string
  teamId string fk
}

teams {
  id string pk
  name string
}

users.teamId > teams.id
```

**Cardinalités** :
- `>` : many-to-one
- `<` : one-to-many
- `-` : one-to-one
- `<>` : many-to-many

**Attributs** : `name type [pk|fk|unique|index]`

### 3. Flow Chart — `A[Label] -> B[Label]`

**Syntaxe** :
```
flowchart

start [icon: play, color: green] > step1: action
step1 > decision {shape: diamond}
decision > step2: oui
decision > step3: non
step3 > end [icon: stop, color: red]
```

**Formes spéciales** :
- `{shape: diamond}` : décision
- `{shape: oval}` : début/fin
- `{shape: parallelogram}` : I/O
- `{shape: hexagon}` : préparation

### 4. Sequence Diagram — `actor1 > actor2: message`

**Syntaxe** :
```
sequence
  actor1 > actor2: GET /api
  actor2 > actor3: internal call
  actor3 --> actor2: response
  actor2 --> actor1: 200 OK
```

**Flèches** :
- `>` : appel synchrone
- `-->` : retour
- `<>` : bidirectionnel
- `--` : ligne simple

## Connexions

| Syntaxe | Type |
|---------|------|
| `A > B` | Flèche gauche-droite |
| `A < B` | Flèche droite-gauche |
| `A <> B` | Bidirectionnel |
| `A - B` | Ligne |
| `A -- B` | Ligne pointillée |
| `A --> B` | Flèche pointillée |
| `A > B: label` | Avec libellé |
| `A > B, C, D` | One-to-many |

## Bonnes pratiques

1. **Groupes imbriqués** : utiliser `{ }` pour VPC → Subnet → Service.
2. **Couleurs par zone** : `blue` pour VPC principal, `orange` pour DR, `green` pour sécurité, `red` pour utilisateur.
3. **Legend** : toujours inclure une légende en bas à droite.
4. **direction** : `direction down` par défaut, `direction right` pour flux horizontal.
5. **Pas de nœuds anonymes** : nommer chaque nœud, ou utiliser `label:` pour le texte visible.
6. **Icônes** : privilégier les icônes standards (AWS, K8s, tech logos) plutôt que les généralistes.

## Fichiers existants du projet

| Fichier | Contenu |
|---------|---------|
| `docs/diagrams/eraser/architecture-cloud.eraser` | HLD 5 couches |
| `docs/diagrams/eraser/kubernetes-topology.eraser` | K8s 3 zones |
| `docs/diagrams/eraser/security-zones.eraser` | Périmètre ATEX/mTLS |
| `docs/diagrams/eraser/network.eraser` | DMZ, VPC, sous-réseaux |

## MCP eraser (configuré)

L'opencode.json déclare le MCP `eraser` :
```json
"mcp": {
  "eraser": {
    "type": "http",
    "url": "https://app.eraser.io/api/mcp"
  }
}
```

Outils disponibles (selon version du MCP) :
- `list_diagrams`
- `create_diagram`
- `update_diagram`
- `get_diagram_code`
- `export_diagram` (PNG/SVG)

## Workflow d'édition

```bash
# 1. Éditer le fichier .eraser localement
code docs/diagrams/eraser/mon-diagramme.eraser

# 2. Visualiser sur https://app.eraser.io (File > Open)

# 3. OU utiliser le MCP eraser depuis opencode
#    "Crée le diagramme X à partir de docs/diagrams/eraser/mon-diagramme.eraser"

# 4. Exporter en PNG/SVG pour intégration dans une PARTIE

# 5. Référencer depuis la PARTIE
# ![Architecture](docs/diagrams/eraser/mon-diagramme.png)
```

## Validation CI

La CI (`.github/workflows/ci.yml` job `eraser-syntax`) liste simplement les fichiers `.eraser` présents. La validation syntaxique complète se fait via :
- Prévisualisation sur https://app.eraser.io
- MCP eraser (si compte configuré)

## Ressources

- Documentation officielle : https://docs.eraser.io
- Référence syntaxe architecture : https://docs.eraser.io/docs/syntax
- Référence ERD : https://docs.eraser.io/docs/syntax-1
- Référence flow charts : https://docs.eraser.io/docs/syntax-3
- Liste icônes : https://docs.eraser.io/docs/icons
- ADR lié : `docs/adr/0002-hybrid-diagrams-mermaid-eraser.md`
