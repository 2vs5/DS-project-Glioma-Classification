Interpretable Glioma Classification via Cluster-Guided Multi-Omics Modelling

1. Development Environment
For  Python notebook( .ipynb) file:
●	We performed the majority of this project using Google Colab for accessibility and ease of use. So, you do not need to download the .ipynb files.
●	All notebooks can be executed directly within the shared Google Colab environment.
●	Since Colab resets the environment every time it restarts, each notebook includes its own package installation cells. 
●	Therefore, you do not need to manually set up any Python environment.

For  R Markdown (.Rmd) files:
●	Certain steps in our workflow required the R programming due to library limitations or data format compatibility.
●	R Markdown (.Rmd) files cannot be executed on Colab.
●	These files must be downloaded and run locally in an appropriate R environment.

2. Folder Structure
2.1 Code folder 
●	0.1_Data_collection_GDC_download.Rmd
○	Omics data collection from the GDC portal.
○	Note: To reduce preprocessing time, omics data were also manually downloaded from the UCSC Xena browser. This code is kept for potential use of data from the latest version without being preprocessed from the Xena source.

●	0.2_Data_collection_GDC_clinical_info.Rmd
○	Clinical data collection from the GDC portal

●	0.3_R_code-clinical_data_convert_to_csv.ipynb
○	Converts clinical data downloaded from 0.2.Data_collection_TCGA_clinical_info.Rmd from *.Rmd format into .csv.

●	1_Data_preprocessing-gene_mapping.ipynb
○	Attempts to perform gene name mapping across each omics layer by converting probes to gene symbols.
○	Initially intended to unify feature names across modalities.
○	Note: MOFA+ requires probe-level data, so this step is no longer used in the pipeline. However, the notebook is retained for potential future gene-level analysis.

●	1.2_Data_preprocessing_snv.Rmd
○	Preprocess mutation files to a matrix of SNVs with rows representing gene-level identifiers and columns representing samples for both the  GBM and LGG datasets

●	2.1_RNA_seq_sample_filtering_by_plate.ipynb:RNA_seq.ipynb
○	Addresses plate duplication issues in RNA-seq data when multiple entries exist for a single sample.

●	2.2_filtering_sex_related_gene_and_protein_gene_name_mapping.ipynb
○	Filters out sex-related genes from all omics layers.
○	Additionally, for the proteomics data, it maps features from target names to corresponding gene names for consistency.

●	3.1_Intra-cohort_preprocessing_sample_filtering.ipynb
○	Sample alignment: TCGA sample identifiers differ slightly across omics layers. This script standardizes them using the shortest consistent format.
○	Remove incomplete cases: Samples lacking data in one or more omics layers are excluded.
○	Remove low-information samples: Samples with more than 50% missing values (NA) in any modality are removed, even if all modalities are present.

●	3.2_TCGA_preprocess_methylation.Rmd
○	Probe filtering for methylation data in both GBM and LGG datasets, including missing data handling, sex-related probe filtering, and SNP-related probe filtering.
○	Sample filtering of the GBM dataset to exclude data from the same samples from different probing platforms.
○	Integrate GBM methylation data and convert Beta values to M values.
 
●	4.1_Data_spliting.ipynb
○	Splits the dataset into training, validation, internal test, and external test sets.

●	4.2.1_Data_cleaning_high_NA_feature_filtering.ipynb
○	Removes features (e.g., genes, probes) that are missing in more than 50% of samples within each omics layer.

●	4.2.2_Data_cleaning_sex_disease_association_test.ipynb
○	Statistical tests for any association between the disease (LGG and GBM) and sex, using chi-square and Fisher’s exact tests.

●	4.2.3_Data_cleaning_Feature_selection_methylation.Rmd
○	Feature selection (DMP/DMR) between the GBM and LGG datasets.  
○	Feature mapping of probe-sample matrix to gene-sample matrix.
○	Note: DMP and DMR are not used as the input of the MOFA+ model due to the potential filtering of high-variance features, so this step is no longer used in the pipeline. After filtering the high-variance features of methylation data, the probe-sample matrices of GBM and LGG are used for the omics data integration. The gene-sample matrix format of methylation data is used for the annotation analysis.

