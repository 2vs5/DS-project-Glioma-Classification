# Interpretable Glioma Classification via Cluster-Guided Multi-Omics Modelling

> A reproducible pipeline for **interpretable glioma subtype classification** on TCGA GBM/LGG using multi-omics integration with **MOFA+**, **consensus clustering**, supervised classification, and **SHAP** explanations.

---

## Table of Contents
- [Development Environment](#development-environment)
- [Usage](#usage)
  - [Run on Google Colab](#run-on-google-colab)
  - [Run Locally](#run-locally)
  - [R Markdown Execution Guide](#r-markdown-execution-guide)
- [Notes](#notes)

---

## Development Environment

### Python Notebooks (`.ipynb`)
- The project primarily uses **Google Colab** for accessibility and reproducibility.
- All `.ipynb` notebooks are **executable directly in the shared Colab environment**.
- Because Colab resets environments, each notebook includes its **own package-installation cells**.
- **No manual Python environment setup is required.**

### R Markdown (`.Rmd`)
- Some steps require **R** due to library limitations or data format compatibility.
- `.Rmd` files **cannot run on Colab**.
- Download and execute `.Rmd` files **locally** in an appropriate R environment.

---

## Usage

This repository supports two execution modes:

1) **Google Colab (recommended)** for Python notebooks — zero local setup, just run top-to-bottom.  
2) **Local** for both Python notebooks and all **R Markdown** (`.Rmd`) files.

---

### Run on Google Colab

1. Open any `.ipynb` in the repo — it will launch in **Google Colab**.
2. Run the first cell to install packages (each notebook includes its own setup).
3. Mount Google Drive to access the shared data:
   ```python
   from google.colab import drive
   drive.mount('/content/drive')
4. (If present) set your project root and verify paths:
   ```python
   PROJECT_ROOT = "/content/drive/MyDrive/DS_project"
   import os; assert os.path.exists(PROJECT_ROOT), "Update PROJECT_ROOT to your Drive path."
5. Runtime → Run all (top to bottom)

### Run Locally

1. Create an environment (example with conda):
   ```bash
   conda create -n glioma-multiomics python=3.10 -y
   conda activate glioma-multiomics
   pip install numpy pandas scikit-learn matplotlib shap lifelines mofapy2 jupyter

   
2. Launch Jupyter and open the notebook(s):
   ```python
     jupyter lab
3. Replace Colab paths with your local path (or set a variable at the top of the notebook):
   ```python
     PROJECT_ROOT = "/home/your_username/Ds_project"

## Notes

- MOFA+ input: Use probe-level methylation matrices (optionally high-variance filtered). Gene-level matrices are for downstream annotation.
- Missingness rules:
-   Samples: drop if any omics layer is missing, or if >50% NA in any modality.
-   Features: drop features with >50% NA within each layer.

- Clustering & classification:
  - Consensus clustering on Train; assign Val/Internal/External via centroid similarity.
  - Evaluate classifiers; explain with SHAP.
  - Reproducibility: keep folder names/paths consistent, run notebooks in order, and record seeds where applicable.
  - Performance gotcha: if Colab RAM runs low, reduce feature counts (variance threshold) or number of MOFA+ factors.
