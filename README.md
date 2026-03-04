# Mini-projet 2 – Introduction à la science des données

Ce dépôt contient le **mini-projet 2** du cours *Introduction à la science des données* (L1).  
L’objectif est de mettre en pratique la démarche VI-ME-RÉ-BAR (Visualisation, Mesure, Référence, Barres d’erreur) sur un problème de **classification d’images**.

---

## Jeu de données

- **Emplacement** : le dossier **`data/`** contient les images du projet (votre jeu de données).
- **Problème** : classification binaire entre **poires** et **pommes de pin**.
- Les images sont chargées, prétraitées puis utilisées pour extraire des attributs (couleur, forme, etc.) et entraîner des classificateurs.

---

## Structure du dépôt

| Élément | Description |
|--------|-------------|
| **`data/`** | **Jeu de données** : images (poires et pommes de pin) utilisées pour l’analyse et la classification. |
| `index.md` | Page principale du mini-projet (objectifs, planning, liens vers les feuilles). |
| `0_diaporama.md` | Notebook pour la soutenance. |
| `0_analyse.md` | Feuille d’analyse de données (chargement, attributs, modèles, résultats). |
| **`tps/`** | Feuilles de TP de référence : jeu de données, ACP, extraction d’attributs, classificateurs. |
| `utilities.py` | Fonctions utilitaires (chargement d’images, filtres, extraction d’attributs, validation croisée, etc.). |


Un dossier **`clean_data/`** peut être créé pour stocker les images prétraitées et les fichiers exportés (ex. CSV) après nettoyage.

---

## Prérequis

- **Python** 3.x
- Bibliothèques : `numpy`, `pandas`, `matplotlib`, `seaborn`, `scikit-learn`, `Pillow`, `scipy`


---

## Installation et utilisation



1. Lancer Jupyter et ouvrir les feuilles :
   ```bash
   jupyter lab
   ```
   Commencer par `index.md`, puis suivre les étapes (chargement depuis `data/`, analyse, extraction d’attributs, classificateurs).

---

## Déroulement du projet

1. **Jeu de données** : images dans `data/` (poires vs pommes de pin).
2. **Préparation** : recadrage, redimensionnement, éventuel seuillage / filtrage.
3. **Attributs** : extraction (couleur, forme, ACP, etc.) et construction d’un tableau (DataFrame).
4. **Classification** : entraînement et évaluation de modèles (k-NN, arbre, SVM, etc.) avec validation croisée.
5. **Analyse** : comparaison des performances, discussion des biais, perspectives.
6. **Soutenance** : diaporama à partir de `0_diaporama.md`.

---

## Auteurs

Projet réalisé dans le cadre du cours **Introduction à la science des données (L1)**.  
Binôme : Jeremy RAKOTONIRINA, Matias Lievain--Paredes.
