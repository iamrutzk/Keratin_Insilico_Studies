In-silico workflow for avian feather keratin regulatory evolution

Recommended overall strategy

The best approach is a provenance-first, staged reanalysis centered on the 15-library chicken feather RNA-seq experiment used by Bhattacharjee et al. (2016). Do not begin by combining every public dataset into one large matrix. First reproduce the published result, then improve it with current genome annotation and locus-aware quantification, and only then add independent ATAC-seq, ChIP-seq/Capture-C, single-cell, and comparative-genomics evidence.

This order is important because the original study established a valuable hypothesis but has several limitations: it used galGal4/Ensembl release 77, FPKM-based analysis, only 15 libraries, highly similar tandem beta-keratin loci, and feather regions that also differ in anatomy, maturation, cell composition, and possibly breed/strain. The computational objective should therefore be an evidence-tiered catalogue of stable regulatory associations and evolutionary hypotheses, not causal claims.

The core project can be completed entirely in silico. New feather sampling, cell isolation, reporter assays, EMSA, ChIP/CUT&Tag, CRISPR, qPCR, in situ hybridization, and proteomics are optional validation or data-generation experiments, not requirements for the computational workflow.

What the 2016 paper provides

Bhattacharjee et al. reanalyzed transcriptomes from five chicken feather regions:

•
Early body feather: EB1–EB3

•
Late body feather: LB1–LB3

•
Early flight feather: EF1–EF3

•
Middle flight feather: MF1–MF3

•
Late flight feather: LF1–LF3

The paper used 15 transcriptomes in total and reported 11,533 expressed genes, including 130 expressed beta-keratin genes. It identified beta-keratin-associated co-expression clusters and predicted TF–target relationships using co-expression, motif enrichment, and differential-expression support. Two edge counts must be kept separate:

•
372 preliminary TF–beta-keratin pairs, involving 81 TFs and 91 beta-keratin genes.

•
262 supported pairs, involving 56 TFs and 91 beta-keratin genes, after the paper’s additional support criteria.

The abstract emphasizes 26 major regulators. These numbers should be used as audit targets, not automatically treated as modern discoveries.

The raw data are publicly available under NCBI BioProject PRJNA245063 and SRA study SRP041478. The paper’s supplementary files are also essential because they contain the historical FPKM matrix, clusters, motif predictions, TF–target tables, conservation analyses, differential-expression summaries, and EMSA information.

Phase 1 — Freeze the project design and provenance

Before downloading reads, create a project manifest with these fields:

•
Accession, BioProject, BioSample, SRA experiment, and run ID.

•
Feather region and replicate.

•
Tissue/compartment and developmental description.

•
Breed or strain, sex, and animal identity where available.

•
Library layout, read length, strandedness, and sequencing platform.

•
Genome assembly and annotation release.

•
Motif database and release.

•
File URL, checksum, and download date.

Inspect the SRA metadata carefully. The run metadata indicate that the early-body samples and other region groups may have different breed/strain labels. If breed is confounded with feather region, do not describe a simple region contrast as a pure developmental effect.

Use a version-controlled workflow system such as Snakemake or Nextflow, with containers or lockfiles. Save every reference FASTA, GTF, motif file, parameter file, and checksum.

Phase 2 — Download the baseline data

Primary RNA-seq resource

Use the official records:

•


NCBI BioProject PRJNA245063

•


NCBI SRA study SRP041478

The verified run accessions are:

Plain Text


SRR1264528  SRR1265109  SRR1265122
SRR1265901  SRR1265914  SRR1265943
SRR1265944  SRR1265945  SRR1265946
SRR1265947  SRR1265948  SRR1265949
SRR1265950  SRR1265951  SRR1265952



Retrieve the supplementary archive from the article page:

•


Bhattacharjee et al. supplementary files

Preserve Tables S1–S13 and Figures S1–S6 in the project archive. Table S1 is especially important for reproducing the historical FPKM analysis.

Recommended directory structure

Plain Text


project/
  metadata/
    sample_sheet.tsv
    accession_manifest.tsv
  references/
    galGal4_ensembl77/
    GRCg7b_current/
    keratin_locus_catalog/
    motifs/
  raw_data/
    sra/
    fastq/
  results/
    historical_branch/
    modern_rnaseq/
    epigenomics/
    single_cell/
    comparative_genomics/
    alpha_beta_integration/
  workflow/
  reports/



