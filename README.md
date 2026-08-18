# Integrated Re-analysis of Extracellular Vesicle miRNA Cargo and Macrophage Transcriptional Reprogramming in Pancreatic Cancer

A reproducible, assay-matched, species-aware computational re-analysis of **GSE285193** integrating human pancreatic cancer extracellular-vesicle miRNA profiles with transcriptional responses in recipient mouse bone marrow-derived macrophages.

## Project overview

Pancreatic ductal adenocarcinoma is characterized by a complex and immunosuppressive tumor microenvironment in which tumor-associated macrophages contribute to tumor progression and therapeutic resistance.

Extracellular vesicles released by pancreatic cancer cells can transfer regulatory molecules, including microRNAs, and influence transcriptional programs in recipient cells.

This project re-analyzes the publicly available sequencing data deposited under **GEO accession GSE285193** to investigate relationships between pancreatic cancer-derived extracellular-vesicle miRNA cargo and macrophage transcriptional reprogramming.

The analysis extends beyond the original study's primary focus on the **miR-182-5p–TLR4–JAK2/STAT3 axis** by applying an integrated systems-level framework to identify additional EV-associated miRNAs, macrophage transcriptional responses, pathways, cross-species miRNA–mRNA relationships, and lncRNA-centered candidate regulatory relationships.

## Study design

The dataset contains **18 sequencing samples** organized into two biologically related but analytically distinct arms.

### Human extracellular-vesicle small RNA-seq

Nine EV small RNA-seq libraries were generated from:

* hTERT-HPNE non-tumorigenic pancreatic epithelial cells
* PANC-1 pancreatic cancer cells
* PPCL-68 patient-derived xenograft-associated pancreatic cancer cells

Each group contains three biological replicates.

This arm was used to characterize differential abundance of EV-associated miRNAs.

### Mouse BMDM bulk RNA-seq

Nine bulk RNA-seq libraries were generated from mouse bone marrow-derived macrophages exposed to EVs derived from:

* hTERT-HPNE cells
* PANC-1 cells
* PPCL-68 cells

Each treatment group contains three biological replicates.

This arm was used to characterize:

* differential gene expression
* pathway-level responses
* macrophage-state transcriptional programs
* exploratory immune and stromal transcriptional signatures
* lncRNA expression
* circRNA-associated back-splice junction signals

## Experimental contrasts

Three pairwise contrasts were used throughout the analysis:

1. `PANC1_vs_HPNE`
2. `PPCL68_vs_HPNE`
3. `PPCL68_vs_PANC1`

Positive log2 fold-change values represent higher abundance or expression in the first group named in the contrast.

Negative values represent higher abundance or expression in the second group.

## Analysis workflow

```text
GSE285193
│
├── Human EV small RNA-seq
│   │
│   ├── nf-core/smrnaseq
│   ├── miRNA quantification
│   ├── edgeR differential abundance
│   ├── significant EV miRNAs
│   └── experimentally supported human miRNA targets
│
└── Mouse BMDM bulk RNA-seq
    │
    ├── nf-core/rnaseq
    ├── DESeq2 differential expression
    ├── pathway analysis
    ├── macrophage-state module scoring
    ├── immune/stromal transcriptional signatures
    ├── circRNA analysis
    └── lncRNA analysis
             │
             ▼
Cross-species integration
             │
             ├── human miRNA-target evidence
             ├── human-to-mouse orthology
             ├── mouse differential expression
             └── contrast-matched directional analysis
                         │
                         ▼
             Candidate EV-miRNA–mRNA relationships
                         │
                         ▼
              lncRNA–miRNA–mRNA integration
```

## Human EV miRNA analysis

Small RNA-seq preprocessing and miRNA quantification were performed using:

* **nf-core/smrnaseq 2.4.1**
* **Nextflow 25.04.8**
* **GRCh38 human reference genome**
* miRTrace
* Bowtie
* miRTop
* MultiQC

Differential miRNA abundance was evaluated using **edgeR**.

Significant differential abundance required:

```text
FDR < 0.05
|log2 fold change| >= 1
```

Raw non-negative integer miRNA counts were used for statistical modeling.

TMM-normalized logCPM and transformed expression values were used for exploratory visualization and quality assessment.

## Mouse BMDM RNA-seq analysis

Mouse BMDM RNA-seq processing was performed using:

