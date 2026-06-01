# 0001. Adopter Bun comme runtime backend

- **Statut** : accepted
- **Date** : 2026-06-01
- **Auteur** : Équipe DTI Solutions
- **Décideurs** : Lead architecture DTI, Lead backend DTI, CSPH (relecture)

## Contexte et énoncé du problème

Le backend du système de traçabilité CSPH doit absorber :
- ~17 messages/seconde en pointe (500 camions, frame toutes les 30 s)
- 99,5 % de SLA (réglementaire)
- Ingestion via MQTT (camions) + HTTPS REST (PDAs)
- 9 microservices prévus (cf. [`PARTIE_VI §15`](../PARTIE_VI.md#15-backend--traitement-des-données))

Le langage/runtime doit permettre :
1. Une vélocité élevée de l'équipe (compétence interne).
2. Une performance I/O concurrente (parité Go).
3. Un typage fort partagé avec le dashboard Next.js (TypeScript bout-en-bout).
4. Un écosystème IoT mature (MQTT, Kafka, PostgreSQL, Redis, X.509).

## Options considérées

### Option 1 — Go 1.22+

- ✅ Performance brute, goroutines, binary unique
- ✅ Écosystème IoT mature (paho, sarama, pgx)
- ✅ Battle-tested pour 99,5 % SLA
- ❌ Équipe DTI peu expérimentée (montée en pente)
- ❌ Pas de partage de types avec Next.js
- ❌ Verbosité pour CRUD / API REST

### Option 2 — Node.js 22 LTS + TypeScript

- ✅ Écosystème npm immense
- ✅ TypeScript natif
- ✅ Compétence équipe existante
- ❌ Event-loop mono-thread, plus lent que Go pour I/O concurrents
- ❌ Démarrage plus lent que Bun
- ❌ Pas de test runner intégré aussi rapide

### Option 3 — Bun 1.x (choisi)

- ✅ Runtime TypeScript natif, **zéro transpilation**
- ✅ `Bun.serve()` 2-3× plus rapide qu'Express
- ✅ `bun test` intégré, ultra rapide
- ✅ npm-compatible (99 % des packages fonctionnent)
- ✅ Worker threads pour CPU-bound (crypto X.509, ML fraude)
- ✅ Démarrage < 50 ms (cold start idéal pour K8s)
- ✅ Partage de types avec Next.js via `packages/contracts/`
- ✅ Compétence équipe DTI (raison principale)
- ⚠️ Moins battle-tested que Go pour SLA critique (mitigé par tests + observabilité)
- ⚠️ Quelques packages natifs manquants (rare)

### Option 4 — Deno 1.4x

- ✅ Sécurité par défaut, TypeScript natif
- ✅ Permissions granulaires
- ❌ Écosystème npm plus petit que Bun
- ❌ Moins connu de l'équipe

## Décision

**Option 3 — Bun 1.x** est retenu.

**Justifications principales** :
1. **Compétence équipe** : l'équipe maîtrise Bun, pas Go. C'est le critère numéro un.
2. **TypeScript bout-en-bout** : types partagés avec le dashboard = moins de bugs d'interface.
3. **Performance suffisante** : Bun atteint 80-90 % de la perf Go pour I/O concurrents, ce qui suffit pour 17 msg/s.
4. **Vélocité** : `bun test`, `bun install`, hot reload = itération rapide.
5. **Écosystème** : compatibilité npm couvre 99 % des besoins (mqtt.js, kafkajs, pg, ioredis, node-forge).

**Mitigations pour le risque SLA 99,5 %** :
- Tests d'intégration exhaustifs (cf. `PARTIE_VI §15.5`).
- Observabilité **OpenTelemetry** dès le jour 1.
- Graceful shutdown + health probes K8s obligatoires.
- **Chaos engineering** : tests trimestriels de panne d'un pod.
- **Plan B** : adapter Go uniquement pour `ingestion-camions` (hot path) si SLA non tenu.

## Conséquences

### Positives
- Onboarding rapide des nouveaux devs (compétence existante).
- Partage de types TS → API contractuel plus strict.
- Démarrage rapide → cold start K8s économique.
- Tests ultra rapides → TDD encouragé.

### Négatives
- Moins de recul en production critique IoT que Go.
- Documentation et Stack Overflow moins fournie.
- Quelques packages natifs à compiler (rare).

### Neutres
- L'équipe doit se former à Bun.serve() et aux patterns worker threads.
- Les ADR futurs qui mentionnent "service backend" supposent Bun par défaut.

## Liens

- **PARTIES liées** : [`PARTIE_VI §15`](../PARTIE_VI.md#15-backend--traitement-des-données), [`PARTIE_VI §15.5`](../PARTIE_VI.md#155-performance--concurrence-bun-tuned) (à créer)
- **Compétences** : [Skill `iot-development`](../agents/), [Skill `api-design-ciph`](../agents/)
- **Documentation Bun** : https://bun.sh/docs
- **ADR liés** : [0002 (stratégie diagrammes)](./0002-hybrid-diagrams-mermaid-eraser.md), [0003 (Git workflow)](./0003-gitflow-conventional-commits.md)