Phase 3 — Build two reference branches

Run two branches in parallel.

Historical branch

Use the reference context closest to the paper:

•
galGal4

•
Ensembl release 77

•
The manually updated beta-keratin annotation used in the study

•
The original promoter definitions and motif version, if recoverable

This branch is for auditability. Its purpose is to ask: Can the published results be reproduced from the original data and definitions?

Modern branch

Use a current chicken assembly and annotation, for example:

•
NCBI 

GRCg7b assembly GCF_016699485.2

•
Ensembl 

GRCg7b assembly GCA_016699485.1

Create a crosswalk containing:

•
Historical gene ID and current gene ID.

•
Historical and current chromosome/scaffold.

•
Strand and coordinates.

•
Transcript models and TSSs.

•
Beta-keratin subclass.

•
Complete, partial, pseudogene, or unresolved status.

•
Unique-read and multi-mapping status.

•
Synteny anchors and unplaced-locus status.

Do not mix coordinates from galGal4, galGal6, GRCg6a, and GRCg7b without an explicit remapping step. For highly similar beta-keratin tandem arrays, remapping raw reads is preferable to relying on old count tables.

Phase 4 — Reproduce the historical analysis

The historical branch should reproduce, as closely as possible:

1.
Adapter and quality trimming.

2.
Splice-aware alignment or the original-compatible mapping strategy.

3.
FPKM calculation.

4.
Upper-quartile normalization.

5.
Pearson correlation across the 15 samples.

6.
The paper’s correlation threshold and clustering approach.

7.
Complete-linkage hierarchical clustering.

8.
GO enrichment using an expressed-gene background.

9.
CIS-BP motif enrichment and FIMO-style scanning.

10.
The paper’s differential-expression definitions.

11.
The preliminary and supported TF–target edge sets.

Create a discrepancy report with these categories:

•
Same result.

•
Changed because of reference/annotation.

•
Changed because of mapping or multi-mapping.

•
Changed because of normalization/statistics.

•
Changed because of metadata or sample interpretation.

•
Not reproducible from the available files.

Do not compare the paper’s nominal p-value thresholds directly with modern FDR results. Recalculate all modern significance values from raw counts.

Phase 5 — Modern RNA-seq reanalysis

Quality control

Run FastQC and MultiQC. Check:

•
Adapter contamination.

•
Base quality and read-length distribution.

•
Paired-end synchronization.

•
Library strandedness.

•
Read duplication.

•
Mapping rate.

•
Junction and insert-size distributions.

•
Mitochondrial and ribosomal fractions.

•
Sample correlation and PCA.

Use verified metadata for batch, animal, lane, sex, breed, and tissue. Do not add covariates simply because they are available in a spreadsheet; use them only when biologically and technically justified.

Quantification

Use two complementary methods:

•
STAR two-pass plus gene-level counting for auditable genome alignments.

•
Salmon selective alignment with decoys plus tximport for transcript-level uncertainty-aware quantification.

For beta-keratin loci, report separately:

•
Uniquely mapped reads.

•
Ambiguous or multi-mapped reads.

•
Secondary alignments.

•
Locus-level coverage.

•
Reads assigned using unique k-mers or paralog-specific sequence.

Do not force every ambiguous read to one paralog. Report conservative unique-read estimates alongside ambiguity-aware estimates.

Differential expression

Use DESeq2, edgeR, or limma-voom on integer counts. A reasonable starting design is:

Plain Text


~ verified_batch + feather_region



Add breed, sex, animal, or other terms only if metadata support them and the design remains estimable. Prespecify contrasts among EB, LB, EF, MF, and LF. Report:

•
Effect size.

•
FDR-adjusted p-value.

•
Shrinkage estimate.

•
Expression direction.

•
Mean abundance.

•
Mapping confidence.

Phase 6 — Rebuild co-expression and TF evidence

Start by reproducing the paper’s target-anchored correlation analysis. Then run a modern sensitivity analysis.

Because the study has only 15 libraries, use:

