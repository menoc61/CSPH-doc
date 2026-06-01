# Changelog

> Tous les changements notables du projet CSPH GPL Tracking sont documentés ici.
> Format inspiré de [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/).
> Le projet adhère au [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Non publié]

### Added
- `docs/00-INDEX.md` — Master TOC du projet
- `docs/parties/` — Structure pour PARTIES futures (TEMPLATE, CONVENTIONS, INDEX, README)
- `docs/adr/` — Architecture Decision Records
  - `0001-use-bun-runtime.md` — Bun adopté comme runtime backend
  - `0002-hybrid-diagrams-mermaid-eraser.md` — Stratégie hybride Mermaid + eraser.io
  - `0003-gitflow-conventional-commits.md` — Workflow Git formalisé
- `docs/diagrams/eraser/` — 4 diagrammes techniques en diagram-as-code
  - `architecture-cloud.eraser`
  - `kubernetes-topology.eraser`
  - `security-zones.eraser`
  - `network.eraser`
- `docs/diagrams/INDEX.md` — Index des diagrammes
- `docs/superpowers/specs/2026-06-01-csph-doc-architecture-design.md` — Design doc
- `docs/superpowers/plans/2026-06-01-csph-doc-architecture.md` — Plan d'implémentation
- `CONTRIBUTING.md` — Guide du contributeur
- `CHANGELOG.md` — Ce fichier
- `.github/` — Templates & CI
  - `CODEOWNERS`
  - `PULL_REQUEST_TEMPLATE.md`
  - `ISSUE_TEMPLATE/{bug,feature,doc}.md`
  - `workflows/ci.yml` (lint + link-check + Mermaid render)
- `.opencode/skills/eraser-diagrams/SKILL.md` — Skill pour eraser.io

### Changed
- `opencode.json` — Ajout du MCP `eraser` dans la config
- `PARTIE_VI.md` — Ajout de 7 sous-sections pour scénarios omis :
  - `§15.5` Performance & Concurrence (Bun-tuned)
  - `§16.6` Backup, Rétention & RPO/RTO
  - `§17.5` Géofencing & zones de livraison
  - `§17.6` Détection de vol de cargaison
  - `§18.5` Reprise après sinistre (DR)
  - `§18.6` Observabilité & SRE
  - `§18.7` Mise à jour OTA firmware ADAM-6717
- `AGENTS.md` — Mention ADR et nouvelle structure `docs/parties/`
- `README.md` — Section Project Structure mise à jour

### Fixed
- N/A (première version)

### Deprecated
- N/A

### Removed
- N/A

### Security
- N/A

---

## Format

- **Added** pour les nouvelles fonctionnalités.
- **Changed** pour les changements aux fonctionnalités existantes.
- **Deprecated** pour les fonctionnalités qui seront retirées prochainement.
- **Removed** pour les fonctionnalités retirées.
- **Fixed** pour les corrections de bugs.
- **Security** pour les corrections de vulnérabilités.

## Types de versions

- **MAJOR** (`vX.0.0`) : breaking change, décision CSPH requise.
- **MINOR** (`v0.X.0`) : nouvelle fonctionnalité rétro-compatible.
- **PATCH** (`v0.0.X`) : bugfix rétro-compatible.

## Politique de release

- Chaque release est **taguée** semver.
- Chaque release est **signée** par CSPH (validation).
- Un **CHANGELOG** est généré automatiquement depuis les Conventional Commits via `release-please`.
- Les releases sont publiées sur GitHub Releases avec notes de version.