●	4.3_batch_correction.ipynb
○	Performs batch effect correction for DNA methylation datasets (450k vs 27k platforms).

●	4.4_feature_selection.ipynb
○	Performs feature selection based on variance thresholding across samples.

●	5.1_Multi-Omics_data_integration.ipynb
○	Integrates all omics data using MOFA+ (Multi-Omics Factor Analysis), which captures latent factors shared across modalities.

●	5.2_Multi-Omics_integration_quality_assessment.Rmd
○	Assesses how much variance was explained across all omics layers in order to select a MOFA+ model with the optimal number of factors, and shows which omics layers contributed to which factors.

●	6_Clustering_kmeans.ipynb
○	Perform Consensus Clustering on the Training set and assign cluster label of validation/internal/external test dataset by trained consensus matrices centroid.
○	Visualization of  cluster assignment confidence

●	7-8_Classfication_and_model_evaluation_SHAP.ipynb
○	Contains code for clustering-based classification of glioma subtypes using the integrated multi-omics data.
○	SHAP explanation using classification models.

●	9.1.1_Cluster_Annotation.ipynb
○	Compare clusters against known variables. Statistical associations between clusters and variables are tested.

●	9.1.2_Cluster_annotation_factor_analysis.Rmd
○	Identifies discriminative factors across clusters and performs Gene Set Enrichment Analysis (GSEA) to biologically annotate each factor.

●	9.2_Survival_Analysis.ipynb
○	Kaplan-Meier survival analysis with log-rank tests

2.2 Data folder
●	0.reference
○	gencode.v36.annotation.gtf.gz
○	HM27.hg38.manifest.gencode.v36.tsv.gz
○	HM450.hg38.manifest.gencode.v36.tsv.gz

●	0.clinical_rdata_to_csv
○	Clinical data converted from .Rmd to .csv via `0.3.R_code-clinical_data_convert_to_csv.ipynb`

●	1_raw_data 
○	Contains GBM and LGG data downloaded from TCGA (GDC, Xena) along with clinical data from step 0.3

●	2_gene_name_mapping_data
○	Output from `1. Data_preprocessing-gene_mapping.ipynb` Mapping of probe IDs to gene names for each omics layer

●	3_RNA_seq_sample_filtering_by_plate
○	Results from: `2. RNA_seq_sample_filtering_by_plate.ipynb` and `2.2_filtering_sex_related_gene_and_protein_gene_name_mapping.ipynb`

●	4_Intra-cohort-preprocessing
○	Output of `3.1.Intra-cohort_preprocessing_Data_cleaning(sample_filtering).ipynb` Sample alignment, missing data filtering, and cohort cleaning

●	5_Inter-cohort_preprocessing
○	5.1.data_spliting: Results of `4.1.Data_spliting.ipynb`
○	5.2.data_cleaning(Feature_filtering): Output from `4.2.Data_cleaning(feature_filtering).ipynb`
○	5.3.1.batch_correction: Output from `4.3_batch_correction.ipynb`
○	5.3.2.probe_to_Ensembl: Output from `4.2.3_Data_cleaning_Feature_selection_methylation.Rmd`
○	5.4.feature_selection : Results of `4.4_feature_selection.ipynb`
○	5.5Multi-omics_integration: Output from `5.1_Multi-Omics_data_integration.ipynb`

●	6_Unsupervised_clustering
○	Output of `6_Clustering_kmeans.ipynb`
○	Kmeans_30mixed: clustering results using 30-factor latent space from MOFA+
○	…

●	7_classification
○	base_line: 100x bootstrap classification results using baseline features
○	mixed30: 100x bootstrap results using 30-factor latent space from MOFA+



●	9_Biological_Interpretation
○	9.1_Cluster_Annotation: Inputs for 9.1.1_Cluster_Annotation.ipynb and 9.1.2_Cluster_annotation_factor_analysis.Rmd
○	9.2_SHAP: result image of sharp implementation

3. Usage
When you click on any Python notebook (.ipynb) file in this repository, it will automatically open in Google Colab.
You can simply run all cells in the notebook from top to bottom.