•
Bootstrap resampling.

•
Leave-one-replicate-out analysis.

•
Module preservation checks.

•
Partial correlations or models controlling for feather region.

•
A simpler target-anchored network if full WGCNA is unstable.

This is important because feather region simultaneously represents position, growth stage, maturation, and cell composition. A strong correlation does not necessarily mean direct co-regulation.

Motif analysis

Use versioned motif files from:

•


JASPAR

•

CIS-BP

•


FIMO documentation

The historical paper used CIS-BP v1.01. Current CIS-BP and JASPAR releases should be used in the modern branch, but the exact version must be recorded. Do not silently replace the historical motif database.

For each gene:

1.
Extract a strand-aware 1-kb promoter for historical comparison.

2.
Add 2-kb and distal candidate regions as sensitivity analyses.

3.
Use current TSS annotations and record TSS uncertainty.

4.
Scan both strands.

5.
Use GC- and length-matched background sequences.

6.
Report motif version, score, p-value, and q-value.

7.
Test module-level enrichment rather than counting isolated hits.

Classify each TF–target edge by evidence tier:

•
Tier 1: expression association only.

•
Tier 2: expression association plus promoter motif.

•
Tier 3: motif plus conservation or chromatin support.

•
Tier 4: motif/chromatin/contact support plus cell-state concordance.

•
Tier 5: direct biochemical or perturbational validation.

The in-silico project should not call a TF an activator or repressor based only on correlation.

Phase 7 — Test chromosome-wise and subclass-specific regulation

The central evolutionary hypothesis is that feather beta-keratin genes acquired chromosome- or subclass-specific regulation after gene-family expansion.

For each beta-keratin locus:

•
Assign current chromosome/scaffold.

•
Assign feather, scale, claw, keratinocyte, or unresolved class.

•
Quantify expression using both all reads and uniquely assignable reads.

•
Test region-by-chromosome and region-by-subclass interactions.

•
Repeat using the historical locus map.

•
Repeat after excluding unresolved or highly multi-mapping loci.

Use Fisher/hypergeometric tests or regression-based enrichment with FDR correction. The conclusion should only be considered robust if it survives changes in:

•
Assembly.

•
Annotation.

•
Aligner.

•
Read-assignment strategy.

•
Promoter definition.

•
Motif database.

•
Replicate resampling.

Phase 8 — Add public epigenomic evidence

The original 15-library project is RNA-seq only. It cannot establish accessibility, histone occupancy, TF binding, or enhancer activity. Those properties must be inferred from independent public data.

Priority epigenomic resources

Feather transition ATAC-seq

•


NCBI BioProject PRJNA1084783

•


Nature Communications paper and code

•


Analysis code

The reported experiments include feather filament and feather follicle ATAC-seq. Confirm sample-level stage, tissue, breed, and assembly metadata before using them.

Embryonic chicken keratin-cluster chromatin data

•


GEO GSE136224

•


Liang et al. developmental-cell paper

This resource includes RNA-seq, H3K27ac, H3K4me1, H3K4me3 ChIP-seq, and NG Capture-C across embryonic skin stages. It is especially useful for promoter/enhancer and chromatin-contact context around keratin clusters.

Broad chicken regulatory atlas

•


ENA PRJEB55656

•


Chicken FAANG processed dataset

•


FAANG pilot PRJEB14330

•


GEO GSE158430

PRJEB55656 includes RNA-seq, ATAC-seq, H3K4me3, H3K27me3, H3K4me1, H3K27ac ChIP-seq, and input libraries. It is a broad chicken atlas, not automatically a feather-matched experiment. Inspect sample-level metadata and the “F1 Follicle” label before treating any sample as feather relevant.

ATAC-seq workflow

1.
Download raw reads and verify checksums.

2.
Run FastQC/MultiQC.

3.
Trim adapters.

4.
Align to the same pinned chicken assembly.

5.
Remove mitochondrial reads and low-MAPQ alignments.

6.
Remove PCR duplicates where appropriate.

7.
Call peaks per replicate and on pooled libraries.

8.
Build a reproducible consensus or IDR peak set.

9.
Report FRiP, TSS enrichment, fragment-length periodicity, duplicate rate, NRF/PBC, and replicate correlation.

