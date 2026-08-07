Archaeological Site Prediction Using LiDAR and Satellite Imagery

## Overview

This project explores the use of **LiDAR-derived terrain data, Sentinel-2 satellite imagery, and Convolutional Neural Networks (CNNs)** to identify areas with archaeological potential.

The main objective is to build a deep-learning pipeline capable of analysing multiple geospatial layers simultaneously and predicting whether a given region is likely to contain an archaeological site.

The project focuses on archaeological landscapes in **Ireland**, using high-resolution terrain information together with multispectral satellite imagery.

---

## Project Objective

Traditional archaeological surveys can require significant time and manual inspection of large geographic regions.

This project aims to assist archaeological exploration by automatically analysing terrain and satellite characteristics that may indicate the presence of archaeological structures.

The final goal is to generate an **archaeological probability map**, highlighting areas that may be useful for further investigation or field surveys.

---

## Data Sources

The project combines two major types of geospatial data.

### LiDAR / Terrain Data

High-resolution Digital Terrain Model (DTM) data is used to capture subtle variations in ground elevation.

LiDAR-derived terrain visualisations help reveal structures that may be difficult to identify from normal satellite imagery, including:

* Mounds
* Enclosures
* Earthworks
* Depressions
* Ancient walls
* Terrain anomalies

The LiDAR data used in the project has a spatial resolution of approximately **1 metre per pixel**.

### Sentinel-2 Satellite Imagery

Sentinel-2 imagery is used to provide spectral and surface information.

The following bands are included:

* Red
* Green
* Blue
* Near Infrared (NIR)

The original Sentinel-2 imagery has approximately **10 metre resolution** and is aligned with the LiDAR dataset before being used by the model.

---

## Input Channels

The CNN receives an **11-channel input image** for every geographic patch.

The channels contain a combination of satellite and terrain-derived information.

### Satellite Channels

1. Red
2. Green
3. Blue
4. Near Infrared

### Terrain Channels

The remaining channels are generated from the Digital Terrain Model and include terrain representations such as:

* Slope
* Sky View Factor
* Local Relief Model
* Multi-directional Hillshade

Four hillshade directions are used to reveal terrain structures illuminated from different angles.

Combining these layers allows the neural network to analyse the same area using multiple representations of the landscape.

---

## Data Processing Pipeline

The general processing pipeline is:

```text
LiDAR / DTM Data
        +
Sentinel-2 Imagery
        ↓
Terrain Feature Generation
        ↓
Raster Alignment and Resampling
        ↓
11-Channel Raster Stack
        ↓
Patch Extraction
        ↓
Archaeological Site Labelling
        ↓
Train / Validation / Test Split
        ↓
CNN Training
        ↓
Site Probability Prediction
        ↓
Archaeological Potential Map
```

---

## Patch Generation

The study region is divided into smaller raster patches before being passed to the neural network.

Each patch has a size of:

```text
250 × 250 pixels
```

Since the reference LiDAR data has approximately 1 metre resolution, each patch represents roughly:

```text
250 m × 250 m
```

of terrain.

Each patch contains all **11 channels**, meaning the CNN receives multiple representations of exactly the same geographic area.

---

## Labelling

The model performs **binary classification**.

Each patch receives one of two labels:

```text
0 → No known archaeological site
1 → Archaeological site present
```

Known archaeological monument locations are used to determine positive samples.

Areas without known monuments are used as negative samples.

---

## Dataset Splitting

The dataset is divided into:

* Training set
* Validation set
* Test set

The split is performed spatially rather than randomly wherever possible.

This is important because neighbouring geographic patches can contain highly similar terrain.

A purely random split could place nearby patches in both the training and test datasets, causing **spatial data leakage** and producing unrealistically high model performance.

---

## Binary Classification

The model produces a single raw output value called a **logit**.

During evaluation, the sigmoid function converts this value into a probability:

```text
Probability = sigmoid(logit)
```

The default decision rule is:

```text
Probability >= 0.5 → Archaeological Site

Probability < 0.5 → No Archaeological Site
```

---

## Evaluation Metrics

Model performance is evaluated using several metrics.

### Accuracy

Measures the percentage of predictions that were classified correctly.

### Precision

Measures how many predicted archaeological sites were actually archaeological sites.

### Recall

Measures how many real archaeological sites were successfully detected.

Recall is particularly important in this project because missing a potential archaeological location may be more significant than investigating an occasional false positive.

### F1 Score

Combines precision and recall into a single metric.

### Confusion Matrix

The confusion matrix evaluates:

```text
True Positive
True Negative
False Positive
False Negative
```

### ROC-AUC

ROC-AUC measures the model's ability to distinguish between archaeological and non-archaeological regions across different classification thresholds.

---

## Technologies Used

The project uses:

* Python
* PyTorch
* NumPy
* Rasterio
* GeoPandas
* QGIS
* GDAL
* Scikit-learn
* Matplotlib
* Sentinel-2 Imagery
* LiDAR / Digital Terrain Models

---

## Expected Output

The final system is intended to process an unseen geographic region and assign an archaeological probability to each patch.

These probabilities can then be converted into a georeferenced heatmap.

```text
Low Probability
        ↓
0.0 ───────────── 1.0
                    ↑
              High Probability
```

Areas with high predicted probabilities can be prioritised for further archaeological analysis.

---

## Why Combine LiDAR and Satellite Data?

LiDAR and satellite imagery provide different but complementary information.

**LiDAR** provides detailed terrain geometry and elevation information.

**Satellite imagery** provides spectral and surface characteristics.

Using both allows the neural network to analyse:

```text
Terrain Shape
+
Elevation Patterns
+
Vegetation / Surface Information
+
Spectral Information
```

This multi-source approach provides significantly more information than using either dataset independently.
