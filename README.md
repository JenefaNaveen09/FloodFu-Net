# FloodFu-Net

FloodFu-Net is a deep learning model for flood extent segmentation using paired Sentinel-1 SAR and Sentinel-2 optical imagery. This repository contains the Jupyter notebook accompanying the manuscript *Learning Complementary Radar and Optical Representations for Satellite-Based Flood Inundation Mapping*.

## Method

FloodFu-Net processes radar and optical data in separate encoders. Three CrossGate modules combine features from the two sensors, and a U-Net decoder produces a pixel-wise flood probability map.

The model uses four SAR channels (VV, VH, VV − VH, and VV + VH) and six optical channels (NDWI, MNDWI, AWEI, red, near-infrared, and shortwave-infrared).

## Dataset

The notebook uses the hand-labeled [Sen1Floods11 dataset](https://storage.googleapis.com/sen1floods11/v1.1/). It downloads paired Sentinel-1 images, Sentinel-2 images, and flood labels from the public dataset bucket.

The experiment uses 128 × 128 patches with a stride of 128 pixels. Patches must contain at least 30% valid labeled pixels. The notebook is configured to download up to 300 training, 60 validation, and 60 test chips.

## Run the notebook

1. Open `FloodFuNet_Flood.ipynb` in Google Colab.
2. Select a GPU runtime if available.
3. Run the cells in order. The notebook mounts Google Drive and downloads the selected dataset files.
4. Continue through the training, evaluation, baseline comparison, and visualization stages.

In Colab, the notebook saves data, checkpoints, and figures to `/content/drive/MyDrive/FloodFuNet/`. Completed stages save files that the notebook can reuse after a disconnect.

## Training configuration

- Framework: PyTorch
- Optimizer: AdamW
- Epochs: 30
- Batch size: 16
- Initial learning rate: `1e-3`
- Weight decay: `1e-4`
- Scheduler: CosineAnnealingLR
- Loss: Masked binary cross-entropy and Dice loss

## Reported results

The manuscript reports the following results on 858 test patches:

| Method | Flood IoU | F1-score |
| --- | ---: | ---: |
| FloodFu-Net | **0.786** | **0.880** |
| U-Net (optical only) | 0.777 | 0.874 |
| U-Net (early fusion) | 0.761 | 0.864 |

The U-Net baselines were trained for 15 epochs, compared with 30 epochs for FloodFu-Net. The results therefore reflect both model and training-duration differences.

## Citation

If you use this work, please cite:

Jenefa Archpaul, Vidhya K, Sheeba Merlin, B. S. Revathi, Antony Taurshia, and T. M. Thiyagu. *Learning Complementary Radar and Optical Representations for Satellite-Based Flood Inundation Mapping*. Manuscript.

## Contact

Jenefa Archpaul — jenefaa@karunya.edu
