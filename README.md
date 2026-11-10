# Score de crédit pour une clientèle de particuliers

Modèle de notation qui estime la probabilité de défaut d'un client à partir de ses données de crédit, de compte et de profil. Projet réalisé à l'ESSAI.

**Défaut** : plus de 90 jours de retard de paiement sur les 12 derniers mois.

## Démarche (`scoring_credit.ipynb`)

1. **Qualité des données et exploration** : valeurs manquantes, distributions, taux de défaut par catégorie.
2. **Préparation** : traitement des valeurs manquantes et aberrantes, contrôle qu'aucune variable postérieure au défaut n'est utilisée.
3. **Création de variables** : âge, ratios d'endettement (encours sur revenus, encours sur garanties), intensité de la relation bancaire, indicateurs de risque.
4. **Encodage WoE** et séparation apprentissage / test.
5. **Rééquilibrage** des classes par SMOTE.
6. **Sélection des variables par Information Value** (seuil de 0,02), puis élimination des variables trop corrélées. Les variables à l'IV anormalement élevée (supérieure à 0,5, comme le solde arrêté) sont signalées comme suspectes avant d'être retenues.
7. **Régression logistique** (pseudo R² de McFadden : 0,318), analyse des coefficients et des odds ratios.
8. **Évaluation** sur l'échantillon de test : courbe ROC, AUC, Gini, KS, matrice de confusion.
9. **Échelle de notation** : regroupement des clients en classes de risque, avec un taux de défaut qui augmente d'une classe à l'autre.

## Résultats (échantillon de test)

| AUC | Gini | KS |
|---|---|---|
| 0,820 | 0,640 | 0,588 |

**Échelle de notation retenue (4 classes)** : les bornes sont fixées sur les PD de l'apprentissage. On part de 8 classes et on réduit leur nombre jusqu'à obtenir un taux de défaut strictement croissant.

| Classe | Part du portefeuille | Taux de défaut observé |
|---|---|---|
| C1 | 46,0 % | 0,51 % |
| C2 | 30,3 % | 1,15 % |
| C3 | 17,1 % | 2,38 % |
| C4 | 6,6 % | 15,79 % |

**Limites** : l'échantillon de test ne compte que 35 défauts, ce qui rend les taux par classe fragiles. Le modèle est entraîné sur des données rééquilibrées : ses PD brutes surestiment le risque, d'où l'utilisation des taux observés par classe.

## Données

La base utilisée concerne de vrais clients et n'est donc **pas publiée**. Pour la même raison, les sorties du notebook qui affichaient des lignes de la base ont été retirées ; les graphiques et les résultats agrégés sont conservés.

Le fichier `data/echantillon_fictif.csv` contient **100 lignes fictives** avec les mêmes 29 colonnes. Chaque colonne a été tirée au hasard séparément : l'échantillon montre la structure des données, mais il ne correspond à aucun client réel et ne permet pas de reproduire les résultats.

Technologies : Python, pandas, scikit-learn, imbalanced-learn, statsmodels.
