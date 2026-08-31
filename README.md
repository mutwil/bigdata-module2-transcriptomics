# Big Data in Biotechnology — Module 2: Transcriptomics (course workbook)

Static Quarto site for Module 2 of NPLK19000U. **Nothing runs in the browser** —
every code block is meant to be copied into RStudio on ERDA (or the student's own
machine). All paths assume one folder, `~/work/module2`; the appendix on the page
has the complete Absalon→ERDA file manifest.

**Live site:** https://mutwil.github.io/bigdata-module2-transcriptomics/

## Contents

- schedule for weeks 39–41 with per-block times, slides and materials
- Session 1: read QC with dada2 (`plotQualityProfile`, expected errors, `filterAndTrim`)
- Session 2: kallisto in the terminal; building and sanity-checking the count matrix
- Session 3: the full DESeq2 workflow, incl. implementing median-of-ratios by hand
- Session 4: enrichment (`phyper`/`fisher.test`) with a positive control and an
  honestly-null result, clustering, threshold sensitivity
- Session 5: pointers for the group assignment
- appendix: connecting to RStudio on ERDA (DAG, the course notebook image, `~/work`)

"Your turn" exercises use folded `callout-tip` solutions.

## Layout

```
index.qmd              the workbook (plain markdown — code blocks are NOT executed)
_quarto.yml            site config
lesson.css             styles: FILES & SETUP box, TERMINAL box, provenance notes, schedule table
external-links.html    off-site links open in a new tab
data/gene_sets.csv     keyword-derived functional categories (Session 4; fetched by
                       the page's download.file() one-liner)
.github/workflows/     quarto render + deploy to Pages on push (no R needed in CI)
```

## Notes for teachers

- `gene_sets.csv` is keyword-derived from the annotation text, not curated GO/PFAM —
  the page says so to students. With InterProScan/eggNOG output it could be replaced
  by real terms, which would likely turn Session 4's null result into a positive one.
- Session 2.4 deliberately walks students into discovering that
  `abundance_files_from_kallisto.zip` contains CON1/CON2 as byte-identical
  duplicates — a real error in the archive worth fixing at the source.
- The file manifest assumes `kallisto` and `GSE103878_1104.transcripts.fasta` are
  posted on Absalon (in 2025 they came via an ERDA share link).
- An earlier version of this site ran the exercises in the browser via webR
  (quarto-live); see git history before September 2026 if that is ever wanted again.
