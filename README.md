# Streaming Subscriber Churn Prediction & Next-Best-Action Engine

Predictive modeling, behavioral segmentation, and ROI-based retention recommendations for a 5,000-subscriber streaming platform.

Most churn systems send everyone the same discount. This project predicts who's likely to cancel, groups the at-risk subscribers by *why* they're at risk, and prices out a specific, cost-appropriate save for each group instead.

**[Open the interactive dashboard](dashboard/ott-churn-dashboard.html)** · **[Read the PDF report](report/OTT_Churn_Analysis_Report.pdf)**

## Results

| Metric | Value |
|---|---|
| Subscribers analyzed | 5,000 |
| Model accuracy (ROC-AUC) | 0.88 |
| Recall | 0.84 |
| At-risk subscribers identified | 2,874 |
| Estimated annual savings | ~$85,326 |

## How it works

1. **Predict.** A Logistic Regression classifier scores every subscriber's churn probability, using watch time, login recency, plan tier, monthly fee, and tenure. Logistic Regression was chosen over more complex models for coefficient interpretability, without giving up much on performance.
2. **Segment.** K-Means clusters the 2,874 medium- and high-risk subscribers into three behaviorally distinct groups, by engagement, plan tier, and login recency.
3. **Decide.** Each segment gets one recommended retention action, priced using a stated, adjustable assumption set rather than a single blanket offer.

## What actually drives churn

Two behaviors explain almost everything. Demographics explain almost nothing.

- Subscribers watching under 5 hours a week churn at **73%**. Past 20 hours, that drops to **9%** (r = -0.54).
- Once a subscriber has been quiet for 30+ days, churn nearly quadruples (r = +0.72). This is the strongest single predictor in the model.
- Basic-plan subscribers churn at roughly **1.4x** the rate of Standard or Premium.
- Age, gender, region, and device all sit within a few points of a coin flip. None of them meaningfully separate stayers from leavers.

## Segments and recommended actions

| Segment | Size | Root cause | Recommended action | Value recovered |
|---|---|---|---|---|
| Higher-value, going quiet | 907 | Pays above-average fees; median 50 days since last login | Proactive win-back outreach: one month free or a complimentary upgrade | $34,366 |
| Basic plan, going quiet | 1,185 | Lowest-cost plan; median 37 days since last login | Low-cost reactivation: reminder email/push with a free-week offer | $25,568 |
| Barely watching | 782 | Engagement collapsed to under 3 hours watched | Content-led re-engagement: personalized genre-based recommendation email | $25,392 |

Impact estimates assume a targeted intervention recovers 25% of subscribers who'd otherwise churn in that segment, and that a retained subscriber is worth 12 months of their monthly fee. That assumption is stated explicitly so it can be swapped for real historical win-back rates.

## Preview

<img src="assets/report-preview-drivers.png" width="420"> <img src="assets/report-preview-segments.png" width="420">

Full pages in [`report/OTT_Churn_Analysis_Report.pdf`](report/OTT_Churn_Analysis_Report.pdf).

## Repository structure

```
ott-churn-retention/
├── data/
│   ├── tableau_export_ott.csv         # subscriber-level data, model outputs, segment labels
│   └── segment_action_savings.csv     # segment-level rollup with recommended actions
├── dashboard/
│   └── ott-churn-dashboard.html       # self-contained interactive dashboard
├── report/
│   └── OTT_Churn_Analysis_Report.pdf  # static 3-page writeup of the same analysis
├── docs/
│   └── OTT_Churn_Project_Summary.pdf  # original one-page project summary
└── assets/                            # preview images used in this README
```

## The dashboard

`dashboard/ott-churn-dashboard.html` is a single static file, no build step or server required. Download it and open it directly in a browser, or host it for free on GitHub Pages.

It covers:

- The churn drivers above, charted from the full dataset
- The three segments with their root causes, recommended actions, and dollar impact
- A live explorer: filter all 5,000 subscribers by risk tier, segment, plan, region, or device, and watch the stats, a churn-probability scatter plot, and a sortable subscriber table update as you filter

It needs an internet connection to load its charting library and fonts from a CDN. Everything else, including all 5,000 subscriber records, is embedded in the file itself.

To host it on GitHub Pages: enable Pages in your repo settings pointing at this branch, then link directly to `dashboard/ott-churn-dashboard.html`.

## Tech stack

Python (pandas for data prep, scikit-learn for classification and clustering) for the analytical pipeline. Tableau for the original stakeholder dashboard. Chart.js for the interactive HTML console in this repo.

## Data

5,000-subscriber streaming platform sample covering subscription tier, watch hours, login recency, monthly fee, number of profiles, and churn outcome, alongside the model's churn probability, risk tier, and segment assignment for each subscriber.

---

Built by Shreyash. [Portfolio](https://shreyash021104.github.io/portfolio) · [X](https://x.com/Shreyash021104)
