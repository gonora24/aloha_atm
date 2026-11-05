# ALOHA ATM

Any-point Trajectory Modeling (ATM) for the bimanual **ALOHA** robot.

This repository adapts [ATM](https://github.com/Large-Trajectory-Model/ATM) (Wen et al., RSS 2024) to ALOHA data. It was developed as part of the practical course and seminar **"Deep Learning in Robotics"**.

> **Fork notice.** This repository is a fork of [`pascalsiekiera/aloha_atm`](https://github.com/pascalsiekiera/aloha_atm), my lab partner, which in turn builds on the original [ATM codebase](https://github.com/Large-Trajectory-Model/ATM). The core method and large parts of the code come from the original authors.

---

## Overview

ATM learns manipulation policies in two stages:

1. **Track transformer.** Predicts the future trajectories ("tracks") of arbitrary points in the image. It can be trained on videos without action labels.
2. **Track-guided policy.** Maps observations and predicted tracks to robot actions, trained on a small set of action-labeled demonstrations.

In this repository, both stages are applied to ALOHA demonstrations, including a two-handed variant for the bimanual setup.

## Changes from the original ATM

- **CoTracker3** instead of CoTracker2 for point tracking (CoTracker2 had loading issues).
- Preprocessing, dataset splitting, training and evaluation scripts for **ALOHA** (`scripts/*_aloha_*`).
- Separate training scripts for the **two-handed** setup (`*_twohanded.sh`, `*_two_handed.sh`).
- Utilities to render and merge evaluation videos.

## Repository structure

```
aloha_atm/
├── atm/                    # core ATM code (models, dataloaders, utils)
├── engine/                 # training and evaluation loops
├── conf/                   # configuration files
├── scripts/                # entry points (preprocessing, training, evaluation)
├── aloha/                  # ALOHA-specific files (e.g. task embedding caches)
├── libero/                 # original ATM LIBERO code - do not modify
├── libero_old/             # older LIBERO version, kept for reference
├── trained_models/         # trained checkpoints
├── outputs/                # training and evaluation outputs
├── output_videos_new/      # rendered evaluation videos
├── plots/                  # result plots
├── preprocess.sh
├── train_transformers.sh           # track transformer training
├── train_transformers_two_handed.sh
├── train_policy.sh                 # policy training
├── train_policy_twohanded.sh
├── environment.yml
└── requirements.txt
```

> **Important:** do not touch the `libero/` folder.

## Setup

```bash
git clone <this-repo-url> --recurse-submodules
cd aloha_atm

conda env create -f environment.yml
conda activate aloha_atm

python3 scripts/preprocess_aloha.py
```

## Usage

The full pipeline has five steps:

| Step | Script | Purpose |
|---|---|---|
| 1 | `scripts/preprocess_aloha.py` (or `preprocess.sh`) | Preprocess the raw ALOHA data and compute point tracks |
| 2 | `scripts/split_aloha_dataset.py` | Split the data into training and validation sets |
| 3 | `scripts/train_aloha_track_transformer.py` (or `train_transformers.sh`) | Train the track transformer |
| 4 | `scripts/train_aloha_policy.py` (or `train_policy.sh`) | Train the track-guided policy |
| 5 | `scripts/eval_policy_network.py` / `scripts/merge_videos.py` | Evaluate the policy and merge the rollout videos |

Shell wrappers for the two-handed setup:

```bash
bash train_transformers_two_handed.sh
bash train_policy_twohanded.sh
```

## Results

<video src='output_videos_new/two_handed_policy_5x.mp4' width=180/> | <video src='output_videos_new/one_handed_policy_5x.mp4' width=180/>

## Citation

If you use this code, please cite the original ATM paper and this repository:

```bibtex
@article{wen2023any,
  title   = {Any-point Trajectory Modeling for Policy Learning},
  author  = {Wen, Chuan and Lin, Xingyu and So, John and Chen, Kai and Dou, Qi and Gao, Yang and Abbeel, Pieter},
  journal = {arXiv preprint arXiv:2401.00025},
  year    = {2023}
}
```

## Acknowledgements

- [ATM](https://github.com/Large-Trajectory-Model/ATM) by Wen et al. - the codebase this work builds on
- [`pascalsiekiera/aloha_atm`](https://github.com/pascalsiekiera/aloha_atm) - the repository this fork is based on
- [CoTracker](https://github.com/facebookresearch/co-tracker) for point tracking
- [LIBERO](https://github.com/Lifelong-Robot-Learning/LIBERO) and [ALOHA](https://tonyzhaozh.github.io/aloha/)
