# CPStools Pro — User Guide

**CPStools Pro** is an integrated, cross-platform desktop application for **chloroplast (plastid) genome analysis**. It bundles assembly post-processing, annotation, quality control, comparative genomics, marker discovery, phylogenetics, and publication-ready visualization into a single graphical workbench, while also exposing every module via a scriptable command-line interface.

> Designed for plant molecular systematists, evolutionary biologists, and bioinformatics labs working at the chloroplast / organellar genome scale.

---

## Table of Contents

```
CPStools Pro
├── Overview
├── Key Features
├── System Requirements
├── Installation
├── Software Overview Homepage
├── Assembly & Standardization
│   ├── IR Detection
│   ├── Sequence Standardization
│   ├── Auto Adjust
│   ├── Auto Annotation
│   ├── Annotation Check
│   └── Annotation Editor
├── Statistics
│   ├── Gene Summary
│   ├── GC Content
│   ├── Format Conversion
│   └── Gene Extraction
├── Comparison
│   ├── Pi Analysis
│   ├── Ka/Ks
│   ├── RSCU
│   ├── Codon Analysis
│   ├── SSR Detection
│   ├── LSR Detection
│   ├── Variant Analysis
│   └── Barcoding Analysis
├── Phylogeny
│   ├── Sequence Extraction
│   └── Input Builder
├── Visualization
├── Citation
└── License & Contact
```

---

## Overview

A typical chloroplast genome project — from raw assembly contigs to a publication-ready phylogenetic figure — usually requires stitching together half a dozen different web servers, command-line tools, and custom scripts. CPStools Pro consolidates that pipeline into one workbench:

1. **Assembly & Standardization** — verify the canonical LSC–IRb–SSC–IRa quadripartite structure, re-orient the sequence, and produce a high-quality GenBank annotation.
2. **Statistics** — describe the gene content, base composition, and convert between the formats required by downstream databases (NCBI, mVISTA, GFF3 consumers).
3. **Comparison** — quantify nucleotide diversity (π), selection pressure (Ka/Ks), codon usage (RSCU), and discover SSR / LSR / indel / SNV markers across multiple plastomes.
4. **Phylogeny** — build single- or multi-locus / whole-plastome alignments and prepare ready-to-run input files for IQ-TREE, RAxML, MrBayes, or BEAST.
5. **Visualization** — produce circular plastome maps, RSCU heatmaps, sequencing depth plots, and annotated phylogenetic trees.

All modules accept and produce community-standard files (FASTA, GenBank, GFF3, TBL, mVISTA, Newick), so CPStools Pro slots naturally into existing workflows.

---

## Key Features

- **One-click workflows** — go from a raw FASTA assembly to a fully annotated, properly oriented GenBank record in a single dialog.
- **Reference-based annotation** with adjustable identity / coverage / e-value thresholds and a side-by-side annotation editor for manual curation.
- **Comparative genomics suite** covering π, Ka/Ks, RSCU / codon usage, SSRs, LSRs, indels, SNVs, and DNA barcoding window scans.
- **Phylogeny-ready exports** — common-gene extraction, concatenated supermatrices, and partition files for downstream tree builders.
- **Publication-quality figures** in SVG / PNG / PDF, including circular plastome maps and RSCU heatmaps.
- **Dual interface** — every GUI action has an equivalent `cpstools <subcommand>` CLI call for batch and HPC use.
- **Cross-platform** — native builds for Windows, macOS (Apple Silicon & Intel), and Linux.

---

## System Requirements

| Platform    | Minimum             | Recommended         |
| ----------- | ------------------- | ------------------- |
| **Windows** | Windows 10 64-bit, 4 GB RAM | Windows 11, 8 GB+ RAM |
| **macOS**   | macOS 10.15+, 4 GB RAM       | macOS 12.0+, 8 GB+ RAM |
| **Linux**   | Ubuntu 18.04+, 4 GB RAM      | Ubuntu 20.04+, 8 GB+ RAM |

---



---

## Software Overview Homepage

The homepage is the launchpad for every workflow. Tools are grouped into five panels — **Assembly & Standardization**, **Statistics**, **Comparison**, **Phylogeny**, and **Visualization** — mirroring a typical plastome project from raw assembly to figure.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427163954423.png" alt="CPStools Pro homepage" style="zoom: 33%;" />

---

## Assembly & Standardization

The first stage of any plastome project: confirm the genome's quadripartite architecture, normalize its orientation, and produce a clean, NCBI-ready annotation.

### IR Detection

Identifies the **Large Single-Copy (LSC)**, **Inverted Repeat b (IRb)**, **Small Single-Copy (SSC)**, and **Inverted Repeat a (IRa)** boundaries by self-alignment of the input sequence.

