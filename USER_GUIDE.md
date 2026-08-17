# Guide utilisateur : Dashboard RH Attrition & Master Data

Ce guide s'adresse à quelqu'un qui consulte les résultats (le dashboard ou le notebook exécuté) sans avoir écrit le code. Il répond à la user story 4.1 du backlog : comprendre chaque indicateur et sa limite, sans mal l'interpréter.

## 1. Avant de lire un chiffre : la donnée a été auditée

Tous les KPIs de ce document reposent sur un jeu de données audité (`DATA_CATALOG.md`) : pas de valeur manquante, pas de doublon d'identifiant, et 3 champs sans valeur informative retirés en amont. Vous n'avez pas besoin de revérifier ça, c'est fait.

## 2. Taux d'attrition par département / par poste

**Ce que ça montre** : la part de collaborateurs ayant quitté l'entreprise, département par département ou poste par poste.

**Comment le lire** : regardez toujours l'effectif (`n=`) affiché à côté du taux. Un taux de 20 % sur 60 personnes (12 départs) n'a pas le même poids qu'un taux de 20 % sur 900 personnes. Un petit effectif peut donner un taux qui bouge beaucoup pour peu de départs réels.

**Ce que ça ne montre pas** : ce graphique ne dit pas *pourquoi* les gens partent, seulement *où* le taux est élevé. Pour le "pourquoi", voir le KPI suivant.

## 3. Facteurs associés à l'attrition

**Ce que ça montre** : si le fait de faire des heures supplémentaires, d'être peu satisfait de son poste, ou de ne pas avoir été promu depuis longtemps, est statistiquement lié au départ.

**Comment le lire** : chaque facteur est accompagné d'une p-value. En dessous de 0,05, on considère que le lien observé n'est pas dû au hasard. Au-dessus, on ne peut pas conclure.

**Piège à éviter** : "statistiquement associé" ne veut pas dire "cause". Le graphique ne prouve pas que faire des heures supplémentaires *fait* partir les gens, seulement que les deux vont souvent ensemble dans les données. D'autres causes communes peuvent expliquer les deux.

## 4. Écart de rémunération par genre

**Ce que ça montre** : l'écart de revenu médian entre hommes et femmes, à niveau de poste identique (`JobLevel`), pas une moyenne brute toutes catégories confondues.

**Comment le lire** : le "niveau de poste équivalent" est la précision importante. Comparer un salaire brut homme/femme sans contrôler le niveau peut faire apparaître un écart qui n'existe pas vraiment, ou en cacher un.

**Résultat sur ce jeu de données** : aucun écart systémique marqué n'a été détecté à ce niveau d'agrégation. C'est un résultat négatif, gardé tel quel plutôt que présenté autrement pour paraître plus intéressant.

## 5. Bien-être par département (Work-Life Balance)

**Ce que ça montre** : la distribution du score d'équilibre vie pro/perso déclaré (échelle 1 à 4), par département.

**Comment le lire** : c'est une boîte à moustaches (boxplot), la ligne du milieu est la médiane, la boîte contient la moitié centrale des réponses. Un département avec une boîte basse et étroite a une majorité de collaborateurs peu satisfaits sur ce point ; une boîte large signifie des situations très variées au sein du même département.

## Questions fréquentes

**Pourquoi certains chiffres viennent de requêtes SQL et d'autres de calculs Python ?** Les agrégations simples (taux par département, par poste) sont écrites en SQL, transposable directement à un entrepôt comme Snowflake. Les tests statistiques et les graphiques restent en Python, ce n'est pas le rôle du SQL.

**Ce dashboard reflète-t-il une vraie entreprise ?** Non, le jeu de données est fictif, généré par IBM pour démontrer son outil Watson Analytics, pas une extraction réelle de SIRH. Il est utilisé ici pour démontrer la méthode (catalogue, backlog, SQL, modèle), pas pour produire un diagnostic RH généralisable. Voir la section "Limites" du notebook pour le détail.

**Qui contacter si un chiffre semble faux ?** Dans un contexte réel, ce serait le rôle du Product Owner du produit data de trancher, en remontant au catalogue de données (`DATA_CATALOG.md`) pour vérifier si la définition du champ correspond à l'usage qui en est fait ici.
