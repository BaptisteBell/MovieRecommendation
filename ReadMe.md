# Projet -- Systhème de Recommandation

Auteur:
- Baptiste Bellamy

## Contexte

Ce projet vise à développer un système de recommandation de films utilisant l'ensemble de données MovieLens 1M et la base de données IMDB. Le système est conçu pour fournir des recommandations de films.

## Methodologie

**1-Chargement et prétraitement des données :**  
Chargement et pré-traitement des données `MovieLens-1M` et `IMdB`  
Fusionner les datasets afin d'obtenir un dataset plus détaillé.  

**Feature Engineering :**
Extraire les données et la matrice item_user

**Developpement du modèle :**
Creér un modèle capable de prédire la note des utilisateurs sur les films  

**Algorithme de Recommandation :**  
Faire un algorithme capable de donner une liste de film recommandée pour un couple d'utilisateur.

## Code et Expérience

### Chargement des données

Les données sont chargées depuis le dossier `./data/`. 
Quelques traitement sont effectué dès le chargement des données afon de facilité le pré-traitement pour le merge comme la séparation du *Titre* et de *l'année de sortie* pour `MovieLens-1M`.
Les différents dataframe résultant sont :  
- MovieLens-1M:
    - `df_ratings` : contient les notes des utilisateurs pour les films
    - `df_users` : contient les informations des utilisateurs
    - `df_movies` : contient les informations du film
- IMdB: 
    - `df_title_ratings` : contient les informations sur la notation des films
    - `df_title_basics` : contient les informations supplémentaires des films

### Traitement des données
Plusieurs fonctions ont été faites afin de traiter les informations pour pouvoir merge les deux dataset `df_movies` et `df_title_basics`. Toutes les fonctions sont du traitements sur les colonnes des deux datasets.

### Merge des datasets
Les datasets `df_movies` et `df_title_basics` sont joins puis le dataset `df_title_ratings` et merge aussi. Quelques post-traitement sont effectué comme le regroupement des `Genres` en une seul colonne.
Les datasets `df_movies` et `df_title_basics` sont merge en fonction de `Title` et `Year`. L'année est prise en compte puisque certains film ont le même nom, mais une année différentes.

### Feature Engineering
Les dataframe finaux sont créés avec les bons types pour chaque colonne :
- `ratings` : contient les notes des utilisateurs pour les films
- `users` :   contient les informations des utilisateurs
- `movies` : contient les informations des films (dataframe merge)

### Modèle
Plusieurs modèle ont été implémenter afin de permettre des d'atteindre l'objectif de prédire une note d'un film par un utilisateur.
J'ai tout d'abord essayé de faire un modèle `SVD` (je n'ai pas gardé le code). Cependant cela n'a pas été concluant du au résultats qui était très loin d'être convaincant.  
J'ai par la suite implémenter le modèle `Spark ALS`. Ce modèle a été retenue.
J'ai réutiliser les différents exercices faits en cours (adapté à notre situation) pour obtenir un résultats.  
J'ai donc join tout les dataframes ensemble pour le données à Spark, puis avec ALS j'ai entrainé le modèle.

### Metriques et Evaluations
La métriques choisis est la **RMSE**.  
Après évaluation du modèle et calcule de la **RMSE**. Nous obtenons sur notre dataset de test une RMSE égale à **0.88**. Nous voyons que le modèle est plutot correcte puisqu'elle est assez basse.

### Algorithme de Recommandation
Notre algorithme de recommandation (`Recommend_movies(user1_id, user2_id, nb_recommendations)`) et fait la manière suivantes :
- La fonction prend en entrée 2 `UserID` ainqi que le nombre de film que l'on veut recommander.
- La fonction prédit la notation pour **chaque** film pour les deux utilisateur (fonction `Evaluate_prediction(user_id)` qui retourne pour utilisateur donné la liste des prédictions pour chaque film)
- Nous calculons la **moyenne des notes** pour chaque films entre les deux utilisateurs
- Les `nb_recommendations` avec la moyenne la plus haute sont alors retournées

## Notes
Le point négatifs de ALS est qu'il ne prend pas assez en considération les informations de l'utilisateur et du film. Pour cela j'ai essayé d'implémenter un modèle NNE (Neural Network Encoding), cependant les résultats était loin d'être convainquant. Vous pouvez retrouver mon code dans le fichier `./infos/Experiment.ipynb`

## Conclusion
Le système de recommandation de films exploite avec succès des techniques de filtrage collaboratif pour fournir des recommandations de films personnalisées. L'utilisation de l'ensemble de données MovieLens 1M et de la base de données IMDB permet une vue complète des préférences de l'utilisateur et des attributs du film. Les améliorations futures pourraient inclure l’intégration d’un filtrage basé sur le contenu et d’approches hybrides pour améliorer encore la qualité des recommandations.
