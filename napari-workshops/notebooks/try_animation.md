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

# Introduction to animation with napari

+++

Creating a short animation is a great way to introduce your data during a presentation and provide insight into the image analysis. It is especially valuable while working with 3D data that can otherwise be presented only in a form of 2D screenshots.

+++

To grasp the process of creating an animation, it is essential to understand the concept of keyframes (https://en.wikipedia.org/wiki/Key_frame ). Keyframes can be likened to anchor points that steer an animation. Thus, by defining the keyframes, you determine what will be portrayed. The transitions between keyframes are smooth and are automatically filled in for you. Keyframes in a napari animation can capture such events as change of a frame, zoom, camera angle (3D projections) or even brightness of an image. The speed of the animation is controlled by the number of in-between frames (‘Steps’) inserted between the key frames.

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
:tags: [remove-output]
# Instal animation plugin
# This step has to be performed only once in a given environment.
# You can remove this cell or use '#' to comment the code out after a successful installation.

!pip install napari-animation

# No problem if you execute it again though, Python will just tell you 'Requirement already satisfied'.
```

```{code-cell} ipython3
import os
import zarr
import dask.array as da

import napari

from napari_animation import Animation
from napari_animation.easing import Easing
```

## Reading in data

+++

Get data from OpenOrganelle.

```{code-cell} ipython3
group = zarr.open(zarr.N5FSStore('s3://janelia-cosem-datasets/jrc_mus-kidney/jrc_mus-kidney.n5', anon=True)) # access the root of the n5 container

# Get access to all resolution levels of the data.
img_data = [
    da.from_zarr(group[f'em/fibsem-uint8/s{i}'])
    for i in range(0, 4)
]

# Get label data for all resolution levels
label_data = [
    da.from_zarr(group[f'labels/empanada-mito_seg/s{i}'])
    for i in range(0, 4)
]
```

Get an example cube of the data and load it into memory.

```{code-cell} ipython3
cropped_img = img_data[3][50:150:2,400:500,1000:1100].compute()
print(cropped_img.shape)

# Note that the segmentations are downsampled by 2, so we need to use a different scale for labels
cropped_label = label_data[2][50:150:2,400:500,1000:1100].compute()
print(cropped_label.shape)
```

## Visualize in napari

```{code-cell} ipython3
:tags: [remove-output]
viewer = napari.Viewer()
viewer.add_image(cropped_img)
```

## Use animation plugin interactively

+++

- From the upper menu choose Plugins - wizard (napari-animation)
- With the bottom slides choose first slice \#0 
- Click 'Capture' to set it as a first keyframe
- Choose number of Steps to 30
- Move slider to slice \#40
- Capture the keyframe
- Move slider to slice \#49
- Choose number of Steps to 60
- Capture the keyframe
- Save your first animation

+++

## Use script to record an animation

```{code-cell} ipython3
:tags: [remove-output]

# define pathway to save your animation
save_path = 'my_animation_from_script.mp4'
```

```{note}
If you don\'t specify a full pathway but only a name of the file it will be saved in your current working directory (cwd).
If you don\'t know where it is use os.getcwd()
```

```{code-cell} ipython3
animation = Animation(viewer)

viewer.reset_view()
viewer.dims.current_step = (0, 0, 0)
animation.capture_keyframe()

viewer.dims.current_step = (40, 0, 0)
animation.capture_keyframe(steps=40)

viewer.dims.current_step = (49, 0, 0)
animation.capture_keyframe(steps=70)

animation.animate(save_path, canvas_only=True, fps=24)
```

## Script from the community

Check out this demo from the community that was collaboratively developed on the by Callum Tromans-Coia, Lorenzo Gaifas, and Alister Burt. For more details see https://forum.image.sc/t/creating-an-animation-for-visualisation-of-3d-labels-emerging-from-a-2d-plane/77517/6.


```{code-cell} ipython3
viewer = napari.Viewer(ndisplay=3)

image_layer = viewer.add_image(cropped_img, name="image", depiction="plane", blending='additive')
labels_layer = viewer.add_labels(cropped_label, name="labels")

viewer.camera.angles = (-18.23797054423494, 41.97404742075617, 141.96173085742896)
```

```{code-cell} ipython3
animation = Animation(viewer)

labels_layer.visible = False
image_layer.plane.position = (0, 0, 0)
animation.capture_keyframe(steps=30)

image_layer.plane.position = (49, 0, 0)
animation.capture_keyframe(steps=30)

image_layer.plane.position = (0, 0, 0)

animation.capture_keyframe(steps=30)

# define a function to replace label data when viewer position changes
def replace_labels_data():
    z_cutoff = int(image_layer.plane.position[0])
    new_labels_data = cropped_label.copy()
    new_labels_data[z_cutoff:] = 0
    labels_layer.data = new_labels_data


image_layer.plane.events.position.connect(replace_labels_data)
labels_layer.visible = True
labels_layer.experimental_clipping_planes = [{
    "position": (0, 0, 0),
    "normal": (-1, 0, 0),  # point up in z (i.e: show stuff above plane)
}]

image_layer.plane.position = (49, 0, 0)
# access first plane, since it's a list
labels_layer.experimental_clipping_planes[0].position = (49, 0, 0)
animation.capture_keyframe(steps=30)

image_layer.plane.position = (0, 0, 0)
animation.capture_keyframe(steps=30)

animation.animate("animation_labels.mp4", canvas_only=True)
image_layer.plane.position = (0, 0, 0)
```
