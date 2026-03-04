---
jupytext:
  notebook_metadata_filter: rise
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
rise:
  auto_select: first
  autolaunch: false
  backimage: fond.png
  centered: false
  controls: false
  enable_chalkboard: true
  height: 100%
  margin: 0
  maxScale: 1
  minScale: 1
  scroll: true
  slideNumber: true
  start_slideshow_at: selected
  transition: none
  width: 90%
---

+++ {"slideshow": {"slide_type": ""}, "editable": true, "tags": []}

# Titre

- Binôme: Jeremy Rakotonirina, Matias Lievain--Paredes
- Adresses mails: jeremy.rakotonirina@universite-paris-saclay.fr matias.lievain-paredes@universite-paris-saclay.fr
-
- [Dépôt GitLab](https://gitlab.dsi.universite-paris-saclay.fr/jeremy_rakotonirina_travo/L1InfoInitiationScienceDonnees/2023-2024/Semaine7)

+++ {"slideshow": {"slide_type": ""}, "editable": true, "tags": []}

## Jeu de données


+++ {"editable": true, "slideshow": {"slide_type": ""}, "tags": []}

![image](images_diapo/image1.png)

+++ {"slideshow": {"slide_type": ""}, "editable": true, "tags": []}

## Prétraitement 


+++

<u>Recadrage</u>

+++

![blabla](images_diapo/image4.png)

```{code-cell} ipython3
def my_preprocessing(img):
    foreground = my_foreground_filter(img)
    img = transparent_background(img, foreground)
    return img
```

![blabla](images_diapo/image5.png)

+++ {"editable": true, "slideshow": {"slide_type": ""}, "tags": []}

_Après le prétraitement (lissage, réduction du bruit)_

+++

<u>Raisons du choix des attributs redness et greeness </u>

+++

![blabla](images_diapo/image6.png)

+++ {"editable": true, "slideshow": {"slide_type": ""}, "tags": []}

![blabla](images_diapo/image7.png)

+++ {"editable": true, "slideshow": {"slide_type": ""}, "tags": []}

![blabla](images_diapo/image8.png)

+++ {"slideshow": {"slide_type": ""}, "editable": true, "tags": []}

## Classificateurs favoris 



+++ {"editable": true, "slideshow": {"slide_type": ""}, "tags": []}

<u> Tableau de performances des classificateurs </u>

+++ {"editable": true, "slideshow": {"slide_type": ""}, "tags": []}

![blabla](images_diapo/image9.png)

+++



+++ {"slideshow": {"slide_type": "slide"}}

## Interprétations, Discussion et conclusion


+++ {"editable": true, "slideshow": {"slide_type": ""}, "tags": []}

![image.png](images_diapo/image2.png)

+++ {"editable": true, "slideshow": {"slide_type": ""}, "tags": []}

![image.png](images_diapo/image3.png)

+++

![image.png](images_diapo/image10.png)

+++

_Images prétraitées du jeu de données ci-dessus_

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---

```
