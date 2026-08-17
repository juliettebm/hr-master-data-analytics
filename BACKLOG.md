# Backlog produit : Dashboard RH Attrition & Master Data

Ce document simule le travail qu'un Product Owner ferait en amont d'un développement : partir d'un besoin métier, le découper en user stories avec des critères d'acceptation, définir les KPIs qui en découlent, prioriser, et prévoir des itérations. Le notebook (`notebooks/01_hr_analytics_kpis.ipynb`) est le livrable qui répond à ce backlog.

## Contexte produit

Une direction RH veut suivre l'attrition et identifier les leviers d'action, mais dispose aujourd'hui de données dispersées et non cataloguées. Le produit cible est un dashboard de suivi RH appuyé sur un référentiel de données documenté (voir `DATA_CATALOG.md`), pas juste un export ponctuel.

## Epics

### Epic 1 — Fiabiliser la donnée avant tout dashboard
Sans ça, n'importe quel KPI construit dessus est invérifiable.

| # | User story | Critères d'acceptation | Priorité (MoSCoW) |
|---|---|---|---|
| 1.1 | En tant que Data/PO, je veux un catalogue de données RH documenté afin que toute personne qui consomme le dashboard sache ce que chaque champ signifie et ses limites | Chaque champ du jeu de données a une définition métier, un type et une règle de qualité associée | Must |
| 1.2 | En tant que PO, je veux un audit de qualité automatisé (complétude, unicité, champs constants) afin de ne pas propager une anomalie de données dans un KPI | L'audit tourne dans le notebook et produit un rapport reproductible, pas une vérification manuelle ponctuelle | Must |

### Epic 2 — Suivre le risque d'attrition

| # | User story | Critères d'acceptation | KPI associé | Priorité |
|---|---|---|---|---|
| 2.1 | En tant que HR Business Partner, je veux voir le taux d'attrition par département et par poste afin de prioriser mes actions de rétention | Le dashboard affiche un taux d'attrition ventilé, avec les effectifs sous-jacents visibles (pas juste un %) | Taux d'attrition par `Department` / `JobRole` | Must |
| 2.2 | En tant que HR Business Partner, je veux comprendre quels facteurs sont associés à l'attrition (satisfaction, heures sup, ancienneté sans promotion) afin d'agir sur les causes plutôt que sur le symptôme | Le dashboard croise `Attrition` avec au moins 3 facteurs différents, avec un test statistique associé, pas juste une intuition visuelle | Attrition vs `JobSatisfaction`, `OverTime`, `YearsSinceLastPromotion` | Must |

### Epic 3 — Suivre l'équité et le bien-être

| # | User story | Critères d'acceptation | KPI associé | Priorité |
|---|---|---|---|---|
| 3.1 | En tant que responsable RSE/RH, je veux suivre l'écart de rémunération par genre à poste et niveau équivalents afin de détecter une inéquité salariale | Le dashboard compare `MonthlyIncome` par `Gender`, contrôlé par `JobLevel` et `JobRole` (pas une moyenne brute non contrôlée, qui serait trompeuse) | Écart de revenu médian par genre, à niveau de poste équivalent | Should |
| 3.2 | En tant que HR Business Partner, je veux suivre le work-life balance déclaré par département afin d'anticiper un risque d'épuisement avant qu'il se traduise en départs | Le dashboard affiche la distribution de `WorkLifeBalance` par `Department` | Score moyen `WorkLifeBalance` par département | Could |

### Epic 4 — Rendre le dashboard exploitable sans moi (documentation utilisateur)
C'est la brique "mode d'emploi du dashboard" qu'on retrouve dans les missions PO de ce type de poste.

| # | User story | Critères d'acceptation | Priorité |
|---|---|---|---|
| 4.1 | En tant que nouvel utilisateur du dashboard, je veux un guide court expliquant chaque graphique et sa lecture afin de ne pas mal interpréter un KPI | Une section "Comment lire ce résultat" accompagne chaque KPI clé dans le notebook | Must |

## Roadmap simplifiée (sprints)

- **Sprint 1** : Epic 1 (catalogue + audit qualité) — condition bloquante pour tout le reste.
- **Sprint 2** : Epic 2 (attrition et facteurs associés) — cœur de la valeur métier.
- **Sprint 3** : Epic 3 (équité, bien-être) — enrichissement.
- **Sprint 4** : Epic 4 (documentation utilisateur) — en continu, pas un lot isolé en réalité, listé à part ici pour la lisibilité du backlog.

## Definition of Done

Une user story est "faite" si : le KPI est calculé dans le notebook à partir d'une donnée passée par le catalogue, le résultat est accompagné d'une explication de lecture, et la limite méthodologique éventuelle est mentionnée plutôt que passée sous silence.

## Itérations et retours (note de transparence)

Ce projet est réalisé en solo sur un jeu de données public, il n'y a pas eu de vrai cycle de co-design avec des utilisateurs métier ni de beta-test. Le backlog ci-dessus est structuré comme s'il en existait un (c'est l'exercice recherché : montrer la méthode), mais je ne prétends pas avoir animé une communauté d'utilisateurs sur ce projet précis. Cette compétence-là (animation Teams, co-design, beta-test) reste à démontrer en situation réelle, pas simulée ici.