- **Input**: single FASTA or GenBank file
- **Output**: textual coordinate report (`LSC: 1–84296`, `IRb: 84297–118054`, …) plus a tab-separated table that can be reused by the *Sequence Standardization* and *Auto Adjust* modules.
- **Use it to**: validate that the assembly is structurally complete (a missing or unequal IR pair almost always signals an assembly error).

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427164220323.png" alt="IR Detection" style="zoom: 33%;" />

### Sequence Standardization

Re-orients raw assemblies into the canonical **LSC → IRb → SSC → IRa** layout used by NCBI and most downstream comparison tools.

- **Input**: one or more FASTA files plus the IR coordinate file produced above.
- **Modes**: `LSC` (start at LSC origin), `SSC` (flip SSC orientation), `RP` (reverse complement).
- **Output**: standardized FASTA files ready for annotation and cross-genome comparison.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427164730370.png" alt="Sequence Standardization" style="zoom: 33%;" />

### Auto Adjust

A **one-click pipeline** that chains *IR Detection* and *Sequence Standardization*: drop in a folder of raw FASTA assemblies and receive a folder of standardized plastomes whose start position, IR orientation, and strand are all consistent.

- **Best for**: batch processing dozens to hundreds of newly assembled genomes before any comparative analysis.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427165241929.png" alt="Auto Adjust" style="zoom: 33%;" />

### Auto Annotation

Reference-based annotation that transfers gene, CDS, tRNA, and rRNA features from a closely related, well-curated GenBank record onto the target sequence.

- **Inputs**: reference `.gb` + target `.fasta`.
- **Tunable thresholds**: identity (default 0.85), coverage (default 0.75), e-value.
- **Outputs**: `final.gb` (annotated GenBank), `annotation_report.txt` (per-gene summary), and a `tmp/` working folder.
- **Quality benchmarks** (typical angiosperm plastome): 110–130 genes, 75–85 CDS, 30–37 tRNAs, 4 rRNAs.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427170146237.png" alt="Auto Annotation" style="zoom: 33%;" />

### Annotation Check

Compares an annotated GenBank file against a trusted reference (or against itself for self-consistency checks) and flags missing genes, length anomalies, frame errors, and non-standard feature qualifiers.

- **Use it to**: triage annotations before NCBI submission or before publication-quality figure rendering.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427170652649.png" alt="Annotation Check" style="zoom: 33%;" />

### Annotation Editor

Interactive table-based editor for the gene/CDS/tRNA/rRNA features of a GenBank file. Edit names, coordinates, strands, exon boundaries, and qualifiers, then re-save in valid GenBank syntax.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427170957632.png" alt="Annotation Editor" style="zoom:33%;" />

---

## Statistics

Descriptive analyses and format conversions that summarize a single plastome or a batch of plastomes.

### Gene Summary

Tabulates gene content by **functional category** (photosynthesis, ribosome, NADH dehydrogenase, RNA polymerase, …), counts mono- and multi-exon genes, and produces an intron-presence matrix.

- **Output**: per-genome statistics CSV plus an aggregated cross-species matrix.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427171544831.png" alt="Gene Summary" style="zoom:33%;" />

### GC Content

Calculates GC content at the **whole-genome**, **region** (LSC / SSC / IR), and **per-gene** level. Useful for spotting compositional anomalies and contamination.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427171726984.png" alt="GC Content" style="zoom:33%;" />

### Format Conversion

Bridges CPStools Pro to the rest of the bioinformatics ecosystem.

| From → To           | Purpose                                |
| ------------------- | -------------------------------------- |
| GenBank → FASTA     | Sequence extraction                    |
| GenBank → TBL + FSA | NCBI submission package                |
| GenBank → GFF3      | Generic annotation interchange         |
| GenBank → mVISTA    | Comparative genomics visualization     |
| Gene list → GenBank | Reconstruct GenBank from per-gene FASTA|

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427172231835.png" alt="Format Conversion" style="zoom:33%;" />

### Gene Extraction

Pulls a named gene (or list of genes) from a target GenBank using a reference for orientation and naming, returning per-species FASTA files for downstream alignment or single-locus phylogeny.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427172440827.png" alt="Gene Extraction" style="zoom:33%;" />

---

## Comparison

Cross-species analyses for population, phylogeographic, and molecular-evolution studies.

### Pi Analysis

Computes **nucleotide diversity (π)** for each shared gene across a folder of GenBank files, using MAFFT for alignment.

- **Output**: `Pi_values.csv`, `Pi_sorted.csv`, plus per-gene alignment files.
- **Interpretation**: π > 0.02 indicates a strong candidate molecular marker; π < 0.005 is typically too conserved to be informative.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427172838288.png" alt="Pi Analysis" style="zoom:33%;" />