10.
Identify differential accessible regions with DESeq2, edgeR, or limma-voom.

ChIP-seq workflow

Process matched input controls. Use narrow peak models for TF/CTCF-style data and broad models for histone marks where appropriate. Interpret:

•
H3K27ac: active regulatory context.

•
H3K4me1: enhancer/primed-enhancer context.

•
H3K4me3: promoter context.

•
H3K27me3: repressive context.

A single histone mark is not proof of enhancer function.

Enhancer-to-gene linking

Use an evidence hierarchy:

1.
Matched Capture-C/Hi-C/HiChIP contact.

2.
ABC-style activity-by-contact score.

3.
Single-cell co-accessibility.

4.
Conserved syntenic regulatory interval.

5.
Nearest-gene proximity as a low-confidence fallback.

Useful tools include:

•


TOBIAS for ATAC footprinting.

•


chromVAR for motif-associated accessibility.

•


ArchR for single-cell chromatin analysis.

•


ABC model for enhancer–gene ranking.

Footprints, motif hits, and ABC links remain computational hypotheses.

Phase 9 — Add single-cell and single-nucleus data

Primary feather cell atlas

•


GEO GSE275984

•


BioProject PRJNA1154228

•


Original code on Zenodo

This embryonic chicken resource includes scRNA-seq and snRNA-seq from developing feather/skin samples. Use it as the primary cell-state reference, keeping scRNA and snRNA layers separate.

Complementary adult skin atlas

•


GSA CRA014628

This resource profiles adult black- and white-feather chicken skin and is useful for keratinocyte, melanocyte, dermal, endothelial, follicle-stem, and immune reference states. It is not a matched embryonic feather dataset, so it should not be treated as a direct developmental replicate.

Single-cell workflow

1.
Download matrices or raw reads and record modality.

2.
Use a pinned chicken reference and preserve the historical-to-current gene crosswalk.

3.
Run sample-level and cell-level QC.

4.
Detect doublets with Scrublet or DoubletFinder.

5.
Estimate ambient RNA with SoupX or DecontX.

6.
Keep scRNA and snRNA separate before integration.

7.
Integrate batches with Seurat anchors or scVI, while preserving raw counts.

8.
Annotate with positive and negative markers.

9.
Subcluster epithelial cells.

10.
Use sample-level pseudobulk for differential testing.

11.
Score alpha-keratin, beta-keratin, cornification, and intermediate-filament modules.

12.
Infer regulons with SCENIC/pySCENIC using chicken-aware motif resources.

13.
Use velocity and CellRank only if spliced/unspliced data and stage metadata are reliable.

Provisional epithelial labels

The labels below should be treated as computational hypotheses and checked across samples:

•
Barbule-like plate: AXL, NFKBIZ, KITLG, FAM129A.

•
Ramus/rachis-like cortical state: AXL, ESRRB, PLEKHA6, MCF2, CRACD.

•
Medullary ramus: CHL1, THSD7B, HEPHL1, TSHZ1.

•
Feather sheath/periderm: KRT18, NEBL, ALCAM, CDH2, PSCA.

•
Dermal/pulp: COL1A1 and related extracellular-matrix genes.

•
Endothelial: CDH5, VWF, PTPRB.

•
Immune: PTPRC, LCP1.

•
Melanocyte: MLANA, PMEL.

Ambient beta-keratin RNA and doublets are important risks in these data. Do not accept a cell label from one highly expressed keratin gene alone.

Phase 10 — Comparative avian and reptile phylogenomics

Only start this phase after the chicken locus catalogue is stable.

Suggested species resources

•


NCBI Datasets genome downloads

•


Ensembl Compara

•


Avian Phylogenomics Project dataset

•


Koochekian reptile synteny panel

•


Li et al. turtle–bird beta-keratin study

Include palaeognath birds, neognath birds, turtles, crocodilians, lizards, and snakes, while recording assembly quality and chromosome/scaffold status.

Comparative workflow

1.
Pin assembly accessions and annotation releases.

2.
Download genome, CDS, protein, GFF3/GTF, and assembly reports.

3.
Construct a curated corneous beta-protein/beta-keratin locus catalogue.

