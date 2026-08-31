# Big Data in Biotechnology — Module 2: Transcriptomics (interactive)

A browser-based companion to Module 2 of NPLK19000U. R runs **in the page** via
[quarto-live](https://github.com/r-wasm/quarto-live) / webR — students need no
install and no login.

**Live site:** https://mutwil.github.io/bigdata-module2-transcriptomics/

## The hybrid design

webR ships base R only — no Bioconductor, no CLI tools. So the module is split:

| Runs in the browser (green cells) | Run locally on ERDA (blue blocks) |
|---|---|
| Quality profiles, expected errors, filter trade-offs | `dada2::filterAndTrim()` |
| Sample naming, count-matrix assembly, QC checks | `kallisto index` / `kallisto quant` |
| Size factors, normalisation, VST, PCA, clustering | the full `DESeq2` workflow |
| MA/volcano, multiple testing, enrichment (`phyper`) | `clusterProfiler`, `fgsea` (GSEA) |

Anything heavy was pre-computed into small CSVs (~6 MB total) that the page loads
with `read.csv()`.

## Layout

```
index.qmd              the lesson
_quarto.yml            site config
lesson.css             styling for the "runs here" / "run locally" blocks
editor-theme.css       code-editor colours
save-work.html         localStorage save/restore of student code
external-links.html    off-site links open in a new tab
fold-cells.html        auto-fold long cells
data/                  pre-computed CSVs (see below)
.github/workflows/     render + deploy to GitHub Pages on push
```

## Data provenance

All from the real Module 2 dataset — *Colletotrichum fructicola* RNAseq
(Liang et al. 2018, *BMC Genomics* 19:557), GEO **GSE103878**. Four growth forms
x 3 replicates: CON (conidia), APP (appressoria), CIH (cellophane infectious
hyphae), IL (infected apple leaf).

| File | What it is |
|---|---|
| `counts.csv` | the course count matrix, 17,827 genes x 12 samples |
| `annotation.csv` | gene id -> functional description |
| `de_APP_vs_CON.csv`, `de_CIH_vs_CON.csv` | DESeq2 results (via pydeseq2, a faithful port) |
| `vst.csv` | variance-stabilised matrix |
| `size_factors.csv` | DESeq2 size factors |
| `quality_profiles.csv` | per-cycle Phred summaries computed from the actual FASTQ files |
| `read_retention.csv` | reads surviving a grid of trimLeft x truncLen x maxEE |
| `abundance/*.csv` | real kallisto output, subset to 3,000 transcripts |
| `gene_sets.csv` | **keyword-derived** functional categories (see caveat) |
| `kallisto_summary.csv` | per-sample totals + file checksums |

Regenerate with `precompute_module2.py` (needs `pydeseq2`, `pandas`, `numpy` and
the original Absalon files).

### Two things teachers should know

1. **The gene sets are not curated GO/PFAM.** This genome ships no GO annotation
   with the course data, so categories were built by keyword-matching the
   annotation text. The page states this in the text. The enrichment *method* is
   the real one; the categories are a stand-in.
2. **The enrichment analysis returns nothing significant, deliberately.** With
   2,591 DE genes against 29 broad protein-family categories, no term survives BH
   correction — that is what the data honestly gives. Rather than p-hack a result,
   Session 4 makes the null result the lesson: a positive control proves the code
   works, then §4.6 diagnoses the four reasons, which motivates GSEA exactly as the
   lecture does.

### A bug found in the 2025 material

`abundance_files_from_kallisto.zip` contains **`CON1_abundance.tsv` and
`CON2_abundance.tsv` as byte-identical duplicates** (MD5 `9c6d85a3...`). CON2 is a
copy of CON1. The main count table is unaffected. Section 2.4 turns this into a
"check your matrix before you trust it" exercise, but the archive should be fixed.

## Publishing

Push to `main`; the Action installs Quarto + R, adds the quarto-live extension and
deploys to Pages. If the deploy step returns HTTP 202 "Deployment failed, try again
later", the render still succeeded — rerun with `gh run rerun <RUN_ID> --failed`.

## Optional add-ons not enabled here

The reference template (`bioinformatik-adopt-a-gene`) also has an **AI tutor** and a
fillable **dossier** panel. Both post student text to the Anthropic API through a
Cloudflare Worker and need their own API key set as a Worker secret. They were left
out of this build; add them by copying `tutor.html` / `dossier.html` + `tutor-proxy/`
and pointing them at a new Worker.
