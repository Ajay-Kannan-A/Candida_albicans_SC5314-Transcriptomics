# Analysis of the Transcriptomic Response of Candida albicans SC5314 (Hyphal State) to Pulsatilla Decoction Butanol Extract

An end-to-end differential gene expression (DGE) pipeline validating and exploring the transcriptomic changes in the reference clinical strain *Candida albicans* SC5314 when treated with Pulsatilla decoction butanol extract (EAEB) versus a Wild Type (WT) control group. 

## Repository Structure
* `/data` - Contains the primary processed datasets, including:
  * **`Candida_DESeq2_Analysis.xlsx`** (The comprehensive master workbook containing full baseline metrics, curated significance tables, and sorted directional expression profiles).
  * Configuration pathways and links to reference assembly builds.
* `/images` - Downstream pipeline visualizations including Volcano plots and Expression heatmaps.

---

## Bioinformatic Pipeline Workflow & Technical Rigor

1. **Data Acquisition & Quality Control**: Paired-end raw reads were imported directly into Galaxy. Initial quality assessments were conducted via **FastQC**, with outputs aggregated and visualized using **MultiQC**.
2. **Stringent Trimming**: Low-quality bases and adapter sequences were filtered using **TrimGalore** with a highly stringent quality score cut-off of 30 (Q >= 30) to optimize downstream sequence accuracy and eliminate false-positive variant alignments.
3. **Reference Genome & Annotation**: To ensure high-confidence mapping and accurate structural genomic coordination, the reference genome sequence (FASTA) and structural annotation file (GTF) were obtained from **Ensembl Fungi** for the target *Candida albicans* SC5314 assembly.
4. **High-Efficiency Alignment**: Processed reads were mapped against the Ensembl Fungi reference genome using **HISAT2**. High-efficiency overall alignment rates were achieved across all biological replicates, verifying excellent library construction quality:
   * **WT1 (SRR35655189)**: 97.36% | **WT2 (SRR35655190)**: 97.15% | **WT3 (SRR35655188)**: 97.30%
   * **EAEB1 (SRR35655187)**: 97.14% | **EAEB2 (SRR35655186)**: 97.05% | **EAEB3 (SRR35655185)**: 95.68%
5. **Quantification**: Abundance mapping was completed using **featureCounts** against the Ensembl Fungi GTF file, purposefully disabling multi-mapping reads (`don't count multimapping reads = Yes`) to avoid locus ambiguity and ensure single-source counting accuracy.
6. **Differential Expression**: **DESeq2** was deployed to compute statistically significant transcriptomic alterations using empirical Bayes shrinkage.

---

## Downstream Data Analytics & Master Workbook Setup

Rather than maintaining fragmented text files, all downstream subsets and logical transformations are organized as standalone, curated sheets within a single comprehensive master file located at `/data/Candida_DESeq2_Analysis.xlsx`. This structural layout includes:

* **`All_Genes`**: The unedited, full baseline output containing all scored transcripts processed by DESeq2 across the *Candida* genome.
* **`Significant DEGs`**: Isolated by applying a strict statistical threshold of **Padj < 0.05** to filter out low-confidence background expression shifts and control for false discovery rates (FDR).
* **`Upregulated`**: Screened using **Padj < 0.05** AND a positive fold-change threshold of **log2FC > 1** (representing a minimum 2-fold increase in expression under extract treatment).
* **`Top20_Up`**: Derived by filtering for 'Upregulated' indicators and sorting by descending magnitude of change (**highest log2FC values**) to isolate the top 20 most drastically induced defense factors.
* **`Downregulated`**: Screened using **Padj < 0.05** AND a negative fold-change threshold of **log2FC < -1** (representing a minimum 2-fold decrease in expression under extract treatment).
* **`Top20_Down`**: Derived by filtering for 'Downregulated' indicators and sorting by ascending magnitude of change (**most negative log2FC values**) to isolate the top 20 most intensely suppressed physiological mechanisms.

7. **Functional Annotation**: Significant DEGs derived from the top-tier filtered sheets were analyzed for functional category enrichment via the **Candida Genome Database (CGD) GO Term Finder** tool.

---

## Transcriptomic Visualizations

### 1. Volcano Plot
The volcano plot illustrates statistical significance (-log10 Padj) against the magnitude of change (log2 Fold Change). Statistically significant differentially expressed genes (Padj < 0.05 and absolute log2FC > 1) are distinctly colored based on the thresholds described in the filtering methodology.

![Volcano Plot](images/download.png)

### 2. Expression Profiles (Top 40 DEGs Heatmap)
This visual highlights highly contrasting expression matrix shifts comparing the Top 20 Upregulated and Top 20 Downregulated significant genes extracted from the curated Excel sheets.