●	Accessing Shared Data via Google Drive
Since the necessary data files are stored in a shared Google Drive folder, you must first mount your Drive within Colab.
At the beginning of each notebook, run the following code block to connect to your Google Drive:
from google.colab import drive
drive.mount('/content/drive')

●	Running Locally
If you prefer to run the notebooks on your local machine, simply replace all instances of the Colab path: /content/drive/MyDrive/ 
with the local path to your project directory, such as:
/home/your_username/Ds_project/
Make sure that all file paths are updated accordingly.

●	0.*_Data_collection_*.Rmd
○	0.1_Data_collection_GDC_download.Rmd
○	0.2_Data_collection_GDC_clinical_info.Rmd
○	Download .Rmd files and run the code locally.

●	1.2_Data_preprocessing_snv.Rmd
○	Download this .rmd file along with the following input files  from DS_project/Data/1_raw_data/:
■	TCGA-GBM.somaticmutation_wxs.tsv.gz
■	TCGA-LGG.somaticmutation_wxs.tsv.gz
○	Change the input path and run the code.

●	3.2_TCGA_preprocess_methylation.Rmd
○	Download this .rmd file along with the following reference files from DS_project/Data/0.reference/:
■	HM450.hg38.snp.tsv.gz
○	Download this .rmd file along with the following input files from DS_project/Data/1_raw_data/:
■	TCGA-GBM.methylation270.tsv.gz
■	TCGA-GBM.methylation450.filtered.tsv.gz
■	TCGA-LGG.methylation450.filtered.tsv.gz
○	Change the input path and run the code.

●	4.2.3_Data_cleaning_Feature_selection_methylation.Rmd
○	Download this .rmd file along with the following reference files from DS_project/Data/0.reference/:
■	gencode.v36.annotation.gtf.gz
■	HM27.hg38.manifest.gencode.v36.tsv.gz
○	Download this .rmd file along with the following input files from DS_project/Data/0.reference/5_Inter-cohort_preprocessing/5.3.1.batch_correction/:
■	*_methylation_450_27_batch_corrected_*.csv
■	*_methylation_450_27_batch_corrected_*.csv
○	Identify the outpath in .Rmd, the output of the gene-sample methylation matrix will be generated to the defined outpath.
○	Change the input path and run the code.

●	5.2_Multi-Omics_integration_quality_assessment.Rmd
○	Download this .rmd file along with the following input files:
■	DS_project/Data/5_Inter-cohort_preprocessing/5.5Multi-omics_integration/trained_model_factor_[5,10,15,20,25,26,27,28,29,30,31,32,33,34,35].hdf5
○	Create an input folder in the same directory as the .rmd file and move the .hdf5 files into it. 
○	Run the code.

●	9.1.2_Cluster_annotation_factor_analysis.Rmd
○	Download this .rmd file along with the following input files:
■	model
●	DS_project/Data/5_Inter-cohort_preprocessing/5.5Multi-omics_integration/trained_model_factor_30mixed.hdf5
■	clinical datasets
●	DS_project/Data/0.clinical_rdata_to_csv/TCGA-GBM-clinicalXML_patient.csv
●	DS_project/Data/0.clinical_rdata_to_csv/TCGA-LGG-clinicalXML_patient.csv
■	clustering results
●	DS_project/Data/6_Unsupervised_clustering/kmeans_30mixed/k_[2,3,4,5]/train_cluster_labels_consensus_complete.csv
■	gene ID mapping
●	DS_project/Data/2_gene_name_mapping_data/chromosome_probe_gene.csv
■	functional databases
●	DS_project/Data/9_Biological_Interpretation/9.1_Cluster_Annotation/MSigDB_v6.0_C2_human.RData
●	DS_project/Data/9_Biological_Interpretation/9.1_Cluster_Annotation/MSigDB_v6.0_C5_human.RData
○	Create an input folder in the same directory as the .rmd file.
○	Move the model file, clinical datasets and functional databases into the input folder
○	Move the clustering results into the input/kmeans_30mixed/k_[2,3,4,5] folders.
○	Move the gene ID mapping file into the input/gene_name_mapping folder
○	Run the code.
