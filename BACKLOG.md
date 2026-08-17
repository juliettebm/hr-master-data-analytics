# Backlog produit : Dashboard RH Attrition & Master Data

Ma mission : fiabiliser le suivi de l'attrition et de l'équité salariale pour la direction RH. Le besoin est découpé ci-dessous en user stories avec critères d'acceptation, KPIs associés et priorisation. Le notebook (`notebooks/01_hr_analytics_kpis.ipynb`) est le livrable qui répond à ce backlog.

## Contexte produit

Une direction RH veut suivre l'attrition et identifier les leviers d'action, mais dispose aujourd'hui de données dispersées et non cataloguées. Le produit cible est un dashboard de suivi RH appuyé sur un référentiel de données documenté (voir `DATA_CATALOG.md`), pas juste un export ponctuel.

## Epics

### Epic 1 : Fiabiliser la donnée avant tout dashboard
Sans ça, n'importe quel KPI construit dessus est invérifiable.

| # | User story | Critères d'acceptation | Priorité (MoSCoW) |
|---|---|---|---|
| 1.1 | En tant que Data/PO, je veux un catalogue de données RH documenté afin que toute personne qui consomme le dashboard sache ce que chaque champ signifie et ses limites | Chaque champ du jeu de données a une définition métier, un type et une règle de qualité associée | Must |
| 1.2 | En tant que PO, je veux un audit de qualité automatisé (complétude, unicité, champs constants) afin de ne pas propager une anomalie de données dans un KPI | L'audit tourne dans le notebook et produit un rapport reproductible, pas une vérification manuelle ponctuelle | Must |

### Epic 2 : Suivre le risque d'attrition

| # | User story | Critères d'acceptation | KPI associé | Priorité |
|---|---|---|---|---|
| 2.1 | En tant que HR Business Partner, je veux voir le taux d'attrition par département et par poste afin de prioriser mes actions de rétention | Le dashboard affiche un taux d'attrition ventilé, avec les effectifs sous-jacents visibles (pas juste un %) | Taux d'attrition par `Department` / `JobRole` | Must |
| 2.2 | En tant que HR Business Partner, je veux comprendre quels facteurs sont associés à l'attrition (satisfaction, heures sup, ancienneté sans promotion) afin d'agir sur les causes plutôt que sur le symptôme | Le dashboard croise `Attrition` avec au moins 3 facteurs différents, avec un test statistique associé, pas juste une intuition visuelle | Attrition vs `JobSatisfaction`, `OverTime`, `YearsSinceLastPromotion` | Must |

### Epic 3 : Suivre l'équité et le bien-être

| # | User story | Critères d'acceptation | KPI associé | Priorité |
|---|---|---|---|---|
| 3.1 | En tant que responsable RSE/RH, je veux suivre l'écart de rémunération par genre à poste et niveau équivalents afin de détecter une inéquité salariale | Le dashboard compare `MonthlyIncome` par `Gender`, contrôlé par `JobLevel` et `JobRole` (pas une moyenne brute non contrôlée, qui serait trompeuse) | Écart de revenu médian par genre, à niveau de poste équivalent | Should |
| 3.2 | En tant que HR Business Partner, je veux suivre le work-life balance déclaré par département afin d'anticiper un risque d'épuisement avant qu'il se traduise en départs | Le dashboard affiche la distribution de `WorkLifeBalance` par `Department` | Score moyen `WorkLifeBalance` par département | Could |

### Epic 4 : Rendre le dashboard exploitable sans moi (documentation utilisateur)
C'est la brique "mode d'emploi du dashboard" qu'on retrouve dans les missions PO de ce type de poste.

| # | User story | Critères d'acceptation | Priorité |
|---|---|---|---|
| 4.1 | En tant que nouvel utilisateur du dashboard, je veux un guide court expliquant chaque graphique et sa lecture afin de ne pas mal interpréter un KPI | Une section "Observations" accompagne chaque KPI clé dans le notebook | Must |

### Epic 5 : Prioriser la rétention par score de risque

| # | User story | Critères d'acceptation | KPI associé | Priorité |
|---|---|---|---|---|
| 5.1 | En tant que HR Business Partner, je veux un score de risque de départ par collaborateur afin de prioriser mes actions de rétention avant le départ plutôt qu'après | Le modèle retenu est comparé à une référence naïve sur PR-AUC (pas ROC-AUC seule, vu le déséquilibre de classes), et le score est exporté par collaborateur | Score de risque, bande Faible/Moyen/Élevé | Should |

## Roadmap simplifiée (sprints)

- **Sprint 1** : Epic 1 (catalogue + audit qualité), condition bloquante pour tout le reste.
- **Sprint 2** : Epic 2 (attrition et facteurs associés), cœur de la valeur métier.
- **Sprint 3** : Epic 3 (équité, bien-être), enrichissement.
- **Sprint 4** : Epic 4 (documentation utilisateur), en continu plutôt qu'un lot isolé, listé à part ici pour la lisibilité du backlog.
- **Sprint 5** : Epic 5 (score de risque), notebook complémentaire une fois le socle KPI stable.

## Definition of Done

Une user story est "faite" si : le KPI est calculé dans le notebook à partir d'une donnée passée par le catalogue, le résultat est accompagné d'une explication de lecture, et la limite méthodologique éventuelle est mentionnée plutôt que passée sous silence.