4.
Add alpha-keratins and non-keratin EDC genes as controls.

5.
Search proteins and genomes with BLAST/TBLASTN and profile/HMM methods.

6.
Classify complete, partial, pseudogene, and unresolved models.

7.
Use flanking non-keratin genes as synteny anchors.

8.
Build codon-aware protein and nucleotide alignments.

9.
Test gene conversion and recombination.

10.
Infer gene trees and reconcile them with the species tree.

11.
Use Progressive Cactus or other whole-genome alignments for conserved noncoding regions.

12.
Test promoter and enhancer motif gain/loss.

13.
Reconstruct ancestral sequences and branch-specific regulatory changes.

14.
Repeat results under alternative species trees, taxon subsets, assemblies, and motif databases.

A conserved motif is not proof of a conserved enhancer. An inferred ancestral switch should be reported as a candidate evolutionary hypothesis until functional data exist.

Phase 11 — Integrate alpha- and beta-keratins

Use the original beta-keratin network as the backbone, but expand the gene catalogue to include:

•
Type-I and type-II alpha-keratins.

•
Feather, scale, claw, and keratinocyte beta-keratins/corneous beta-proteins.

•
Desmosomal and plakin proteins such as DSP, DSC1, JUP, PKP1/2, EVPL, PPL, and PLEC.

•
Intermediate-filament-associated and cytoskeletal proteins.

•
Cornification and epidermal-barrier genes.

Useful protein resources

•


UniProt chicken feather keratin 1, P02450

•


UniProt chicken KRT5, Q6PVZ5

•


InterPro avian keratin family PF02422/IPR003461

•


InterPro intermediate-filament rod domain

•


Chicken 3D-skin differentiation study

•


Chicken frizzle feather KRT75 study

Integration workflow

1.
Build a locus-aware alpha/beta keratin catalogue.

2.
Quantify expression using current and historical annotations.

3.
Reproduce the original beta-keratin modules.

4.
Add alpha-keratin and IF-associated genes.

5.
Compare alpha:beta expression ratios across feather regions.

6.
Annotate protein domains with InterProScan.

7.
Build a heterogeneous evidence graph containing genes, loci, proteins, domains, motifs, modules, and sample states.

8.
Overlay single-cell expression and regulons.

9.
Add epigenomic evidence where tissue and stage match.

10.
Rank candidates by replicated expression, locus confidence, domain plausibility, regulatory support, and cross-dataset reproducibility.

Do not infer a physical alpha–beta protein complex from co-expression or domain co-occurrence alone.

Proteomics boundary

PRJNA245063 is RNA-seq, not a verified feather proteomics project. A 2019 feather-follicle study reported 5,203 proteins, but no raw MS/MS accession was verified in the available evidence. Use reported protein tables only as processed evidence unless raw spectra are obtained.

For future public or newly generated spectra:

•
Build a Gallus-specific paralog-aware FASTA.

•
Require unique peptides for closely related beta-keratins.

•
Map peptides to exact loci and domains.

•
Compare protein abundance with transcript modules.

•
Report shared-peptide ambiguity explicitly.

Public dataset priority list

Priority
Resource
Best use
Main caution
1
PRJNA245063 / SRP041478
Direct reanalysis of the published 15-library feather experiment
Small design; old annotation; possible breed/region confounding
2
Bhattacharjee supplementary Tables S1–S13
Historical audit and published edge definitions
FPKM and historical reference context
3
GRCg7b plus galGal4/Ensembl 77
Current analysis and reproducibility branch
Coordinates cannot be mixed
4
GSE275984 / PRJNA1154228
Embryonic feather cell states and keratin localization
scRNA/snRNA and stage-dependent sampling
5
PRJNA1084783
Feather filament/follicle ATAC-seq
Confirm tissue/stage/assembly metadata
6
GSE136224
Histone marks and Capture-C around keratin clusters
Embryonic skin context is not identical to regenerating feather epithelium
7
GSE146956 / PRJNA612502
Independent feather-follicle bulk RNA-seq
Plumage, age, and follicle-component differences
8
PRJNA1103878
Embryonic back-skin/feather-follicle RNA-seq
Breed and developmental-stage differences
9
PRJEB55656 / Chicken FAANG
Broad chicken ATAC/ChIP/RNA context
Inspect sample-level feather relevance
10
CRA014628
Adult skin cell-type reference
Not a matched embryonic feather cohort
11
JASPAR and CIS-BP
TF motifs
Versioned resources; motifs are not binding
12
NCBI/Ensembl/Avian Phylogenomics
Comparative gene families, synteny, and evolution
Assembly and annotation quality varies
13
UniProt/InterPro
Protein/domain annotation
Does not replace feather proteomics




