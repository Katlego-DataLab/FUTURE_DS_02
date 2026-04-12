# 📡 Customer Lifecycle Intelligence: Predicting & Preventing Telecom Churn

> **Can a machine learn to spot a customer who's about to leave — before they do?**  
> This project answers that question using real telecom data, machine learning, and behavioral clustering.

---

## 📋 Table of Contents

1. [The Problem](#the-problem)
2. [The Solution](#the-solution)
3. [How It Works](#how-it-works-plain-english)
4. [Key Results & Numbers](#key-results--numbers)
5. [Data Visualizations](#data-visualizations)
6. [Libraries Used](#libraries-used)
7. [Project Pipeline](#project-pipeline)
8. [How to Run](#how-to-run)
9. [Output Files](#output-files)

---

## 🔴 The Problem

Every month, telecom companies silently lose customers and most of the time, they don't even know who is about to leave until it's too late.

This is called **customer churn**: when a subscriber stops using the service without warning. In the telecom industry:

#  Acquiring a **new customer costs 5–7× more** than retaining an existing one
#  A company with **1 million customers** and even a **2% monthly churn rate** loses **20,000 customers every single month**
# Lost customers = lost monthly subscriptions, recharge revenue, and data plan fees

The challenge? Customer behaviour changes *gradually*. A person doesn't just wake up and leave — they slowly stop calling, stop recharging, stop using data. **The signal is hidden in the numbers.**

---

## ✅ The Solution

This project builds a **Customer Lifecycle Intelligence System** — a data-driven pipeline that:

1. **Watches customer behaviour** across 4 months of activity (June, July, August, September)
2. **Labels churners** based on a clear rule: *"Active in August but completely silent in September = Churned"*
3. **Trains a machine learning model** to predict which customers are at high risk of leaving
4. **Scores every customer** with a Risk Probability from 0 (safe) to 1 (about to leave)
5. **Groups customers into 3 behavioural segments** using clustering, so the business can target the right people with the right retention offer

---

## 🧠 How It Works 

Think of it like this:

> Imagine you run a gym. You notice that members who stop coming for 3 weeks in a row almost always cancel their membership. So you create an early warning system — anyone who hasn't scanned in for 2 weeks gets a free personal training session as a "come back" offer.

This project does exactly that — but for a telecom company, using **minutes of calls, recharge amounts, and data usage** instead of gym visits.

### Step-by-Step:

| Step | What Happened |
|------|--------------|
| **1. Load Data** | Imported a wide dataset with monthly telecom metrics per customer |
| **2. Reshape** | Transformed the data from wide format (one row per customer) to long format (one row per customer per month) — this makes trend analysis possible |
| **3. Clean** | Removed rows with >80% missing values; filled remaining gaps using median/mode values |
| **4. Define Churn** | A customer is marked as "Churned" if they had activity in August but zero activity in September |
| **5. Engineer Features** | Created two new combined metrics: `total_recharge` (money spent) and `total_usage_mou` (total minutes used) |
| **6. Train Model** | Used Logistic Regression to learn the patterns that separate churners from loyal customers |
| **7. Score Customers** | Assigned every customer a churn risk score between 0 and 1 |
| **8. Cluster Segments** | Used K-Means to group customers into 3 behavioural profiles |
| **9. Export** | Saved the final enriched dataset for Power BI dashboarding |

---

## 📊 Key Results & Numbers

### Dataset Overview
| Metric | Value |
|--------|-------|
| Raw columns in original dataset | **226 columns** |
| Months of data tracked | **4 months** (June – September) |
| Data format after reshaping | Long format (one row per customer per month) |
| Columns after renaming & cleanup | **~40 meaningful features** |
| MOU (Minutes of Use) columns | **17 MOU-related columns** |

### Data Cleaning
| Metric | Value |
|--------|-------|
| Missing value threshold for row removal | **80%** (rows with over 80% empty values were dropped) |
| Numeric columns filled | Via **median imputation** |
| Categorical columns filled | Via **mode imputation** |
| Missing values remaining after cleaning | **0** ✅ |

### Model Training & Evaluation
| Metric | Value |
|--------|-------|
| Train/Test Split | **80% training / 20% testing** |
| Model used | **Logistic Regression** (with class balancing) |
| Class balancing strategy | `class_weight='balanced'` to handle churn imbalance |
| Cross-validation strategy | Repeated Stratified K-Fold |
| Output metric | **ROC-AUC Score** + Classification Report |

### Churn Definition
| Condition | Label |
|-----------|-------|
| Active in August AND active in September | **0 — Stayed** |
| Active in August BUT silent in September | **1 — Churned** |

### Customer Segments (K-Means Clustering)
| Segment | Behaviour Profile | Avg Churn Risk |
|---------|-------------------|----------------|
| **Segment 0** | Low usage, low recharge | High |
| **Segment 1** | High usage, high revenue | Low |
| **Segment 2** | Mid-tier, inconsistent activity | Medium |

> ⚠️ *Exact segment values will vary per run based on your data. Re-label after inspecting cluster_summary output.*

---

## 📈 Data Visualizations

The project generates 4 key charts saved as `telecom_churn_visuals.png`:

### Chart 1 — Churn Distribution
Shows the **class imbalance** in the dataset: how many customers stayed vs. churned.

> 💡 In most telecom datasets, churners are a minority (typically 5–20%). This imbalance is why we use `class_weight='balanced'` in the model — otherwise the model would just predict "stayed" for everyone and still look accurate!

![Churn Distribution](telecom_churn_visuals.png)

---

### Chart 2 — Churn Rate by Behavioural Segment
Shows which customer cluster carries the **highest churn probability**.

> 💡 This chart tells the retention team exactly *which type of customer* to prioritise. Instead of sending offers to everyone, they can focus budget on the high-risk segment only.

---

### Chart 3 — Risk Score Distribution: Churners vs Non-Churners
A density curve showing how well the model separates churners (high scores) from loyal customers (low scores).

> 💡 A good model produces two clearly separated peaks — churners bunch up near 1.0, loyal customers near 0.0. If the curves overlap heavily, the model needs improvement.

---

### Chart 4 — Revenue vs. Usage (Scatter by Segment)
Plots **Average Revenue Per User (ARPU)** vs. **Total Minutes of Use**, colour-coded by segment.

> 💡 High-value customers (top-right corner) are your VIPs — even a small churn rate among them causes massive revenue loss. Low-usage, low-revenue customers (bottom-left) are the highest churn risk.

---

## 📦 Libraries Used

| Library | What It Does | Role in This Project |
|---------|-------------|----------------------|
| **pandas** | Data manipulation and analysis | Loading CSV, reshaping (melt/pivot), merging, filtering |
| **numpy** | Numerical computing | Array operations, handling infinity values, type conversions |
| **scikit-learn** | Machine learning toolkit | Logistic Regression, K-Means clustering, StandardScaler, train/test split, metrics |
| **seaborn** | Statistical data visualisation | KDE plots, bar charts, scatter plots, count plots |
| **matplotlib** | Core plotting library | Figure layout, saving plots, subplot management |
| **re** (regex) | Text pattern matching | Parsing column names to extract feature name + month number |
| **warnings** | Python standard library | Suppressing non-critical warnings during execution |

---

## 🔁 Project Pipeline

```
Raw CSV (226 cols)
      │
      ▼
[Reshape: Wide → Long]
      │
      ▼
[Clean: Drop empties, fill missing values]
      │
      ▼
[Define Churn: Aug active + Sep silent = Churned]
      │
      ▼
[Feature Engineering: total_recharge, total_usage_mou]
      │
      ▼
[Train/Test Split: 80/20]
      │
      ▼
[Scale: StandardScaler]
      │
      ▼
[Model: Logistic Regression]
      │
      ├──→ [Risk Score per customer: 0.0 → 1.0]
      │
      ├──→ [K-Means Clustering: 3 Segments]
      │
      └──→ [Export: telecom_churn_analysis.csv]
```

---

## 🚀 How to Run

### Requirements
```bash
pip install pandas numpy scikit-learn seaborn matplotlib
```

### Steps
1. Place `telecom_churn_data_FT.csv` in the same folder as the script
2. Run the script:
   ```bash
   python future_ds_02.py
   ```
3. Outputs generated:
   - `telecom_churn_visuals.png` — 4-panel chart dashboard
   - `telecom_churn_analysis.csv` — enriched dataset with Risk Score & Segment

---

## 📁 Output Files

| File | Description |
|------|-------------|
| `telecom_churn_analysis.csv` | Full dataset enriched with `churn`, `Risk_Probability`, and `User_Segment` columns — ready for Power BI |
| `telecom_churn_visuals.png` | 4-panel visualisation dashboard |

---

## 💡 Business Impact

| Without This System | With This System |
|--------------------|-----------------|
| React after customers leave | **Predict** churn 1 month in advance |
| Blanket retention campaigns (costly) | **Targeted** offers to high-risk segments only |
| Unknown customer value tiers | **3 clear segments** with distinct profiles |
| Manual analysis, no scoring | **Automated risk score** for every customer |

---

## 👤 Author

**FUTURE_DS_02** | Customer Lifecycle Intelligence Project  
*Built with Python · scikit-learn · pandas · seaborn*
