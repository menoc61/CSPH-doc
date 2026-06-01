# Architecture Decision Records (ADR)

> Toute **décision structurante** du projet CSPH GPL Tracking doit être tracée ici.
> Format inspiré de [MADR](https://adr.github.io/madr/) (light).

## Pourquoi des ADR ?

- **Mémoire** : on oublie pourquoi on a choisi X dans 6 mois.
- **Transparence** : CSPH, auditeurs, nouveaux membres voient le raisonnement.
- **Alternatives** : expliciter ce qui a été écarté évite de re-discuter.
- **Traçabilité** : chaque décision a un numéro, une date, un auteur, un statut.

## Quand écrire un ADR ?

| Situation | ADR requis |
|-----------|------------|
| Choix de runtime, framework, base de données | ✅ Oui |
| Choix d'un protocole (MQTT vs AMQP) | ✅ Oui |
| Architecture d'un nouveau microservice | ✅ Oui |
| Changement d'une décision déjà actée | ✅ Oui (nouvel ADR `supersedes: NNNN`) |
| Bugfix, refactoring sans impact archi | ❌ Non (PR suffit) |
| Choix de bibliothèque triviale | ❌ Non (PR suffit) |

## Format

Chaque ADR est un fichier `NNNN-titre-en-kebab-case.md` avec ce template :

```markdown
# NNNN. Titre court de la décision

- **Statut** : proposed | accepted | rejected | deprecated | superseded by NNNN
- **Date** : YYYY-MM-DD
- **Auteur** : Prénom Nom
- **Décideurs** : Prénom Nom (rôle), ...

## Contexte et énoncé du problème

Quelle est la situation ? Quelle question faut-il trancher ?

## Options considérées

### Option 1 — <nom>
- ✅ Avantages
- ❌ Inconvénients

### Option 2 — <nom>
...

## Décision

Quelle option est retenue, et pourquoi ?

## Conséquences

- Positives : ...
- Négatives : ...
- Neutres : ...

## Liens

- PARTIES liées : [`PARTIE_XX`](../parties/PARTIE_XX.md)
- Code / config : ...
- Références externes : ...
```

## Cycle de vie

```
proposed → accepted → (deprecated | superseded by NNNN)
                  ↘ rejected
```

| Statut | Description |
|--------|-------------|
| `proposed` | Soumis à discussion, pas encore acté |
| `accepted` | Décision entérinée, à implémenter |
| `rejected` | Examen, mais non retenu |
| `deprecated` | Anciennement accepté, plus d'actualité |
| `superseded by NNNN` | Remplacé par un ADR plus récent (lier) |

## Liste des ADR

| # | Titre | Statut | Date | Auteur |
|---|-------|--------|------|--------|
| [0001](./0001-use-bun-runtime.md) | Adopter Bun comme runtime backend | ✅ Accepted | 2026-06-01 | Équipe DTI |
| [0002](./0002-hybrid-diagrams-mermaid-eraser.md) | Stratégie hybride Mermaid + eraser.io | ✅ Accepted | 2026-06-01 | Équipe DTI |
| [0003](./0003-gitflow-conventional-commits.md) | Workflow Git (Gitflow + Conventional Commits) | ✅ Accepted | 2026-06-01 | Équipe DTI |

## Liens croisés

- Chaque **PARTIE** dans `docs/parties/PARTIE_X_*.md` référence ses ADR dans son frontmatter (`adr: [0001, 0002]`).
- Chaque **ADR** référence les PARTIES qui l'ont déclenché (section `Liens`).
- L'index des ADR est dans [`docs/00-INDEX.md`](../00-INDEX.md#3-architecture-decision-records-adr).
