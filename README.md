**Nom :** ***DATE-MASSE Joseph***

#### **Filière :** L2_IA

## 1. Introduction et Problématique

Dans un contexte commercial concurrentiel, la capacité à anticiper les revenus est cruciale pour la gestion des stocks, la planification financière, leurs stratégies de prix et leurs politiques promotionnelles pour maximiser leur chiffre d'affaires. Ce mini-projet vise à exploiter les données de ventes d'un supermarché pour construire un modèle prédictif fiable.

**Problématique :**  
Comment prédire avec précision la **Recette Totale** générée par la vente de produits en fonction de leurs caractéristiques (prix, quantité, stock, promotions, etc.) ?

**Objectifs :**

- Analyser et nettoyer le dataset de ventes.
- Comparer plusieurs algorithmes de régression (linéaires et non-linéaires).
- Identifier les facteurs les plus influents sur le chiffre d'affaires.
- Déployer le modèle le plus performant.

## 2. Description du Dataset

Le dataset utilisé provient du fichier `ventes_supermarche_aff.xlsx`. Il s'agit de données transversales (cross-sectional) représentant des transactions ou des périodes de vente.

### **Caractéristiques principales :**

Le jeu de données utilisé contient **1000 observations** de ventes dans un supermarché, avec **8 variables** :

| Variable | Type | Description |
| --- | --- | --- |
| Produit | Catégorielle | Nom du produit (9 catégories) |
| Prix_Unitaire | Quantitative | Prix de vente unitaire en FCFA |
| Quantité_Vendue | Quantitative | Nombre d'unités vendues |
| Coût_Approvisionnement | Quantitative | Coût d'achat du produit |
| Stock_Moyen | Quantitative | Niveau de stock moyen |
| Remise_Promo | Binaire | Présence (1) ou absence (0) de promotion |
| Jours_Depuis_Reappro | Quantitative | Délai depuis dernier réapprovisionnement |
| Recette_Totale | Quantitative (Numérique continue) (Var cible) | Chiffre d'affaires généré |

### **Statistiques descriptives clés (Variable Cible) :**

- **Minimum :** 3 680 FCFA
- **Maximum :** 9 153 480 FCFA
- **Moyenne :** 1 171 984 FCFA
- **Médiane :** 252 266 FCFA
- *Observation :* La forte différence entre la moyenne et la médiane suggère une distribution asymétrique avec la présence de ventes très élevées.

## 3. Méthodologie d'Analyse

L'analyse a été réalisée en Python en utilisant les bibliothèques `pandas`, `scikit-learn`, `matplotlib` et `seaborn`.

### 3.1. Prétraitement des données

1. **Gestion des valeurs manquantes :** Aucune valeur manquante détectée dans le dataset.
2. **Encodage :** La variable catégorielle `Produit` a été transformée via un **One-Hot Encoding** (passant de 1 à 9 variables binaires), portant le nombre total de colonnes à 16.
3. **Détection des Outliers :** Une analyse par la méthode IQR a révélé que la variable `Prix_Unitaire` contient 21,5% de valeurs aberrantes (notamment pour le "Lait Peak en poudre" et le "Riz 25kg").
  - *Décision :* Ces valeurs ont été **conservées** car elles reflètent la réalité économique (produits haut de gamme ou grands conditionnements) et non des erreurs de saisie.
4. **Division des données :** Séparation en ensemble d'entraînement (80%) et de test (20%) avec `random_state=42`.
5. **Standardisation :** Application du `StandardScaler` sur les variables numériques pour centrer et réduire les données (moyenne=0, écart-type=1).

### **3.2 Modèles de régression testés**

| Modèle | Type | Caractéristiques |
| --- | --- | --- |
| Régression Linéaire | Linéaire | Modèle de référence (baseline) |
| Ridge (L2) | Linéaire régularisé | Pénalisation L2 pour réduire la variance |
| Lasso (L1) | Linéaire régularisé | Pénalisation L1 pour sélection de variables |
| Huber | Robuste | Réduit l'influence des outliers |
| Random Forest | Ensemble | Capte les relations non-linéaires |

### 3.3. Validation et Métriques

- **Validation :** Validation croisée (Cross-Validation) à 5 plis (5-Fold CV) pour assurer la robustesse.

- **Métriques d'évaluation :**
  - **R² (Coefficient de détermination) :** Qualité de l'ajustement.
  - **RMSE (Root Mean Squared Error) :** Erreur quadratique moyenne (en FCFA).
  - **MAE (Mean Absolute Error) :** Erreur absolue moyenne (en FCFA).

