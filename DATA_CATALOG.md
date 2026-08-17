# Catalogue de données : RH Employee Attrition

Catalogue de données au sens gouvernance/Master Data : un inventaire structuré des champs, leur définition métier, leur qualité mesurée et les règles qui s'y appliquent. Ce n'est pas juste une liste de colonnes, c'est le document qu'on donnerait à un product owner ou à un nouvel analyste avant qu'il touche au jeu de données, pour éviter qu'il réinvente une définition métier ou rate une anomalie déjà connue.

## 1. Domaines métier (regroupement des champs)

Le jeu de données mélange plusieurs domaines Master Data qui, dans un vrai SI RH, vivraient probablement dans des tables ou des référentiels distincts. Les regrouper ici sert à documenter les frontières logiques avant toute jointure ou tout nouveau dashboard.

| Domaine | Champs | Rôle |
|---|---|---|
| Identité collaborateur | `EmployeeNumber`, `Age`, `Gender`, `MaritalStatus`, `Over18` | Référentiel individu |
| Organisation | `Department`, `JobRole`, `JobLevel`, `BusinessTravel` | Rattachement structurel |
| Rémunération | `DailyRate`, `HourlyRate`, `MonthlyIncome`, `MonthlyRate`, `PercentSalaryHike`, `StockOptionLevel` | Données sensibles, accès restreint dans un vrai SI |
| Engagement et satisfaction | `EnvironmentSatisfaction`, `JobSatisfaction`, `JobInvolvement`, `RelationshipSatisfaction`, `WorkLifeBalance` | Mesures déclaratives (enquêtes internes) |
| Performance et développement | `PerformanceRating`, `TrainingTimesLastYear`, `Education`, `EducationField` | Évaluation et montée en compétence |
| Ancienneté et mobilité | `TotalWorkingYears`, `YearsAtCompany`, `YearsInCurrentRole`, `YearsSinceLastPromotion`, `YearsWithCurrManager`, `NumCompaniesWorked`, `DistanceFromHome` | Trajectoire du collaborateur |
| Cible / signal | `Attrition`, `OverTime` | Sert au calcul des KPIs de risque |

## 2. Dictionnaire de champs

| Champ | Type | Définition métier | Valeurs / plage observée |
|---|---|---|---|
| `EmployeeNumber` | int, clé primaire | Identifiant unique du collaborateur | 1 à 2068, 1470 valeurs distinctes pour 1470 lignes : pas de doublon |
| `Age` | int | Âge en années | 18-60 |
| `Attrition` | catégoriel (2) | Le collaborateur a-t-il quitté l'entreprise | Yes / No |
| `BusinessTravel` | catégoriel (3) | Fréquence des déplacements professionnels | Non-Travel / Travel_Rarely / Travel_Frequently |
| `Department` | catégoriel (3) | Direction de rattachement | Sales / R&D / HR |
| `DistanceFromHome` | int (km) | Distance domicile-travail | 1-29 |
| `Education` | ordinal (1-5) | Niveau d'études, échelle interne IBM (1=Below College ... 5=Doctor) | 1-5 |
| `EducationField` | catégoriel (6) | Domaine d'études | Life Sciences, Medical, Marketing, Technical Degree, HR, Other |
| `EmployeeCount` | int | Toujours égal à 1 | **Constante, voir règle Q1** |
| `EnvironmentSatisfaction` | ordinal (1-4) | Satisfaction déclarée vis-à-vis de l'environnement de travail | 1-4 |
| `Gender` | catégoriel (2) | Genre déclaré | Male / Female |
| `JobInvolvement` | ordinal (1-4) | Niveau d'implication déclaré | 1-4 |
| `JobLevel` | ordinal (1-5) | Niveau hiérarchique | 1-5 |
| `JobRole` | catégoriel (9) | Intitulé de poste | 9 intitulés |
| `JobSatisfaction` | ordinal (1-4) | Satisfaction au poste | 1-4 |
| `MaritalStatus` | catégoriel (3) | Situation familiale | Single / Married / Divorced |
| `MonthlyIncome` | int | Revenu mensuel | 1009-19999 |
| `MonthlyRate` | int | Taux mensuel (champ synthétique du jeu de données source, sans définition métier claire, voir Q2) | 2094-26999 |
| `NumCompaniesWorked` | int | Nombre d'employeurs précédents | 0-9 |
| `Over18` | catégoriel (1) | Majorité | **Constante ("Y" partout), voir règle Q1** |
| `OverTime` | catégoriel (2) | Fait des heures supplémentaires | Yes / No |
| `PercentSalaryHike` | int (%) | Dernière augmentation en % | 11-25 |
| `PerformanceRating` | ordinal | Évaluation de performance | Observé sur seulement 2 des 4 valeurs possibles de l'échelle documentée, voir règle Q3 |
| `RelationshipSatisfaction` | ordinal (1-4) | Satisfaction relationnelle au travail | 1-4 |
| `StandardHours` | int | Toujours égal à 80 | **Constante, voir règle Q1** |
| `StockOptionLevel` | ordinal (0-3) | Niveau de stock-options | 0-3 |
| `TotalWorkingYears` | int | Ancienneté totale, tous employeurs | 0-40 |
| `TrainingTimesLastYear` | int | Nombre de formations suivies l'an dernier | 0-6 |
| `WorkLifeBalance` | ordinal (1-4) | Équilibre vie pro/perso déclaré | 1-4 |
| `YearsAtCompany` | int | Ancienneté dans l'entreprise | 0-37 |
| `YearsInCurrentRole` | int | Ancienneté dans le poste actuel | 0-18 |
| `YearsSinceLastPromotion` | int | Années depuis la dernière promotion | 0-15 |
| `YearsWithCurrManager` | int | Ancienneté avec le manager actuel | 0-17 |

