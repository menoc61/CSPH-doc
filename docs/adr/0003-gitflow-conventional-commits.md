# 0003. Workflow Git (Gitflow + Conventional Commits)

- **Statut** : accepted
- **Date** : 2026-06-01
- **Auteur** : Équipe DTI Solutions
- **Décideurs** : Lead architecture DTI, Lead dev DTI, CSPH (relecture processus)

## Contexte et énoncé du problème

Le projet CSPH est un système **réglementaire critique** (fraude sur les hydrocarbures, données de péréquation étatiques). Il faut :

1. Une **traçabilité fine** des modifications (qui, quand, pourquoi).
2. Un **processus de release** clair (semver pour versions prod).
3. Une **protection** de la branche principale contre les régressions.
4. Un **format de commit** machine-readable (génération changelog auto, semantic-release).
5. Une collaboration efficace entre DTI (intégrateur) et CSPH (client).

## Options considérées

### Option 1 — Trunk-based development

- ✅ Intégration continue maximale
- ✅ Moins de branches à gérer
- ❌ Moins adapté aux releases réglementées (CSPH signe chaque version)
- ❌ Demande une discipline d'équipe très forte (feature flags partout)
- ❌ Mauvaise réversibilité en cas de bug critique en prod

### Option 2 — GitHub Flow (branches éphémères + PR)

- ✅ Simple, adapté à l'intégration continue
- ❌ Pas de branche `develop` dédiée → merges dans `main` fréquents
- ❌ Pas de support natif pour les releases versionnées

### Option 3 — Gitflow + Conventional Commits (choisi)

- ✅ Branches `main` (prod) + `develop` (intégration) + features/fixes/docs
- ✅ Tags semver pour releases
- ✅ Convention `feat:`, `fix:`, `docs:`, etc. → changelog auto
- ✅ Protection forte de `main` (PR obligatoire, revue, CI verte)
- ✅ Adapté aux **releases réglementées** CSPH (v1.0, v1.1, v2.0 signées)
- ✅ Support standardisé dans l'écosystème (release-please, semantic-release)

## Décision

**Option 3 — Gitflow + Conventional Commits** est retenu.

### Branches

| Branche | Rôle | Protection |
|---------|------|------------|
| `main` | Production, toujours stable | 🛡 Protégée : PR obligatoire, 1 approbation, CI verte, signature commits |
| `develop` | Intégration des features | 🛡 Protégée : PR obligatoire, CI verte |
| `feat/<scope>-<short>` | Nouvelle fonctionnalité | Merge vers `develop` |
| `fix/<scope>-<short>` | Correctif | Merge vers `develop` ET `main` si hotfix |
| `docs/<part>-<short>` | Documentation | Merge vers `develop` |
| `chore/<scope>-<short>` | Tooling, deps | Merge vers `develop` |
| `release/<semver>` | Préparation release | Pas de nouvelles features, que bugfixes |
| `hotfix/<scope>-<short>` | Fix urgent prod | Branchée depuis `main`, merge vers `main` ET `develop` |

### Conventional Commits

Format : `<type>(<scope>): <description courte>`

| Type | Description | Exemple |
|------|-------------|---------|
| `feat` | Nouvelle fonctionnalité | `feat(ingestion): add bun-serve endpoint` |
| `fix` | Correctif de bug | `fix(mqtt): correct ACL for marketer M02` |
| `docs` | Documentation uniquement | `docs(VI): add §15.5 performance` |
| `refactor` | Refactoring sans changement de comportement | `refactor(svc-auth): split JWT validation` |
| `test` | Ajout ou modif de tests | `test(ingestion): add kafka producer test` |
| `chore` | Tooling, deps, config | `chore(deps): bump bun to 1.2.0` |
| `perf` | Amélioration de performance | `perf(ingestion): batch kafka writes` |
| `ci` | CI/CD | `ci(workflow): add mermaid render check` |
| `revert` | Annulation d'un commit précédent | `revert: feat(ingestion): broken` |

### Versioning semver

Format : `vMAJOR.MINOR.PATCH` (ex. `v1.4.2`)

- **MAJOR** : breaking change, décision CSPH requise
- **MINOR** : nouvelle fonctionnalité, rétro-compatible
- **PATCH** : bugfix, rétro-compatible

### Processus de release

1. Branche `release/v1.X.0` depuis `develop`
2. Stabilisation (bugfixes uniquement)
3. Merge vers `main` + tag `v1.X.0`
4. Merge vers `develop` (resync)
5. Génération auto du CHANGELOG depuis Conventional Commits
6. Notification CSPH pour signature / validation

## Conséquences

### Positives
- **Traçabilité totale** : chaque commit typé, changelog auto-généré.
- **Protection forte de `main`** : pas de régression prod possible.
- **Adapté aux releases CSPH** : processus clair, signature par version.
- **Écosystème riche** : `release-please`, `semantic-release`, `commitlint`, `husky`.
- **Lecture facilitée** : `git log --oneline` filtrable par type.

### Négatives
- Plus de branches à gérer (vs trunk-based).
- Demande une discipline de commit (Conventional Commits).
- Nécessite des hooks pre-commit (Husky recommandé) pour valider le format.

### Neutres
- Les ADR futurs supposent ce workflow par défaut.
- L'outillage (commitlint, Husky) est recommandé mais pas imposé immédiatement.

## Outillage recommandé (non bloquant)

| Outil | Usage |
|-------|-------|
| `commitlint` | Valide le format Conventional Commits (pre-commit hook) |
| `Husky` ou `lefthook` | Gestion des hooks Git |
| `release-please` | Génération auto du CHANGELOG et des PRs de release |
| `semantic-release` (alt.) | Idem, plus ancien |

## Liens

- **PARTIES liées** : [`PARTIE_VI`](../PARTIE_VI.md) (toutes les modifications passent par ce workflow)
- **Config** : [`.github/CODEOWNERS`](../../.github/CODEOWNERS), [`.github/PULL_REQUEST_TEMPLATE.md`](../../.github/PULL_REQUEST_TEMPLATE.md)
- **CI** : [`.github/workflows/ci.yml`](../../.github/workflows/ci.yml)
- **Templates** : [`.github/ISSUE_TEMPLATE/`](../../.github/ISSUE_TEMPLATE/)
- **ADR liés** : [0001 (Bun)](./0001-use-bun-runtime.md), [0002 (diagrammes)](./0002-hybrid-diagrams-mermaid-eraser.md)
- **Documentation Conventional Commits** : https://www.conventionalcommits.org
- **Documentation Gitflow** : https://nvie.com/posts/a-successful-git-branching-model/
