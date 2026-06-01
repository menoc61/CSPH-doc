# 00 — Index général du projet CSPH GPL Tracking

> **Porte d'entrée unique** de toute la documentation du projet.
> Date de dernière mise à jour : 2026-06-01

## 1. Présentation

| Élément | Lien |
|---------|------|
| Mémoire technique source | [`SystemDesignDoc.md`](./SystemDesignDoc.md) |
| Configuration agent (opencode) | [`AGENTS.md`](https://github.com/menoc61/CSPH-doc/blob/master/AGENTS.md) |
| Glossaire domaine | [`CONTEXT.md`](https://github.com/menoc61/CSPH-doc/blob/master/CONTEXT.md) |
| README projet | [`README.md`](https://github.com/menoc61/CSPH-doc/blob/master/README.md) |
| Guide contributeur | [`CONTRIBUTING.md`](https://github.com/menoc61/CSPH-doc/blob/master/CONTRIBUTING.md) |
| Changelog | [`CHANGELOG.md`](https://github.com/menoc61/CSPH-doc/blob/master/CHANGELOG.md) |

## 2. PARTIES du mémoire technique

> Une **PARTIE** = un livrable technique majeur (~500-3000 lignes).
> Convention de nommage : `PARTIE_<N>_<titre-court>.md` (voir [`parties/CONVENTIONS.md`](./parties/CONVENTIONS.md)).

| # | Titre | Fichier | Statut | Dernière MAJ |
|---|-------|---------|--------|--------------|
| VI | Conception du système (HLD & Backend) | [`PARTIE_VI.md`](./PARTIE_VI.md) | ✅ Publié | 2026-06-01 |
| — | _Autres PARTIES à venir_ | [`parties/INDEX.md`](./parties/INDEX.md) | 📋 Planifié | — |

## 3. Architecture Decision Records (ADR)

> Toute décision structurante doit être tracée dans [`adr/`](./adr/). Format MADR-light.

| # | Titre | Statut |
|---|-------|--------|
| [0001](./adr/0001-use-bun-runtime.md) | Adopter Bun comme runtime backend | ✅ Accepted |
| [0002](./adr/0002-hybrid-diagrams-mermaid-eraser.md) | Stratégie hybride Mermaid + eraser.io | ✅ Accepted |
| [0003](./adr/0003-gitflow-conventional-commits.md) | Workflow Git (Gitflow + Conventional Commits) | ✅ Accepted |

## 4. Diagrammes

> Deux familles : **Mermaid** (rendu inline dans PARTIE_VI.md) et **eraser.io** (cloud/K8s/sécurité/réseau).
> Voir [`diagrams/INDEX.md`](./diagrams/INDEX.md) pour la matrice de choix.

| Catégorie | Quantité | Localisation |
|-----------|----------|--------------|
| Mermaid (flow, séquence, ER) | 15 | Inline dans [`PARTIE_VI.md`](./PARTIE_VI.md) (sections §13-§18) |
| eraser.io (cloud, K8s, sécurité, réseau) | 4 | [`diagrams/eraser/`](./diagrams/eraser/) + docs markdown |

## 5. Skills (opencode)

> Compétences spécialisées chargées par opencode.
> Liste complète : [`AGENTS.md`](https://github.com/menoc61/CSPH-doc/blob/master/AGENTS.md#agent-skills).

- **17 skills projet** dans `.opencode/skills/` (IoT, RFID, API, dashboard, alertes, sécurité, PDF)
- **Skills globaux** dans `.agents/skills/` (architecture, devops, Playwright, etc.)
- **Skill nouveau** : `eraser-diagrams` (diagram-as-code)

## 6. Configuration agent / opencode

| Fichier | Rôle |
|---------|------|
| [`opencode.json`](https://github.com/menoc61/CSPH-doc/blob/master/opencode.json) | Config opencode (skills paths, MCP, permissions) |
| [`mcp.json`](https://github.com/menoc61/CSPH-doc/blob/master/mcp.json) | Config MCP servers (eraser.io) |
| [`agents/issue-tracker.md`](./agents/issue-tracker.md) | Convention `.scratch/` pour issues |
| [`agents/triage-labels.md`](./agents/triage-labels.md) | Labels de triage |
| [`agents/domain.md`](./agents/domain.md) | Layout docs domaine |

## 7. Workflow & qualité

| Élément | Référence |
|---------|-----------|
| Comment contribuer | [`CONTRIBUTING.md`](https://github.com/menoc61/CSPH-doc/blob/master/CONTRIBUTING.md) |
| Comment créer une PARTIE | [`parties/CONVENTIONS.md`](./parties/CONVENTIONS.md) |
| Squelette PARTIE | [`parties/TEMPLATE.md`](./parties/TEMPLATE.md) |
| CI GitHub Actions | [`.github/workflows/ci.yml`](https://github.com/menoc61/CSPH-doc/blob/master/.github/workflows/ci.yml) |
| Revues obligatoires | [`.github/CODEOWNERS`](https://github.com/menoc61/CSPH-doc/blob/master/.github/CODEOWNERS) |

## 8. Statut global du projet

| Item | État |
|------|------|
| Mémoire technique source | Squelettique (parties I-V à compléter) |
| PARTIE VI (HLD & Backend) | ✅ Complète + diagrammes Mermaid |
| Architecture documentée | ✅ ADR-0001 à 0003 |
| Environnement opencode | ✅ 17 skills + eraser |
| Repo Git | ⚠️ Initialisé localement, remote à configurer |
| CI | 🔧 Configurée, à activer sur remote |

---

**Légende :** ✅ fait · 🔧 en cours · ⚠️ à faire · 📋 planifié