* **nf-core/rnaseq 3.26.0**
* **Nextflow 25.04.8**
* **GRCm39 mouse reference genome**
* **GENCODE vM39**
* STAR
* Salmon
* MultiQC

The workflow was executed on the **SDSC Expanse high-performance computing system** using Slurm and Singularity containers.

Differential gene expression was analyzed using **DESeq2**.

The principal significance criteria were:

```text
Adjusted P value < 0.05
|log2 fold change| >= 1
```

Additional analyses included pathway enrichment, macrophage-state module scoring, selected marker-gene evaluation, and exploratory mouse-compatible immune and stromal transcriptional-signature analysis.

## Cross-species miRNA–mRNA integration

The human EV miRNA and mouse macrophage RNA-seq arms represent different species and molecular compartments and were therefore analyzed independently before integration.

Experimentally supported human miRNA-target relationships were retrieved using **multiMiR**.

The primary integration retained **Functional MTI** evidence.

Human targets were mapped to mouse orthologs using **babelgene**. Ortholog mappings were retained when supported by at least two underlying orthology resources.

Candidate relationships were prioritized when:

1. the EV miRNA showed significant differential abundance
2. the corresponding human miRNA-target relationship had qualifying experimental support
3. a supported mouse ortholog could be identified
4. the mouse target gene showed statistically supported differential expression in the corresponding contrast
5. EV miRNA and mouse target mRNA changed in opposite directions

These relationships are interpreted as **inverse-supported, ortholog-linked regulatory candidates**, not as proof of direct targeting in mouse macrophages.

## circRNA analysis

A separate exploratory circRNA analysis evaluated recurrent back-splice junctions in the paired-end mouse BMDM RNA-seq libraries.

Back-splice junctions were detected, but **no junction met the prespecified differential-abundance criteria** in any treatment contrast.

The result remained unchanged under the sensitivity analysis.

The circRNA branch was therefore retained as a negative exploratory result and was not used to construct a circRNA–miRNA–mRNA network.

## lncRNA-centered integration

Differentially expressed mouse lncRNAs were integrated with significant EV miRNAs and candidate mouse target mRNAs.

Mouse lncRNAs were annotated using **GENCODE vM39**.

To maintain species compatibility, human EV miRNAs entered this analysis only when their mature sequences exactly matched corresponding mouse miRNA sequences.

Representative lncRNA sequences were screened for canonical:

* 8mer seed matches
* 7mer-m8 seed matches
* 7mer-A1 seed matches

Candidate lncRNA–miRNA–mRNA triplets were retained only when:

1. lncRNA and EV miRNA changed in opposite directions
2. EV miRNA and mouse target mRNA changed in opposite directions
3. lncRNA and mouse target mRNA changed in the same direction

These relationships are considered sequence-predicted and directionally coherent hypotheses rather than experimentally demonstrated ceRNA interactions.

## Key findings

### EV miRNA cargo

**15 unique miRNAs** showed significant differential abundance across the three EV contrasts.

### Macrophage transcriptional remodeling

Pancreatic cancer-derived EV exposure was associated with extensive and EV-source-dependent macrophage transcriptional remodeling involving:

* inflammatory programs
* immune-regulatory programs
* antigen-presentation pathways
* signaling pathways
* metabolic programs
* macrophage-state-associated transcriptional modules

Lower **Tlr4** transcript abundance was observed following exposure to EVs from both pancreatic cancer models, providing agreement with an important component of the original study.

In contrast, **Arg1** showed transcript-level behavior that differed from the direction reported in the original experimental study.

The combined results support a complex macrophage response rather than a simple binary M1/M2 polarization model.

### Cross-species integration

The final cross-species analysis identified:

**36 inverse-supported, ortholog-linked EV-miRNA–mouse-mRNA relationships**

These included recurrent miR-7-5p-centered candidates and contrast-specific relationships involving:

* `miR-200a-5p → Pten`
* `miR-99b-3p → Gsk3b`
* `miR-409-3p → Met`

### miR-182-5p

miR-182-5p was detected in the human EV dataset but did not satisfy the combined:

```text
FDR < 0.05
|log2FC| >= 1
```

criteria used to define significant EV miRNAs in the unbiased analysis.

It therefore did not enter the primary cross-species candidate network.

This does not contradict the experimentally demonstrated miR-182-5p mechanism reported by the original study. It reflects application of the predefined statistical and evidence-filtering framework to the sequencing dataset.

