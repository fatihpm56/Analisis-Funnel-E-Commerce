# 🛒 E-commerce Funnel Analysis

**From 407K Visitors to Buyers: A Conversion Funnel Analysis of an Electronics E-commerce Store**

Data period: September 24, 2020 – February 28, 2021 | Tools: Python (Google Colab), Power BI

<img width="1225" height="733" alt="Dashboard Analisis Funnel E-commerce" src="https://github.com/user-attachments/assets/67a76123-77e4-4f40-83b1-cfba1ca1ac87" />

---

## 📑 Table of Contents

1. [Project Background](#1-project-background)
2. [Problem Statement, Analysis Objectives, and Business Problem](#2-problem-statement-analysis-objectives-and-business-problem)
3. [Analysis](#3-analysis)
   - [Data Understanding](#31-data-understanding)
   - [Data Preprocessing](#32-data-preprocessing)
   - [Analysis and Insights](#33-analysis-and-insights)
4. [Recommendations and Actionable Insights](#4-recommendations-and-actionable-insights)
5. [Tech Stack](#5-tech-stack)
6. [Repository Structure](#6-repository-structure)
7. [Author](#7-author)

---

## 1. Project Background

This online electronics and computer store recorded **~884K user behavior events** (view, cart, purchase) from **407K unique users** over 5 months (September 24, 2020 – February 28, 2021). Large traffic does not automatically translate into revenue: most visitors drop off partway through their shopping journey.

This project turns user behavior data into business decisions that can increase conversion and revenue **without increasing traffic acquisition costs**.

**Expected outcomes**
- Funnel drop-off points are identified.
- Insights that can be acted on directly.
- An interactive dashboard for ongoing monitoring.

**Beneficiaries:** the marketing team, the catalog & merchandising team, and e-commerce management.

| 407K | 884K | 5 months | 5.13M |
|:--:|:--:|:--:|:--:|
| unique users | events after cleaning | Sep 24, 2020 – Feb 28, 2021 | total revenue |

---

## 2. Problem Statement, Analysis Objectives, and Business Problem

### Problem Statement
The online store attracts large traffic, but only **5.2% of unique users** end up making a purchase, and only **4.7% of product views** result in a transaction. The funnel shows that the biggest leakage happens at the early stage: only **6.8% of views** move on to cart, while **~69% of carts** are successfully converted into purchases. In other words, visitors who have added a product to their cart tend to be serious buyers, while most visitors are not yet interested or confident enough to take the step to the cart.

### Analysis Objectives
Analyze user behavior across the funnel (*view → cart → purchase*) to:
- identify **where and why** users drop off,
- find which **categories, brands, price ranges, and times** have high or low conversion,
- determine the **most effective actions** to increase conversion and revenue.

### Business Problem
| | |
|---|---|
| **Problem** | Only 5.2% of users have ever purchased; 4.7% of views end in a transaction; ~50% of products in carts are not bought. Revenue also depends heavily on a single category (*computers*, ~73%). |
| **Why it matters** | Small improvements in the funnel directly impact revenue without adding traffic acquisition cost. |
| **Impact if solved** | Clear improvement priorities, better-targeted promotions, and reduced dependence on a single category. |

### Business Questions
1. At which funnel stage does conversion drop, and which category has the weakest conversion?
2. How does price influence user decisions, both for adding a product to the cart and for buying it?
3. When (hour and month) is conversion highest and lowest, and what is the conversion trend?
4. Which brands are most effective at turning views into purchases, and which have high traffic but low conversion?

---

## 3. Analysis

### 3.1 Data Understanding

**Source:** `events.csv`, a website activity log recorded automatically per event.

| Aspect | Description |
|---|---|
| Size | 885,129 rows × 9 columns |
| Columns | `event_time`, `event_type`, `product_id`, `category_id`, `category_code`, `brand`, `price`, `user_id`, `user_session` |
| Event types | `view`, `cart`, `purchase` |
| Coverage | ~407K users, ~53K products, ~490K sessions |
| Period | Sep 24, 2020 – Feb 28, 2021 |
| Time zone | UTC |

**Data quality issues found**
- **655 duplicate rows**
- **162 rows** with missing `user_session`
- `category_code` missing in ~27% of rows and `brand` in ~24%
- Extreme prices (maximum 64,771)

### 3.2 Data Preprocessing

| No | Step | Description |
|---|---|---|
| 1 | Remove duplicates | 655 duplicate rows dropped |
| 2 | Convert time | `event_time` converted to datetime (UTC) |
| 3 | Handle missing values | Rows without `user_session` dropped; missing `category_code` and `brand` filled with `unknown` |
| 4 | Split hierarchical category | `category_code` split into `cat1`, `cat2`, `cat3` |
| 5 | Time feature engineering | Created `date`, `month`, `hour`, `dow` (day of week) columns |
| 6 | Convert ID types | `product_id`, `category_id`, `user_id` cast to string |
| 7 | Flag price outliers | Prices above the 99.9th percentile flagged (`is_outlier_price`), **not removed**, to avoid biasing revenue |
| 8 | Save clean data | Final dataset of **884,312 rows** |

> **Note:** September 2020 only contains data from September 24 onward, so that month is partial and not directly comparable to other months.

### 3.3 Analysis and Insights

**Technique:** descriptive and exploratory analysis (funnel, category, price, brand, time patterns). Conversion formulas used:

- **View → Cart (v2c)** = cart count / view count
- **Cart → Purchase (c2p)** = purchase count / cart count
- **View → Purchase (v2p)** = purchase count / view count

#### 🔹 Funnel and Category
- Of **406.82K** users who viewed products, only **36.95K (9.1%)** added to cart and **21.30K (5.2%)** ultimately purchased.
- By events: 792.94K views → 54.03K carts → 37.34K purchases. **View → purchase is only 4.7%.**
- **The biggest drop occurs at the view → cart stage.**
- Categories with the weakest v2c: **apparel (0.85%)**, **country_yard (1.37%)**, and **medicine (2.79%)**.
- Above-average categories: **computers (9.89%)** and **stationery (8.79%)**; below average: electronics (5.84%), furniture (5.04%), construction (4.75%).

#### 🔹 Price Effect
Prices were divided into 5 buckets (quintiles): <21, 21–47, 47–96, 96–227, >227.

| Price range | View → Cart | Cart → Purchase |
|---|:--:|:--:|
| 1. Very cheap (<21) | 5.75% | 79.00% |
| 2. Cheap (21–47) | 6.40% | 81.03% |
| 3. Medium (47–96) | 5.88% | 69.75% |
| 4. Expensive (96–227) | 7.46% | 65.98% |
| 5. Very expensive (>227) | 8.63% | 55.63% |

**Insight:** the more expensive a product, the more often it is added to the cart, but the less often it is bought. The barrier for expensive products lies in the **final decision (checkout)**, not in interest.

#### 🔹 Time Patterns
- View → purchase conversion peaks at around **10:00 UTC (5.38%)** and is lowest at **01:00 UTC (3.61%)**.
- In the afternoon, traffic is high but conversion declines.
- **The monthly trend rises steadily** from **4.29% (Oct 2020)** to **5.17% (Feb 2021)**.

#### 🔹 Brand
- **Most effective:** sapphire (v2p 11.44%), msi (8.97%), gigabyte (7.65%). msi also generates the highest revenue among identified brands (~643K).
- **High traffic, low conversion:** asus (24K views, v2p 5.20%) and palit (21K views, v2p 5.02%, c2p only 48.06%).
- The **unknown** brand accounts for ~190K views (the largest share) with v2p of 5.00%, making many products hard to analyze and optimize.

#### 🔹 Power BI Dashboard
A one-page dashboard containing KPIs (Total Users and Revenue), funnel, monthly trend, hourly pattern, conversion by price, category, and brand. It includes **Brand, Category, DayName, and Month** filters to drill into segments.

---

## 4. Recommendations and Actionable Insights

| No | Recommendation | Action | Target / Impact |
|:--:|---|---|---|
| 1 | **Improve product pages** | Complete photos, specifications, and reviews for below-average categories (electronics, furniture, construction, plus apparel and country_yard); fill in missing catalog data so it displays | View → cart up from 6.8% to 7.5% |
| 2 | **Address expensive products** | Offer installments, warranty, free shipping, and abandoned cart reminders | Cart → purchase in the most expensive range up from 56% to 65% |
| 3 | **Schedule promotions by time pattern** | Concentrate campaigns, flash sales, and notifications at **09:00–11:00 UTC**; use time-limited promos in the afternoon; reduce ads in the early morning | More efficient and better-targeted promotions |
| 4 | **Manage the brand portfolio** | Maintain stock and pricing for msi, gigabyte, sapphire; investigate the cause of palit's low c2p; review weak-converting brands such as asus | Strengthen effective brands, fix weak ones |
| 5 | **Clean up catalog data** | Make brand and category fields mandatory (~a quarter of data is empty) | More accurate analysis and recommendations |

**Business impact:** improvement priorities become clear, promotions are better targeted, and dependence on a single category (*computers*, ~73% of revenue) can be reduced.

---

## 5. Tech Stack

- **Python** (Pandas, NumPy, Matplotlib, Seaborn, Plotly) on Google Colab, for cleaning and EDA
- **Power BI**, for the interactive dashboard
- **PowerPoint**, for presenting results

---

## 6. Repository Structure

```
├── README.md
├── Analisis_Funnel_E_Commerce.ipynb         # Cleaning & EDA notebook
├── Analisis_Funnel_Ecommerce_Fatih.pptx     # Presentation slides
├── Dashboard_Analisis_Funnel_E-commerce.png # Dashboard preview
└── data/                                    # (optional) events.csv
```

---

## 7. Author

**Fatih Putra M.**
Fresh Graduate in Geophysical Engineering (ITERA) | Aspiring Data Analyst

- LinkedIn: https://www.linkedin.com/in/fatihputramaulana/
- Email: fatihpm56@gmail.com
