---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "56952047c4310bf0c313ca44bb5af720", "grade": false, "grade_id": "cell-883bbb5e1919ca1e", "locked": true, "schema_version": 3, "solution": false, "task": false}}

# Analyse de données

:::{admonition} Consignes
:class: dropdown

Vous documenterez votre analyse de données dans cette feuille. Nous
vous fournissons seulement l'ossature. À vous de piocher dans les
feuilles et TPs précédents les éléments que vous souhaiterez
réutiliser et adapter pour composer votre propre analyse!

:::

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "07b6f7ec89dbe99e092902adb7ff9cc8", "grade": false, "grade_id": "cell-e15fdd0116a9b9e2", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Import des bibliothèques

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "96489bd4f670f5daa152f95fa18ce301", "grade": false, "grade_id": "cell-406ad2a159064cfa", "locked": true, "schema_version": 3, "solution": false, "task": false}}

On commence par importer les bibliothèques dont nous aurons besoin.

:::{admonition} Consignes
:class: dropdown

Inspirez vous des précédentes feuilles. N'oubliez pas d'importer
`utilities` et `intro_science_donnees`.

:::

```{code-cell} ipython3
---
deletable: false
editable: true
nbgrader:
  cell_type: code
  checksum: 8e6a7da13b6e1d41caa1c33a8c0104e5
  grade: false
  grade_id: cell-76e64ee7bcb96bb5
  locked: false
  schema_version: 3
  solution: true
  task: false
slideshow:
  slide_type: ''
tags: []
---
import os, re
from glob import glob as ls
from PIL import Image, ImageDraw, ImageFont
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import pandas as pd
import seaborn as sns; sns.set()
from PIL import Image

import warnings
warnings.filterwarnings("ignore")
from sys import path
from glob import glob as ls
import sys
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import balanced_accuracy_score as sklearn_metric

from utilities import *
from intro_science_donnees import data

from typing import Any, Dict, Union
import numpy as np  # type: ignore
import pandas as pd  # type: ignore
from PIL import Image, ExifTags  # type: ignore
from sklearn.model_selection import StratifiedShuffleSplit  # type: ignore

from intro_science_donnees import *
%load_ext autoreload
%autoreload 2
import os
from PIL import Image, ImageDraw, ImageFont
import matplotlib.pyplot as plt
%matplotlib inline
import scipy
from scipy import signal
import pandas as pd
import seaborn as sns
from glob import glob as ls
import sys
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import balanced_accuracy_score as sklearn_metric

from utilities import *
from intro_science_donnees import data
from intro_science_donnees import *
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "e319f7fbbc65f7368fd94b546edb9e34", "grade": false, "grade_id": "cell-6ec03d05a4e1c693", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Chargement des images

:::{admonition} Consignes
:class: dropdown

- Chargez vos images recentrées et réduites.
- Affichez les.

:::

```{code-cell} ipython3
---
deletable: false
editable: true
nbgrader:
  cell_type: code
  checksum: a842cb19c2f315d9a183cf8a17bd295f
  grade: false
  grade_id: cell-211e275ccc954714
  locked: false
  schema_version: 3
  solution: true
  task: false
slideshow:
  slide_type: ''
tags: []
---
extension = 'jpg' 
dataset_dir = os.path.join('..','data')
images_a = load_images(dataset_dir, f"a*.{extension}")
images_b = load_images(dataset_dir, f"b*.{extension}")
images = pd.concat([images_a, images_b])
for i in range(len(images)):
    images.iloc[i]=crop_image(images.iloc[i],160)
image_grid(images)
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "0e4e77fedcf1d467c6139e4c44ff1e5e", "grade": false, "grade_id": "cell-e723e3fb5001bd97", "locked": true, "schema_version": 3, "solution": false, "task": false}}

## Prétraitement : extraction des attributs

:::{admonition} Consignes
:class: dropdown

- Choisissez entre des attributs ad-hoc ou obtenus via une ACP depuis
  les données brutes (représentation en pixel).
- N'oubliez pas de normaliser votre table une fois les traitements
  effectués.
  
:::

```{code-cell} ipython3
def my_foreground_filter(img):
            
    return scipy.ndimage.gaussian_filter(invert_if_light_background(
                      foreground_redness_filter(img, theta=.45)),
                  sigma=.1)
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
show_source(my_preprocessing)
```

On a fait cela car on a vu sur l'image suivante qu'il suffisait juste de recadrer.

+++

![image.png](attachment:820c44d1-611a-4824-b608-262a7e074059.png)!

+++

Image obtenue après le filtre redness.

+++

On a donc recadré au début, pour effacer les imprécisions autour.

```{code-cell} ipython3
clean_images=images.apply(my_preprocessing)
```

```{code-cell} ipython3
# conversion
df_clean = clean_images.apply(image_to_series)
# ajout des étiquettes
df_clean['class'] = df_clean.index.map(lambda name: 1 if name[0] == 'a' else -1)
```

On a choisi les attributs redness et greeness après étude des corrélations.

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: []
---
df_features = pd.DataFrame({
        'redness':    clean_images.apply(redness),
        'greenness': clean_images.apply(greenness),
        'blueness': clean_images.apply(blueness),
        'elongation': clean_images.apply(elongation),
        'perimeter': clean_images.apply(perimeter),
        'surface': clean_images.apply(surface),
        
})

