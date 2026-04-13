# 🛒 Retail Analytica

A comprehensive end-to-end retail and marketing analytics project built with Python. This project covers the full data analytics pipeline — from raw data ingestion to executive-ready KPI Reports — applied to a 10,000-order retail dataset spanning January 2022 to February 2023.

---

## Project Structure

The project is organized across five analytical stages:

```
Retail_Analytica/
│
├── Part 1 — Data Acquisition & Setup
├── Part 2 — Data Cleaning & Preprocessing
├── Part 3 — Exploratory Data Analysis (EDA)
├── Part 4 — Customer Segmentation & Advanced Analytics
└── Part 5 — KPI Design & Executive Reporting
```

---

## Dataset Overview

| Attribute | Detail |
|---|---|
| Total Records | 10,000 orders |
| Columns (raw) | 19 |
| Columns (after feature engineering) | 45 |
| Analysis Period | Jan 2022 – Feb 2023 |
| Unique Customers | 1,986 |
| Unique Products (SKUs) | 499 |
| Product Categories | 4 (Electronics, Office Supplies, Furniture, Clothing) |
| Regions | East, West, Central, South |

Key fields include: `Order_ID`, `Order_Date`, `Ship_Date`, `Customer_ID`, `Segment`, `Region`, `Product_Category`, `Product_Sub_Category`, `Sales`, `Quantity`, `Discount`, `Profit`, `Shipping_Cost`, `Order_Priority`.

---

## Part 1: Data Acquisition & Setup

- Loaded raw retail sales CSV and validated structure
- Derived `Unit_Price` and `Revenue` from base fields
- Conducted initial inspection: shape, dtypes, value counts
- Identified minimal data quality issues: only 80 missing cells (0.04%) across `Customer_Name` and `Profit`, zero duplicates

---

## Part 2: Data Cleaning & Preprocessing

- **Missing value treatment**: Numerical columns filled with median; categorical columns filled with mode
- **Deduplication**: No duplicates found; index reset for integrity
- **Type conversion**: Date columns cast to `datetime64`; categorical columns to `category` dtype — reduced memory from 7.32 MB to 3.49 MB
- **Outlier detection (IQR method)**:
  - Sales: 328 outliers (3.28%), bounds [-71.87, 275.33]
  - Profit: 87 outliers (0.87%), bounds [-61.87, 101.47]
  - Quantity: No outliers
  - Outliers capped via Winsorization
- **Feature engineering (26 new features)**:
  - Time features: Year, Month, Quarter, Day of Week, Weekend flag, Month-start/end flags, Week of Year
  - Revenue metrics: Discount Amount, Net Revenue, Profit Margin, Profit Ratio
  - Delivery metrics: Delivery Days, Delivery Category
  - Customer flags: Order Count, Repeat Customer Flag
  - Product metrics: Product-level aggregated Sales, Average Sales, Order Count
  - Sales tier categorization

Post-cleaning: 10,000 complete rows, 0 missing values, 45 columns.

---

## Part 3: Exploratory Data Analysis (EDA)

### Univariate Analysis
Distributions plotted for `Sales`, `Quantity`, `Profit`, `Discount`, and `Unit_Price` with mean/median markers and skewness/kurtosis statistics.

### Bivariate Analysis

**Sales by Product Category:**
| Category | Total Sales | Avg Order | Orders |
|---|---|---|---|
| Electronics | $333,409 | $110.07 | 3,029 |
| Office Supplies | $328,391 | $111.36 | 2,949 |
| Furniture | $219,556 | $109.18 | 2,011 |
| Clothing | $217,841 | $108.32 | 2,011 |

**Sales by Region:** East leads in revenue (~$323,959), followed by other regions with relatively balanced distribution.

**Discount Impact:** Orders with no discount (40.3% of orders) and those with 11-20% discounts (34.4% of orders) constitute the bulk of revenue.

### Time Series Analysis
- Monthly sales trend tracked across 14 months (~$77K–$84K/month)
- Quarterly comparisons across 2022 and early 2023
- Day-of-week sales patterns analyzed

### Key EDA Findings
1. **Electronics** is the top revenue category at **30.3%** of total revenue
2. **96.3% customer retention rate** — 1,912 of 1,986 customers made repeat purchases
3. **Top 20% of products generate 27.4% of revenue** — Pareto principle partially confirmed (weaker concentration than typical)

---

## Part 4: Customer Segmentation & Advanced Analytics