![Expression Heatmap](images/download%20%281%29.png)

---

## Deep Biological Interpretation & Discussion

The transcriptomic shift of *Candida albicans* under treatment demonstrates a highly coordinated stress response characterized by two primary physiological adaptations: a dramatic upregulation of chemical defense systems and a profound shutdown of cell-surface morphogenesis.

### 1. Active Xenobiotic Efflux and Membrane Remodeling (Upregulated Mechanisms)
The topmost significantly upregulated genes are dominated by classic multi-drug resistance (MDR) factors. Notably, **CDR1** and **CDR2** (ABC transporters) along with **RTA3** show massive fold-enrichment values. 

* **The Biology:** When *C. albicans* encounters toxic stressors, it responds by activating zinc-cluster transcription factors (such as Tac1) to drive the over-expression of ATP-binding cassette (ABC) pumps. This massive influx of expression is an active cellular mechanism designed to pump the extract out across the plasma membrane to lower intracellular drug concentrations. 
* Concurrently, the enrichment of terms like **"regulation of membrane lipid distribution" (100.08x enrichment)** implies that the yeast is dynamically changing its plasma membrane ergosterol and phospholipid composition to stabilize its membrane structure against extract-induced permeabilization.

| GO ID | GO Biological Process | Fold Enrichment | FDR | Key Associated Genes |
| :--- | :--- | :---: | :---: | :--- |
| GO:0097035 | Regulation of membrane lipid distribution | 100.08x | 8.46e-05 | CDR1, RTA3, CDR2 |
| GO:0071383 | Cellular response to steroid hormone stimulus | 166.80x | 3.16e-04 | CDR1, CDR2 |
| GO:0042493 | Response to drug | 3.96x | 3.87e-03 | CDR1, CDR2, PDR16, RTA3 |

### 2. Collapse of Biofilm Architecture and Virulence Factors (Downregulated Mechanisms)
Crucially, the treatment triggers a definitive suppression of the genes responsible for pathogenicity. The data shows highly significant downregulation of **ALS3** (Agglutinin-like sequence 3) and **HYR1** (Hyphally Regulated protein 1).

* **The Biology:** *ALS3* encodes a major cell-surface adhesin required for initial host attachment and tissue invasion, while *HYR1* protects the fungus against host neutrophilic killing. The dramatic down-regulation of these elements correlates perfectly with the enriched GO terms **"Biofilm formation"** and **"Aggregation of unicellular organisms" (9.91x enrichment)**. 
* Because these key adhesins and cell-wall cross-linking proteins (including protein mannosyltransferases like *PMT2* and *PMT4*) are shut down, the yeast loses its molecular machinery to aggregate, shift into invasive hyphal morphology, or build protective extracellular matrix barriers (biofilms). This strongly indicates that the extract effectively weakens *C. albicans* virulence and fitness in its hyphal state.

| GO ID | GO Biological Process | Fold Enrichment | FDR | Key Associated Genes |
| :--- | :--- | :---: | :---: | :--- |
| GO:0042710 | Biofilm formation | 9.91x | 2.64e-04 | ALS3, HYR1, YVC1, PMT2, PMT4, CSA2 |
| GO:0098630 | Aggregation of unicellular organisms | 9.91x | 2.64e-04 | ALS3, YVC1, PMT2, PMT4, HYR1, CSA2 |
| GO:0007163 | Establishment or maintenance of cell polarity | 14.20x | 9.44e-03 | ALS3, SEC23, SEC24 |

### Conclusion
Taken together, this RNA-Seq analysis suggests that while *C. albicans* activates strong membrane-bound detoxification pumps (*CDR1*, *CDR2*) to survive the compound, the treatment successfully undermines its structural integrity, arresting its cell polarity pathways and suppressing its biofilm-forming capabilities. This makes the underlying extract a highly promising candidate for further anti-fungal development.

---

## Data Availability & Literature Citation

### Raw Transcriptomic Data Source
The raw paired-end sequence FASTQ datasets analyzed in this pipeline were obtained from the open-access **European Nucleotide Archive (ENA)** under BioProject Accession **PRJNA1335903**.

### Primary Literature Reference
The original wet-lab cultivation, extract treatment, and sequencing experiments were designed and executed by Chu et al. (2025). If you use aspects of this validation model, please credit the primary investigation:

* Chu, F., Wu, H., Li, C., Qiu, W., Zang, L., Wu, D., Shao, J., Wang, T., & Wang, C. (2025). Transcriptomics analysis reveals the effect of Pulsatilla decoction butanol extract on endoplasmic reticulum and peroxisome function of Candida albicans in hyphal state. *Journal of Ethnopharmacology*, 337(Pt 1), 118826. https://doi.org/10.1016/j.jep.2024.118826
