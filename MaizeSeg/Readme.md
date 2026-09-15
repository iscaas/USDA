# MaizeSeg

**An Under-Canopy Maize Image Dataset for Vegetation–Background Segmentation**

[![License: CC BY-NC 4.0](https://img.shields.io/badge/Data%20License-CC--BY--NC--4.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)

> Repository: **[github.com/your-org/MaizeSeg](https://github.com/your-org/MaizeSeg)**
> Paper: *MaizeSeg: An Under-Canopy Maize Image Dataset for Vegetation Background Segmentation Enabling Downstream Stress Phenotyping* (IEEE Access, under review)
> Dataset DOI: *[to be added upon publication]*

---

## Table of Contents

- [Overview](#overview)
- [Why MaizeSeg?](#why-maizeseg)
- [Dataset](#dataset)
  - [Summary](#summary)
  - [Collection Site and Nitrogen Treatments](#collection-site-and-nitrogen-treatments)
  - [Image Acquisition](#image-acquisition)
  - [Annotation Protocol](#annotation-protocol)
  - [Data Format](#data-format)
  - [Download](#download)
- [Model & Benchmark Preview](#model--benchmark-preview)

---

## Overview

**MaizeSeg** is a high-resolution, pixel-annotated RGB image dataset for **vegetation–background segmentation** in **under-canopy maize (*Zea mays L.*) field imagery**. It was built to support reliable nutrient-stress phenotyping under real field conditions — where lighting variability, occlusion, soil exposure, crop residue, and stress-induced necrotic discoloration make vegetation/background separation substantially harder than in above-canopy or lab-based datasets.

<p align="center">
  <img src="https://github.com/USDA/MaizeSeg/blob/main/MaizeSeg/Assets/Aerial.png?raw=true" width="850" alt="Geographical location and experimental setup used for MaizeSeg data acquisition"/>
</p>
<p align="center"><em>Geographical location and experimental setup used for MaizeSeg data acquisition. The map indicates the study location in Kansas, USA, while the enlarged panels show the experimental maize plot, nitrogen-treatment row arrangement, and the mobile under-canopy image-acquisition platform.</em></p>

---

## Why MaizeSeg?

Existing RGB datasets for maize phenotyping are largely collected **above-canopy** (aerial/UAV) or in **controlled lab settings** with clean, monochrome backgrounds. Neither setting reflects the conditions under which **mobile nutrient deficiencies** (e.g., nitrogen) first become visible — typically in the **lower canopy**, where:

- Illumination is highly non-uniform (shadows, partial occlusion by upper leaves)
- Senescent and necrotic tissue take on yellow/brown/low-chroma hues that visually resemble **soil and crop residue**
- Vegetation and background pixels are frequently confused by simple color-index or thresholding methods

MaizeSeg directly targets this gap with **2,974 pixel-annotated under-canopy images**, collected across a full range of nitrogen-treatment conditions and growth stages (V10–R4), to provide a realistic benchmark for vegetation segmentation.

| | Above-canopy / UAV datasets | Lab / leaf-only datasets | **MaizeSeg** |
|---|---|---|---|
| Under-canopy viewpoint | ✗ | ✗ | ✅ |
| Field-realistic occlusion & lighting | Partial | ✗ | ✅ |
| Pixel-level vegetation masks | Rare | N/A | ✅ |
| Nutrient-stress visual variability | Rare | Rare | ✅ |
| Public availability | Varies | Varies | ✅ |

<p align="center">
  <img src="https://github.com/USDA/MaizeSeg/blob/main/MaizeSeg/Assets/Data.png?raw=true" width="850" alt="Representative under-canopy RGB images included in MaizeSeg"/>
</p>
<p align="center"><em>Representative under-canopy RGB images included in MaizeSeg, spanning nitrogen-sufficient rows, the transition region, and nitrogen-deficient rows under varying illumination, canopy density, leaf occlusion, senescence, soil exposure, residue, and viewing geometry.</em></p>

---

## Dataset

### Summary

| Attribute | Description |
|---|---|
| Dataset name | MaizeSeg |
| Crop type | Maize (*Zea mays L.*) |
| Total images | 2,974 RGB images |
| Image resolution | 4608×2592 and 4608×3456 pixels |
| Growth stages covered | V10–R4 |
| Classes | Vegetation (foreground), Background (soil, sky, residue, etc.) |
| Annotation type | Pixel-wise binary segmentation masks |
| Nitrogen conditions | Nitrogen-deficient (0 lb N/ac): 1,543 images · Nitrogen-sufficient (200 lb N/ac): 1,431 images |
| Acquisition view | Under-canopy, ground-level, ~45° camera angle |
| Environment | Field conditions — variable illumination, shadows, occlusion, soil, residue |

### Collection Site and Nitrogen Treatments

Images were collected at the **Kansas State University North Farm Agronomy Education Center (AEC)**, on a ~1,875 ft² experimental plot of 12 maize rows — 6 rows nitrogen-deficient (no applied fertilizer, 0 lb N/ac) and 6 rows nitrogen-sufficient (200 lb N/ac applied pre-planting). Nitrogen variability was used specifically to introduce realistic visual stress complexity (chlorosis, senescence, necrotic patterning) rather than as a standalone classification target — **MaizeSeg is a segmentation dataset, not a nitrogen-classification dataset.**

### Image Acquisition

Images were collected using a custom self-maneuvered mobile platform designed to move between narrow maize rows. A Canon SX530 HS 16 MP RGB camera was mounted on the platform at an approximately 45° viewing angle to capture under-canopy images from a ground-level perspective, representing the visual conditions encountered by ground-based agricultural robots and field phenotyping systems operating within maize rows.

<p align="center">
  <img src="https://github.com/USDA/MaizeSeg/blob/main/MaizeSeg/Assets/Image_Acq.png?raw=true" width="850" alt="Under-canopy image acquisition methodology"/>
</p>
<p align="center"><em>Under-canopy image acquisition methodology. A ground-level RGB camera mounted on the mobile platform captured oblique images while moving between maize rows, at the low-angle perspective represented throughout MaizeSeg.</em></p>

### Annotation Protocol

- Manual pixel-level annotation performed with **LabelMe**.
- All maize tissue (green, chlorotic, senescent, necrotic) → **vegetation/foreground**; soil, sky, residue, machinery → **background**.
- HSV thresholding used only as an *auxiliary* refinement aid for unambiguous regions (e.g., sky); never used to resolve ambiguous vegetation/soil boundaries.
- All masks visually inspected at full resolution; ambiguous boundaries manually corrected.

### Data Format

The dataset is organized into **train** and **test** splits, each containing paired RGB images and binary segmentation masks.

```
MaizeSeg/
├── train/
│   ├── images/
│   │   ├── img_0001.jpg
│   │   ├── img_0002.jpg
│   │   └── ...
│   └── masks/
│       ├── img_0001.png
│       ├── img_0002.png
│       └── ...
└── test/
    ├── images/
    │   ├── img_2401.jpg
    │   ├── img_2402.jpg
    │   └── ...
    └── masks/
        ├── img_2401.png
        ├── img_2402.png
        └── ...
```

- **Images**: RGB, stored as `.jpg`, at native resolution (4608×2592 or 4608×3456).
- **Masks**: single-channel binary `.png`, same filename as the corresponding image. Black pixels (0) denote vegetation; white pixels (255) denote background.
- Each image in `images/` has a corresponding mask of the same name in `masks/`.

### Download

The dataset (images + masks) is hosted externally due to its size.

| Resource | Link |
|---|---|
| Images + Masks | **[Download from Google Drive](https://drive.google.com/file/d/1S5uFQTtHR_-c0h4gg8OTlTZRNrcvnjjs/view?usp=sharing)** |
| Dataset DOI | *[to be added upon publication]* |

---

## Model & Benchmark Preview

A lightweight segmentation model and benchmark suite accompany the dataset. Full model, training, and ablation details will be documented separately — the figures below give a preview.

<p align="center">
  <img src="https://github.com/USDA/MaizeSeg/blob/main/MaizeSeg/Assets/General_Framework.png?raw=true" width="850" alt="Encoder-decoder segmentation benchmarking framework"/>
</p>
<p align="center"><em>Encoder–decoder segmentation benchmarking framework used to evaluate encoder backbones and decoder attention variants on MaizeSeg.</em></p>

<p align="center">
  <img src="https://github.com/USDA/MaizeSeg/blob/main/MaizeSeg/Assets/MobileVIT_NoAttn.png?raw=true" width="850" alt="Selected MobileViT-S encoder with no-attention decoder architecture"/>
</p>
<p align="center"><em>Architecture of the selected MobileViT-S encoder paired with a lightweight no-attention decoder for vegetation-background segmentation.</em></p>

<p align="center">
  <img src="https://github.com/USDA/MaizeSeg/blob/main/MaizeSeg/Assets/Comparison.png?raw=true" width="850" alt="Comparison of classical vegetation-index methods and the segmentation model"/>
</p>
<p align="center"><em>Qualitative and quantitative comparison of classical vegetation-index methods against the learning-based segmentation model on representative MaizeSeg test images.</em></p>

<p align="center">
  <img src="https://github.com/USDA/MaizeSeg/blob/main/MaizeSeg/Assets/Qualitative_Results_Model.png?raw=true" width="850" alt="Qualitative segmentation results on MaizeSeg test images"/>
</p>
<p align="center"><em>Qualitative results of the selected model on representative MaizeSeg test images — input, ground truth, predicted mask, and extracted vegetation.</em></p>

<p align="center">
  <img src="https://github.com/USDA/MaizeSeg/blob/main/MaizeSeg/Assets/Thresholding_Bg_Fg.png?raw=true" width="850" alt="Effect of vegetation-background segmentation on HSV thresholding"/>
</p>
<p align="center"><em>Effect of vegetation-background segmentation on downstream HSV thresholding, comparing segmented versus non-segmented inputs.</em></p>

<p align="center">
  <img src="https://github.com/USDA/MaizeSeg/blob/main/MaizeSeg/Assets/Contamination.png?raw=true" width="850" alt="Effect of vegetation-background masking on stress-associated color detection"/>
</p>
<p align="center"><em>Effect of vegetation-background masking on stress-associated color detection — responses obtained directly from the original RGB image versus after restricting analysis to vegetation pixels.</em></p>
