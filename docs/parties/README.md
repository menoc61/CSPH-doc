# Dossier `docs/parties/`

Ce dossier contient **toutes les PARTIES** du mémoire technique CSPH GPL Tracking,
à l'exception de `PARTIE_VI.md` qui reste à la racine de `docs/` pour des raisons
historiques (créée avant l'adoption de cette structure).

## Navigation rapide

| Fichier | Rôle |
|---------|------|
| [`TEMPLATE.md`](./TEMPLATE.md) | **À copier** pour créer une nouvelle PARTIE |
| [`CONVENTIONS.md`](./CONVENTIONS.md) | Règles de nommage, structure, validation |
| [`INDEX.md`](./INDEX.md) | Tableau de toutes les PARTIES (statut, date, auteur) |

## Workflow minimal pour créer une PARTIE

```bash
# 1. Copier le template
cp docs/parties/TEMPLATE.md docs/parties/PARTIE_VII_dimensionnement.md

# 2. Éditer le frontmatter
#    - id, title, status, author, created, updated

# 3. Remplir les sections (supprimer celles non utilisées)

# 4. Ajouter l'entrée dans INDEX.md

# 5. Commit Conventional
git add docs/parties/PARTIE_VII_*.md docs/parties/INDEX.md
git commit -m "docs(VII): add dimensioning PARTIE"
```

## Pourquoi ce dossier séparé ?

- **Autonomie** : un membre de l'équipe peut créer une PARTIE sans toucher au reste.
- **Modularité** : PR par PARTIE, revue dédiée, tag release incrémental.
- **Scalabilité** : 20+ PARTIES supportées sans pollution de `docs/`.
- **Convention** : le template force la cohérence.

Voir [`CONVENTIONS.md`](./CONVENTIONS.md) pour le détail des règles.
