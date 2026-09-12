# ROP screening verification artifacts

Split tables, trained leave-one-dataset-out (LODO) checkpoints, and SHA256 checksums for:

> A reproducible multi-center, cross-device deep learning model for referral-warranted retinopathy of prematurity screening trained entirely on publicly available data.

Analysis code lives in a separate repository: https://github.com/Cybing521/rop-screening-model

This repository does **not** re-host fundus photographs.

The twelve LODO checkpoints (`best.pth`, ~17 MB each) are GitHub Release assets, not git objects:

https://github.com/Cybing521/rop-screening-artifacts/releases/tag/lodo-v2

After cloning this repository, download the weights into `weights/`:

```bash
gh release download lodo-v2 --dir weights --pattern "*.pth"
shasum -a 256 -c checksums/SHA256SUMS.txt
```

## Contents

| Path | Role |
|---|---|
| `weights/holdout_{D1,D2,D3,D4}_s{42,7,2024}_best.pth` | Twelve LODO checkpoints (`best.pth` by validation AUC; EfficientNet-B0) |
| `splits/splits_d1.csv` | D1 patient-level 70/10/20 split |
| `splits/cv_folds_d1.json` | D1 patient-level 5-fold assignment |
| `splits/df_all.csv` | Image-level labels and relative paths used in training and evaluation |
| `checksums/SHA256SUMS.txt` | SHA256 of every file under `weights/` and `splits/` |

Verify after clone:

```bash
shasum -a 256 -c checksums/SHA256SUMS.txt
```

Each checkpoint is about 17 MB (under GitHub's 100 MB file limit). Total weights are about 202 MB.

## What is not here

- Raw fundus JPEGs from any of the four sources.
- **FARFUM-RoP (D3)** pixels. That collection is CC BY-NC-ND 4.0. The manuscript cites the original record and does not redistribute derived image data. Do not add D3 images to this repository.
- Nested-validation locked-threshold checkpoints. Those runs, if completed, are a separate analysis and are not a replacement for Table 3 AUCs.

## How to obtain the images

Download from the original hosts, then place files so that `splits/df_all.csv` `image_path` values resolve. Confirm identity against the source publications rather than against a copy of the pixels from this repository.

| ID | Dataset | Licence | Access |
|---|---|---|---|
| D1 | Ostrava (Timkovi? et al., Sci Data 2024) | CC0 | Kaggle `jananowakova/retinal-image-dataset-of-infants-and-rop` |
| D2 | Shenzhen (Zhao et al., Sci Data 2024) | CC BY 4.0 | figshare 25514449 |
| D3 | FARFUM-RoP (Akbari et al., Sci Data 2024) | CC BY-NC-ND 4.0 | figshare; research use only, no redistribution |
| D4 | ROP-VL (Chen et al., 2025) | CC BY 4.0 | figshare [10.6084/m9.figshare.30143461](https://doi.org/10.6084/m9.figshare.30143461) |

Rebuild labels with the analysis-code scripts (`build_manifest.py`, `make_splits.py`, `gen_cv_folds.py`) and confirm they match the committed files in `splits/`.

## Mapping to the paper

Hold-out `H` in `{D1,D2,D3,D4}` and seed `S` in `{42, 7, 2024}` correspond to `weights/holdout_{H}_s{S}_best.pth`. These are the Table 3 LODO runs. Primary reported metrics remain image-level AUC; do not treat a 95%-sensitivity point computed on the evaluation set as a locked clinical cut-off.

## Licence

MIT for the files in this repository, subject to the original dataset licences for any images you download yourself. See `LICENSE`.
