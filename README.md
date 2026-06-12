# 💊 MedScan-MA — Reconnaissance de médicaments par photo

> API de reconnaissance de médicaments à partir d'une photo de leur boîte
> (Vision par ordinateur + OCR français/arabe).

![Python](https://img.shields.io/badge/Python-3.10+-blue)
![FastAPI](https://img.shields.io/badge/FastAPI-API-009688)
![PaddleOCR](https://img.shields.io/badge/PaddleOCR-FR%2FAR-orange)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 📖 Présentation

**MedScan-MA** identifie automatiquement un médicament à partir d'une simple photo
de sa boîte. Le service lit le texte présent sur l'emballage (en **français** et en
**arabe**), le compare à une base de médicaments marocains, puis renvoie le médicament
le plus probable via une **API**.

Projet réalisé dans le cadre du **Master IT** — Université Mohammed V,
Faculté des Sciences, Rabat.

---

## ✨ Fonctionnalités

- 📷 Reconnaissance d'un médicament à partir d'une photo de sa boîte
- 🌍 OCR multilingue **français + arabe** (PaddleOCR)
- 🧼 Prétraitement d'image robuste (flou, éclairage, angle)
- 🔍 Comparaison floue contre une base de médicaments de référence
- 🔌 Service exposé via une **API REST** (FastAPI + documentation Swagger automatique)
- 📊 Évaluation des performances (Accuracy@1 et Accuracy@3)

---

## 🧭 Architecture (pipeline)

```
Photo de la boîte
        │
        ▼
1. Prétraitement (OpenCV)      → agrandir, niveaux de gris, débruiter
        │
        ▼
2. OCR (PaddleOCR FR + AR)     → extraction de tous les textes de la boîte
        │
        ▼
3. Nettoyage du texte          → majuscules, sans accents, normalisation
        │
        ▼
4. Comparaison floue (rapidfuzz) → recherche du nom le plus ressemblant
        │
        ▼
5. Réponse API (FastAPI)       → top 3 des médicaments + score de confiance (JSON)
```

---

## 🛠️ Technologies

| Outil        | Rôle                                              |
|--------------|---------------------------------------------------|
| Python 3.10+ | Langage principal                                 |
| OpenCV       | Prétraitement des images                          |
| PaddleOCR    | Lecture du texte (OCR) en français et en arabe    |
| rapidfuzz    | Comparaison floue de chaînes de caractères        |
| pandas       | Gestion de la base de médicaments                 |
| FastAPI      | Création de l'API REST                            |
| Uvicorn      | Serveur pour lancer l'API                         |

---

## 📁 Structure du projet

```
medscan-ma/
├── data/
│   ├── medicaments.csv          # Base de référence nettoyée
│   ├── train/                   # Photos pour le réglage
│   └── test/                    # Photos pour l'évaluation
├── src/
│   ├── preprocess.py            # Prétraitement des images (OpenCV)
│   ├── recognition.py           # OCR + nettoyage + matching
│   └── build_database.py        # Préparation de la base de médicaments
├── main.py                      # API FastAPI
├── evaluate.py                  # Calcul de l'Accuracy@1 et @3
├── requirements.txt             # Dépendances Python
└── README.md
```

---

## ⚙️ Installation

### 1. Cloner le dépôt

```bash
git clone https://github.com/ChakirMohamed/medscan-ma.git
cd medscan-ma
```

### 2. Créer un environnement virtuel (recommandé)

```bash
python -m venv venv
source venv/bin/activate        # Sur Windows : venv\Scripts\activate
```

### 3. Installer les dépendances

```bash
pip install -r requirements.txt
```

**Contenu de `requirements.txt` :**

```
paddlepaddle
paddleocr
opencv-python
rapidfuzz
pandas
openpyxl
fastapi
uvicorn
python-multipart
```

> ℹ️ Au premier lancement, PaddleOCR télécharge automatiquement les modèles
> (français et arabe). Cette étape est plus lente, c'est normal.

---

## 🚀 Utilisation

### Préparer la base de médicaments

Télécharger le fichier Excel officiel depuis le portail Open Data du Maroc
(`data.gov.ma`), le placer dans `data/`, puis exécuter :

```bash
python src/build_database.py
```

### Lancer l'API

```bash
uvicorn main:app --reload
```

L'API est alors disponible sur `http://localhost:8000`.
La documentation interactive (pour tester en envoyant une photo) se trouve sur :

```
http://localhost:8000/docs
```

### Exemple d'appel

**Requête** (envoi d'une photo au point d'accès `/recognize`) :

```bash
curl -X POST "http://localhost:8000/recognize" \
     -F "file=@photo_boite.jpg"
```

**Réponse (JSON) :**

```json
{
  "candidates": [
    { "medicament": "DOLIPRANE", "confiance": 94.0 },
    { "medicament": "DAFALGAN",  "confiance": 71.5 }
  ]
}
```

---

## 📊 Évaluation

Le système est testé sur les photos du dossier `data/test/`, à l'aide de deux mesures :

- **Accuracy@1** : le bon médicament est proposé en première position.
- **Accuracy@3** : le bon médicament figure parmi les 3 propositions.

```bash
python evaluate.py
```

```
Accuracy@1 : 85 %
Accuracy@3 : 96 %
```

---

## 🗂️ Dataset

- **Base de référence** : référentiel officiel des médicaments marocains
  (portail Open Data — `data.gov.ma`), nettoyé et normalisé.
- **Images** : 20 à 40 photos de boîtes prises manuellement, nommées selon la vérité
  terrain (ex. `doliprane_01.jpg`) et séparées en `train/` et `test/`. Des conditions
  variées (flou, éclairage, angle) sont volontairement incluses pour tester la robustesse.

---

## 📦 Livrables

- [x] Code source (ce dépôt)
- [x] Documentation et guide d'installation (ce README)
- [ ] Présentation du projet (PPTX)
- [ ] Vidéo de démonstration (7 minutes maximum)

---

## 👤 Auteur

**Mohamed Chakir** — [@ChakirMohamed](https://github.com/ChakirMohamed)

## 🎓 Encadrement

- **Encadrant :** Abdelhak Mahmoudi
- **Co-encadrants :** Saad Frihi, Yasine Lehmiani

Master IT — Université Mohammed V, Faculté des Sciences, Rabat — 2025/2026

---