### Ka/Ks

Estimates **non-synonymous (Ka) vs. synonymous (Ks) substitution rates** for shared CDS regions and reports the **Ka/Ks** ratio.

- **Methods**: Nei–Gojobori (NG, default), Li–Wu–Luo (LWL), Yang–Nielsen (YN).
- **Interpretation**: Ka/Ks < 1 = purifying selection, ≈ 1 = neutral, > 1 = positive selection.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427173020226.png" alt="Ka/Ks" style="zoom:33%;" />

### RSCU

Calculates **Relative Synonymous Codon Usage** for every CDS in every input GenBank, with an option to filter out short genes (default < 300 bp).

- **Output**: an RSCU matrix plus raw codon-usage frequencies, ready for the heatmap visualization in the *Visualization* panel.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427173210197.png" alt="RSCU" style="zoom:33%;" />

### Codon Analysis

Extends RSCU with codon-usage indices (ENC, CAI, GC1/GC2/GC3, AT/GC skew) and neutrality / parity-rule plots that reveal whether codon usage is dominated by mutation pressure or translational selection.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427173346205.png" alt="Codon Analysis" style="zoom:33%;" />

### SSR Detection

Finds **Simple Sequence Repeats (microsatellites)** and writes them back into a GenBank file as features so they can be visualized on the circular map.

- **Default minimum repeat counts**: mono ≥ 10, di ≥ 6, tri ≥ 5, tetra/penta/hexa ≥ 4.
- **Outputs**: `*_SSRs.txt` (table) and `*_SSRs_annotated.gb` (annotated GenBank).

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427173453090.png" alt="SSR Detection" style="zoom:33%;" />

### LSR Detection

Finds **Long Sequence Repeats** — Forward (F), Palindromic (P), Reverse (R), and Complement (C) — that are diagnostic of plastome rearrangement and intra-genomic recombination.

- **Tunable**: minimum length (default 30 bp), maximum length (default 1 000 bp, filters the IR), max mismatches (default 3).
- **Optional**: import REPuter results directly.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427173706879.png" alt="LSR Detection" style="zoom:33%;" />

### Variant Analysis

Detects **indels** and **SNVs** from multi-sequence alignments, classifies them as species-specific or shared, and reports their distribution along the genome.

- **Use it to**: identify diagnostic variants for species identification or population structure analyses.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427174153449.png" alt="Variant Analysis" style="zoom:33%;" />

### Barcoding Analysis

Sliding-window scan across an alignment that ranks candidate **DNA barcoding regions** by diagnostic-site count, gap rate, and primer-design feasibility (via integrated Primer3).

- **Tunable**: window size (default 800), step (default 100), top-K windows (default 5), max diagnostic sites, max gap rate.
- **Outputs**: `top_windows.tsv`, `diagnostic_sites.tsv`, `fingerprints.tsv`, and (optionally) `primers_*.tsv`.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427174307056.png" alt="Barcoding Analysis" style="zoom:33%;" />

---

## Phylogeny

Build the alignments and input files needed to run downstream tree-inference software.

### Sequence Extraction

Identifies **shared genes** across a folder of GenBank files and exports per-gene FASTA files for **CDS** (`-m cds`) or **protein** (`-m pro`) supermatrices.

- **Output**: `common_genes.txt`, individual gene FASTAs, and the list of species missing each gene.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427174447692.png" alt="Sequence Extraction" style="zoom:33%;" />

### Input Builder

Aligns each shared gene with MAFFT, **concatenates** the alignments into a supermatrix, and writes a partition file plus ready-to-run input for **IQ-TREE**, **RAxML**, **MrBayes**, or **BEAST**.

- **Output**: `concatenated.fasta` / `.phy` / `.nex`, partition file, and a model-selection-friendly per-gene alignment folder.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427174655637.png" alt="Input Builder" style="zoom:33%;" />

---

## Visualization

A unified panel for publication-ready figures:

- **Circular plastome maps** — gene-coloured rings with LSC/IRb/SSC/IRa boundaries and optional SSR/LSR overlays. Exports SVG / PNG / PDF.
- **RSCU heatmaps** — codon × species matrix with hierarchical clustering.
- **Sequencing depth plots** — coverage profile from paired-end reads, with windowed smoothing.
- **Phylogenetic tree beautifier** — interactive editing of node labels, branch colours, support values, and root position.

<img src="/Users/xuwenbo/Library/Application Support/typora-user-images/image-20260427174742876.png" alt="Visualization" style="zoom:33%;" />

---

## Citation



---

## License & Contact

- **License**: see the `LICENSE` file shipped with the application.
- **Issues / feature requests**: please open a ticket on the project's issue tracker.
- **Author contact**: xwb7533@163.com
