# Cancer Clinical Trial Patient Selection — Unsupervised ML

**Type:** Solo Individual Exam

## Overview
Designed an unsupervised learning pipeline for Michigan Medicine to identify patients most likely to have cancer for enrollment in an expensive clinical trial. The challenge was maximising **precision** (not recall) — minimising false positives while guaranteeing at least 7 confirmed true cancer patients to achieve quorum for the trial.

## Client Constraint
> "The goal is NOT to identify all cancer patients. What matters is that if your methodology identifies someone as a cancer patient they should not turn out to be a False Positive. We are maximising Precision while identifying at least 7 True Positive patients."

## Dataset
Wisconsin Breast Cancer (WBC) data — 30 numeric health indicators across 378 patients.

## Pipeline

### Step 1 — PCA Dimensionality Reduction
- Ran PCA on 30 scaled predictors
- Evaluated variance capture at 90%, 95%, and 97% thresholds
- **Selected 95%**: 11 principal components capturing 96% of variance
- Rationale: 90% too aggressive (loses structure); 97% adds 5+ components for marginal gain

### Step 2 — Outlier Detection

**Local Outlier Factor (LOF, minPts = 10)**
- Scored each patient by isolation relative to neighbours in PCA space
- Tested quantile thresholds from 90th to 99.5th percentile
- Precision peaked around 33% at extreme thresholds while maintaining 7+ true positives

**DBSCAN (initial eps = 6, minPts = 10)**
- Identified noise points as likely cancer cases
- Initial run: 15 noise points, **60% precision**, 9 true positives
- Grid-searched eps from 4 to 7 to maximise precision while maintaining 7+ true positives
- **Tuned result: 77.8% precision** — strongest result in the entire analysis

### Step 3 — Clustering (K-means)
- Used WSS elbow and silhouette diagnostics to select k = 2
- Cluster 2 (37 patients): 15 cancer cases → **40.5% precision**
- Cancer cases did not form a tight separable group — clustering unsuitable for high-precision objectives

## Results
| Method | Precision | True Positives |
|---|---|---|
| Tuned DBSCAN | **77.8%** | 9 |
| Initial DBSCAN | 60.0% | 9 |
| K-means (k=2) | 40.5% | 15 |
| LOF (threshold tuned) | ~33% | 7+ |

## Conclusion
DBSCAN significantly outperforms clustering for high-precision patient selection tasks. Its ability to identify geometric outliers in PCA space, combined with parameter tuning, makes it the right tool when false positives carry a high cost.

## Tech Stack
`R` `PCA` `DBSCAN` `LOF` `K-means` `Unsupervised Learning` `Silhouette Analysis`

## Files
- `Cancer_Detection_Unsupervised_Learning.html` — Full rendered R Markdown report
- `Cancer_Detection_Unsupervised_Learning.Rmd` — Source R Markdown file
