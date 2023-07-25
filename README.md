## Geospatial Data

The AI-generated geospatial data in Satlas Explorer can be downloaded using the links below.
All data is released under the Apache License 2.0.

### Marine Infrastructure (Off-shore Turbines and Platforms)

Marine infrastructure points are available as a single GeoJSON or KML file:

- GeoJSON: https://pub-956f3eb0f5974f37b9228e0a62f449bf.r2.dev/outputs/marine/marine.geojson
- KML: https://pub-956f3eb0f5974f37b9228e0a62f449bf.r2.dev/outputs/marine/marine.kml

Each point is annotated with these attributes:
- `category`: either `offshore_wind_turbine` or `offshore_platform`
- `start`: when the object was constructed
- `end`: when the object was removed, or `2022-12` if still present

Points are also available as snapshots at different points in time, in which case
the file only contains points present at that time (rather than the `start` and
`end` attributes.) Replace YYYY-MM with a year between 2016 and 2022 and month
between 01 and 12:

https://pub-956f3eb0f5974f37b9228e0a62f449bf.r2.dev/outputs/marine/YYYY-MM.geojson

### Tree Cover

Tree cover is available as GeoTIFF files for each WebMercator tile at zoom 7.
WebMercator tiles are indexed from the top-left of the projection coordinate
system from (0, 0) to (127, 127). The resolution is 10 m/pixel.

Replace YYYY-MM with a year between 2016 and 2022 and month either 01 or 07,
along with the unpadded X and Y index of the tile (like 20_44.tif for Seattle):

https://pub-956f3eb0f5974f37b9228e0a62f449bf.r2.dev/outputs/tree-cover/YYYY-MM/X_Y.tif

For example, for Seattle in 2022-07:

https://pub-956f3eb0f5974f37b9228e0a62f449bf.r2.dev/outputs/tree-cover/2022-07/20_44.tif

The GeoTIFF contains a single 8-bit band, with these values:

    0: no data
    1: no trees
    2: low
    3: medium
    4: high
    5: full tree cover

### Renewable Energy Infrastructure (On-shore Wind Turbines and Solar Farms)

Renewable energy infrastructure points (on-shore wind turbines) and polygons (solar farms) are available as a single GeoJSON file:

- GeoJSON: https://pub-956f3eb0f5974f37b9228e0a62f449bf.r2.dev/outputs/renewable/renewable.geojson

Each feature is annotated with these attributes:
- `category`: either `wind_turbine` or `solar_farm`
- `start`: when the object was constructed
- `end`: when the object was removed, or `2022-12` if still present

### Super-Resolution

Due to the large size, the super-resolved imagery is not currently available for bulk download.

For the training and inference code and pre-trained model weights, see https://github.com/allenai/satlas-super-resolution/.

## Training Data and Models

The fine-tuning training data and pre-trained models can be downloaded at https://pub-956f3eb0f5974f37b9228e0a62f449bf.r2.dev/satlas_explorer_datasets/satlas_explorer_datasets_2023-07-24.tar.

This download link contains an archive with four folders:
- `base_models/` contains models trained on SatlasPretrain that are used as initialization for fine-tuning.
- `labels/` contains the fine-tuning training data.
- `models/` contains the pre-trained model weights.
- `splits/` contains metadata about the training and validation splits.

Each training dataset is structured like this:

    labels/solar_farm/
        4267_2839/
            gt.png
            images/
                fp03-2020-12/
                    tci.png
                    b05.png
                    b06.png
                    b07.png
                    b08.png
                    b11.png
                    b12.png
                fp03-2021-01/
                    ...
                ...
        ...

Each folder like `4267_2839/` contains a different training example, which corresponds to a particular geographic location. The `images/` subfolder contains images captured at different times at that location. The current datasets all use [Sentinel-2 images](https://sentinel.esa.int/web/sentinel/missions/sentinel-2). `tci.png` contains B02, B03, and B04. The 10 m/pixel and 20 m/pixel bands (except B8A) are used as input and included in the training data, while the 60 m/pixel bands are not used.

For segmentation (solar farm) and regression (tree cover) labels, `gt.png` contains a greyscale mask. For segmentation, the pixel value indicates the class ID. For regression, the pixel value indicates the ground truth value.

For object detection labels (on-shore wind turbines and marine infrastructure), `gt.json` contains bounding box labels like this:

    [
        [14, 467, 54, 507, "wind_turbine"],
        [53, 473, 93, 513, "wind_turbine"]
    ]

Each box is in the form `[start_col, start_row, end_col, end_row, category_name]`. The current tasks are annotated as points so the boxes are all the same size, the center point is the actual label and can be easily derived if needed.

See https://github.com/allenai/satlas/ for details on using the training data or models.