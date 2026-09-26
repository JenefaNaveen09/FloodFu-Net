# FloodFu-Net

Code accompanying **“Learning Complementary Radar and Optical Representations for Satellite-Based Flood Inundation Mapping.”** FloodFu-Net segments flooded areas in paired Sentinel-1 SAR and Sentinel-2 optical imagery using separate sensor encoders, three cross-sensor fusion gates, and a U-Net decoder.

## Overview

The model takes a four-channel SAR tensor and a six-channel optical tensor as separate inputs:

| Input | Channels |
| --- | --- |
| Sentinel-1 SAR | VV, VH, VV − VH, VV + VH |
| Sentinel-2 optical | NDWI, MNDWI, AWEI, red (B4), NIR (B8), SWIR (B11) |

Each branch encodes its inputs at three feature levels with widths 32, 64, and 128. CrossGate modules combine the corresponding SAR and optical features. A U-Net decoder produces a single-channel flood logit map; sigmoid probabilities are thresholded at 0.5 for binary predictions. Training excludes pixels with unknown annotations from the binary cross-entropy and Dice losses.

## Dataset

The study uses the **hand-labeled Sen1Floods11** dataset, available at [the Sen1Floods11 data bucket](https://storage.googleapis.com/sen1floods11/v1.1/). Dataset images and labels are distributed by their original providers and are not included here.

| Setting | Value reported in the manuscript |
| --- | ---: |
| Source chip size | 512 × 512 pixels |
| Patch size and stride | 128 × 128 pixels; stride 128 |
| Minimum valid-pixel fraction | 0.30 |
| Downloaded train / validation / test chips | 300 / 60 / 60 |
| Usable train / validation / test patches | 3,711 / 848 / 858 |

Labels are `0` for dry, `1` for flood, and `-1` for unknown. The patch counts above refer to the particular subset evaluated in the manuscript, not the complete Sen1Floods11 dataset.

## Training configuration

The reported implementation uses PyTorch 2.x and CUDA. FloodFu-Net was trained with AdamW for 30 epochs, batch size 16, initial learning rate `1e-3`, weight decay `1e-4`, and a `CosineAnnealingLR` schedule with `T_max=30`. Masked binary cross-entropy and Dice loss were weighted equally. No data augmentation was reported.

## Reported results

The following numbers are from the manuscript's evaluation on **858 test patches**:

| Method | Flood IoU | F1-score |
| --- | ---: | ---: |
| FloodFu-Net | **0.786** | **0.880** |
| U-Net (optical only) | 0.777 | 0.874 |
| U-Net (early fusion) | 0.761 | 0.864 |

The FloodFu-Net configuration has approximately **0.844 million parameters**. The U-Net baselines in the study were trained for 15 epochs, so the comparison also reflects different training durations. These values describe the evaluated subset and are not a claim of performance on independently held-out flood events.

## Using this repository

Consult the uploaded source files for their actual entry points, dependencies, input paths, and checkpoint format. This README does not prescribe installation or execution commands because those depend on the files included in the repository. Obtain Sen1Floods11 from the dataset provider and retain the original train, validation, and test separation when reproducing the reported evaluation.

## Citation

If you use this work, cite the manuscript **“Learning Complementary Radar and Optical Representations for Satellite-Based Flood Inundation Mapping”** by Jenefa Archpaul, Vidhya K, Sheeba Merlin, B. S. Revathi, Antony Taurshia, and T. M. Thiyagu. Add the journal citation and DOI here once they are available.

## Contact

For questions about the study, contact the corresponding author, Jenefa Archpaul, at `jenefaa@karunya.edu`.
