# CSPH Doc Architecture Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Outiller l'environnement CSPH pour produire des PARTIES en autonomie, valider via CI, et tracer les décisions via ADR. Backend en Bun, diagrammes hybrides Mermaid + eraser.io.

**Architecture:** Doc-as-code. Structure `docs/parties/` pour futures PARTIES, `docs/adr/` pour décisions, `docs/diagrams/eraser/` pour diagrammes techniques. CI GitHub Actions (lint + link-check + Mermaid).

**Tech Stack:** Markdown, eraser.io diagram-as-code, GitHub Actions, Conventional Commits, Gitflow.

---

## Phase 1 — Structure documentaire (8 fichiers)

### Task 1.1 : `docs/00-INDEX.md` (master TOC)

**Files:** Create `docs/00-INDEX.md`

Contenu : porte d'entrée, navigation vers toutes les sections, statut des PARTIES, ADRs, diagrammes.

### Task 1.2 : `docs/parties/README.md` (entrée dossier)

**Files:** Create `docs/parties/README.md`

Contenu : à quoi sert ce dossier, comment l'utiliser, pointer vers TEMPLATE et CONVENTIONS.

### Task 1.3 : `docs/parties/TEMPLATE.md` (squelette PARTIE)

**Files:** Create `docs/parties/TEMPLATE.md`

Contenu : frontmatter obligatoire, structure de sections, footer de cross-références, placeholders guidés.

### Task 1.4 : `docs/parties/CONVENTIONS.md` (guide)

**Files:** Create `docs/parties/CONVENTIONS.md`

Contenu : naming (PARTIE_X_titre-court.md), numérotation, TOC obligatoire, ADRs liés, validation.

### Task 1.5 : `docs/parties/INDEX.md` (TOC des PARTIES)

**Files:** Create `docs/parties/INDEX.md`

Contenu : tableau des PARTIES existantes, statut, date, auteur, lien.

### Task 1.6 : `docs/diagrams/INDEX.md` (TOC diagrammes)

**Files:** Create `docs/diagrams/INDEX.md`

Contenu : Mermaid existants (15) + eraser (4) + critère de choix.

### Task 1.7 : `docs/adr/README.md` (intro ADR)

**Files:** Create `docs/adr/README.md`

Contenu : format MADR-light, processus, qui écrit, comment relier aux PARTIES.

### Task 1.8 : Skill `eraser-diagrams`

**Files:** Create `.opencode/skills/eraser-diagrams/SKILL.md`

Contenu : syntaxe eraser diagram-as-code, exemples, quand utiliser.

---

## Phase 2 — 3 ADR (3 fichiers)

### Task 2.1 : `0001-use-bun-runtime.md`

Statut : Accepted. Contexte Bun vs Go, décision, conséquences, alternatives (Go, Deno, Node 22).

### Task 2.2 : `0002-hybrid-diagrams-mermaid-eraser.md`

Statut : Accepted. Critère de choix par type de diagramme, double source, migration.

### Task 2.3 : `0003-gitflow-conventional-commits.md`

Statut : Accepted. Branches, préfixes, protection, semver.

---

## Phase 3 — Workflow contributeur (8 fichiers)

### Task 3.1 : `CONTRIBUTING.md`

Workflow Git complet, comment créer une PARTIE, comment ouvrir une PR, comment écrire un ADR.

### Task 3.2 : `CHANGELOG.md`

Semver, sections Keep a Changelog, première entrée v0.1.0.

### Task 3.3 : `.github/CODEOWNERS`

Revues par dossier : docs/parties/ → lead doc, .opencode/skills/ → lead arch, etc.

### Task 3.4 : `.github/PULL_REQUEST_TEMPLATE.md`

Checklist : tests, docs, ADR, changelog, lien vers issue.

### Task 3.5-3.7 : `.github/ISSUE_TEMPLATE/{bug,feature,doc}.md`

Trois templates minimalistes.

### Task 3.8 : `.github/workflows/ci.yml`

Lint MD (`markdownlint`), link-check (`lycheeverse/lychee-action`), Mermaid render (`mermaid-cli`).

---

## Phase 4 — 4 diagrammes eraser.io (4 fichiers)

### Task 4.1 : `docs/diagrams/eraser/architecture-cloud.eraser`

HLD 5 couches, icônes cloud, label par composant.

### Task 4.2 : `docs/diagrams/eraser/kubernetes-topology.eraser`

3 zones A/B/C, StatefulSets, services.

### Task 4.3 : `docs/diagrams/eraser/security-zones.eraser`

Zones ATEX, périmètres mTLS, ACL.

### Task 4.4 : `docs/diagrams/eraser/network.eraser`

DMZ, sous-réseaux, flux.

---

## Phase 5 — Mises à jour fichiers existants (4 fichiers)

### Task 5.1 : `opencode.json`

Ajouter `"eraser"` dans le bloc `mcp` (doublon avec mcp.json, permet aussi d'autres MCP futurs).

### Task 5.2 : `PARTIE_VI.md`

Ajouter §15.5, 16.6, 17.5, 17.6, 18.5, 18.6, 18.7 + bandeaux vers nouveaux diagrammes eraser + MAJ TOC.

### Task 5.3 : `AGENTS.md`

Mentionner ADR et nouvelle structure `docs/parties/`.

### Task 5.4 : `README.md`

Mettre à jour la section "Project Structure" pour refléter la nouvelle arborescence.

---

## Self-review

- **Couverture spec :** 10 décisions couvertes, 4 phases d'implémentation, ~24 fichiers.
- **Pas de placeholder :** tous les contenus seront produits, pas de TBD.
- **Cohérence types :** les références (file paths, ADR numbers) sont cohérentes.
- **Hors-scope :** clairement listé (pas de nouvelles PARTIES, pas de remote GitHub).

---

## Execution handoff

Plan complet, prêt à exécution. L'utilisateur a donné son accord pour « implement all ».
