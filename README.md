# STAT340 — Compulsory Assignment

**Private repository.** This holds the graded compulsory assignment for STAT340
(Statistical Learning) at NMBU, spring 2025. It is deliberately not public: the
assignment and its data set are reused between cohorts, so publishing a worked
solution would undercut the course.

## The analysis

*Gender and MBTI-style personality traits as predictors of STEM interest and
instructional-style preference.*

Two independent samples of 5 000 Norwegian pupils each (train/test) completed an
online STEM-interest assessment from the National Centre for Science
Recruitment. The report asks two questions:

1. Do sex and MBTI-style traits predict overall STEM interest?
2. Can those same traits infer a pupil's preferred instructional-style persona?

Four methods, each fitted on the training set and validated on the untouched
test set:

| Method | Purpose | Headline result |
|---|---|---|
| Multiple linear regression | Mean STEM interest ~ sex + MBTI | Sex (M) β = +0.25, Feeling β = –0.19, all p < 0.001; R² = 0.036 |
| PCA | Compress 9 instructional-preference items | PC1 20.7 %, PC2 19.2 % of variance |
| PAM clustering | Identify learner personas on PC1–PC2 | k = 2, avg. silhouette 0.341 |
| Logistic regression | Predict persona from sex + MBTI | 64 % accuracy on the test set |

## Contents

```
report/
  stat340-report.Rmd    the analysis
  stat340-report.html   knitted output (the submitted artefact)
data/
  CompulsorySTAT340.Rdata   Train, Test, and the Variables_names dictionary
```

## Reproducing

```r
rmarkdown::render("report/stat340-report.Rmd")
```

Requires `dplyr`, `tidyr`, `ggplot2`, `knitr`, `broom`, and `cluster` — all on
CRAN, no compiler toolchain needed. `set.seed(340)` is set at the top, so
repeated knits give identical output.

## Corrections made when this was cleaned up

The version committed here is a consolidation of eight overlapping drafts. Along
with merging them, the following substantive errors in the original were fixed:

- **PCA test-set projection.** The PCA was fitted on `scale(Train[, items])`,
  so the stored centre and scale belonged to already-standardised data. Applying
  `predict()` to the raw test items therefore produced incorrectly scaled test
  scores. Now fitted with `prcomp(Train[, items], scale. = TRUE)`.
- **Mismatched cluster labels.** The training clusters were labelled
  `Discussion-oriented` / `Independent/hands-on` while the test clusters were
  labelled `Cluster1` / `Cluster2`, so the confusion matrix compared two
  differently-labelled factors. Both now use a single `persona_levels` vector.
- **Item count.** The text described "12 instructional-style items (I1–I12)";
  the delivered data contains **nine** (I2, I5 and I11 are absent). The PCA was
  always running on nine.
- **Item names.** Several loadings were attributed to the wrong questionnaire
  items — I7 and I8 were swapped, and I9 was described as "Dialog Inspirational"
  when it is "Practical Problem based". The loadings table now joins its
  descriptions from the dataset's own `Variables_names` dictionary so the labels
  cannot drift again. The numeric loadings were correct throughout.
- **Reported R².** The text stated R² = 0.062; the model gives **0.036**. Both
  this and the test accuracy are now computed inline rather than typed in.
- **Plot ordering.** The cluster scatter plot referenced `learner_cluster2` and
  `medoids2` roughly 25 lines before either was created, and a stray `+` after
  `labs()` detached `theme_minimal()` into a separate expression that errored at
  runtime. The document did not knit end-to-end as written.

Dead chunks, a duplicated section heading, a duplicated coefficient table, and a
chunk referencing an undefined `pam_best` object were also removed.
