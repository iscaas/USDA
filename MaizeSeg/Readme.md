# MaizeSeg

**An Under-Canopy Maize Image Dataset for Vegetation–Background Segmentation Enabling Downstream Stress Phenotyping**

[![License: CC BY-NC 4.0](https://img.shields.io/badge/Data%20License-CC--BY--NC--4.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)
[![License: MIT](https://img.shields.io/badge/Code%20License-MIT-blue.svg)](LICENSE)
[![Python 3.9+](https://img.shields.io/badge/python-3.9%2B-blue.svg)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?logo=pytorch&logoColor=white)](https://pytorch.org/)

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
  - [Annotation Protocol](#annotation-protocol)
  - [Download](#download)
  - [Folder Structure](#folder-structure)
- [Model](#model)
  - [Architecture](#architecture)
  - [Encoder Benchmark](#encoder-benchmark)
  - [Decoder Ablation](#decoder-ablation)
  - [Comparison with Classical Methods](#comparison-with-classical-methods)
- [Downstream Application: Stress-Associated Color Detection](#downstream-application-stress-associated-color-detection)
- [Repository Structure](#repository-structure)
- [Installation](#installation)
- [Quickstart](#quickstart)
- [Reproducing Paper Results](#reproducing-paper-results)
- [Pretrained Weights](#pretrained-weights)
- [Demo](#demo)
- [Limitations](#limitations)
- [Roadmap / Future Work](#roadmap--future-work)
- [Citation](#citation)
- [License](#license)
- [Acknowledgments](#acknowledgments)
- [Contact](#contact)

---

## Overview

**MaizeSeg** is a high-resolution, pixel-annotated RGB image dataset for **vegetation–background segmentation** in **under-canopy maize (*Zea mays L.*) field imagery**. It was built to support reliable nutrient-stress phenotyping under real field conditions — where lighting variability, occlusion, soil exposure, crop residue, and stress-induced necrotic discoloration make vegetation/background separation substantially harder than in above-canopy or lab-based datasets.

Alongside the dataset, this repository provides:

- A **lightweight encoder–decoder segmentation model** (MobileViT-S encoder + a no-attention decoder built from Feature Refinement Modules) that achieves **F1 = 0.9844 / IoU = 0.9692** on the MaizeSeg test set.
- Full **benchmarking code** for 5 encoder backbones × 4 decoder variants, plus classical color-index and HSV thresholding baselines.
- A **downstream experiment** quantifying background contamination when stress-associated color detection is applied without vegetation masking.

<p align="center">
  <img src="assets/qualitative_results.png" width="800" alt="Qualitative segmentation results on MaizeSeg test images"/>
</p>

---

## Why MaizeSeg?

Existing RGB datasets for maize phenotyping are largely collected **above-canopy** (aerial/UAV) or in **controlled lab settings** with clean, monochrome backgrounds. Neither setting reflects the conditions under which **mobile nutrient deficiencies** (e.g., nitrogen) first become visible — typically in the **lower canopy**, where:

- Illumination is highly non-uniform (shadows, partial occlusion by upper leaves)
- Senescent and necrotic tissue take on yellow/brown/low-chroma hues that visually resemble **soil and crop residue**
- Vegetation and background pixels are frequently confused by simple color-index or thresholding methods

MaizeSeg directly targets this gap with **2,974 pixel-annotated under-canopy images**, collected across a full range of nitrogen-treatment conditions and growth stages (V10–R4), to provide a realistic benchmark for vegetation segmentation *before* downstream stress-color analysis.

| | Above-canopy / UAV datasets | Lab / leaf-only datasets | **MaizeSeg** |
|---|---|---|---|
| Under-canopy viewpoint | ✗ | ✗ | ✅ |
| Field-realistic occlusion & lighting | Partial | ✗ | ✅ |
| Pixel-level vegetation masks | Rare | N/A | ✅ |
| Nutrient-stress visual variability | Rare | Rare | ✅ |
| Public availability | Varies | Varies | ✅ |

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
| Split | 70% train (2,082) / 10% val (297) / 20% test (595), fixed seed = 2021 |

### Collection Site and Nitrogen Treatments

Images were collected at the **Kansas State University North Farm Agronomy Education Center (AEC)**, on a ~1,875 ft² experimental plot of 12 maize rows — 6 rows nitrogen-deficient (no applied fertilizer, 0 lb N/ac) and 6 rows nitrogen-sufficient (200 lb N/ac applied pre-planting). Nitrogen variability was used specifically to introduce realistic visual stress complexity (chlorosis, senescence, necrotic patterning) rather than as a standalone classification target — **MaizeSeg is a segmentation dataset, not a nitrogen-classification dataset.**

### Annotation Protocol

- Manual pixel-level annotation performed with **LabelMe**.
- All maize tissue (green, chlorotic, senescent, necrotic) → **vegetation/foreground**; soil, sky, residue, machinery → **background**.
- HSV thresholding used only as an *auxiliary* refinement aid for unambiguous regions (e.g., sky); never used to resolve ambiguous vegetation/soil boundaries.
- All masks visually inspected at full resolution; ambiguous boundaries manually corrected.

### Download

The dataset (images + masks) is hosted externally due to its size. Metadata, licensing, and a full datasheet are included.

```bash
# Download and extract MaizeSeg
bash data/download.sh
```

| Resource | Link |
|---|---|
| Images + Masks | *[Zenodo / Hugging Face link — to be added]* |
| Dataset DOI | *[to be added upon publication]* |
| Datasheet | [`data/DATASHEET.md`](data/DATASHEET.md) |

### Folder Structure

```
data/
├── images/
│   ├── train/
│   ├── val/
│   └── test/
├── masks/
│   ├── train/
│   ├── val/
│   └── test/
└── split_seed2021.json     # exact train/val/test image IDs used in the paper
```

---

## Model

### Architecture

The segmentation model follows a standard **encoder–decoder** design with **deep supervision**:

- **Encoder**: extracts multi-scale hierarchical features `{x1, ..., x5}`.
- **Decoder**: progressive top-down fusion via bilinear upsampling + lateral skip connections + **Feature Refinement Modules (FRMs)** (1×1 Conv-BN-ReLU → 3×3 Conv-BN-ReLU).
- **Deep supervision**: auxiliary prediction heads at `P3`, `P2`, plus the final head `P1` used at inference.
- **Loss**: weighted BCE + weighted IoU (all supervised outputs) + auxiliary Dice loss (on `P1` only).

Five encoders and four decoder attention configurations were benchmarked; see below.

<p align="center">
  <img src="assets/architecture.png" width="800" alt="Selected MobileViT-S + no-attention decoder architecture"/>
</p>

### Encoder Benchmark

All encoders were paired with the same full channel-spatial attention decoder for a fair comparison.

| Encoder | Params (M) | G-Ops | F1-score | IoU | Accuracy |
|---|---|---|---|---|---|
| ResNet18 | 12.22 | 11.96 | 0.9798 | 0.9604 | 0.9654 |
| ResNet34 | 22.33 | 21.65 | 0.9803 | 0.9614 | 0.9662 |
| EfficientNetV2-S | 20.23 | 15.01 | 0.9810 | 0.9628 | 0.9675 |
| ConvNeXt-Tiny | 30.17 | 28.74 | 0.9823 | 0.9651 | 0.9695 |
| **MobileViT-S** | **5.35** | **9.61** | **0.9842** | **0.9689** | **0.9729** |

**MobileViT-S** achieved the best segmentation performance *and* the lowest computational cost, and was fixed as the encoder for the decoder ablation.

### Decoder Ablation

All decoders share the same MobileViT-S encoder.

| Decoder | Params (M) | G-Ops | F1-score | IoU | MAE |
|---|---|---|---|---|---|
| Full Channel–Spatial Attention | 5.3529 | 9.6088 | 0.9843 | 0.9690 | 0.0270 |
| Channel-Only Attention | 5.3525 | 9.6003 | 0.9843 | 0.9692 | 0.0269 |
| Spatial-Only Attention | 5.3491 | 9.6088 | 0.9843 | 0.9691 | 0.0270 |
| **No Attention (selected)** | **5.3487** | **9.6003** | **0.9844** | **0.9692** | **0.0269** |

The no-attention decoder matches or slightly exceeds the attention-based variants while using the fewest parameters/operations — selected as the final configuration for its accuracy–efficiency trade-off.

### Comparison with Classical Methods

| Method | Precision | Recall | F1-score | IoU |
|---|---|---|---|---|
| NGRDI | — | — | 0.7122 | 0.5531 |
| Best HSV thresholding | 0.9570 | 0.8240 | 0.8855 | 0.7946 |
| **MobileViT-S + No-Attention (ours)** | **0.9879** | **0.9809** | **0.9844** | **0.9692** |

---

## Downstream Application: Stress-Associated Color Detection

To demonstrate why vegetation masking matters, three color-based stress-response methods (HSV yellow-orange-brown thresholding, ExR high response, RGD high response) were applied **directly to unmasked RGB images**, and the fraction of detected pixels falling outside the vegetation mask was measured.

| Method | Background Contamination (%) |
|---|---|
| HSV (Yellow–Orange–Brown) | 13.71 |
| ExR high response | 22.46 |
| RGD high response | 25.22 |

Without vegetation masking, up to **~1 in 4** detected "stress-associated" pixels can actually originate from soil, residue, or shadow — underscoring segmentation as a necessary preprocessing step, not just a standalone task.

---

## Repository Structure

```
MaizeSeg/
├── README.md
├── LICENSE
├── CITATION.cff
├── environment.yml
├── requirements.txt
├── data/
│   ├── DATASHEET.md
│   ├── download.sh
│   └── split_seed2021.json
├── models/
│   ├── encoders/            # resnet.py, efficientnetv2.py, convnext.py, mobilevit.py
│   ├── decoders/            # full_attention.py, channel_only.py, spatial_only.py, no_attention.py
│   └── build_model.py
├── baselines/
│   ├── vegetation_indices.py    # ExG, ExGR, NGRDI, GLI, VARI, RGBVI, CIVE, MExG
│   └── hsv_threshold.py
├── training/
│   ├── train.py
│   ├── losses.py
│   └── configs/
│       ├── mobilevit_no_attention.yaml
│       ├── mobilevit_full_attention.yaml
│       └── ...
├── evaluation/
│   ├── evaluate.py
│   └── benchmark_all.py
├── downstream/
│   └── stress_color_response.py
├── scripts/
│   ├── inference.py
│   └── mask_to_vegetation.py
├── notebooks/
│   ├── quickstart.ipynb
│   └── qualitative_results.ipynb
├── checkpoints/
│   └── README.md
├── demo/
│   └── app.py                # Gradio/Streamlit demo
└── assets/
    └── (figures used in this README)
```

---

## Installation

```bash
git clone https://github.com/your-org/MaizeSeg.git
cd MaizeSeg

# Option 1: conda
conda env create -f environment.yml
conda activate maizeseg

# Option 2: pip
pip install -r requirements.txt
```

**Requirements**: Python ≥ 3.9, PyTorch ≥ 2.0, CUDA-capable GPU recommended for training (inference runs fine on CPU).

---

## Quickstart

```python
from models.build_model import build_model
from scripts.inference import predict_mask
from PIL import Image

# Load the selected MobileViT-S + no-attention model
model = build_model(encoder="mobilevit_s", decoder="no_attention",
                     checkpoint="checkpoints/mobilevit_s_no_attention.pt")

image = Image.open("data/images/test/example.jpg").convert("RGB")
mask = predict_mask(model, image)          # binary vegetation mask, 1=vegetation
mask.save("example_mask.png")
```

Or from the command line:

```bash
python scripts/inference.py \
    --image path/to/image.jpg \
    --encoder mobilevit_s \
    --decoder no_attention \
    --checkpoint checkpoints/mobilevit_s_no_attention.pt \
    --output out_mask.png
```

---

## Reproducing Paper Results

```bash
# Train a specific encoder/decoder configuration
python training/train.py --config training/configs/mobilevit_no_attention.yaml

# Reproduce the full encoder benchmark (Table II) and decoder ablation (Table III)
python evaluation/benchmark_all.py --split test

# Reproduce the classical baseline comparison (vegetation indices + HSV)
python baselines/hsv_threshold.py --split test
python baselines/vegetation_indices.py --split test

# Reproduce the downstream background-contamination experiment (Table V)
python downstream/stress_color_response.py --split test
```

All training configs mirror the paper's protocol: Adam optimizer, initial LR = 1e-4 with polynomial decay (power=0.9), batch size 16, 80 epochs, gradient clipping to [-0.5, 0.5], structure-aware loss (weighted BCE + weighted IoU + auxiliary Dice on the finest prediction head).

---

## Pretrained Weights

| Encoder | Decoder | F1-score | IoU | Params (M) | Download |
|---|---|---|---|---|---|
| MobileViT-S | No Attention (**selected**) | 0.9844 | 0.9692 | 5.35 | *[link]* |
| MobileViT-S | Channel-Only Attention | 0.9843 | 0.9692 | 5.35 | *[link]* |
| MobileViT-S | Full Channel-Spatial Attention | 0.9843 | 0.9690 | 5.35 | *[link]* |
| ConvNeXt-Tiny | Full Channel-Spatial Attention | 0.9823 | 0.9651 | 30.17 | *[link]* |

---

## Demo

A lightweight interactive demo is included under [`demo/`](demo/) — upload an under-canopy maize image and view the predicted vegetation mask and extracted vegetation overlay in the browser.

```bash
cd demo
python app.py
```

---

## Limitations

- Collected at a **single experimental field site during one growing season**, using a fixed camera configuration. Reported metrics reflect performance on the **MaizeSeg data distribution**; generalization to other fields, seasons, hybrids, or camera systems has **not** been evaluated in this release.
- Provides **binary** vegetation/background labels only — it does not include pixel-level chlorosis/senescence/necrosis classification.
- Manual annotation, while expert-performed, may contain minor inconsistencies near ambiguous leaf–soil boundaries, motion blur, or heavy shadow.

See the full discussion in the paper's Limitations and Future Work section.

---

## Roadmap / Future Work

- [ ] Multi-season, multi-site data collection
- [ ] Row-level / field-level holdout splits for generalization evaluation
- [ ] Pixel-level chlorosis/senescence/necrosis annotations
- [ ] Domain adaptation / self-supervised pretraining baselines
- [ ] Integration with under-canopy robotic navigation (traversability estimation from vegetation masks)

Track progress and open discussions in [Issues](https://github.com/your-org/MaizeSeg/issues).

---

## Citation

If you use MaizeSeg in your research, please cite:

```bibtex
@article{abbas2026maizeseg,
  title   = {MaizeSeg: An Under-Canopy Maize Image Dataset for Vegetation Background Segmentation Enabling Downstream Stress Phenotyping},
  author  = {Abbas, Sardar Ali and Khan, Abbas and Munir, Arslan},
  journal = {IEEE Access},
  year    = {2026},
  note    = {Under review}
}
```

A `CITATION.cff` file is also included for GitHub's built-in "Cite this repository" feature.

---

## License

- **Code** in this repository is released under the [MIT License](LICENSE).
- **Dataset** (images + annotations) is released under **[CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)** — free for non-commercial research use with attribution. *(Confirm and update this to match your actual dataset license before publishing.)*

---

## Acknowledgments

This work was supported by the **United States Department of Agriculture (USDA) National Institute of Food and Agriculture (NIFA)** under Award Number **2023-67021-44838**. Any opinions, findings, and conclusions or recommendations expressed in this material are those of the authors and do not necessarily reflect the views of the USDA NIFA.

Data collected at the **Kansas State University North Farm Agronomy Education Center**.

---

## Contact

For questions, issues, or collaboration inquiries, please open a [GitHub Issue](https://github.com/your-org/MaizeSeg/issues) or contact the corresponding author:

**Arslan Munir** — arslanm@fau.edu
Department of Electrical Engineering and Computer Science, Florida Atlantic University, Boca Raton, FL, USA
