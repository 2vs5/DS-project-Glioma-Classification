# Interpretable Glioma Classification via Cluster-Guided Multi-Omics Modelling

> A reproducible pipeline for **interpretable glioma subtype classification** on TCGA GBM/LGG using multi-omics integration with **MOFA+**, **consensus clustering**, supervised classification, and **SHAP** explanations.

---

## Table of Contents
- [Development Environment](#development-environment)
- [Folder Structure](#folder-structure)
  - [Code](#code)
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

## Folder Structure

### Code
Code/
|-- 0.1_Data_collection_GDC_download.Rmd
|   `-- Omics data collection from the GDC portal. (Xena manual download note)
|-- 0.2_Data_collection_GDC_clinical_info.Rmd
|   `-- Clinical data collection from the GDC portal.
|-- 0.3_R_code-clinical_data_convert_to_csv.ipynb
|   `-- Converts 0.2 output (.Rmd) to .csv.
|-- 1_Data_preprocessing-gene_mapping.ipynb
|   `-- Probe -> gene symbol mapping (kept for gene-level analysis; MOFA+ uses probe-level).
|-- 1.2_Data_preprocessing_snv.Rmd
|   `-- Preprocess to (gene x sample) SNV matrix for GBM/LGG.
|-- 2.1_RNA_seq_sample_filtering_by_plate.ipynb
|   `-- Resolve RNA-seq plate duplication.
|-- 2.2_filtering_sex_related_gene_and_protein_gene_name_mapping.ipynb
|   `-- Remove sex-related genes; proteomics target -> gene mapping.
|-- 3.1_Intra-cohort_preprocessing_sample_filtering.ipynb
|   |-- Standardize sample IDs across layers.
|   |-- Remove incomplete cases.
|   `-- Drop samples with >50% NA in any modality.
|-- 3.2_TCGA_preprocess_methylation.Rmd
|   |-- Probe filtering (missing/sex/SNP) for GBM/LGG.
|   |-- Exclude duplicated GBM platform samples.
|   `-- Integrate GBM methylation; Beta -> M values.
|-- 4.1_Data_spliting.ipynb
|   `-- Split: train / val / internal test / external test.
|-- 4.2.1_Data_cleaning_high_NA_feature_filtering.ipynb
|   `-- Drop features with >50% NA per layer.
|-- 4.2.2_Data_cleaning_sex_disease_association_test.ipynb
|   `-- Association tests (LGG vs GBM) x (sex): chi-square, Fisher.
|-- 4.2.3_Data_cleaning_Feature_selection_methylation.Rmd
|   |-- DMP/DMR selection; probe -> gene mapping (annotation use).
|   `-- Note: MOFA+ uses high-variance probe matrices as input.
|-- 4.3_batch_correction.ipynb
|   `-- Batch correction for methylation (450k vs 27k).
|-- 4.4_feature_selection.ipynb
|   `-- Variance-based feature selection.
|-- 5.1_Multi-Omics_data_integration.ipynb
|   `-- MOFA+ integration (latent factors).
|-- 5.2_Multi-Omics_integration_quality_assessment.Rmd
|   `-- Explained variance & layer contributions; choose #factors.
|-- 6_Clustering_kmeans.ipynb
|   |-- Consensus clustering on Train.
|   `-- Assign labels to Val/Internal/External via centroid; visualize confidence.
|-- 7-8_Classfication_and_model_evaluation_SHAP.ipynb
|   |-- Classification on integrated features.
|   `-- SHAP explanations.
|-- 9.1.1_Cluster_Annotation.ipynb
|   `-- Compare clusters to known variables; statistical tests.
|-- 9.1.2_Cluster_annotation_factor_analysis.Rmd
|   `-- Discriminative factors + GSEA annotation.
`-- 9.2_Survival_Analysis.ipynb
    `-- Kaplan–Meier survival analysis with log-rank tests.

   ├─ 9.1_Cluster_Annotation/  # inputs for 9.1.1 / 9.1.2
   └─ 9.2_SHAP/                # SHAP result images
