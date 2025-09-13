# Interpretable Glioma Classification via Cluster-Guided Multi-Omics Modelling

> A reproducible pipeline for **interpretable glioma subtype classification** on TCGA GBM/LGG using multi-omics integration with **MOFA+**, **consensus clustering**, supervised classification, and **SHAP** explanations.

---

## Table of Contents
- [Development Environment](#development-environment)
- [Folder Structure](#folder-structure)
  - [Code](#code)
  - [Data](#data)
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
├─ 0.1_Data_collection_GDC_download.Rmd
│  └─ Omics data collection from the GDC portal.
│     * Note: To reduce preprocessing time, omics data were also manually downloaded
│       from the UCSC Xena browser. This script is kept to fetch the latest data
│       directly from GDC if needed.
├─ 0.2_Data_collection_GDC_clinical_info.Rmd
│  └─ Clinical data collection from the GDC portal.
├─ 0.3_R_code-clinical_data_convert_to_csv.ipynb
│  └─ Converts clinical data from 0.2 (.Rmd output) into .csv.
├─ 1_Data_preprocessing-gene_mapping.ipynb
│  └─ Probe → gene symbol mapping across omics layers.
│     * Note: MOFA+ favors probe-level inputs; this is retained for potential
│       gene-level analyses.
├─ 1.2_Data_preprocessing_snv.Rmd
│  └─ Preprocess mutation files to a (gene × sample) SNV matrix for GBM and LGG.
├─ 2.1_RNA_seq_sample_filtering_by_plate.ipynb
│  └─ Resolves RNA-seq plate duplication issues.
├─ 2.2_filtering_sex_related_gene_and_protein_gene_name_mapping.ipynb
│  └─ Removes sex-related genes across omics; maps proteomics target names → gene names.
├─ 3.1_Intra-cohort_preprocessing_sample_filtering.ipynb
│  ├─ Standardizes sample IDs across layers.
│  ├─ Removes incomplete cases (missing any omics layer).
│  └─ Removes samples with >50% NA in any modality.
├─ 3.2_TCGA_preprocess_methylation.Rmd
│  ├─ Probe filtering (missing data, sex-related, SNP-related) for GBM/LGG.
│  ├─ Excludes GBM samples duplicated across platforms.
│  └─ Integrates GBM methylation and converts Beta → M values.
├─ 4.1_Data_spliting.ipynb
│  └─ Splits into train / validation / internal test / external test.
├─ 4.2.1_Data_cleaning_high_NA_feature_filtering.ipynb
│  └─ Removes features with >50% missingness within each omics layer.
├─ 4.2.2_Data_cleaning_sex_disease_association_test.ipynb
│  └─ Tests association between disease (LGG vs GBM) and sex (χ², Fisher).
├─ 4.2.3_Data_cleaning_Feature_selection_methylation.Rmd
│  ├─ DMP/DMR selection and probe→gene mapping (GBM vs LGG).
│  └─ Note: MOFA+ uses high-variance probe matrices; gene matrices are for annotation.
├─ 4.3_batch_correction.ipynb
│  └─ Batch effect correction for methylation (450k vs 27k).
├─ 4.4_feature_selection.ipynb
│  └─ Variance-based feature selection.
├─ 5.1_Multi-Omics_data_integration.ipynb
│  └─ Integrates omics with MOFA+ (latent factors).
├─ 5.2_Multi-Omics_integration_quality_assessment.Rmd
│  └─ Assesses explained variance and layer contributions; selects #factors.
├─ 6_Clustering_kmeans.ipynb
│  ├─ Consensus clustering on Train set.
│  └─ Assigns labels to validation/internal/external test via centroid;
│     visualizes assignment confidence.
├─ 7-8_Classfication_and_model_evaluation_SHAP.ipynb
│  ├─ Clustering-guided classification on integrated features.
│  └─ SHAP explanations.
├─ 9.1.1_Cluster_Annotation.ipynb
│  └─ Compares clusters to known variables; statistical testing.
├─ 9.1.2_Cluster_annotation_factor_analysis.Rmd
│  └─ Identifies discriminative factors and runs GSEA for biological annotation.
└─ 9.2_Survival_Analysis.ipynb
   └─ Kaplan–Meier survival analysis with log-rank tests.


### Data
Data/
├─ 0.reference/
│  ├─ gencode.v36.annotation.gtf.gz
│  ├─ HM27.hg38.manifest.gencode.v36.tsv.gz
│  └─ HM450.hg38.manifest.gencode.v36.tsv.gz
├─ 0.clinical_rdata_to_csv/
│  └─ Clinical .csv converted by 0.3 notebook.
├─ 1_raw_data/
│  └─ GBM/LGG raw omics (GDC, Xena) + clinical data.
├─ 2_gene_name_mapping_data/
│  └─ Output of 1_Data_preprocessing-gene_mapping.ipynb (probe→gene).
├─ 3_RNA_seq_sample_filtering_by_plate/
│  └─ Outputs from 2.1 and 2.2 notebooks.
├─ 4_Intra-cohort-preprocessing/
│  └─ Output of 3.1 notebook (alignment / missingness / cohort cleaning).
├─ 5_Inter-cohort_preprocessing/
│  ├─ 5.1.data_spliting/               # from 4.1
│  ├─ 5.2.data_cleaning/               # from 4.2
│  ├─ 5.3.1.batch_correction/          # from 4.3
│  ├─ 5.3.2.probe_to_Ensembl/          # from 4.2.3
│  ├─ 5.4.feature_selection/           # from 4.4
│  └─ 5.5Multi-omics_integration/      # from 5.1
├─ 6_Unsupervised_clustering/
│  ├─ kmeans_30mixed/   # clustering with MOFA+ 30-factor latent space
│  └─ …
├─ 7_classification/
│  ├─ base_line/        # 100× bootstrap with baseline features
│  └─ mixed30/          # 100× bootstrap with MOFA+ 30-factor features
└─ 9_Biological_Interpretation/
   ├─ 9.1_Cluster_Annotation/  # inputs for 9.1.1 / 9.1.2
   └─ 9.2_SHAP/                # SHAP result images
