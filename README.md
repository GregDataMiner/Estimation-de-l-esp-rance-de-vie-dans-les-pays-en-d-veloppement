# 📊 Estimation de l’espérance de vie dans les pays en développement  
**Projet d’économétrie du risque - Analyse des données de panel sous Stata**  

🔗 **[Accéder à l’étude complète](main/projet.pdf)** 

---

## 📖 Contexte et problématique  

L'espérance de vie est un indicateur clé de la santé publique et du développement économique. Cependant, dans les pays en développement, elle varie fortement en fonction de plusieurs facteurs, notamment :  
✔ **Les dépenses de santé** 💰  
✔ **L’accès aux infrastructures sanitaires** 🚰  
✔ **Le taux de chômage** 📉  
✔ **Les émissions de CO2** 🌍  

L’objectif de cette étude est de **quantifier l’effet de ces variables sur l’espérance de vie** à l’aide d’une **analyse économétrique sur des données de panel**.  

---

## 📂 Structure du projet  

```
📁 estimation-vie-developpement
│── 📄 rapport_final.pdf         # Rapport d'analyse des résultats  
│── 📄 slides_presentation.pptx  # Présentation du projet  
│── 📄 code_stata.do             # Script Stata complet pour l’analyse  
│── 📄 README.md                 # Présentation du projet  
```

---

## 🔍 **Méthodologie adoptée**  

### 1️⃣ **Données utilisées**  
- **Type de données** : Données de panel sur plusieurs pays en développement  
- **Période couverte** : Plusieurs années (préciser la période)  
- **Sources** : World Bank, WHO, autres organismes internationaux  

#### **1.1 Traitement des données**  
Avant d’estimer notre modèle, nous avons appliqué plusieurs traitements aux données :  
✅ **Suppression des variables avec trop de valeurs manquantes**  
   - `corruption` a été exclue (100% de valeurs manquantes).  
   - `educationexpenditure` (31% de valeurs manquantes) a été retirée après analyse.  
✅ **Transformation des variables**  
   - La variable **CO2** a été transformée en **logarithme** (`co2_log`) pour s’adapter à l’échelle de l’espérance de vie.  
✅ **Vérification des doublons**  
   - Aucun doublon détecté, confirmant l’applicabilité d’un **modèle de panel**.  

---

### 2️⃣ **Tests de spécification du modèle**  

Avant d'estimer la régression, nous avons effectué plusieurs tests économétriques sous **Stata** pour vérifier les hypothèses du modèle.

#### **2.1 Test de variabilité temporelle (Hypothèse FE.4)**  
```stata
xtsum lifeexpectancyworldbank healthexpenditure unemployment sanitation co2
```
✅ Ce test permet de voir si les variables évoluent bien dans le temps, confirmant la pertinence d’un modèle de panel.  

#### **2.2 Test d’autocorrélation (Hypothèse FE.6 - Test de Wooldridge)**  
```stata
xtserial lifeexpectancyworldbank healthexpenditure unemployment sanitation co2
```
✅ Résultat : **F(1, 40) = 81.365, Prob > F = 0.0000**  
➡ **Rejet de l’hypothèse nulle**, indiquant la présence d’autocorrélation dans le modèle.  

#### **2.3 Comparaison effets fixes vs aléatoires (Hypothèse FE.3 - Test de Hausman)**  
```stata
xtreg lifeexpectancyworldbank healthexpenditure unemployment sanitation co2, fe
estimates store fixed

xtreg lifeexpectancyworldbank healthexpenditure unemployment sanitation co2, re
estimates store random

hausman fixed random
```
✅ **Résultat** : Le modèle à **effets fixes** est plus adapté car il capture les différences structurelles entre pays.  

---

### 3️⃣ **Estimation finale du modèle**  

Nous avons estimé notre modèle à effets fixes avec **erreurs robustes (cluster)** pour corriger l’autocorrélation intra-groupe.

```stata
xtreg lifeexpectancyworldbank healthexpenditure unemployment sanitation co2_log, fe vce(cluster id_country)
```
➡ **Correction de l’autocorrélation et de l’hétéroscédasticité** via `vce(cluster id_country)`.  

### 🔹 **Comparaison des modèles**  
| Modèle | Autocorrélation | Robustesse | R² | Interprétation |
|--------|---------------|-----------|----|----------------|
| `xtregar` | AR(1) corrigé | Moins robuste | Faible | Explication faible de la variance intra-groupe |
| `xtreg fe vce(cluster)` | Corrigé | Plus robuste | Élevé | Meilleure capacité explicative |

📌 **Conclusion** : Le modèle **xtreg avec erreurs robustes (cluster)** est le plus approprié.

---

## 📊 **Discussion des résultats**  

| Facteur | Effet sur l’espérance de vie |
|---------|-----------------------------|
| **Dépenses de santé** | Augmentation de **0,64 ans** par unité de dépense supplémentaire |
| **Situation sanitaire** | Augmentation de **0,13 ans** par amélioration des infrastructures |
| **Émissions de CO2** | Réduction de **0,0235%** par augmentation de 1% des émissions |

✅ **Dépenses de santé** : Effet **positif et significatif** → Plus de financement améliore l’accès aux soins et la prévention.  
✅ **Infrastructures sanitaires** : Effet **positif et modéré** → Un impact mesurable mais plus faible que les dépenses directes en santé.  
✅ **Émissions de CO2** : Effet **négatif et statistiquement significatif** → La pollution de l’air **réduit directement l’espérance de vie**.  

📌 **Implication politique** :  
➡ Il est essentiel d'**augmenter les investissements en santé et en infrastructures sanitaires** pour améliorer la longévité.  
➡ Réduire les émissions de **CO2** pourrait **augmenter significativement l’espérance de vie** dans les pays en développement.  

---

## 📢 **Conclusions et perspectives**  

### 🔹 **Points forts de l’étude**  
✔ Utilisation d’un **modèle de panel** robuste (correction des erreurs standard).  
✔ Comparaison entre **effets fixes et aléatoires** pour choisir le meilleur modèle.  
✔ Analyse approfondie des effets des politiques publiques sur l’espérance de vie.  

### 🔹 **Limites du modèle**  
❌ **Données manquantes** → Certaines variables ont dû être retirées.  
❌ **Autres facteurs ignorés** → Variables telles que la **qualité de l’éducation** ou **l’accès à l’eau potable** pourraient être intégrées.  

📌 **Perspectives futures** :  
🔹 Améliorer la qualité des données en intégrant de nouvelles sources (UNICEF, WHO).  
🔹 Tester d’autres **modèles économétriques avancés** (GMM, Machine Learning).  
🔹 Étendre l’analyse à un **plus grand nombre de pays**.  

---

### ✅ **Améliorations apportées :**
- **Détails complets de la méthodologie** (tests économétriques, hypothèses, estimation finale).  
- **Tableaux clairs pour comparer les modèles et les résultats**.  
- **Discussion approfondie des implications économiques et politiques**.  
- **Ajout des limites et perspectives du projet** pour une vision complète.  

**🔗 Prochaine étape : Ajoute le lien du projet (PDF, Stata, slides) pour permettre aux utilisateurs d’accéder aux résultats !** 🚀