## 3. Glossaire métier

- **Attrition** : ici, un départ constaté (volontaire ou non) sur la période couverte par le jeu de données, pas un taux. Le KPI "taux d'attrition" est calculé à partir de ce champ dans le notebook, il ne préexiste pas dans la donnée source.
- **Collaborateur à risque** : au sens de ce projet, un collaborateur dont les caractéristiques (faible satisfaction, heures supplémentaires fréquentes, longue période sans promotion) sont statistiquement associées à l'attrition, pas un individu identifié nommément. Voir la limite méthodologique en fin de notebook.
- **Master Data** vs **donnée transactionnelle** : dans ce catalogue, l'identité, l'organisation et l'ancienneté sont traitées comme des données de référence (peu volatiles), la satisfaction et la performance comme des données réévaluées périodiquement (enquêtes, campagnes annuelles). Cette distinction conditionne la fréquence de rafraîchissement attendue dans un vrai pipeline.

## 4. Règles de qualité identifiées (audit exécuté dans le notebook, section 2)

| Règle | Constat | Action recommandée |
|---|---|---|
| **Q1 : champs constants** | `EmployeeCount` (=1), `Over18` (="Y"), `StandardHours` (=80) n'ont qu'une seule valeur sur les 1470 lignes | Aucune valeur informative, à exclure de tout modèle ou dashboard, documentés ici pour ne pas être réinterprétés comme un bug de chargement |
| **Q2 : champ sans définition métier stable** | `MonthlyRate` n'a pas de lien statistique clair avec `MonthlyIncome` et sa définition métier n'est pas documentée dans les sources d'origine du jeu de données | Champ à traiter comme suspect, exclu des KPIs de rémunération de ce projet |
| **Q3 : encodage borné mais partiellement observé** | `PerformanceRating` est documenté sur une échelle 1-4 mais seules 2 valeurs apparaissent réellement dans l'échantillon | Ne pas conclure à une erreur de chargement : documenté comme limite de représentativité de l'échantillon, pas de la donnée |
| **Q4 : unicité de la clé primaire** | `EmployeeNumber` : 1470 valeurs distinctes pour 1470 lignes | Validé, utilisable comme clé de jointure |
| **Q5 : complétude** | Aucune valeur manquante détectée sur les 35 champs (vérifié dans le notebook) | Validé |

## 5. Utilisation

Ce catalogue est la référence pour le notebook `notebooks/01_hr_analytics_kpis.ipynb` et pour le backlog produit (`BACKLOG.md`). Toute nouvelle user story qui introduirait un champ non listé ici doit d'abord passer par une mise à jour de ce catalogue, c'est la règle qu'on suivrait dans un vrai contexte de gouvernance Master Data.
