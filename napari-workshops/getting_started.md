# Getting started

```{note}
As you are building your workshop, consider who your audience is and what their
goals are. One way to do that is to come up with a few
[learner profiles](https://software-carpentry.org/audience/).

Make sure this page contains basic information about your workshop, such as the
main contents, instructors' names and contact information if available, and
information about the date and location for this session.

Below you can see an example of how this can be done.
```

This workshop will take you through the basics of bioimage analysis in
Python and [napari](https://www.napari.org), and introduce you to the napari
plugin engine. We will be performing some analysis on segmented nuclei using a
combination of `napari`, `scikit-image`, `scipy`, and `cellpose`. Additionally,
we will make a spot detection plugin.

## Instructors
The instructors for this workshop are
- Name1
- Name2

## Goals
The aim of this workshop is to provide an introduction to bioimage analysis in
Python and `napari`. By the end of the workshop you should be able to
- use napari as an image viewer
- perform interactive analysis with napari in a
  [jupyter notebook](https://jupyter.org/)
- make use of the [napari plugin ecosystem](https://www.napari-hub.org/)
- make simple plugins using the napari hookspecs

## Pre-tutorial setup

So that we can best utilize our time together, please do the following before
arriving at the tutorial:

1. Install napari and dependencies. Instructions [here](./installation.md).
2. Download the notebooks and launch the `jupyter notebook` application.
  Instructions [here](./notebook_setup.md).

## Tutorial instructions

1. Do the introduction to viewing images in napari
  (`part_0_viewer_intro.ipynb`).
2. Segment and measure nuclei using the cellpose plugin
  (`part_1_segmenting_and_measuring_nuclei.ipynb`).
3. Perform spot detection using your own spot detection function. There are 3
  versions of this tutorial (see description below). They all cover the same
  content, they just require more or less coding depending on experience level.
    - `part_2_spot_detection_tutorial_beginner.ipynb`: this is the activity
      notebook for people new to image processing with python
    - `part_2_spot_detection_tutorial_advanced.ipynb`: this is the activity
      notebook for people with experience performing image processing with
      python
    - `part_2_spot_detection_tutorial_solution.ipynb`: this is the solution to
      the activity.
4. Turn your spot detection function into a napari plugin.
