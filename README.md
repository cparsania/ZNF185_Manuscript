# ZNF185 knockdown RNA-seq — manuscript analysis

[![render](https://github.com/cparsania/ZNF185_Manuscript/actions/workflows/render.yml/badge.svg)](https://github.com/cparsania/ZNF185_Manuscript/actions/workflows/render.yml)

**📊 Rendered reports:** [analysis report](https://cparsania.github.io/ZNF185_Manuscript/) · [methods](https://cparsania.github.io/ZNF185_Manuscript/methods.html)
*(URLs go live when the repository is made public at publication; until then, download the reports from the latest [Actions run artifacts](https://github.com/cparsania/ZNF185_Manuscript/actions/workflows/render.yml).)*

Reproducible differential-expression analysis for the ZNF185 knockdown RNA-seq
experiment: control (NT) vs two independent *ZNF185*-targeting shRNAs (SH1,
SH2), three biological replicates each. Differential expression with DESeq2 via
the [VISTA](https://bioconductor.org/packages/VISTA/) Bioconductor package;
Hallmark/GO enrichment via clusterProfiler.

## Repository layout

| Path | Contents |
|---|---|
| `analysis/ZNF185_RNAseq_VISTA_analysis.qmd` | Main analysis (count matrix → DEGs → enrichment → figures) |
| `analysis/ZNF185_RNAseq_methods.qmd` | One-paragraph manuscript Methods with references (`references.bib`) |
| `analysis/ZNF185_RNAseq_QC_metrics.qmd` | rRNA-rate / exon-efficiency QC — **documentation only**, see below |
| `data/ZNF185_RNAseq_feature_count.txt` | Raw featureCounts matrix (60,671 Ensembl genes × 9 samples) — identical to the processed file deposited at GEO |
| `data/sample_info.tsv` | Sample-to-group assignments |
| `data/protein_coding_genes_GRCh38_101.txt` | 19,966 protein-coding gene IDs derived from `Homo_sapiens.GRCh38.101.gtf` (`type=="gene" & gene_biotype=="protein_coding"`) — replaces the 1.2 GB GTF in the reproduction path |
| `data/star_logs/` | The nine STAR `Log.final.out` files (alignment statistics) |
| `data/ZNF185_RNAseq_QC_metrics.tsv` | QC metrics computed from the BAMs (see below) |
| `results/` | Created on render; all outputs land here (git-ignored) |

## Reproduce

Requirements: [Quarto](https://quarto.org) ≥ 1.4 and R ≥ 4.5 with Bioconductor 3.23.

```r
install.packages("renv")
renv::restore()                  # installs pinned package versions from renv.lock
pak::pak("cparsania/parcutils")  # STAR-log summary helpers (GitHub-only)
```

Then:

```bash
quarto render          # renders the analysis + methods documents
```

Outputs (DEG tables, normalized counts, RDS, asset bundle) are written to
`results/`; the self-contained HTML reports appear next to the qmd files.
Runtime is roughly 15–40 minutes (enrichment dominates). To run against a
different count matrix or output location, override the parameters:

```bash
quarto render analysis/ZNF185_RNAseq_VISTA_analysis.qmd -P counts_file=/path/to/counts.txt
```

GSEA uses stochastic p-value estimation; the qmd sets `set.seed(42)` so
reruns are exact.

Continuous verification: the `render` GitHub Actions workflow re-runs the full
analysis from `renv.lock` on a clean runner. It triggers on version tags and
manual dispatch (Actions tab → render → Run workflow); rendered reports are
attached as workflow artifacts.

Online reports: the workflow also deploys the rendered HTML to GitHub Pages,
but only once this repository is public (the deploy job is gated on
visibility). At publication: make the repo public, enable Pages (Settings →
Pages → Source: *GitHub Actions*), and re-run the workflow — the analysis
report then lives at <https://cparsania.github.io/ZNF185_Manuscript/> (methods
at `/methods.html`).

## Raw data

Raw fastq files and the count matrix are deposited at GEO/SRA:
**GSExxxxxx** *(accession pending — update on release)*. Alignment used STAR
2.7.10a against GRCh38 / Ensembl release 101; full details in the Methods
section of the analysis document.

## QC metrics document

`analysis/ZNF185_RNAseq_QC_metrics.qmd` documents the rRNA-rate and
Expression-Profiling-Efficiency computation. It requires the STAR BAM files
(~26 GB, not distributed here — re-derivable from SRA) and is therefore **not
rendered by default** (`_quarto.yml` excludes it). Its output table is
provided as `data/ZNF185_RNAseq_QC_metrics.tsv`.

## Citation

*(Manuscript citation to be added upon publication.)* Analysis software:
Parsania C. (2026). VISTA: Visualization and Integrated System for
Transcriptomic Analysis. R/Bioconductor. doi:10.18129/B9.bioc.VISTA
