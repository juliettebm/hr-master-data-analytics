# Guide utilisateur : Dashboard RH Attrition & Master Data

## Avant de lire un chiffre : la donnée a été auditée

Tous les KPIs de ce document reposent sur un jeu de données audité (`DATA_CATALOG.md`) : pas de valeur manquante, pas de doublon d'identifiant, et 3 champs sans valeur informative retirés en amont. Pas besoin de revérifier ça, c'est fait.

## Taux d'attrition par département / par poste

**Ce que ça montre** : la part de collaborateurs ayant quitté l'entreprise, département par département ou poste par poste.

**Comment le lire** : regardez toujours l'effectif (`n=`) affiché à côté du taux. Un taux de 20 % sur 60 personnes (12 départs) n'a pas le même poids qu'un taux de 20 % sur 900 personnes. Un petit effectif peut donner un taux qui bouge beaucoup pour peu de départs réels.

**Ce que ça ne montre pas** : ce graphique ne dit pas *pourquoi* les gens partent, seulement *où* le taux est élevé. Pour le "pourquoi", voir le KPI suivant.

## Facteurs associés à l'attrition

**Ce que ça montre** : si le fait de faire des heures supplémentaires, le revenu, l'âge, la satisfaction au poste, ou le temps depuis la dernière promotion, sont statistiquement liés au départ.

**Comment le lire** : chaque facteur est accompagné d'une p-value. En dessous de 0,05, on considère que le lien observé n'est pas dû au hasard. Les cinq facteurs testés le sont.

**Piège à éviter** : "statistiquement associé" ne veut pas dire "cause". Le fait de faire des heures supplémentaires n'est pas prouvé comme *causant* le départ, seulement que les deux vont souvent ensemble dans les données. D'autres causes communes peuvent expliquer les deux, en particulier pour l'âge et le revenu, corrélés au niveau de poste et à l'ancienneté : une partie du signal peut recouper ce que ces autres variables expliquent déjà.

## Écart de rémunération par genre

**Ce que ça montre** : l'écart de revenu médian entre hommes et femmes, à niveau de poste identique (`JobLevel`), pas une moyenne brute toutes catégories confondues.

**Comment le lire** : le "niveau de poste équivalent" est la précision importante. Comparer un salaire brut homme/femme sans contrôler le niveau peut faire apparaître un écart qui n'existe pas vraiment, ou en cacher un.

**Résultat sur ce jeu de données** : aucun écart systémique marqué n'a été détecté à ce niveau d'agrégation. Résultat négatif, gardé tel quel.

## Bien-être par département (Work-Life Balance)

**Ce que ça montre** : la distribution du score d'équilibre vie pro/perso déclaré (échelle 1 à 4), par département.

**Comment le lire** : c'est une boîte à moustaches (boxplot), la ligne du milieu est la médiane, la boîte contient la moitié centrale des réponses. Un département avec une boîte basse et étroite a une majorité de collaborateurs peu satisfaits sur ce point ; une boîte large signifie des situations très variées au sein du même département.

## Questions ad hoc : distance domicile-travail, revenu par niveau d'études

**Ce que ça montre** : deux questions posées en dehors du plan initial. La distance domicile-travail des partants vs des restants, par poste. Le revenu médian des partants vs des restants, par niveau d'études.

**Comment le lire** : les deux écarts varient fortement selon le poste ou le niveau, ce ne sont pas des effets uniformes sur toute la population. Regardez le sous-groupe concerné, pas seulement le chiffre global.

## Score de risque de départ (notebook 02, export Power BI)

**Ce que ça montre** : un score de risque par collaborateur (`risk_score`, 0 à 1) et une bande (`risk_band` : Faible / Moyen / Élevé), issus d'un modèle de régression logistique.

**Comment le lire** : les bandes sont des seuils simples (tiers de l'intervalle), pas une calibration validée avec la direction RH, à affiner selon la capacité réelle de l'équipe à agir sur les cas signalés.

## Questions fréquentes

**Pourquoi certains chiffres viennent de requêtes SQL et d'autres de calculs Python ?** Les agrégations simples (taux par département, par poste) sont écrites en SQL, transposable directement à un entrepôt comme Snowflake. Les tests statistiques et les graphiques restent en Python, ce n'est pas le rôle du SQL.

**Ce dashboard reflète-t-il une vraie entreprise ?** Non, le jeu de données est fictif, généré par IBM pour démontrer son outil Watson Analytics, pas une extraction réelle de SIRH.

**Qui contacter si un chiffre semble faux ?** Le catalogue de données (`DATA_CATALOG.md`) en premier, pour vérifier si la définition du champ correspond à l'usage qui en est fait ici.
