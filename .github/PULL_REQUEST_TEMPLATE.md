<!-- .github/PULL_REQUEST_TEMPLATE.md -->

## Description

<!-- Décrivez en 2-3 phrases ce que cette PR fait et pourquoi. -->

## Type de changement

- [ ] 🐛 Bug fix (changement non-breaking qui corrige un problème)
- [ ] ✨ Nouvelle fonctionnalité (changement non-breaking qui ajoute une fonctionnalité)
- [ ] 💥 Breaking change (fix ou feature qui casserait la fonctionnalité existante)
- [ ] 📚 Documentation (uniquement)
- [ ] 🏗️ Infra / CI
- [ ] ♻️ Refactoring (sans changement de comportement)

## Lien

- **Issue liée** : Closes #<numéro>
- **PARTIE** (si applicable) : PARTIE_<X>
- **ADR** (si applicable) : [ADR-XXXX](docs/adr/XXXX-*.md)

## Checklist auteur

- [ ] Mon code suit les conventions du projet (voir [`CONTRIBUTING.md`](CONTRIBUTING.md))
- [ ] J'ai ajouté des tests qui prouvent que mon fix/feature fonctionne
- [ ] Les tests unitaires et d'intégration passent localement (`bun test`)
- [ ] J'ai mis à jour la documentation (README, PARTIE, ADR, CHANGELOG)
- [ ] Si applicable, j'ai mis à jour [`docs/00-INDEX.md`](docs/00-INDEX.md) et/ou [`docs/parties/INDEX.md`](docs/parties/INDEX.md)
- [ ] Si applicable, j'ai créé/mis à jour un diagramme (Mermaid ou eraser.io)
- [ ] Mon commit message suit le format [Conventional Commits](https://www.conventionalcommits.org/)
- [ ] J'ai relu mon propre code (pas de `console.log`, code mort, TODO, etc.)

## Checklist pour le reviewer

- [ ] Le titre de la PR suit le format `<type>(<scope>): <description>`
- [ ] La CI est verte
- [ ] Le diff est raisonnable en taille (< 500 lignes idéalement, sinon justifier)
- [ ] Les tests couvrent les cas nominaux ET les cas d'erreur
- [ ] La documentation est cohérente avec le code
- [ ] Aucune info sensible (secret, clé API, mot de passe) n'est exposée

## Screenshots / captures

<!-- Si UI/dashboard : ajoutez des captures avant/après. -->

## Notes complémentaires

<!-- Tout contexte supplémentaire : hésitations, alternatives écartées, TODO futurs, etc. -->