Decision gates

Gate 1 — Data and metadata

Proceed only when every primary run has a valid repository record, checksum, sample label, and known limitation. If breed, sex, or animal identity is unresolved, reduce the claim scope.

Gate 2 — Reference and locus identity

Proceed to gene-specific conclusions only after the historical-to-current locus map is stable. Unplaced, partial, collapsed, or highly multi-mapping loci remain unresolved.

Gate 3 — Reproduction audit

Do not interpret modern differences until the historical branch has a discrepancy report against Tables S1–S13.

Gate 4 — Network stability

Only retain TF–target edges that survive bootstrap or leave-one-replicate-out checks and have clearly separated evidence tiers.

Gate 5 — Independent data support

A stronger computational candidate should have at least two independent evidence types, such as expression plus accessible chromatin, or expression plus cell-state specificity. Motif-only candidates remain low confidence.

Gate 6 — Comparative interpretation

Begin cross-species evolutionary claims only after chicken loci have stable orthology, synteny, and assembly-aware annotation.

Final deliverables

The project should produce:

1.
A reproducible workflow with pinned software and containers.

2.
A complete accession and checksum manifest.

3.
Historical and modern reference branches.

4.
An old-to-new beta-keratin locus crosswalk.

5.
Raw-read QC and mapping reports.

6.
Modern count matrices and differential-expression tables.

7.
Reproduced and modern co-expression modules.

8.
Versioned promoter FASTA and motif-hit tables.

9.
Evidence-tiered TF–target networks.

10.
Chromosome/subclass enrichment models.

11.
ATAC/ChIP peak, footprint, and enhancer–gene tables.

12.
Single-cell objects, annotation confidence, pseudobulk results, and regulons.

13.
Comparative locus catalogues, synteny blocks, gene trees, and motif gain/loss tables.

14.
Alpha–beta keratin and intermediate-filament integration tables.

15.
A discrepancy report against the 2016 publication.

16.
A limitations and validation plan that separates computational inference from wet-lab evidence.

What the computational study can and cannot conclude

It can identify reproducible expression programs, stable co-expression modules, candidate TF–target relationships, candidate enhancers, cell-state-specific keratin programs, syntenic gene-family expansions, and putative evolutionary regulatory switches.

It cannot by itself prove endogenous TF binding, enhancer activity, causal regulation, protein complex formation, or phenotypic effects. Those claims require direct biochemical, chromatin, perturbation, imaging, or protein-level validation.

References and resource links

[1] Bhattacharjee et al. 2016, Regulatory Divergence among Beta-Keratin Genes during Bird Evolution
[2] NCBI BioProject PRJNA245063
[3] NCBI SRA study SRP041478
[4] NCBI chicken GRCg7b assembly
[5] Ensembl chicken GRCg7b genome
[6] Chicken feather ATAC-seq BioProject
[7] Embryonic chicken keratin-cluster chromatin data
[8] Embryonic chicken feather/skin single-cell data
[9] BioProject linked to GSE275984
[10] Chicken skin single-cell dataset
[11] Chicken feather-follicle bulk RNA-seq
[12] Chicken embryonic feather-follicle/back-skin RNA-seq
[13] Functional Annotation of Chicken Genome project
[14] Processed Chicken FAANG regulatory dataset
[15] JASPAR transcription-factor motif database
[16] CIS-BP transcription-factor motif database
[17] Ensembl Compara comparative genomics resources
[18] Avian Phylogenomics Project dataset
[19] Li et al. turtle–bird beta-keratin comparative study
[20] UniProt chicken feather keratin 1
[21] InterPro avian keratin family
[22] Feather-transition regulatory study
[23] Embryonic chicken keratin-cluster chromatin study
