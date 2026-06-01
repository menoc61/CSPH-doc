# Index des PARTIES

> Tableau de toutes les PARTIES du mémoire technique CSPH GPL Tracking.
> **À maintenir à jour** à chaque création/MAJ/archivage d'une PARTIE.

| ID | Titre | Statut | Auteur | Créé | MAJ | Lien | ADR liés |
|----|-------|--------|--------|------|-----|------|----------|
| VI | Conception du système (HLD & Backend) | ✅ published | Équipe DTI | 2026-05 | 2026-06-01 | [PARTIE_VI.md](../PARTIE_VI.md) | 0001, 0002, 0003 |
| TEMPLATE | Squelette réutilisable | 🛠 tool | — | 2026-06-01 | 2026-06-01 | [TEMPLATE.md](./TEMPLATE.md) | — |

## PARTIES à venir (roadmap indicative)

> Liste non-exhaustive, à arbitrer par l'équipe. Voir [`00-INDEX.md`](../00-INDEX.md#2-parties-du-mémoire-technique).

| ID | Titre envisagé | Priorité | Notes |
|----|----------------|----------|-------|
| VII | Dimensionnement & Capacity Planning | 🔴 Haute | Calculs chiffrés, scaling 500→5000 camions |
| VIII | Opérations, Observabilité & SRE | 🟠 Moyenne | Prometheus/Grafana, SLO 99,5% |
| IX | Déploiement, CI/CD & Migration | 🟠 Moyenne | Pipeline, OTA firmware, feature flags |
| X | Résilience, Reprise après sinistre & Tests | 🟠 Moyenne | DR multi-zones, chaos engineering |
| XI | Conformité, Réglementaire & Audit | 🟡 Basse | ATEX/ADR/CEMAC détaillés |
| XII | Coûts, TCO & Modèle économique | 🟡 Basse | Infra, licences, ROI |

**Légende priorité :** 🔴 Haute · 🟠 Moyenne · 🟡 Basse

---

> **Pour proposer une nouvelle PARTIE** : créer une issue avec le template `doc.md`
> (voir [`.github/ISSUE_TEMPLATE/doc.md`](../../.github/ISSUE_TEMPLATE/doc.md))
> et copier [`TEMPLATE.md`](./TEMPLATE.md) comme base.
