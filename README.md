# InWordDB — Infant Word Segmentation Database

One of the first things infants seem to extract and encode in memory are recurring words. This repository nucleates meta-analyses of how infants encode words. The first analysis included here concerns infants' extraction of words from native, natural speech, as evidenced in paradigms built on listening times.

This repository contains the database, the preprocessing and analysis scripts, and the intermediate data files needed to reproduce the results reported in the paper below.

For the most up-to-date version of the dataset, please turn to https://langcog.github.io/metalab/ or metalab.stanford.edu

## Associated publication

Bergmann, C., & Cristia, A. (2016). Development of infants' segmentation of words from native speech: A meta-analytic approach. *Developmental Science*, 19(6), 901–917. https://doi.org/10.1111/desc.12341

If you use this database or these scripts, please cite the paper above.

## Authors and contact

- Christina Bergmann
- Alejandrina Cristia

Project page: https://sites.google.com/site/inworddb/db/files
Contact: inwordb@gmail.com, chbergma@gmail.com

## Repository contents

### Data files

- **InWordDB.csv** — the raw database (599 records). One row per experimental condition. Includes study metadata, infant demographics, stimulus and task properties, and the dependent measures (listening times, exact *t* values, etc.) used to compute effect sizes.
- **InWordDB_ESg.csv** — output of script 1 (495 records). The raw database restricted to native-language conditions, with computed effect sizes (Hedges' *g*), standard errors, weights, and the linguistic-difficulty composite.
- **InWordDBnoOutnoRM_ESg.csv** — output of script 2; this is the analysis dataset. Repeated measures are collapsed to one effect size per unique infant group and one outlier is removed. **168 of its rows carry a non-missing effect size — these are the 168 effect sizes analyzed in the paper.** (The file also retains placeholder rows for collapsed groups in which a field varied within the group; those carry `NA` for `ESg` and are dropped automatically by the analysis scripts.)
- **InWordDB_preprocessed.csv** — a saved copy of the post-script-1 data, equivalent to `InWordDB_ESg.csv`. Not read by any script; retained for convenience and may be removed.

### Scripts (run in numerical order)

- **_1_DataPreparation_InWordDB.R** — reads `InWordDB.csv`, excludes non-native conditions, imputes missing familiarity–novelty correlations (with a fixed seed), computes the linguistic-difficulty composite and effect sizes, and writes `InWordDB_ESg.csv`.
- **_2_MetaAnalysis_InWordDB.R** — reads `InWordDB_ESg.csv`, collapses repeated measures, removes the outlier, writes `InWordDBnoOutnoRM_ESg.csv`, and runs the main models: overall random-effects model, polynomial age model, the difficulty + native-language meta-regressions, and the paired younger/older age-group analysis.
- **_3_Figures.R** — reproduces the figures: the Hunter & Ames (1988) schematic, the funnel plot, effect size as a function of age, and the per-infant-group forest plot.
- **_4_PostHocAnalyses.R** — runs the publication-bias analyses: relative frequency of novelty preferences over years (Spearman correlation), output formatted for the p-curve app, and the simulation estimating how many unpublished novelty effects would be needed to flip the result for older infants (with a fixed seed).

### Other files

- **LICENSE** — the license for this repository (see below).

## How to reproduce

1. Install [R](https://www.r-project.org/) (and optionally RStudio).
2. Install the required packages:
   ```r
   install.packages(c("meta", "metafor", "Hmisc", "ggplot2"))
   ```
3. Set the working directory to the folder containing the scripts and data (`setwd("/path/to/this/folder")`).
4. Run the scripts in order: `_1` → `_2` → `_3` → `_4`. Each script reads the output of the previous one, so you can also start from any provided intermediate file.

Two random seeds (`set.seed(111)`) are set — one for the correlation imputation in script 1, one for the simulation in script 4 — so those steps are deterministic across runs.

### Reproducibility notes

- **Record your environment.** Results depend on package versions; `metafor::rma` estimator defaults can change between releases. Please run `sessionInfo()` and note the R and package versions you used. (A small difference in the heterogeneity *Q* statistic versus the published value is expected across `metafor` versions and does not affect any conclusion.)
- **The paired age-group analysis uses 64 effect sizes,** not the 70 rows where `MultipleAgeGroups == 1`: six of those rows have a missing `MultipleAgeGroupsYO` value, which `rma` drops automatically.
- **Ordered factors** (e.g. `as.ordered(MultipleAgeGroupsYO)`, `as.ordered(difficulty)`) use polynomial contrasts, so the reported coefficients are on the contrast scale rather than raw level differences.

## License

This project is licensed under the GNU General Public License — see the `LICENSE` file for the full text.