## 4. Résultats des Modèles

Le tableau suivant résume les performances des modèles sur l'ensemble de test (Holdout) et en validation croisée :

| Modèle | R² (Test) | RMSE (Test) | MAE (Test) |
| --- | --- | --- | --- |
| **Random Forest** | **0.997** | **117 142 FCFA** | **50 017 FCFA** |
| Régression Linéaire | 0.803 | 941 174 FCFA | 624 291 FCFA |
| Ridge (L2) | 0.803 | 940 884 FCFA | 623 609 FCFA |
| Lasso (L1) | 0.803 | 941 062 FCFA | 624 200 FCFA |
| Huber (Robuste) | 0.738 | 1 085 460 FCFA | 488 028 FCFA |

**Observation :** Le modèle **Random Forest** surpasse significativement les modèles linéaires avec un R² de 99,7%, indiquant une capacité prédictive exceptionnelle sur ce dataset. Les modèles linéaires stagnent autour de 80% de variance expliquée.

## 5. Interprétation et Discussion

### 5.1. Importance des Variables

L'analyse d'importance des features du modèle Random Forest révèle que deux variables dominent largement la prédiction :

**1. Prix_Unitaire (71%)**  
Le prix unitaire est le facteur prédominant. Une augmentation du prix augmente mécaniquement la recette, mais cet effet doit être nuancé par l'élasticité-prix de la demande.

**2. Quantité_Vendue (28,8%)**  
La seconde variable la plus importante. Plus on vend, plus la recette est élevée. Cela confirme l'intuition commerciale de base.

**3. Variables produits spécifiques**  
Certains produits comme le "Lait Peak en poudre 2.5kg" ont un impact significatif, probablement en raison de leur prix élevé et de leur volume de vente important.

**4. Jours_Depuis_Reappro et Stock_Moyen**  
Ces variables logistiques ont un impact faible mais non négligeable, suggérant que la fraîcheur des produits et la disponibilité influencent les ventes.

**5. Remise_Promo (0,006%)**  
Étonnamment faible impact des promotions. Cela pourrait indiquer que les réductions ne stimulent pas suffisamment les volumes pour compenser la baisse de prix unitaire.

**Interprétation Économique :**  
Ce résultat est cohérent avec la définition mathématique de la recette : Recette = Prix \times Quantité. Le modèle a correctement appris cette relation fondamentale. Les variables comme le stock ou les promotions ont peu d'impact direct sur la recette *réalisée* une fois la vente effectuée, contrairement au prix et au volume vendu.

### 5.2. Analyse des Résidus

L'analyse des résidus du meilleur modèle (Random Forest) montre :

- Une distribution centrée autour de zéro.
- Une absence de motif particulier dans le graphique "Résidus vs Prédictions", confirmant que le modèle capture bien la structure des données sans biais systématique majeur.

### 5.3. Performance des Modèles Linéaires

Les modèles linéaires (Ridge, Lasso, Linéaire) obtiennent des scores similaires (R² ~ 0.80). Leur performance inférieure au Random Forest peut s'expliquer par :

- La présence d'outliers dans le `Prix_Unitaire` qui influencent davantage les modèles linéaires sensibles aux valeurs extrêmes.
- La dimensionalité accrue due au One-Hot Encoding des produits.

## 6. Conclusion et Perspectives

### 6.1. Conclusion

Dans ce projet, nous avons mis en place un pipeline complet de Machine Learning pour la prédiction des ventes. Le modèle **Random Forest** a été identifié comme le meilleur candidat avec une erreur moyenne de prédiction d'environ 50 000 FCFA sur un panier moyen de 1,17 million FCFA. Les facteurs déterminants sont exclusivement le prix unitaire et la quantité vendue.

### 6.2. Forces et Limites

- **Forces :** Bonne capacité prédictive, utilisation de la validation croisée, comparaison de multiples algorithmes.
- **Limites :** Présence d'outliers non traités, variables catégorielles augmentant la dimensionnalité, absence de dimension temporelle pour faire des prévisions futures (forecasting).

### 6.3. Perspectives d'Amélioration

- **Ingénierie de features :** Créer des variables interactives (ex: Prix * Promo).
- **Optimisation :** Utiliser `GridSearchCV` pour optimiser les hyperparamètres du Random Forest.
- **Modèles avancés :** Tester des algorithmes de boosting (XGBoost, LightGBM).
- **Analyse temporelle :** Si des dates étaient disponibles, passer à des modèles de séries temporelles (ARIMA, Prophet) pour prévoir les ventes futures.