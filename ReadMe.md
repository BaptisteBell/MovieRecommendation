# Projet -- Système de Recommandation

Auteur:
- Baptiste Bellamy

## DataSets
Le système de recommandation se situe dans le fichier `projet.ipynb`. Nous utilisons les datasets suivants :
- **MovieLens-1M** : [lien vers MovieLens](https://grouplens.org/datasets/movielens/)
- **IMDb** : [lien vers IMDb Datasets](https://www.imdb.com/interfaces/)
    - title.basics.tsv
    - title.rankings.tsv

Afin d'utiliser le système de recommandation, merci de télécharger les données puis de les mettre dans le dossier `data`. (Mettre title.basics.tsv et title.rankings.tsv dans un dossier nommé `IMDb`)

## Contexte

Ce projet vise à développer un système de recommandation de films utilisant l'ensemble de données `MovieLens-1M `et la base de données `IMDb`. Le système est conçu pour fournir des recommandations de films personnalisées.

## Methodologie

**Chargement et prétraitement des données :**  
Chargement et prétraitement des données `MovieLens-1M` et `IMDb`.  
Fusion des jeux de données afin d'obtenir un ensemble de données plus détaillé.

**Feature Engineering :**
Extraire les données et la matrice item_user

**Developpement du modèle :**
Creér un modèle capable de prédire la note des utilisateurs sur les films  

**Algorithme de Recommandation :**  
Faire un algorithme capable de donner une liste de film recommandée pour un couple d'utilisateur.

## Code et Expérience

### Chargement des données
Les données sont chargées depuis le dossier `./data/`. Merci des les re-téléchargés (trop lourdes pour git)
Quelques traitements sont effectués dès le chargement des données afin de faciliter le prétraitement pour la fusion, comme la séparation du *Titre* et de *l'année de sortie* pour `MovieLens-1M`.

Les différents DataFrames résultants sont :  
- MovieLens-1M :
    - `df_ratings` : contient les notes des utilisateurs pour les films
    - `df_users` : contient les informations des utilisateurs
    - `df_movies` : contient les informations des films
- IMDb :
    - `df_title_ratings` : contient les informations sur la notation des films
    - `df_title_basics` : contient les informations supplémentaires des films

### Traitement des données
Plusieurs fonctions ont été créées afin de traiter les informations pour pouvoir fusionner les deux ensembles de données `df_movies` et `df_title_basics`. Toutes les fonctions effectuent des traitements sur les colonnes des deux ensembles de données.

### Merge des datasets
Les ensembles de données `df_movies` et `df_title_basics` sont joints, puis l'ensemble de données `df_title_ratings` est également fusionné. Quelques post-traitements sont effectués, comme le regroupement des `Genres` en une seule colonne.  
Les ensembles de données `df_movies` et `df_title_basics` sont fusionnés en fonction du `Title` et de l'`Year`. L'année est prise en compte puisque certains films ont le même nom, mais une année différente.
### Feature Engineering
Les DataFrames finaux sont créés avec les bons types pour chaque colonne :
- `ratings` : contient les notes des utilisateurs pour les films
- `users` : contient les informations des utilisateurs
- `movies` : contient les informations des films (DataFrame fusionné)

### Modèle
Plusieurs modèles ont été implémentés afin de permettre d'atteindre l'objectif de prédire la note d'un film par un utilisateur. J'ai tout d'abord essayé de créer un modèle `SVD` (je n'ai pas conservé le code). Cependant, cela n'a pas été concluant en raison des résultats qui étaient très loin d'être convaincants.

J'ai ensuite implémenté le modèle `Spark ALS`. Ce modèle a été retenu. J'ai réutilisé les différents exercices faits en cours (adaptés à notre situation) pour obtenir un résultat. J'ai donc joint tous les DataFrames ensemble pour fournir les données à Spark, puis, avec ALS, j'ai entraîné le modèle.  
De plus, j'ai essayer d'implémenter un modèle `NNE`, afin d'intégrer la dimension des caractéristiques des films et des utilisateurs. Vous le retrouver dans `./info/projet.ipynb`

### Metriques et Evaluations
La métrique choisie est la **RMSE**.   
Après évaluation du modèle et calcul de la **RMSE**, nous obtenons sur notre ensemble de données de test une RMSE égale à **0.88**. Nous voyons que le modèle est plutôt correct puisqu'elle est assez basse.

### Algorithme de Recommandation
Nous avons tout d'abord utilisé la fonction `model.recommendForAllUsers(num_recommendations)` qui donne **num_recommendation** recommandation de film à chaque utilisateur. 
Notre algorithme de recommandation (`get_Recommended_Movies(user1_id, user2_id, res_new)`) est conçu de la manière suivante :
- La fonction prend en entrée 2 `UserID` ainsi que les films recommandés pour chaque user.
- On récupère les recommandations des deux utilisateurs puis l'on sélectionne l'intersection des recommandations
- Si il n'y a pas assez de film en commun (<=2), on ajoute des films recommandés de l'utilisateur1 et de l'utilsateur2 de façon a avoir au moins 5 films.
- On retourne les noms des films recommandés

## Notes
Le point négatif de ALS est qu'il ne prend pas suffisamment en considération les informations de l'utilisateur et du film. Pour cette raison, j'ai essayé d'implémenter un modèle NNE (Neural Network Encoding). Cependant, les résultats étaient loin d'être convaincants. Vous pouvez retrouver mon code dans le fichier `./infos/Experiment.ipynb`. Cependant, les résultats sont corrects et adapté au préférence des utilisateurs.

## Conclusion
Le système de recommandation de films exploite avec succès des techniques de filtrage collaboratif pour fournir des recommandations de films personnalisées. L'utilisation de l'ensemble de données MovieLens 1M et de la base de données IMDb permet une vue complète des préférences de l'utilisateur et des attributs des films. Les améliorations futures pourraient inclure l’intégration d’un filtrage basé sur le contenu et d’approches hybrides (voir `info/Experiment.ipynb`) pour améliorer encore la qualité des recommandations.