df_features
```

```{code-cell} ipython3
epsilon = sys.float_info.epsilon
df_features = (df_features - df_features.mean())/(df_features.std() + epsilon) # normalisation 
```

```{code-cell} ipython3
df_features["class"] = df_clean["class"]
df_features[df_features.isna()] = 0
df_features.style.background_gradient(cmap='coolwarm')
```

+++ {"editable": true, "slideshow": {"slide_type": ""}, "tags": []}

Les attributs ont une forte corrélation c'ets pour cela qu'on n'en a pris que deux et qu'on n'a pas pris un truc coûteux comme les 23 attributs ad-hoc

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "0b04fc60580d53165e039ab88751a746", "grade": false, "grade_id": "cell-a69bb602ef2221a4", "locked": true, "schema_version": 3, "solution": false, "task": false}, "user_expressions": []}

## Sauvegarde intermédiaire

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "35a89b8dc927e0dd763e9cdf592c4bdf", "grade": false, "grade_id": "cell-8cb1846130b9cfe2", "locked": true, "schema_version": 3, "solution": false, "task": false}, "user_expressions": []}

:::{admonition} Consignes
:class: dropdown

Une fois que vous êtes satisfaits des attributs obtenus, faites en une
sauvegarde intermédiaire.

:::

```{code-cell} ipython3
os.makedirs('clean_data', exist_ok=True)
for name, img in clean_images.items():
    img.save(os.path.join('clean_data', os.path.splitext(name)[0]+".png"))
```

```{code-cell} ipython3
df_clean.to_csv('clean_data.csv')
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "b11c92757f734a72bf65f47b9513ca5f", "grade": false, "grade_id": "cell-4576e2c2723b724d", "locked": true, "schema_version": 3, "solution": false, "task": false}, "user_expressions": []}

## Visualisation des données

+++ {"deletable": false, "editable": true, "nbgrader": {"cell_type": "markdown", "checksum": "ba2beca9ed69fdecbf145787bb70f7af", "grade": false, "grade_id": "cell-2a67648ea9c07906", "locked": true, "schema_version": 3, "solution": false, "task": false}, "slideshow": {"slide_type": ""}, "tags": []}

:::{admonition} Consignes
:class: dropdown

Visualisez les attributs et leur corrélation. Mettez en œuvre les
formes que vous jugerez les plus pertinentes (carte thermique, ...).

:::

```{code-cell} ipython3

corr = df_features.corr()
corr.style.format(precision=2).background_gradient(cmap='coolwarm')
```

+++ {"deletable": false, "editable": false, "nbgrader": {"cell_type": "markdown", "checksum": "fed3b57a555c863751f511011266a6de", "grade": false, "grade_id": "cell-c103279002f68467", "locked": true, "schema_version": 3, "solution": false, "task": false}, "user_expressions": []}

## Performance 

:::{admonition} Consignes
:class: dropdown

- Choisissez un ou plusieurs classificateurs et calculez leur
  performances.
- Comparez les performances selon les attributs utilisés (ad-hoc,
  ...).
- Si vous le souhaitez, vous pouvez aussi:
    - comparer un ou plusieurs classificateurs
	- comparer les performances selon le nombre de colonnes (pixels ou
      attributs) considérées.

:::

```{code-cell} ipython3
dataset_dir = 'clean_data'

images = load_images(dataset_dir, "*.png")
df_features = pd.DataFrame({
        'redness':    images.apply(redness),
        'greenness': images.apply(greenness),
        


})
epsilon = sys.float_info.epsilon
df_features = (df_features - df_features.mean())/(df_features.std() + epsilon) # normalisation 
df_features['class'] = df_features.index.map(lambda name: 1 if name[0] == 'a' else -1)
```

```{code-cell} ipython3
from sklearn.neural_network import MLPClassifier
from sklearn.neighbors import KNeighborsClassifier
from sklearn.neighbors import RadiusNeighborsClassifier
from sklearn.svm import SVC
from sklearn.gaussian_process import GaussianProcessClassifier
from sklearn.gaussian_process.kernels import RBF
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier, AdaBoostClassifier
from sklearn.naive_bayes import GaussianNB
from sklearn.discriminant_analysis import QuadraticDiscriminantAnalysis

model_name = ["Nearest Neighbors", "Parzen Windows",  "Linear SVM", "RBF SVM", "Gaussian Process",
         "Decision Tree", "Random Forest", "Neural Net", "AdaBoost",
         "Naive Bayes", "QDA"]
model_list = [
    KNeighborsClassifier(3),
    RadiusNeighborsClassifier(radius=12.0),
    SVC(kernel="linear", C=0.025, probability=True),
    SVC(gamma=2, C=1, probability=True),
    GaussianProcessClassifier(1.0 * RBF(1.0)),
    DecisionTreeClassifier(max_depth=10),
    RandomForestClassifier(max_depth=10, n_estimators=10, max_features=1),
    MLPClassifier(alpha=1, max_iter=1000),
    AdaBoostClassifier(),
    GaussianNB(),
    QuadraticDiscriminantAnalysis()]
```

```{code-cell} ipython3
df_features
```

```{code-cell} ipython3
---
deletable: false
nbgrader:
  cell_type: markdown
  checksum: ae7f04d9aea4689c8f25f33fd8cddf30
  grade: true
  grade_id: cell-c5a8e3d90bb35375
  locked: false
  points: 0
  schema_version: 3
  solution: true
  task: false
---
from sklearn.metrics import balanced_accuracy_score as sklearn_metric
compar_results = systematic_model_experiment(df_features, model_name, model_list, sklearn_metric)
compar_results.style.format(precision=2).background_gradient(cmap='Blues')
```

![image.png](attachment:698ef429-318a-466b-b256-55b6f1775fbc.png)

```{code-cell} ipython3

```
