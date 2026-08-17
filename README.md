# 🧑‍💼 HR Master Data Analytics

[![Dataset](https://img.shields.io/badge/Dataset-IBM%20HR%20Analytics-blue?logo=databricks&logoColor=white)](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset)
[![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python&logoColor=white)](https://www.python.org/)
[![SQL](https://img.shields.io/badge/SQL-DuckDB%20(Snowflake--transposable)-lightgrey?logo=duckdb&logoColor=white)](https://duckdb.org/)
[![SciPy](https://img.shields.io/badge/SciPy-Chi%C2%B2%20%7C%20Mann--Whitney%20U-orange?logo=scipy&logoColor=white)](https://scipy.org/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-Logistic%20Regression%20%7C%20Random%20Forest-orange?logo=scikit-learn&logoColor=white)](https://scikit-learn.org/)
[![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-yellow?logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)](https://jupyter.org/)

Une direction RH me confie le suivi de l'attrition et de l'équité salariale sur 1 470 collaborateurs (IBM HR Analytics). Avant de livrer le moindre chiffre, je documente la donnée (catalogue), je cadre le besoin en backlog (user stories, KPIs), je requête en SQL, puis je restitue avec un guide de lecture.

## Mission

Donner à la direction RH une vision fiable de l'attrition, de ses facteurs, et de l'équité salariale, appuyée sur un référentiel de données documenté plutôt que sur un export ponctuel. Avant de calculer le moindre KPI, je documente la donnée (dictionnaire de champs, règles de qualité), je cadre le besoin métier en user stories avec critères d'acceptation, et je requête en SQL. L'attrition n'est pas prédite ici, elle est mesurée et expliquée : la valeur est dans la méthode qui précède le chiffre.

## Structure du projet

```
hr-master-data-analytics/
├── data/
│   ├── hr_employee_attrition.csv       # jeu de données source (IBM HR Analytics, public)
│   └── processed/powerbi/              # score de risque exporté par le notebook 02
├── notebooks/
│   ├── 01_hr_analytics_kpis.ipynb      # audit qualité + KPIs + requêtage SQL + visualisations
│   └── 02_attrition_risk_model.ipynb   # modèle de risque de départ + export Power BI
├── reports/
│   └── *.png                           # figures exportées des deux notebooks
├── DATA_CATALOG.md                     # dictionnaire de champs, glossaire, règles de qualité
├── BACKLOG.md                          # epics, user stories, KPIs, roadmap, definition of done
├── USER_GUIDE.md                       # guide de lecture du dashboard, indépendant du notebook
├── requirements.txt
└── README.md
```

## Dataset

| | |
|---|---|
| Source | [IBM HR Analytics Employee Attrition & Performance](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset) (jeu fictif, généré par IBM pour démontrer Watson Analytics, pas une extraction réelle) |
| Volume | 1 470 collaborateurs, 35 champs |
| Catalogué dans | `DATA_CATALOG.md` |

## Reproduire

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/01_hr_analytics_kpis.ipynb
```

Le notebook 02 se lance de la même façon, une fois le 01 exécuté au moins une fois.

## Contenu des notebooks

### `01_hr_analytics_kpis.ipynb`

1. **Mission et contexte**
2. **KPIs retenus** : les quatre indicateurs choisis et pourquoi, avant d'écrire la moindre ligne de calcul.
3. **Audit qualité** : complétude, unicité de la clé primaire (`EmployeeNumber`), détection des champs constants (`EmployeeCount`, `Over18`, `StandardHours`), exécuté en code, pas vérifié à l'œil.
4. **Requêtage SQL** (DuckDB, dialecte transposable à Snowflake) : les KPIs d'attrition par département et par poste sont écrits en `SELECT` / `GROUP BY` explicites, pas en `pandas.groupby` caché.
5. **KPI attrition par département et par poste**, avec effectifs affichés à côté du taux pour ne pas lire un pourcentage hors contexte.
6. **Facteurs associés à l'attrition** (heures supplémentaires, satisfaction au poste, ancienneté depuis la dernière promotion), chacun testé statistiquement (Chi², Mann-Whitney U), pas juste comparé visuellement.
7. **Équité salariale par genre**, contrôlée par niveau de poste (`JobLevel`), pour éviter la moyenne brute trompeuse.
8. **Bien-être par département** (Work-Life Balance déclaré).
9. **Deux questions ad hoc** : distance domicile-travail par poste, revenu par niveau d'études, croisés avec l'attrition.
10. **Synthèse**.

### `02_attrition_risk_model.ipynb`

Estime un score de risque de départ par collaborateur, pour prioriser les actions de rétention avant le départ plutôt qu'après. Trois modèles comparés (baseline, régression logistique, forêt aléatoire) sur PR-AUC plutôt que ROC-AUC seule, vu le déséquilibre de classes (16 % de départs). Le score final est exporté en CSV pour Power BI.

Un guide de lecture indépendant, pensé pour quelqu'un qui n'a pas écrit le code, est disponible dans `USER_GUIDE.md`.

## Dashboard Power BI

Le notebook 02 exporte `data/processed/powerbi/hr_attrition_risk_scores.csv` (score et bande de risque par collaborateur, KPIs contextuels). Pas de fichier `.pbix` fourni : ouvrir Power BI Desktop, importer ce CSV, et construire les visuels décrits dans `BACKLOG.md` (Epic 2 et 3) à partir des champs `risk_score` / `risk_band`.

## Principaux résultats (obtenus en exécutant les notebooks)

- Taux d'attrition par département : Ventes 20,6 % (92/446), RH 19,0 % (12/63), R&D 13,8 % (133/961). Au niveau des postes, le poste le plus exposé est Sales Representative (39,8 %, 33/83), le moins exposé Manufacturing Director (6,9 %).
- Trois facteurs testés sont statistiquement associés à l'attrition (p < 0,05) : faire des heures supplémentaires (p ≈ 8·10⁻²¹, l'association la plus forte des trois), la satisfaction au poste (p ≈ 5,6·10⁻⁴) et le nombre d'années depuis la dernière promotion (p ≈ 0,041).
- Écart de revenu médian par genre, contrôlé par niveau de poste : entre -2,5 % et +2,0 % selon le niveau, aucun écart systémique marqué détecté à ce niveau d'agrégation sur ce jeu de données. C'est un résultat négatif, il est gardé tel quel plutôt que reformulé pour paraître plus intéressant.
- Question ad hoc distance domicile-travail : sur le poste Healthcare Representative, les collaborateurs partis habitaient en moyenne 8,5 km plus loin que ceux restés, un effet localisé à certains postes plutôt que général.
- Question ad hoc revenu par niveau d'études : à chaque niveau (y compris Doctor), les collaborateurs partis gagnaient entre 24,7 % et 35,0 % de moins que ceux restés.
- Modèle de risque de départ : régression logistique retenue (PR-AUC 0,598, ROC-AUC 0,798) contre une référence naïve à 0,155 PR-AUC, et contre une forêt aléatoire moins performante ici (PR-AUC 0,417). 285 collaborateurs classés en risque élevé sur 1 470.

## Stack

Python, pandas, matplotlib, seaborn, scipy (tests statistiques), scikit-learn (modélisation), DuckDB (SQL), Jupyter, nbformat/nbclient (génération et exécution reproductible des notebooks).

## Notes méthodologiques (pourquoi X plutôt que Y)

**Pourquoi choisir les KPIs avant d'écrire le moindre calcul ?** Sans ça, le notebook aurait pu partir dans n'importe quelle direction analysable sur ce jeu de données (35 champs). Les quatre KPIs retenus découlent directement de la mission (localiser l'attrition, comprendre ses facteurs, vérifier l'équité, anticiper via le bien-être), pas d'une exploration au hasard.

**Pourquoi un catalogue de données avant tout calcul, et pas directement l'analyse ?** Parce que c'est l'ordre dans lequel ça se passe dans un vrai contexte de gouvernance Master Data : sans ça, un champ constant comme `StandardHours` pourrait être confondu avec un bug de chargement, ou un champ comme `MonthlyRate` (sans définition métier stable) pourrait être utilisé à tort dans un KPI de rémunération.

**Pourquoi des tests statistiques plutôt que des graphiques seuls pour les facteurs d'attrition ?** Un graphique peut suggérer une différence qui n'est qu'un artefact d'échantillonnage. Le test (Chi² pour les variables catégorielles/ordinales, Mann-Whitney U pour la variable continue non gaussienne) donne un seuil de décision explicite plutôt qu'une impression visuelle.

**Pourquoi contrôler l'écart salarial par niveau de poste plutôt qu'une moyenne brute par genre ?** Si la répartition hommes/femmes n'est pas homogène entre niveaux hiérarchiques, une moyenne brute peut afficher un écart qui n'existe pas à niveau égal, ou masquer un écart réel. Contrôler par `JobLevel` évite ce biais de composition.

**Pourquoi la PR-AUC plutôt que la ROC-AUC seule pour choisir le modèle de risque ?** Avec 16 % de départs, une ROC-AUC élevée peut rester atteignable même par un modèle peu utile en pratique. La PR-AUC est plus sensible au déséquilibre de classes, donc plus fiable comme critère de sélection ici.

## Source des données

[IBM HR Analytics Employee Attrition & Performance](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset). Jeu de données fictif, créé par les data scientists d'IBM pour démontrer l'outil Watson Analytics, pas une extraction réelle de système RH. Choisi malgré ça (comme la quasi-totalité des jeux de données RH publics, réels ou synthétiques) parce que les données RH réelles ne sont quasiment jamais publiées, pour des raisons de confidentialité. L'objectif de ce projet est la méthode (catalogue, backlog, SQL, modèle), pas une conclusion RH généralisable.

## Auteur

Juliette Bouli-Mengue
