# 🎧 Spotify Campaign A/B Test Analysis

## 1. Project Background

A music streaming platform developed a new personalization feature/algorithm intended to make the listening experience more relevant to each user. Before rolling it out to everyone, the team ran an A/B test comparing the new experience (**Target**) against the existing one (**Control**) on 100,000 users, split evenly at 50,000 per group.

After the test, the feature was assumed to be fully implemented on 8 March 2026. Daily Total Users data from 1–15 March 2026 was then used to check whether the real-world impact matched the experiment results.

## 2. Problem Statement, Analysis Objectives, and Business Problem

**Business Problem**
Should the company adopt the new personalization feature for all users? Rolling out a change that does not work wastes engineering effort and risks hurting user experience, while skipping one that does work means lost revenue.

**Problem Statement**
Does the Target variant produce a statistically and practically significant improvement over Control in engagement, listening duration, payment conversion, and Premium upgrades? Does the improvement carry over after full implementation?

**Analysis Objectives**
1. Test whether Target outperforms Control on four metrics: Engagement, Duration Played, Payment Rate, and Premium Rate.
2. Measure effect size to judge business relevance, not only statistical significance.
3. Check whether the conversion lift is driven by subscription-tier composition (confounding).
4. Monitor Total Users after implementation to validate the A/B test result.
5. Provide a data-backed rollout recommendation.

**Hypotheses** (α = 5%)
- H0: There is no difference in the mean/proportion of the metric between Target and Control.
- H1: The mean/proportion of the metric is higher in Target than in Control.

## 3. Analysis

### 3.1 Data Understanding

| Item | Detail |
|---|---|
| Experiment dataset | `spotify_campaign_data.csv`, 100,000 users (50,000 Control, 50,000 Target) |
| Monitoring dataset | `AB_Test_Post_Implement.csv`, daily Total Users, 1–15 March 2026 |
| Randomization unit | Per user, to avoid cross-group contamination |
| Primary metric | Payment Rate |
| Supporting metrics | Engagement, Duration Played, Premium Rate |

**Experiment variables**

| Variable | Description |
|---|---|
| Group | Control or Target |
| Engagement | Engagement score (0–1) |
| Duration Played | Listening time in minutes |
| Payment | Payment conversion (binary) |
| Subscription | Subscription tier (Free / Premium) |

With 50,000 users per group, the sample size far exceeds what is needed to detect small differences at 5% significance and 80% power, so confidence intervals are narrow.

### 3.2 Data Preprocessing

- Loaded both CSV files and inspected structure, data types, and group balance (50:50).
- Checked for missing values and duplicates.
- Derived binary indicators for Payment and Premium so they could be analyzed as proportions.
- For the monitoring data, parsed dates and split the period into **Week 1 (1–7 Mar)** as the pre-implementation baseline and **Week 2 (8–14 Mar)** as the post-implementation period.

### 3.3 Analysis and Insights

**Methods**
- Welch's t-test for continuous metrics (Engagement, Duration Played)
- Two-proportion z-test for binary metrics (Payment, Premium)
- Cohen's d for effect size
- Segment check by subscription tier to rule out confounding
- Linear regression for the post-implementation Total Users trend

**A/B Test Results**

<img width="1171" height="602" alt="Screenshot 2026-10-26 115143" src="https://github.com/user-attachments/assets/e3d8c3f2-17ef-4bff-8862-7107fcd373ab" />

| Metric | Control | Target | Difference | p-value | Conclusion |
|---|---|---|---|---|---|
| Engagement (mean) | 0.600 | 0.800 | +0.200 (+33.3%) | < 0.001 | Significant |
| Duration Played (min) | 120.1 | 159.9 | +39.8 (+33.2%) | < 0.001 | Significant |
| Payment Rate | 69.7% | 79.8% | +10.1 pp (+14.5%) | < 0.001 | Significant |
| Premium Rate | 59.7% | 80.3% | +20.5 pp (+34.4%) | < 0.001 | Significant |

**Insights**
- H0 is rejected for all four metrics.
- Effect sizes are large (Cohen's d ≈ 2.0 for Engagement, ≈ 1.3 for Duration Played), so the difference is meaningful in practice, not just statistically.
- The Payment Rate lift holds in both tiers: Target reaches 79.9% for Free and 79.8% for Premium users, versus about 69.5–69.9% in Control. The gain is a real effect of the variant, not a subscription-tier composition effect.
- Engagement and Duration Played rise together with conversion, which suggests that more relevant personalization drives users to listen more and then pay.

**Post-Implementation Monitoring**

<img width="889" height="419" alt="image" src="https://github.com/user-attachments/assets/67d36725-5604-490b-b3ba-e81d7617d87a" />

| Period | Avg. Users/Day | Change |
|---|---|---|
| Week 1 (1–7 Mar) | 16,184 | baseline |
| Week 2 (8–14 Mar) | 24,670 | **+52.4%** |

- The linear trend shows an increase of about +871 users/day (R² = 0.58, p = 0.0009).
- Saturday, historically the weakest day, became one of the strongest in Week 2 (26,207 users on 14 Mar vs 11,660 on 7 Mar), so the rise is more than normal weekly fluctuation.
- The direction agrees with the A/B test result.

**Limitations**
- The full-rollout date (8 March 2026) is an analytical assumption.
- 15 days is short and cannot rule out a novelty effect.
- The before/after comparison cannot fully exclude external factors such as concurrent campaigns.
- Guardrail metrics (churn, complaints, server load) are not covered by this data.

## 4. Recommendations and Actionable Insights

1. **Roll out the Target variant to all users permanently.** All four metrics improved significantly, and post-implementation data points the same way.
2. **Prioritize onboarding and communication for Free-tier users.** Target lifts Premium Rate by +20.5 pp (59.7% → 80.3%), so messaging that highlights the new personalization can accelerate Free-to-Premium conversion.
3. **Report Payment Rate (+10.1 pp, +14.5% relative) as the primary metric to stakeholders.** It is the closest to revenue, while Engagement and Duration Played explain why conversion rises.
4. **Keep monitoring for at least 4–8 weeks** to confirm the lift is sustained, and add guardrail metrics: churn rate, user complaints, and server load.
5. **Verify no other campaigns or promotions ran in early March 2026**, so the Total Users increase can be attributed to the Target feature.
6. **Track long-term downstream metrics** such as retention, customer lifetime value, and user satisfaction.

## 🛠️ Tech Stack

Python · Pandas · NumPy · SciPy · Statsmodels · Matplotlib · Jupyter Notebook / Google Colab

## 📁 Repository Structure

```
├── data/
│   ├── spotify_campaign_data.csv
│   └── AB_Test_Post_Implement.csv
├── notebook/
│   └── AB testing analysis of a Spotify personalization feature.ipynb
└── README.md
```

## 👤 Author

**Fatih Putra Maulana**
