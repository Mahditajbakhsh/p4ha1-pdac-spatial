# P4HA1 in PDAC: spatial transcriptomics analysis

Analysis code for a study of **P4HA1** (collagen prolyl 4-hydroxylase alpha-1) in
pancreatic ductal adenocarcinoma, using Visium spatial transcriptomics across 23
tumour sections.

Izzi Lab, ECM-Hypoxia unit, Faculty of Biochemistry and Molecular Medicine,
University of Oulu.

> **Status: unpublished work in progress.** The numbers in this repository are not
> final and have not been peer reviewed. Do not redistribute.

---

## What the analysis does

For every Visium spot, the pipeline scores a set of gene modules (hypoxia,
collagen synthesis, ER stress, ERAD, CAF subtypes, epithelial) and models whether
P4HA1 is detected in that spot. Each tumour is fitted separately and the
per-tumour coefficients are then combined by DerSimonian-Laird random-effects
pooling. Results are reported as odds ratios per one within-tumour standard
deviation of a module score.

A central rule in the code: any module containing P4HA1 or P4HB is circular as a
predictor of P4HA1 detection. `CollagenMaturation` is therefore scored and
plotted descriptively only, and is excluded from `LOGIT_MODULES`. Sections 11.0,
15.1, 18.1 and 24.2 re-audit this automatically and halt on failure.

## Repository layout

```
pipeline/p4ha1_ecm_pipeline_v4.R   the complete pipeline, sections 0 to 26
docs/section_map.md                what each section does, with line numbers
docs/local_paths.md                the paths you must set before running
figures/                           exported figures from section 12 and later
```

## Running it

The script is written to run **top to bottom in a fresh R session**. Sections are
not independent: later sections read objects, helper functions and run-state flags
defined earlier, and `assert_run_state()` halts if a section is re-entered below
its own settings block.

1. Install the required packages (below).
2. Edit the paths listed in `docs/local_paths.md`.
3. Place the 23 `*-SeuratObj.rds` files in `DOWNLOAD_DIR`.
4. Source the script from the top.

### Required packages

`Seurat`, `SeuratObject`, `ggplot2`, `dplyr`, `tidyr`, `patchwork`

### Optional packages

Used when present, skipped silently when absent: `viridis` (colour scales),
`spdep` (spatial statistics), `ppcor` (partial correlations), `metafor`
(cross-check of the pooling in section 21), `synapser` (only needed when
`INPUT_MODE` is `"manifest"` and `USE_SYNAPSER` is `TRUE`).

### The depth adjustment switch

`ADJUST_DEPTH` is defined once, at line 2178, and every section downstream reads
it. With `ADJUST_DEPTH <- TRUE` all files written by sections 13 onward get
`_depthadj` appended, so adjusted and unadjusted results sit side by side rather
than overwriting each other. Section 17 reads both versions and prints how far
each headline number moved.

## Data

The spatial transcriptomics data are from the HTAN WUSTL PDAC atlas and are not
included here. Only code and exported figures are version controlled; `.gitignore`
excludes `.rds`, `.csv` and other data and output files.

## Known limitations, as stated in the code header

These are the author's own notes at the top of the pipeline, kept here so they are
not lost:

* **Spatial autocorrelation between neighbouring spots is not modelled anywhere.**
  Every confidence interval in the pipeline is therefore narrower than it should
  be. Section 16 measured the size of the problem: of 1000 detection-matched
  random genes, 296 reach p below 0.05 where roughly 50 would be expected. Prefer
  empirical percentiles over model p values.
* **The cohort is read end to end many times.** Sections 9, 10, 11, 13, 14, 15 and
  16 each open every object. Collapsing them into fewer passes was deliberately
  not done, because section 11 drops a sample when gene coverage falls below
  threshold while section 10 does not, so a naive merge would change k and move
  the numbers.

## Reproducibility notes

* `SEED <- 1234` is set once and re-applied immediately before every
  `AddModuleScore` call, because that function samples its control genes. Without
  this, the same module scored in two sections is not the same variable.
* Section 13 onward writes a run manifest recording the run id, the depth flag,
  the seed, R and Seurat versions, gene-set hashes and the model formula. Diff two
  manifests whenever numbers move between runs.
