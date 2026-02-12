# TP 2 — Préparation des données avec Python

## Objectif

Nettoyer le dataset `catnat_dirty.csv` et produire un fichier propre `catnat_clean.csv` prêt à être analysé dans Tableau.

Télécharger `catnat_dirty.csv` [ici](https://drive.google.com/file/d/1jgsiR9bpAoYQ_v6Do9cBdtzFTPZ8NXNp/view?usp=sharing)

## Problèmes à résoudre

Le dataset contient volontairement :

- ~150 doublons
- Valeurs manquantes supplémentaires
- Incohérences de casse (Asia, ASIA, asia...)
- Espaces parasites
- Variantes d'orthographe (USA, US, United States...)
- `Start Year` en format texte avec erreurs ("2020 AD", "Year 2020")
- Outliers aberrants (décès négatifs, magnitude à 999)

---

## Mise en place

```python
import pandas as pd
import numpy as np

df = pd.read_csv("catnat_dirty.csv")
```

---

# Exercice 1 — Exploration et diagnostic

> **Rappel**
>
> Avant de modifier, toujours explorer :
>
> - `df.shape` → dimensions
> - `df.info()` → types et nulls
> - `df.describe()` → statistiques
> - `df.isnull().sum()` → comptage des nulls
> - `df.duplicated().sum()` → comptage des doublons
> - `df['col'].value_counts()` → valeurs uniques

## À faire

1. Affichez les dimensions du dataset
2. Affichez les types de données avec `info()`
3. Comptez les valeurs manquantes par colonne (nombre et pourcentage)
4. Comptez le nombre de doublons
5. Affichez les valeurs uniques de `Region` — repérez les incohérences
6. Affichez les statistiques de `Total Deaths` — repérez les anomalies

## Questions

- Combien y a-t-il de doublons ?
- Quelles colonnes ont le plus de valeurs manquantes ?
- Combien de variantes différentes pour "Asia" ?
- Y a-t-il des valeurs négatives dans `Total Deaths` ?

---

# Exercice 2 — Suppression des doublons

> **Rappel**
>
> ```python
> # Identifier les doublons
> df[df.duplicated()]
> df[df.duplicated(keep=False)]  # Inclut les originaux
>
> # Supprimer les doublons
> df = df.drop_duplicates()
> df = df.drop_duplicates(subset=['col1', 'col2'])  # Sur certaines colonnes
> ```

## À faire

1. Affichez quelques lignes dupliquées pour vérifier
2. Supprimez les doublons exacts
3. Vérifiez que les doublons ont bien été supprimés

---

# Exercice 3 — Correction des types

> **Rappel**
>
> ```python
> # Vérifier les types
> df.dtypes
>
> # Convertir en numérique (erreurs → NaN)
> df['col'] = pd.to_numeric(df['col'], errors='coerce')
>
> # Convertir en entier
> df['col'] = df['col'].astype(int)
>
> # Convertir en date
> df['col'] = pd.to_datetime(df['col'], errors='coerce')
> ```

```python
# petit hint...
dtype.name existe !

# AD signifie Anno Domini, elle correspond à la formule français après JC ...
```

## À faire

1. Vérifiez le type de `Start Year`
2. Affichez quelques valeurs problématiques (contenant "Year" ou "AD")
3. Nettoyez la colonne : supprimez le texte et convertissez en numérique
4. Vérifiez le résultat

---

# Exercice 4 — Traitement des valeurs manquantes

> **Rappel**
>
> | Situation                               | Action                             |
> | --------------------------------------- | ---------------------------------- |
> | Peu de nulls, colonne critique          | Supprimer les lignes               |
> | Beaucoup de nulls, colonne non critique | Supprimer la colonne ou garder NaN |
> | Numérique, distribution asymétrique     | Imputer par médiane                |
> | Catégoriel                              | Imputer par mode ou "Inconnu"      |
>
> ```python
> df.dropna(subset=['col'])           # Supprimer lignes
> df['col'].fillna(valeur)            # Remplacer
> df['col'].fillna(df['col'].median())  # Médiane
> ```

## À faire

1. Pour `Region` (peu de nulls) : supprimez les lignes avec null
2. Pour `Event Name` : remplacez les nulls par "Non nommé"
3. Pour `Total Deaths` : décidez d'une stratégie et appliquez-la
4. Pour `Magnitude` : laissez les nulls (n'a pas de sens pour tous les types)
5. Vérifiez le résultat

---

# Exercice 5 — Traitement des outliers

> **Rappel**
>
> Deux questions :
>
> 1. Est-ce une erreur ? → Corriger ou supprimer
> 2. Est-ce une valeur extrême réelle ? → Garder (ou analyser séparément)
>
> ```python
> # Valeurs impossibles
> df[df['col'] < 0]
> df[df['col'] > seuil_max]
>
> # Supprimer
> df = df[df['col'] >= 0]
>
> # Capper
> df['col'] = df['col'].clip(lower=0, upper=max_val)
> ```

## À faire

1. Identifiez les valeurs négatives dans `Total Deaths`
2. Identifiez les valeurs aberrantes dans `Magnitude` (> 10)
3. Décidez : supprimer ou corriger ?
4. Appliquez le traitement
5. Vérifiez le résultat

---

# Exercice 6 — Nettoyage du texte

> **Rappel**
>
> ```python
> # Casse
> df['col'].str.lower()
> df['col'].str.upper()
> df['col'].str.title()
>
> # Espaces
> df['col'].str.strip()
>
> # Remplacement
> df['col'].replace({'old': 'new'})
>
> # Pipeline complet
> df['col'] = df['col'].str.strip().str.lower()
> ```

## À faire

1. Nettoyez `Region` : strip + title case
2. Vérifiez avec `value_counts()` — combien de catégories maintenant ?
3. Nettoyez `Disaster Type` de la même manière
4. Nettoyez `Country` : strip + title case
5. Corrigez les variantes de pays (USA → United States, etc.)

---

# Exercice 7 — Création de colonnes utiles

> **Rappel**
>
> ```python
> # Nouvelle colonne calculée
> df['new'] = df['col1'] / df['col2']
>
> # Colonne à partir d'une autre
> df['Decennie'] = (df['Year'] // 10) * 10
>
> # Extraction de date
> df['Annee'] = df['Date'].dt.year
> df['Mois'] = df['Date'].dt.month
> ```

## À faire

1. Créez une colonne `Decennie` à partir de `Start Year`
2. Créez une colonne `Has_Deaths` (booléen : True si Total Deaths > 0)
3. Vérifiez vos nouvelles colonnes

---

# Exercice 8 — Sélection et export

> **Rappel**
>
> ```python
> # Sélectionner des colonnes
> df_export = df[['col1', 'col2', 'col3']]
>
> # Renommer
> df_export = df_export.rename(columns={'old': 'new'})
>
> # Export
> df_export.to_csv("fichier.csv", index=False)
> ```

## À faire

1. Sélectionnez les colonnes utiles pour l'analyse
2. Renommez les colonnes si nécessaire (noms clairs, sans espaces)
3. Faites une vérification finale avec `info()` et `head()`
4. Exportez en CSV : `catnat_clean.csv`

---

# Exercice 9 — Vérification dans Tableau

## À faire

1. Ouvrez Tableau Public
2. Connectez-vous au fichier `catnat_clean.csv`
3. Vérifiez dans l'écran "Source de données" :
   - Les types sont-ils corrects ? (# pour nombres, Abc pour texte)
   - Les nombres sont-ils bien reconnus ?
4. Créez un bar chart : `Type_Catastrophe` vs `COUNT(ID_Catastrophe)`
   - Les catégories sont-elles propres ? (pas de doublons)
5. Créez un bar chart : `Region` vs `SUM(Deces)`
   - Les 5 régions sont-elles bien distinctes ?
6. Créez une carte avec `Country`
   - Les pays sont-ils reconnus ?

---

## Pour les curieux 🤨

- **Automatiser** : transformez ce notebook en script réutilisable
- **Documenter** : ajoutez des commentaires expliquant chaque décision
- **Versionner** : utilisez Git pour suivre les modifications
- **Profiling** : testez `pandas-profiling` pour un diagnostic automatique

```python
# Installation
# pip install pandas-profiling

from pandas_profiling import ProfileReport
profile = ProfileReport(df, title="Rapport de qualité")
profile.to_file("rapport_qualite.html")
```
