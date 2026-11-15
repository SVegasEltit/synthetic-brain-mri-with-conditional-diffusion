# Data

## Dataset

This project uses the **BRISC2025** dataset, consisting of brain magnetic resonance imaging (MRI) scans and their corresponding tumor segmentation masks.

The dataset includes three tumor categories:

- **Glioma (`gl`)**
- **Meningioma (`me`)**
- **Pituitary tumor (`pi`)**

Images are also classified according to three anatomical planes: axial (`ax`), coronal (`co`), and sagittal (`sa`).

Only the segmentation component of BRISC2025 is used in this project.

**Original publication:** Fateh, A., Rezvani, Y., Moayedi, S., et al. (2026). *BRISC: Annotated Dataset for Brain Tumor Segmentation and Classification*. **Scientific Data, 13**, 361. [https://doi.org/10.1038/s41597-026-06753-y](https://doi.org/10.1038/s41597-026-06753-y)

**Official dataset:** [BRISC2025 on Kaggle](https://www.kaggle.com/datasets/briscdataset/brisc2025/).

## Data availability

The original BRISC2025 dataset is **not included in this GitHub repository**.

To reproduce the experiments, the dataset must be obtained separately and placed in the directory structure described below.

This separation keeps the repository focused on the implementation, experimental procedures, and results rather than redistributing the original dataset.

## Directory structure

The expected local organization is:

```text
data/
├── README.md
├── BRISC2025_original/
│   └── segmentation_task/
│       ├── train/
│       │   ├── images/
│       │   └── masks/
│       └── test/
│           ├── images/
│           └── masks/
└── BRISC2025/
    └── segmentation_task/
        ├── train/
        │   ├── images/
        │   └── masks/
        └── test/
            ├── images/
            └── masks/
```

The `BRISC2025_original/` directory contains the unmodified source data, whereas `BRISC2025/` contains the cleaned dataset used in the experiments.

Each MRI image must have a corresponding segmentation mask, matched by its file identifier.

Both dataset directories are excluded from version control.

## Duplicate detection and removal

Before model development, the segmentation dataset was examined to identify duplicate observations and reduce potential data leakage between training and evaluation partitions.

The procedure is implemented in `notebooks/OE0/OE0_depuracion_duplicados_BRISC.ipynb` and consists of the following stages:

**1. Exact duplicate detection.** Images are compared using SHA-256 hashes of both the original files and their decoded pixel arrays. This identifies identical observations even when their file representations differ.

**2. Perceptual duplicate screening.** Potential near-duplicates are identified using perceptual hashing (pHash) and the Structural Similarity Index Measure (SSIM). These candidates require visual confirmation before being considered for removal.

**3. Annotation consistency assessment.** Duplicate groups are evaluated using three hierarchical rules:

- **R1 — Label consistency:** groups containing conflicting tumor-type or anatomical-plane labels are excluded entirely.
- **R2 — Mask agreement:** groups with a minimum pairwise Dice coefficient below 0.80 are excluded because of inconsistent tumor annotations.
- **R3 — Representative selection:** otherwise, a single observation is retained and redundant copies are removed.

When selecting a representative observation, priority is given to the original test partition over the training partition. Within the same partition, the observation with the larger annotated tumor area is preferred.

These rules operate on complete image–mask pairs, ensuring that the correspondence between images and annotations is preserved.

The procedure generates auditable records of duplicate groups, exclusion decisions, mask comparisons, and dataset composition before and after cleaning. These are stored under `results/OE0/depuracion_duplicados/`.

By default, perceptually similar pairs are screened but **not automatically removed**: the notebook uses `APLICAR_PERCEPTUALES_CONFIRMADOS = False`. Any removal of perceptual duplicates must be confirmed using the execution records.

The original dataset remains unchanged. Duplicate removal is intended to reduce repeated observations and cross-partition contamination; it does not establish patient-level independence.

## Experimental partitions

The cleaned dataset is organized into the following experimental partitions:

| Partition  | Number of image–mask pairs |
| ---------- | --------------------------:|
| Training   | 3,792                      |
| Validation | 420                        |
| Test       | 420                        |
| **Total**  | **4,632**                  |

The training partition is used for model fitting, the validation partition supports model selection, and the final test partition is reserved for evaluation.

To ensure reproducibility, the experimental partition assignments must be preserved rather than generating new random splits.

# 

## Reproducibility

To reproduce the experiments:

1. Obtain the original BRISC2025 dataset from its authorized distribution source.
2. Place the original segmentation data in `data/BRISC2025_original/`.
3. Execute the duplicate-detection and cleaning procedure documented in `notebooks/OE0/`.
4. Preserve the experimental partition assignments.
5. Execute the notebooks corresponding to OE1–OE4 using the cleaned dataset in `data/BRISC2025/`.

The original dataset is not redistributed. The repository provides the code, experimental configurations, processing procedures, and evaluation results necessary to document the methodology.
