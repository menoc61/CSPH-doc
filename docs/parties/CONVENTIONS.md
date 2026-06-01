# Conventions — Comment créer une PARTIE

> **Règles à suivre impérativement** pour toute nouvelle PARTIE du mémoire technique CSPH.
> Toute violation entraîne un rejet de la PR par le CODEOWNER.

## 1. Nommage des fichiers

| Élément | Convention | Exemple |
|---------|------------|---------|
| Format | `PARTIE_<N>_<titre-en-kebab-case>.md` | `PARTIE_VII_dimensionnement.md` |
| Numéro | Romains majuscules (I, II, …, VII, VIII) | `PARTIE_VII` |
| Titre | Kebab-case, 3-5 mots, sans accents | `dimensionnement-capacity` |
| Localisation | `docs/parties/` (sauf PARTIE_VI, conservé à `docs/`) | `docs/parties/PARTIE_VII_*.md` |

**Pourquoi ?** Tri alphabétique = tri chronologique, lisibilité URL, pas de caractères spéciaux.

## 2. Frontmatter obligatoire

Tout fichier PARTIE **doit** commencer par :

```yaml
---
id: PARTIE_VII
title: "Titre exact affiché en H1"
status: draft | review | published | deprecated
author: "Prénom Nom <email@example.com>"
created: YYYY-MM-DD
updated: YYYY-MM-DD
adr:
  - 0001
  - 0002
related:
  - PARTIE_VI
supersedes: null
---
```

| Champ | Obligatoire | Notes |
|-------|-------------|-------|
| `id` | ✅ | Identifiant unique, format `PARTIE_<N>` |
| `title` | ✅ | < 80 caractères |
| `status` | ✅ | Voir cycle de vie ci-dessous |
| `author` | ✅ | Créateur principal |
| `created` | ✅ | Date création |
| `updated` | ✅ | Date dernière MAJ (à maintenir) |
| `adr` | ✅ | Liste des ADR référencés (peut être `[]`) |
| `related` | Recommandé | Autres PARTIES liées |
| `supersedes` | Si applicable | PARTIE que celle-ci remplace |

## 3. Cycle de vie (statut)

```
draft → review → published → (deprecated | archived)
```

| Statut | Description | Qui peut le changer |
|--------|-------------|---------------------|
| `draft` | En cours de rédaction | Auteur |
| `review` | Prête pour revue | Auteur (PR ouverte) |
| `published` | Validée et intégrée | Reviewer (post-merge) |
| `deprecated` | Remplacée par une nouvelle | Lead doc |

## 4. Structure obligatoire

Le fichier **doit** contenir au minimum :

1. **H1** : `# PARTIE <N> — <Titre>`
2. **Bandeau de cross-références** (immédiatement après le H1) :
   > `> **Voir :** [00-INDEX](../00-INDEX.md) · [ADR liés](../adr/) · [Diagrammes](../diagrams/INDEX.md)`
3. **Résumé exécutif** (3-5 phrases)
4. **Table des matières** (liens anchors)
5. **Sections numérotées** (`## 1. Contexte`, `## 2. Architecture`, …)
6. **Diagrammes** (Mermaid inline ou lien vers `diagrams/eraser/`)
7. **ADR liés** (mentionnés explicitement, jamais juste en frontmatter)
8. **Footer de cross-références** (en fin de fichier)

Voir [`TEMPLATE.md`](./TEMPLATE.md) pour le squelette complet.

## 5. Diagrammes

| Type | Outil | Emplacement |
|------|-------|-------------|
| Flowchart, séquence, ER simple, gantt | Mermaid | Inline dans le `.md` |
| Architecture cloud, K8s, sécurité, réseau | eraser.io | `../diagrams/eraser/<nom>.eraser` |
| ER complexe (> 10 tables) | eraser.io | `../diagrams/eraser/er-*.eraser` |

**Règle :** Mermaid pour ce qui rend bien inline, eraser.io pour ce qui est technique
et destiné à être présenté à des non-techs ou imprimé. Voir [`../diagrams/INDEX.md`](../diagrams/INDEX.md).

## 6. Liens internes

- Toujours en **chemin relatif** (`../adr/0001-...`, pas d'URL absolue).
- **Liens croisés** vers : `00-INDEX.md`, ADR référencés, autres PARTIES, diagrammes.
- **Pas de liens** vers des fichiers en dehors de `docs/` depuis une PARTIE (utiliser `CONTEXT.md` ou `AGENTS.md` pour les renvois méta).

## 7. Validation pré-commit

Avant de commit, vérifier :

- [ ] Frontmatter complet
- [ ] Bandeau de cross-références en haut
- [ ] Footer de cross-références en bas
- [ ] TOC à jour
- [ ] Diagrammes référencés existent (chemin valide)
- [ ] ADR référencés existent dans `../adr/`
- [ ] Statut mis à `review` (si PR ouverte) ou `published` (si merge)
- [ ] Entrée ajoutée dans [`INDEX.md`](./INDEX.md)

## 8. Pull Request

Une PARTIE = une PR. Titre de PR :

```
docs(VII): add dimensioning PARTIE
```

Description obligatoire (template dans `.github/PULL_REQUEST_TEMPLATE.md`) :

```markdown
## PARTIE
- [ ] Nouvelle PARTIE : ID + titre
- [ ] Mise à jour : ID + raison

## Checklist
- [ ] Frontmatter complet
- [ ] TOC à jour
- [ ] Liens valides
- [ ] ADR liés créés/mis à jour
- [ ] Diagrammes ajoutés/référencés
- [ ] INDEX.md mis à jour
- [ ] CHANGELOG.md mis à jour
```

## 9. Anti-patterns (interdits)

- ❌ PARTIE sans frontmatter
- ❌ Frontmatter avec champs non documentés
- ❌ Liens absolus (`/docs/...`)
- ❌ Diagrammes en image binaire (PNG/JPG) sans source
- ❌ Sections `TBD`, `TODO`, "à compléter plus tard"
- ❌ Mélange français/anglais incohérent dans le texte
- ❌ Code snippets sans contexte ou sans fichier source
- ❌ Numérotation de sections incohérente (`## 1.` puis `## 3.` sauté)

## 10. Exemples

- **Bon exemple** : `docs/PARTIE_VI.md` (HLD & Backend, ~2400 lignes, full structure)
- **Squelette** : `docs/parties/TEMPLATE.md` (à copier)
