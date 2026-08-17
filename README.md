# 🧑‍💼 HR Master Data Analytics

[![Dataset](https://img.shields.io/badge/Dataset-IBM%20HR%20Analytics-blue?logo=databricks&logoColor=white)](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset)
[![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python&logoColor=white)](https://www.python.org/)
[![SQL](https://img.shields.io/badge/SQL-DuckDB%20(Snowflake--transposable)-lightgrey?logo=duckdb&logoColor=white)](https://duckdb.org/)
[![SciPy](https://img.shields.io/badge/SciPy-Chi%C2%B2%20%7C%20Mann--Whitney%20U-orange?logo=scipy&logoColor=white)](https://scipy.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)](https://jupyter.org/)

Analyse de l'attrition, des facteurs de départ et de l'équité salariale sur 1 470 collaborateurs (jeu de données IBM HR Analytics), construite comme un vrai produit data d'entreprise plutôt qu'une simple étude : catalogue de données audité, backlog produit avec user stories et KPIs, requêtage SQL, et un dashboard accompagné de son guide de lecture.

## Objectif

Ce n'est pas un projet de prédiction : il n'y a pas de modèle de machine learning ici, l'attrition n'est pas prédite, elle est mesurée et expliquée. Le sujet est en amont du dashboard, pas la performance d'un modèle : avant de calculer le moindre KPI RH, la donnée est documentée (dictionnaire de champs, règles de qualité), le besoin métier est formulé en user stories avec critères d'acceptation, et les KPIs qui en découlent sont justifiés plutôt qu'arbitraires. Le résultat final documente lui-même sa propre lecture et ses limites, plutôt que de livrer un chiffre sans contexte.

## Structure du projet

```
hr-master-data-analytics/
├── data/
│   └── hr_employee_attrition.csv       # jeu de données source (IBM HR Analytics, public)
├── notebooks/
│   └── 01_hr_analytics_kpis.ipynb      # audit qualité + calcul des KPIs + visualisations
├── reports/
│   └── *.png                           # figures exportées du notebook
├── DATA_CATALOG.md                     # dictionnaire de champs, glossaire, règles de qualité
├── BACKLOG.md                          # epics, user stories, KPIs, roadmap, definition of done
├── USER_GUIDE.md                       # guide de lecture du dashboard, indépendant du notebook
├── requirements.txt
└── README.md
```

## Dataset

| | |
|---|---|
| Source | [IBM HR Analytics Employee Attrition & Performance](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset) (jeu public, fictif, à but pédagogique) |
| Volume | 1 470 collaborateurs, 35 champs |
| Catalogué dans | `DATA_CATALOG.md` |

## Reproduire

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/01_hr_analytics_kpis.ipynb
```

## Contenu du notebook

1. **Contexte et objectif**
2. **Audit qualité** : complétude, unicité de la clé primaire (`EmployeeNumber`), détection des champs constants (`EmployeeCount`, `Over18`, `StandardHours`), exécuté en code, pas vérifié à l'œil.
3. **Requêtage SQL** (DuckDB, dialecte transposable à Snowflake) : les KPIs d'attrition par département et par poste sont écrits en `SELECT` / `GROUP BY` explicites, pas en `pandas.groupby` caché.
4. **KPI attrition par département et par poste**, avec effectifs affichés à côté du taux pour ne pas lire un pourcentage hors contexte.
5. **Facteurs associés à l'attrition** (heures supplémentaires, satisfaction au poste, ancienneté depuis la dernière promotion), chacun testé statistiquement (Chi², Mann-Whitney U), pas juste comparé visuellement.
6. **Équité salariale par genre**, contrôlée par niveau de poste (`JobLevel`), pour éviter la moyenne brute trompeuse.
7. **Bien-être par département** (Work-Life Balance déclaré).
8. **Synthèse** : ce qui a été livré par rapport au backlog, et les limites qui ne sont pas masquées.

Un guide de lecture indépendant, pensé pour quelqu'un qui n'a pas écrit le code, est disponible dans `USER_GUIDE.md`.

## Principaux résultats (obtenus en exécutant le notebook)

- Taux d'attrition par département : Ventes 20,6 % (92/446), RH 19,0 % (12/63), R&D 13,8 % (133/961). Au niveau des postes, le poste le plus exposé est Sales Representative (39,8 %, 33/83), le moins exposé Manufacturing Director (6,9 %).
- Trois facteurs testés sont statistiquement associés à l'attrition (p < 0,05) : faire des heures supplémentaires (p ≈ 8·10⁻²¹, l'association la plus forte des trois), la satisfaction au poste (p ≈ 5,6·10⁻⁴) et le nombre d'années depuis la dernière promotion (p ≈ 0,041).
- Écart de revenu médian par genre, contrôlé par niveau de poste : entre -2,5 % et +2,0 % selon le niveau, aucun écart systémique marqué détecté à ce niveau d'agrégation sur ce jeu de données. C'est un résultat négatif, il est gardé tel quel plutôt que reformulé pour paraître plus intéressant.

## Stack

Python, pandas, matplotlib, seaborn, scipy (tests statistiques), Jupyter, nbformat/nbclient (génération et exécution reproductible du notebook).

## Notes méthodologiques (pourquoi X plutôt que Y)

**Pourquoi un catalogue de données avant tout calcul, et pas directement l'analyse ?** Parce que c'est l'ordre dans lequel ça se passe dans un vrai contexte de gouvernance Master Data : sans ça, un champ constant comme `StandardHours` pourrait être confondu avec un bug de chargement, ou un champ comme `MonthlyRate` (sans définition métier stable) pourrait être utilisé à tort dans un KPI de rémunération.

**Pourquoi des tests statistiques plutôt que des graphiques seuls pour les facteurs d'attrition ?** Un graphique peut suggérer une différence qui n'est qu'un artefact d'échantillonnage. Le test (Chi² pour les variables catégorielles/ordinales, Mann-Whitney U pour la variable continue non gaussienne) donne un seuil de décision explicite plutôt qu'une impression visuelle.

**Pourquoi contrôler l'écart salarial par niveau de poste plutôt qu'une moyenne brute par genre ?** Si la répartition hommes/femmes n'est pas homogène entre niveaux hiérarchiques, une moyenne brute peut afficher un écart qui n'existe pas à niveau égal, ou masquer un écart réel. Contrôler par `JobLevel` évite ce biais de composition.

**Pourquoi le volet "animation de communauté / co-design" du backlog n'a pas été simulé ?** Parce que ce projet est réalisé en solo sur un jeu de données public, prétendre à un cycle de co-design ou de beta-test inventé serait trompeur. Le backlog est structuré comme si ce cycle existait (c'est la méthode qui est démontrée), la note de transparence dans `BACKLOG.md` le dit explicitement.

## Source des données

[IBM HR Analytics Employee Attrition & Performance](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset), jeu de données public et fictif publié par IBM.

## Auteur

Juliette Bouli-Mengue
