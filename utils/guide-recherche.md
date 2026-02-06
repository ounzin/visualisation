## Petit guide pratique pour la recherche d'entreprises

Hello à tous ! Désolé j'avais quelques obligations donc je ne vous envoie le document que maintenant j'espère qu'il vous sera utile :)

## URL de base

```
https://recherche-entreprises.api.gouv.fr/search
```

## Quelques approches pour vous ...

### 1. Entreprises informatiques dans votre région

```bash
curl "https://recherche-entreprises.api.gouv.fr/search?activite_principale=62.01Z,62.02A&departement=75&per_page=25"
```

### 2. Entreprises tech + mots-clés (data, IA, ML)

```bash
curl "https://recherche-entreprises.api.gouv.fr/search?q=data&activite_principale=62.01Z,62.02A,63.11Z"
```

### 3. Grandes entreprises tech (>50 salariés)

```bash
curl "https://recherche-entreprises.api.gouv.fr/search?activite_principale=62.01Z&tranche_effectif_salarie=21,22,31,32,41,42,51,52,53"
```

## Paramètres clés

| Paramètre                  | Description               | Exemple                          |
| -------------------------- | ------------------------- | -------------------------------- |
| `q`                        | Mots-clés (nom, activité) | `q=data science`                 |
| `activite_principale`      | Code(s) NAF               | `activite_principale=62.01Z`     |
| `departement`              | Département               | `departement=75`                 |
| `region`                   | Région (11=IDF)           | `region=11`                      |
| `tranche_effectif_salarie` | Taille entreprise         | `tranche_effectif_salarie=21,22` |
| `page`                     | Pagination                | `page=2`                         |
| `per_page`                 | Résultats/page (max 25)   | `per_page=25`                    |

## Codes NAF Tech & Data

| Code       | Activité                                |
| ---------- | --------------------------------------- |
| **62.01Z** | Programmation informatique              |
| **62.02A** | Conseil en systèmes et logiciels        |
| **62.02B** | Tierce maintenance informatique         |
| **62.03Z** | Gestion d'installations informatiques   |
| **63.11Z** | Traitement de données, hébergement      |
| **63.12Z** | Portails internet                       |
| **72.19Z** | R&D en sciences physiques et naturelles |

💡 **Combinez plusieurs codes** pour élargir votre recherche

## Tranches d'effectifs

- **11** : 10 à 19 salariés (startups)
- **12** : 20 à 49 salariés (PME)
- **21** : 50 à 99 salariés
- **22** : 100 à 199 salariés
- **31** : 200 à 249 salariés
- **32** : 250 à 499 salariés
- **41, 42, 51, 52, 53** : >500 salariés (grands groupes)

Soyez pragmatiques ...

## Exemple de réponse

```json
{
  "results": [
    {
      "nom_raison_sociale": "DATA ANALYTICS FRANCE",
      "siren": "123456789",
      "siege": {
        "siret": "12345678900010",
        "activite_principale": "62.02A",
        "libelle_activite_principale": "Conseil en systèmes et logiciels informatiques",
        "code_postal": "92100",
        "commune": "BOULOGNE-BILLANCOURT",
        "tranche_effectif_salarie": "22"
      }
    }
  ],
  "total_results": 156,
  "page": 1,
  "per_page": 25
}
```

## Quelques petits conseils ...

- Ciblez d'abord votre région (Île-de-France = `region=11`)
- Combinez plusieurs codes NAF tech (62.01Z, 62.02A, 63.11Z)
- Filtrez par taille : startups (11,12) ou grands groupes (41,42,51)
- Utilisez des mots-clés : `q=data`, `q=machine learning`, `q=big data`

## Pièges à éviter !!

- Ne cherchez pas que les grandes villes (opportunités en périphérie)
- N'oubliez pas les codes 63.11Z (hébergement/cloud) et 72.19Z (R&D)
- Vérifiez que l'entreprise est active (pas de date_cessation)

## Script Python pour automatiser

```python
import requests
import pandas as pd

url = "https://recherche-entreprises.api.gouv.fr/search"
params = {
    "activite_principale": "62.01Z,62.02A,63.11Z",
    "region": "11",
    "tranche_effectif_salarie": "21,22,31,32",
    "per_page": 25
}

response = requests.get(url, params=params)
data = response.json()

df = pd.DataFrame(data['results'])
# Export pour votre candidature
df.to_csv("entreprises_tech_stage.csv", index=False)
```

## Documentation complète

📖 [recherche-entreprises.api.gouv.fr/docs](https://recherche-entreprises.api.gouv.fr/docs/)

Bonne chance :)
<br>
**Ahmed ADJIBADE**
