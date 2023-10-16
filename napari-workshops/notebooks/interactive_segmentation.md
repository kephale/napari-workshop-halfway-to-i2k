---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.14.7
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# Interactive segmentation with napari

+++

Based on https://haesleinhuepf.github.io/BioImageAnalysisNotebooks/20a_pixel_classification/scikit_learn_random_forest_pixel_classifier.html

+++

Napari provides a simple way to create animations in both manual (interactive) and programmatic (scripting) way through the [Animation Plugin](https://github.com/napari/napari-animation).

In this tutorial we will create a simple animation of [FIB-SEM data](https://openorganelle.janelia.org/datasets/jrc_mus-kidney).

+++

## Setup

```{code-cell} ipython3
:tags: [remove-output]

# this cell is required to run these notebooks on Binder. Make sure that you also have a desktop tab open.
import os
if 'BINDER_SERVICE_HOST' in os.environ:
    os.environ['DISPLAY'] = ':1.0'
```

```{code-cell} ipython3
import os
import zarr
import dask.array as da

from sklearn.ensemble import RandomForestClassifier

from skimage import data, segmentation, feature, future
from skimage.io import imread, imshow
import numpy as np
from functools import partial
import napari
```

## Reading in data

+++

Get data from OpenOrganelle.

```{code-cell} ipython3
group = zarr.open(zarr.N5FSStore('s3://janelia-cosem-datasets/jrc_mus-kidney/jrc_mus-kidney.n5', anon=True)) # access the root of the n5 container

# Get access to all resolution levels of the data.
ddata = [
    da.from_zarr(group[f'em/fibsem-uint8/s{i}'])
    for i in range(0, 4)
]
```

```{code-cell} ipython3
ddata[3].shape
```

Get an example cube of the data and load it into memory.

```{code-cell} ipython3
cropped_img = ddata[3][50:150:2,400:500,1000:1100].compute()
cropped_img.shape
```

### Visualize in napari

```{code-cell} ipython3
viewer = napari.Viewer()
viewer.add_image(cropped_img)
```

## Features

```{code-cell} ipython3
from skimage import filters

def generate_feature_stack(image):
    # determine features
    blurred = filters.gaussian(image, sigma=2)
    edges = filters.sobel(blurred)

    # collect features in a stack
    # The ravel() function turns a nD image into a 1-D image.
    # We need to use it because scikit-learn expects values in a 1-D format here. 
    feature_stack = [
        image.ravel(),
        blurred.ravel(),
        edges.ravel()
    ]
    
    # return stack as numpy-array
    return np.asarray(feature_stack)

feature_stack = generate_feature_stack(cropped_img)

# for idx in range(len(feature_stack)):
#     viewer.add_image(feature_stack[0].reshape(cropped_img.shape))
    
sigma_min = 1
sigma_max = 16
features_func = partial(feature.multiscale_basic_features,
                        intensity=True, edges=False, texture=True,
                        sigma_min=sigma_min, sigma_max=sigma_max,
                        channel_axis=None)
features = features_func(cropped_img)

for idx in range(features.shape[-1]):
    viewer.add_image(features[:, :, :, idx])
```

Add a Labels layer

```{code-cell} ipython3
training_labels = viewer.layers['Labels'].data

clf = RandomForestClassifier(n_estimators=50, n_jobs=-1,
                             max_depth=10, max_samples=0.05)
clf = future.fit_segmenter(training_labels, features, clf)
result = future.predict_segmenter(features, clf)
```

```{code-cell} ipython3
from sklearn.tree import plot_tree
import matplotlib.pyplot as plt

example_tree = clf.estimators_[0]

fig = plt.figure()
ax = plt.axes()
plt.style.use('dark_background')
ax.set_facecolor("black")

plot_tree(example_tree)

plt.savefig('/Users/kharrington/Desktop/mandm_tree.svg')
```

```{code-cell} ipython3
viewer.add_image(result)
```
