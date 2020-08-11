---
id: 0002-initial-use-case
title: 2 - Initial Use Case
---
Date: 2020-08-10

## Status

Proposed

## Context

We define the initial use case for `ml-aoi` spec that exposes assumptions and reasoning for specific layout choices:
providing training data source for Raster Vision model training process.

`ml-aoi` STAC Items represent a reified relation between feature rasters and ground-truth label in a machine learning training dataset.
Each `ml-aoi` Item roughly correspond to a "scene" or a training example.

### Justification for new extension

Current known STAC extensions are not suitable for this purpose. The closest match is the STAC `label` extension.
`label` extension provides a way to define either vector or raster labels over area.
However, it does not provide a mechanism to link those labels with feature images;
links with `rel` type `source` point to imagery from which labels were derived.
Sometimes this imagery will be used as feature input for model training, but not always.
The concept of source label imagery and input feature imagery are semantically distinct.
For instance it is possible to apply a single source of ground-truth building labels to train a model on either Landsat or Sentinel-2 scenes.

### Catalog Lifetime

`ml-aoi` Item links to both raster STAC item and label STAC item.
In this relationship the source raster and label items are static and long lived, being used by several `ml-aoi` catalogs.
By contrast `ml-aoi` catalog is somewhat ephemeral, it captures the training set in order to provide model reproducibility and provenance.
There can be any number of `ml-aoi` catalogs linking to the same raster and label items, while varying selection, training/testing/validation split and class configuration.

#### Multiple Label Items

Should each `ml-aoi` Item be able to bring int multiple labels?
This would be a useful feature for training multi-class classifiers.
One can imagine having a label STAC item for buildings and separate STAC item for fields.



## Decision

TBD

## Consequences

TBD