### RFM Analysis
Calculated Recency, Frequency, and Monetary scores for all 1,986 customers:

| Metric | Mean | Median | Min | Max |
|---|---|---|---|---|
| Recency (days) | 81.6 | 58 | 1 | 408 |
| Frequency (orders) | 5.04 | 5 | 1 | 14 |
| Monetary ($) | $553.47 | $523.54 | $13.52 | $2,073.19 |

Customers were scored 1–5 on each dimension and segmented into: **Champions**, **Loyal Customers**, **Potential**, and **At Risk**.

### K-Means Clustering
Clustering performed on standardized RFM features. Optimal `k` selected via:
- Elbow Method (Inertia)
- Silhouette Score
- Davies-Bouldin Index

**Resulting Clusters:**
- Cluster 0 (VIP / High Frequency): Avg Recency ~46 days, Avg Frequency ~6.88 orders
- Cluster 1 (At Risk): Avg Recency ~112 days, Avg Frequency ~3.43 orders

PCA reduced RFM to 2 components explaining **94.51% of variance** (PC1: 69.98%, PC2: 24.53%), used for 2D and 3D cluster visualization.

### Cohort Analysis
- 14 monthly cohorts tracked from Jan 2022 onwards
- Month-0 retention: 100%; subsequent months show ~27–35% return rates
- Cohort retention heatmap built with Seaborn

### Customer Lifetime Value (CLV)
CLV calculated using: `AOV × Purchase Frequency × Assumed Lifespan (3 years)`

| CLV Metric | Value |
|---|---|
| Mean CLV | $7,637.86 |
| Median CLV | $2,379.05 |
| Min CLV | $253.80 |
| Max CLV | $411,235.34 |

Customers categorized into Low / Medium / High / Very High CLV tiers (~497 per tier).

### Market Basket Analysis
Attempted using `mlxtend` Apriori algorithm on 10,000 transactions. No frequent itemsets found above the 1% support threshold — suggesting diverse, non-repetitive product combinations in this dataset.

---

## Part 5: KPI Design & Dashboard Preparation

### Business KPIs

**Financial Performance:**
| KPI | Value |
|---|---|
| Total Revenue | $1,099,197.18 |
| Total Profit | $197,469.50 |
| Profit Margin | 17.96% |
| Avg Order Value (AOV) | $109.92 |
| Total Units Sold | 50,065 |

**Customer Metrics:**
| KPI | Value |
|---|---|
| Total Customers | 1,986 |
| Revenue per Customer | $553.47 |
| Avg Orders per Customer | 5.04 |
| Repeat Customer Rate | 96.27% |
| One-Time Customers | 74 |

**Product Metrics:**
| KPI | Value |
|---|---|
| Total SKUs | 499 |
| Avg Items per Order | 5.01 |
| Total Categories | 4 |

**Marketing Efficiency:**
- CLV/CAC Ratio, Customer Acquisition Cost, CAC Payback Period, Profit per Customer

### Monthly KPI Trends
14-month trend data computed for: Revenue, Orders, Customers, AOV, Units Sold, Revenue Growth %, Customer Growth %, and Order Growth %.


## Tech Stack

| Library | Purpose |
|---|---|
| `pandas` | Data manipulation |
| `numpy` | Numerical operations |
| `matplotlib` / `seaborn` | Static visualizations |
| `plotly` | Interactive charts |
| `scipy` | Statistical analysis |
| `scikit-learn` | K-Means clustering, PCA, StandardScaler |
| `mlxtend` | Market Basket Analysis (Apriori) |

---


## Key Takeaways

- **High customer loyalty**: A 96.3% repeat purchase rate signals strong retention, but RFM segmentation reveals a meaningful "At Risk" cohort requiring re-engagement campaigns.
- **Balanced category performance**: All four categories perform similarly in AOV (~$108–$111), suggesting pricing consistency but also limited differentiation — an opportunity for category-specific promotions.
- **Discount strategy**: Discounts above 20% represent a smaller share of orders, warranting analysis of whether heavy discounting is eroding margins.
- **CLV distribution is right-skewed**: The median CLV ($2,379) vs. mean ($7,638) indicates a small group of very high-value customers driving disproportionate revenue — prime targets for VIP programs.
- **Pareto principle is weaker than expected**: Top 20% of products contribute only 27.4% of revenue, suggesting a long-tail product mix where inventory breadth matters.

---

## 👤 Author

**Tejashvi**


