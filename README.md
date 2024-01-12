This set of scripts performs (1) an integrative assessment of RBP similarities, and (2) an RBP functional annotation from BioID data.
# 1. Dataset integration for estimating functional RBP similarities
The data integration procedure is handled by **Datasets_integration.R**. The script normalizes signed p-values derived from the analysis of the BioID, eCLIP, and Perturb-seq experiments using Z-scoring followed by cosine distances estimation. Next, the calculation of the empirical p-values using sorted RBP-RBP distances and p-values aggregation are performed to produce the integrated pairwise distances.

In more detail, the functional similarity of RBPs is estimated by joint analysis of eCLIP, BioID, and Perturb-seq data. First, using signed p-values, Z-scores are calculated for every row across 50 and 68 RBPs separately for BioID (for 2936 preys) and Perturb-seq (for 10 latent vectors), respectively. For eCLIP, the Z-scoring step is skipped. 
Next, cosine distance is computed for all RBP pairs (1225 for BioID, 7140 for eCLIP and 2278 for Perturb-seq) followed by ranking and calculation of empirical p-values defined as a fraction of RBP pairs with the cosine distance less than the score of the tested pair. The resulting empirical p-values are aggregated with the logitp function of metap. Raw (non-aggregated) p-values are used for the RBP pairs assessed in a single type of experiment. Finally, global integration heatmap is generated using cosine distance (1 - cosine similarity) and Ward’s clusterization.

The three source files containing signed p-values obtained from the experimental data (signed_log_pv_BioID.tsv, signed_log_pv_eCLIP.tsv, signed_log_pv_Perturbseq.tsv) are available in the 'data' subfolder.
The file contents are as follows:
1. **signed_log_pv_BioID.tsv** contains -log<sub>10</sub>(p-values) * sing(log<sub>2</sub>FC) estimates for each bait-prey pair (50 RBPs x 2936 preys)
2. **signed_log_pv_eCLIP.tsv** is gene-level (120 RBPs x 22471 genes)
3. **signed_log_pv_Perturbseq.tsv** includes the gene expression alteration estimates obtained from the autoencoder (first 10 latent vectors, 68 RBPs x 10 LVs).

# 2. Functional annotation of RBPs using BioID data
**BioID_annotation.R** annotates 50 RBPs with GO terms based on their protein neighborhoods identified by BioID using signed_log_pv_BioID.tsv source file. For this, lists of protein partners for each RBP are ranked by z-scored signed p-values obtained from BioID and tested for the GO terms enrichment using gene set enrichment analysis (gsea). The resulting GO pathways are filtered by NES > 2 for at least one RBP followed by bi-clustering and heatmap visualization.

In detail, the script annotates the RBPs based upon preys identified in BioID experiments starting from the signed p-values estimated as -log<sub>10</sub>(p-values)\*sign(log<sub>2</sub>FC) for every bait-prey pair separately. For each prey, signed p-values are converted to Z-scores by estimating the mean and sd across baits. Z-scores rank the preys and gsea is applied using three GO terms annotation sets (biological processes (BP), molecular functions (MF), and cell components (CC), each taken separately). Lists of 2865 preys Entrez ids are used in gene set enrichment analysis for each RBP of the whole set of fifty. In the end, Ward’s clusterization with cosine distance is used to generate the GO BP annotation heatmap.


[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.10498278.svg)](https://doi.org/10.5281/zenodo.10498278)
