# STAC ML AOI Extension

- **Title: ML AOI**
- **Identifier: ml-aoi**
- **Field Name Prefix: ml-aoi**
- **Scope: Item, Collection**
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-api-spec/blob/master/extensions/README.md#extension-maturity): Proposal**

An Item and Collection extension to provide labeled training data for machine learning models.

## Item

Each `ml-aoi` Item is a represent a relation between label and raster feature items.
While each Item will link

### Core Item fields

* **bbox**: The bounding box for area of interest where labels and imagery intersect.
* **geometry**: Area(s) for which labels are valid.
* **assets**: Label and feature imagery assets.


| Field Name | Type | Name | Description |
|------------|------|------|-------------|
| `ml-aoi:split` | string | Split | Assigns item to one of `train`, `test`, or `validate` sets |

* `ml-aoi:split` is not required to support sharing training sets without split being assigned.
In those cases its expected that the split will be added later before consuming the items.
* `ml-aoi` Items maybe reference same label and image item by reducing the `bbox` and `geometry` fields.
* `ml-aoi` Items may `bbox` field overlap when they belong to different `ml-aoi:split` set.
* `ml-aoi` Items in the same Collection should never have overlapping `geometry` fields.

#### Links

`ml-aoi` Item must link to both label and feature STAC items valid for its area of interest.
These Link objects should set `rel` field to `derived_from` for both label and feature items.

`ml-aoi` Item should be contain enough metadata to make it consumable without the need for following the label and feature link item links. In reality this may not be practical because the use-case may not be fully known at the time the Item is generated. Therefore it is critical that source label and feature items are linked to provide the future consumer the option to collect additional metadata from them.

| Field Name | Type | Name | Description |
|------------|------|------|-------------|
| `ml-aoi:role` | string | Role | `ground-truth` or `feature` |

##### Labels

An `ml-aoi` Item must link to exactly one ground-truth STAC items that are using `label` extension.
Label links should provide `ml-aoi:role` field set to `ground-truth` value.

##### Features

An `ml-aoi` Item must link to at least one raster STAC item.
Feature links should provide `ml-aoi:role` field set to `feature` value.

Linked feature STAC items may use `eo` but that is not required.
It is up to the consumer of `ml-aoi` Items to decide how to use the linked feature rasters.


#### Assets

Item should directly include assets for label and feature rasters.

| Field Name | Type | Name | Description |
|------------|------|------|-------------|
| `ml-aoi:role` | string | Role | `ground-truth` or `feature` |
| `ml-aoi:reference-grid` | bool | Reference Grid | Use raster pixel grid as model training grid |
| `ml-aoi:resampling-method` | string | Resampling Method | Resampling method used to match other rasters to reference pixel grid, should be one of the values [supported by gdalwarp](https://gdal.org/programs/gdalwarp.html#cmdoption-gdalwarp-r) |

##### Labels

Assets for the label item can be copied directly from the label item with their asset name preserved.
Label assets should provide `ml-aoi:role` field set to `ground-truth` value.

##### Rasters

Assets for the label item can be copied directly from the label item with their asset name preserved.
Feature assets should provide `ml-aoi:role` field set to `feature` value.

When multiple raster features are included their resolutions and pixel grids are not likely to align.
One raster may be specify `ml-aoi:reference-grid` field set to `true` to indicate that all other features
should be resampled to match its grid during model training.

Other raster assets


## Collection

All `ml-aoi` Items should belong to a Collection that designates a specific model training input.
Items with-in collection must share their label and feature structure.

### Collection fields

The consumer of `ml-aoi` catalog needs to understand the available label classes and features without crawling the full catalog.
`ml-aoi` collection must use `item-asset` extension to list all label and feature assets present on collection member Items.

When member Items include multiple feature rasters it is possible that not all of them will overlap every AOI.