### circRNA

No back-splice junction satisfied the prespecified differential-abundance criteria under either the primary or sensitivity analysis.

The circRNA branch was therefore reported as a negative result.

### lncRNA–miRNA–mRNA integration

The final lncRNA-centered integration identified:

* **18 directionally coherent candidate triplets**
* **16 unique mouse lncRNAs**
* **5 EV miRNAs**
* **5 candidate mouse target mRNAs**

The five EV miRNAs represented in the final triplet network were:

* `hsa-miR-200a-5p`
* `hsa-miR-409-3p`
* `hsa-miR-615-3p`
* `hsa-miR-708-3p`
* `hsa-miR-99b-3p`

Contrast-specific modules included:

| Contrast        | EV miRNA        | Mouse target | Candidate triplets |
| --------------- | --------------- | ------------ | -----------------: |
| PANC1 vs HPNE   | hsa-miR-615-3p  | Lcor         |                  4 |
| PPCL68 vs HPNE  | hsa-miR-200a-5p | Pten         |                  5 |
| PPCL68 vs HPNE  | hsa-miR-708-3p  | Otub1        |                  6 |
| PPCL68 vs HPNE  | hsa-miR-99b-3p  | Gsk3b        |                  1 |
| PPCL68 vs PANC1 | hsa-miR-409-3p  | Met          |                  2 |

## Interpretation

This study provides a systems-level computational framework connecting altered pancreatic cancer EV miRNA cargo with macrophage transcriptional responses.

The analysis identifies candidate relationships for future mechanistic investigation.

The integrated associations do not independently establish:

* delivery of individual EV miRNAs into recipient macrophages
* intracellular availability of the EV miRNAs
* loading into the RNA-induced silencing complex
* direct miRNA-target binding
* direct lncRNA-miRNA binding
* subcellular colocalization
* functional ceRNA competition
* causal regulation of macrophage gene expression

Experimental validation is required to establish these mechanisms.

## Repository organization

The repository is being organized into separate components corresponding to the major analytical stages:

```text
.
├── README.md
├── LICENSE
├── .gitignore
├── docs/
├── metadata/
├── workflows/
├── scripts/
├── results/
├── figures/
└── environments/
```

Detailed directory contents and execution instructions will be added as the reproducibility files are organized.

## Data availability

The sequencing data analyzed in this project are publicly available through the NCBI Gene Expression Omnibus under accession:

**GSE285193**

Raw sequencing files are not duplicated in this repository.

The repository will instead provide the sample metadata, analysis scripts, workflow configuration, selected processed outputs, figures, and information required to reproduce the analysis from the public source data.

## Reproducibility

The analysis uses versioned nf-core workflows and retains assay-specific processing for the two sequencing arms.

Major computational components include:

| Component                     | Method                                    |
| ----------------------------- | ----------------------------------------- |
| Workflow management           | Nextflow 25.04.8                          |
| Human EV small RNA-seq        | nf-core/smrnaseq 2.4.1                    |
| Mouse BMDM RNA-seq            | nf-core/rnaseq 3.26.0                     |
| Human reference               | GRCh38                                    |
| Mouse reference               | GRCm39                                    |
| Mouse annotation              | GENCODE vM39                              |
| miRNA differential abundance  | edgeR                                     |
| Mouse differential expression | DESeq2                                    |
| Human miRNA-target evidence   | multiMiR                                  |
| Human-to-mouse orthology      | babelgene                                 |
| circRNA analysis              | back-splice-junction-based workflow       |
| lncRNA analysis               | DESeq2 and canonical seed-site prediction |

Exact scripts, configuration files, package information, and execution instructions will be documented within the corresponding repository directories.

## Project status

The computational analysis and final report are complete.

Repository organization and reproducibility documentation are currently being finalized.

## Author

**Aaliah Aly**

Master of Science in Bioinformatics
Georgetown University
Washington, DC

## Supervision

**Dr. Markus Hoffman**
Georgetown University

## License

Analysis code in this repository is released under the **MIT License**.

The original sequencing data remain subject to the terms and attribution requirements of their respective public repositories and source study.

## Citation

If you use this repository or analysis, please cite the original GSE285193 study and this repository.

A formal repository citation will be added through `CITATION.cff` when the repository structure is finalized.
