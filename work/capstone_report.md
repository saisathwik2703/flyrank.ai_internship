# Which pages should be reviewed first for content refresh?

- Author: Saisathwik
- Lane: Refresh / Content Opportunity Scoring
- Repo: https://github.com/saisathwik2703/flyrank.ai_internship
- Date: 2026-08-31

## Abstract

This project asks which content pages should be reviewed first when editorial capacity is limited. It uses the FlyRank ML Internship data framework, whose warehouse release contains 78,835,655 daily performance rows plus pseudonymized content and client dimensions, while the reproducible benchmark reported here uses the repository's verified 30,000-row anonymized starter slice. A transparent refresh-opportunity rule is compared with logistic regression, a decision tree, and a random forest under the same client-holdout evaluation design, with identifiers excluded from features and leakage-prone fields treated conservatively. On the verified starter benchmark, Precision@50 rises from 0.24 for the baseline to 0.74 for the random forest, with ROC AUC rising from 0.627 to 0.750. The result supports a decision-support workflow for prioritizing human review, not a claim that editing a ranked page will cause recovery or that the model predicts Google's algorithm.

> Scope note: the public repository's Week 5-7 personal notebooks are currently blank skeletons. This report therefore uses only metrics already verified in the repository's committed starter benchmark and does not claim a new full-warehouse training run.

## 1. Problem framing

The FlyRank content problem behind this case study is practical: with a large content inventory and limited editorial capacity, which pages deserve review first? The unit of analysis is a page; the output is a ranked queue with scores and reason codes. Wrong high-priority calls waste editorial capacity, while missed positives delay attention to potentially valuable pages. Top-of-queue precision is therefore operationally important.

## 2. Data

Warehouse build: `flyrank_pseudonymized_warehouse_release_v20260703`, export date 2026-07-03.

| Table | Rows | Grain |
|---|---:|---|
| `dim_clients` | 104 | one row per pseudonymized client |
| `dim_content` | 519,606 | one row per pseudonymized content item |
| `fact_content_daily_performance` | 78,835,655 | daily × client × content |
| `fact_content_query_90d` | 2,414,248 | client × content × query hash |

Daily facts cover 2025-01-27 through 2026-06-30. The freshest 3 days were excluded to avoid incomplete newest-day facts. Raw client names, domains, URLs, queries, titles, keywords, and reverse mappings are excluded. Hash IDs are used only for joins/grouping, never as predictive features.

The benchmark results in this report come from the bundled 30,000-row anonymized starter slice because those are the verified model outputs available in the repository.

## 3. Methodology

Starter target: `is_declining_label = trend_direction == "down"`. This is a current-window proxy and is weaker than a future-window capstone target.

Baseline:

```text
baseline_refresh_score =
  0.40 * visibility_score
+ 0.30 * freshness_risk_score
+ 0.25 * position_opportunity_score
+ 0.05 * depth_gap_score
```

Reason codes include `stale_visible_page`, `declining_with_demand`, `thin_visible_page`, `page_one_decay_risk`, `low_ctr_visible_page`, and `low_engagement_visible_page`.

Models compared on the same client-holdout split: logistic regression, decision tree, and random forest. Metrics: ROC AUC, average precision, and Precision@50.

Leakage checks: IDs are excluded from features; product decisions/scores are not used; target-window measurements cannot appear in features; any target-derived current-window fields must be removed when they leak the answer.

## 4. Results

| Method | ROC AUC | Average precision | Precision@50 |
|---|---:|---:|---:|
| Baseline rules | 0.627 | 0.468 | 0.240 |
| Logistic regression | 0.700 | 0.522 | 0.400 |
| Decision tree | 0.742 | 0.575 | 0.540 |
| Random forest | **0.750** | **0.618** | **0.740** |

At K=50, the baseline identifies about 12 positives while the random forest identifies about 37, an approximately 3.1× improvement in top-50 precision on the starter slice.

## 5. Limitations & honest framing

These metrics are not a full-warehouse benchmark. The starter target is a current-window proxy, not an ideal future outcome. Warehouse client histories are unbalanced and tracking availability differs by client. The analysis is observational and cannot establish that a refresh causes recovery. Decline can be confounded by consolidation, seasonality, SERP changes, or low-volume noise.

## 6. Ranked recommendations

1. Review visible pages with sustained decline and meaningful demand first.
2. Protect page-one performers showing decay risk after checking seasonality and related-page consolidation.
3. Investigate low-CTR pages only in the context of position and sufficient impression volume.
4. Treat staleness and thinness as reason codes, not proof; require demand/evidence.
5. Keep a human reviewer in the loop and monitor drift, coverage, precision, base rate, and feature distributions.

## 7. Reproducibility

```bash
git clone https://github.com/saisathwik2703/flyrank.ai_internship.git
cd flyrank.ai_internship
pip install -r requirements.txt
python scripts/run_all.py
```

Notebook: https://github.com/saisathwik2703/flyrank.ai_internship/blob/main/work/notebooks/capstone.ipynb

For a full warehouse rerun, use the fixed 2026-07-03 release and preserve feature/target windows, grouped or time-aware validation, and leakage checks.

## 8. Acknowledgments & data credit

Built on the [FlyRank ML Internship dataset](https://flyrank.ai/).


## 9. Five-minute demo outline

**0:00–0:40 — Question.** Explain the FlyRank content problem: with a large content inventory and limited editorial capacity, which pages should be reviewed first?

**0:40–1:30 — Data & method.** Introduce the pseudonymized warehouse context and the verified 30,000-row starter benchmark. Explain the decline proxy, transparent refresh baseline, client-holdout validation, and leakage controls.

**1:30–2:45 — One chart.** Show the Precision@50 comparison: baseline 0.24, logistic regression 0.40, decision tree 0.54, random forest 0.74.

**2:45–3:45 — One honest result.** On the verified starter benchmark, the random forest reaches 0.74 Precision@50 and 0.750 ROC AUC. This is not presented as a full 78.8M-row model result or causal proof.

**3:45–5:00 — Recommendation.** Review visible pages with sustained decline and meaningful demand first, while keeping a human reviewer in the loop.

## 10. Shareable cuts

### Short social post

I built a content-opportunity ranking workflow for my FlyRank ML Internship capstone. I compared a transparent refresh-rule baseline with logistic regression, a decision tree, and a random forest using client-holdout validation, top-of-queue Precision@50, ROC AUC, and explicit leakage checks. On the verified 30,000-row anonymized starter benchmark, the random forest reached 0.74 Precision@50 vs 0.24 for the baseline — a useful signal for prioritizing human content review, not a claim of causality or search-engine prediction.

### Employer-facing summary

I built an explainable content-review ranking workflow that turns search-performance signals into a prioritized editorial queue. I worked from FlyRank's pseudonymized warehouse framework and reported verified results from the 30,000-row anonymized starter benchmark, comparing rule-based and machine-learning approaches with client-holdout validation and leakage controls. The random forest reached 0.74 Precision@50 versus 0.24 for the baseline, showing how ML can improve review prioritization while keeping the final decision with a human reviewer.
