# Design — Architecture documentaire CSPH GPL Tracking

> **Date :** 2026-06-01
> **Statut :** Approuvé en brainstorm
> **Auteur :** Équipe DTI Solutions (via superpowers/brainstorming)

## Contexte

Le projet CSPH GPL Tracking (traçabilité du gaz hors réseau au Cameroun) dispose aujourd'hui
d'un seul document complet : `docs/PARTIE_VI.md` (HLD & Backend, ~2400 lignes).
Le `SystemDesignDoc.md` source est squelettique, et l'équipe va produire **d'autres PARTIES**
(prototype IoT, RFID/PDA, dimensionnement, opérations, etc.).

Le besoin : outiller l'environnement pour que :
1. L'équipe puisse créer de nouvelles PARTIES en autonomie.
2. La documentation soit navigable, versionnée, validée par CI.
3. Le backend soit en **Bun** (compétence équipe, TypeScript bout-en-bout).
4. Les diagrammes techniques soient en **eraser.io** (cloud/K8s/sécurité), les diagrammes
   simples restent en **Mermaid** (rendu inline opencode/GitHub).
5. Les décisions structurantes soient tracées via **ADR**.

## Décisions clés

| # | Décision | Justification courte |
|---|----------|---------------------|
| 1 | **Bun 1.x** comme runtime backend | Compétence équipe, TS bout-en-bout, perf comparable à Go pour I/O |
| 2 | **Bun.serve() + workers** pour hot path | Event-loop non bloquant, workers pour CPU-bound (crypto, ML) |
| 3 | **Mermaid conservé** pour flow/séquence/ER | Rendu natif, zéro dépendance |
| 4 | **eraser.io** pour cloud/K8s/sécurité/réseau | Icônes natives, diagram-as-code versionné |
| 5 | **Structure `docs/parties/`** pour futures PARTIES | Autonomie de l'équipe, template + conventions |
| 6 | **ADR-0001/0002/0003** pour tracer les décisions | Traçabilité, justifications, alternatives écartées |
| 7 | **Gitflow + Conventional Commits** | Workflow éprouvé, semver, protection `main` |
| 8 | **CI minimale** : lint MD + link-check + mermaid render | < 2 min, bloque les régressions |
| 9 | **§ supplémentaires dans PARTIE_VI** pour scénarios omis | Pas de nouvelles PARTIES, intégration au doc existant |
| 10 | **Codeowners + PR template** | Revues obligatoires, contexte standardisé |

## Architecture cible

```
workspace/
├── AGENTS.md              (mis à jour : mention ADR + structure)
├── README.md              (mis à jour : nouvelle structure)
├── CONTRIBUTING.md        (NOUVEAU : workflow contributeur)
├── CHANGELOG.md           (NOUVEAU : semver)
├── opencode.json          (mis à jour : eraser MCP activé)
├── mcp.json               (inchangé)
├── .github/               (NOUVEAU)
│   ├── CODEOWNERS
│   ├── PULL_REQUEST_TEMPLATE.md
│   ├── ISSUE_TEMPLATE/{bug,feature,doc}.md
│   └── workflows/ci.yml
├── .opencode/skills/      (17 skills + 1 nouveau eraser-diagrams)
├── .agents/skills/        (inchangé)
├── docs/
│   ├── 00-INDEX.md        (NOUVEAU : master TOC)
│   ├── SystemDesignDoc.md (inchangé)
│   ├── PARTIE_VI.md       (étendu : +7 § pour scénarios omis)
│   ├── parties/           (NOUVEAU)
│   │   ├── README.md
│   │   ├── TEMPLATE.md
│   │   ├── CONVENTIONS.md
│   │   └── INDEX.md
│   ├── diagrams/
│   │   ├── INDEX.md       (NOUVEAU)
│   │   └── eraser/        (NOUVEAU : 4 fichiers .eraser + 4 docs .md)
│   ├── adr/               (NOUVEAU)
│   │   ├── README.md
│   │   ├── 0001-use-bun-runtime.md
│   │   ├── 0002-hybrid-diagrams-mermaid-eraser.md
│   │   └── 0003-gitflow-conventional-commits.md
│   ├── agents/            (inchangé)
│   └── superpowers/       (NOUVEAU)
│       ├── specs/2026-06-01-csph-doc-architecture-design.md (ce fichier)
│       └── plans/2026-06-01-csph-doc-architecture.md
```

## Hors-scope (différé)

- Création de nouvelles PARTIES (VII, VIII, …) — à la demande de l'utilisateur, fait plus tard.
- Migration Mermaid → eraser.io des 15 diagrammes existants.
- Mise en place effective de GitHub remote (URL à fournir par l'équipe).
- Skill dédiée `eraser-diagrams` (créée en bonus, activation optionnelle).
- Génération automatique de TOC / changelog (CI renforcée — reportée).

## Risques identifiés

| Risque | Mitigation |
|--------|------------|
| Bun moins battle-tested que Go pour 99,5% SLA | Tests intégration poussés, OTEL dès J1, chaos quarterly |
| Mermaid + eraser = double source de vérité | eraser = source de vérité tech, Mermaid = annexe portable |
| Equipe crée des PARTIES sans respecter conventions | Template + linter markdown en CI (futur) |
| Liens internes cassés | CI `markdown-link-check` |
| Diag Mermaid cassent en rendu | CI `mermaid-cli` validation |

## Métriques de succès

- 0 lien mort dans la doc (validé en CI).
- 0 erreur de rendu Mermaid (validé en CI).
- Toute nouvelle décision passe par un ADR numéroté.
- Toute nouvelle PARTIE respecte le template.
- `git log` lisible : `feat:`, `fix:`, `docs:` visibles.
