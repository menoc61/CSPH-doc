# Contributing — CSPH GPL Tracking

> Guide pour toute contribution au projet (code, documentation, ADR, diagrammes).

## TL;DR

```bash
# 1. Créer une branche
git checkout develop
git pull
git checkout -b feat/<scope>-<short-desc>

# 2. Coder / documenter
# ... vos modifications ...

# 3. Vérifier localement
bun test                          # tests
bun run lint                      # linter
# Vérifier que les liens internes marchent
# Vérifier que les Mermaid se rendent (VS Code ou https://mermaid.live)

# 4. Commit (format Conventional)
git add .
git commit -m "feat(ingestion): add bun-serve endpoint"

# 5. Push + PR
git push -u origin feat/<scope>-<short-desc>
# Ouvrir une PR sur GitHub vers develop
# Remplir le template .github/PULL_REQUEST_TEMPLATE.md
```

## Workflow Git

Voir [ADR-0003](docs/adr/0003-gitflow-conventional-commits.md) pour le détail complet.

| Vous voulez... | Créez... | Cible |
|----------------|----------|-------|
| Ajouter une fonctionnalité | `feat/<scope>-<short>` | `develop` |
| Corriger un bug | `fix/<scope>-<short>` | `develop` |
| Corriger un bug urgent en prod | `hotfix/<scope>-<short>` | `main` + `develop` |
| Modifier la doc | `docs/<part>-<short>` | `develop` |
| Mettre à jour le tooling | `chore/<scope>-<short>` | `develop` |
| Préparer une release | `release/vX.Y.Z` | `main` |

**Règles absolues** :

- ❌ Jamais de commit direct sur `main` ou `develop`.
- ❌ Jamais de merge sans PR + approbation.
- ✅ Squash merge des PR (historique propre).
- ✅ `rebase` local avant push pour éviter les merges parasites.

## Conventional Commits

Format : `<type>(<scope>): <description>`

```bash
feat(ingestion): add Kafka producer batching
fix(mqtt): correct ACL rule for marketer M02
docs(VI): add §15.5 performance benchmarks
chore(deps): bump bun to 1.2.0
refactor(svc-auth): split JWT validation in middleware
test(ingestion): add unit test for kafka bridge
```

| Type | Quand | CHANGELOG ? |
|------|-------|-------------|
| `feat` | Nouvelle fonctionnalité | Section "Added" |
| `fix` | Correctif | Section "Fixed" |
| `docs` | Documentation seulement | Ignoré (sauf API breaking) |
| `refactor` | Refactoring sans changement | Ignoré |
| `test` | Tests | Ignoré |
| `chore` | Tooling, deps | Ignoré |
| `perf` | Performance | Section "Changed" |
| `ci` | CI/CD | Ignoré |
| `revert` | Annulation | Section "Reverted" |

## Comment écrire un ADR

Voir [`docs/adr/README.md`](docs/adr/README.md) pour le processus complet.

```bash
# 1. Copier le template
cp docs/adr/README.md docs/adr/0004-ma-decision.md

# 2. Renuméroter, compléter

# 3. Statut = proposed (discussion) puis accepted (validé)

# 4. Ajouter dans docs/00-INDEX.md
```

## Comment créer une PARTIE

Voir [`docs/parties/CONVENTIONS.md`](docs/parties/CONVENTIONS.md) pour les règles.

```bash
# 1. Copier le template
cp docs/parties/TEMPLATE.md docs/parties/PARTIE_VII_dimensionnement.md

# 2. Compléter le frontmatter
#    - id, title, status=draft, author, created, updated, adr, related

# 3. Remplir les sections (garder la structure)

# 4. Ajouter l'entrée dans docs/parties/INDEX.md

# 5. Ajouter le lien dans docs/00-INDEX.md (section 2)

# 6. Commit + PR
git add docs/parties/ docs/00-INDEX.md
git commit -m "docs(VII): add dimensioning PARTIE"
```

## Comment ajouter un diagramme

### Mermaid (pour flow, séquence, ER simple)

```markdown
\```mermaid
flowchart TB
    A[Camion] -->|MQTT| B[EMQX]
    B --> C[Kafka]
    C --> D[TimescaleDB]
\```
```

→ Inline dans le `.md` ou dans `docs/diagrams/PARTIE_X_diagrams.md`.

### eraser.io (pour cloud, K8s, sécurité, réseau)

```bash
# 1. Créer le fichier source
touch docs/diagrams/eraser/mon-diagramme.eraser

# 2. Éditer la syntaxe eraser diagram-as-code
#    Voir https://docs.eraser.io pour la syntaxe

# 3. Visualiser / éditer dans https://app.eraser.io
#    Ou via le MCP eraser configuré dans opencode.json

# 4. Référencer depuis la PARTIE concernée
#    ![Architecture](docs/diagrams/eraser/mon-diagramme.png)
```

## Tests & qualité

| Type de test | Commande | Bloquant en CI ? |
|--------------|----------|------------------|
| Lint Markdown | `markdownlint docs/` | ✅ |
| Liens internes | `lychee --offline docs/` | ✅ |
| Rendu Mermaid | `mmdc -i ... -o out.svg` | ✅ |
| Syntaxe Eraser | `eraser-cli check docs/diagrams/eraser/` | ✅ |
| Tests unitaires (Bun) | `bun test` | ✅ |
| Tests intégration | `bun test:integration` | ✅ |
| Lint code | `bun run lint` | ✅ |
| TypeScript check | `bun run typecheck` | ✅ |

## Process de revue

1. **PR ouverte** par l'auteur avec description complète (template fourni).
2. **CI verte** : tous les checks passent.
3. **CODEOWNERS notifiés** automatiquement (revues obligatoires).
4. **Revue** : 1 approbation minimum (2 si changements sur `main`).
5. **Squash merge** par l'auteur après approbation.
6. **Branche supprimée** automatiquement par GitHub.
7. **CHANGELOG** mis à jour automatiquement par `release-please` (semaine suivante).

## Contact

- **Lead doc** : _@username_ (à compléter par l'équipe DTI)
- **Lead arch** : _@username_ (à compléter par l'équipe DTI)
- **CSPH référent** : _à compléter_

## Licence

Propriétaire — CSPH / DTI Solutions and Services. Toute contribution est soumise à un accord de cession de droits (CLA) à signer une fois.
