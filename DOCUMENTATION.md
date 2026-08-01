# Superstore Sales — Project Documentation

## A. Project Aim / Goal

The goal of this project is to perform a complete Exploratory Data Analysis (EDA) on a retail orders dataset (the "Sample Superstore" dataset, 2014–2017) in order to:

1. Take a raw, imperfect dataset and clean it into an analysis-ready form.
2. Engineer new fields that make time-based and business-rule-based analysis easier (order timing breakdowns, shipping duration, sales/order/customer classification flags).
3. Summarize the business through pivot tables covering sales, profit, orders, regions, categories, sub-categories, and shipment modes.
4. Communicate the findings through a single-page, KPI-driven Excel dashboard that a business stakeholder could read at a glance.

The underlying business question this dataset is built to answer: **where is the Superstore making money, where is it losing money, and what does that tell us about regions, product categories, customer segments, and shipping behavior?**

## B. Dataset Description

Retail orders dataset, covering four years of order history (January 2014 – December 2017) for a fictional US superstore chain.

**Raw fields** (21 columns, 9,994 order line items):

| Field | Description |
|---|---|
| Row ID | Unique row identifier |
| Order ID | Identifier shared by all line items in the same order |
| Order Date / Ship Date | Date the order was placed / shipped |
| Ship Mode | Standard Class, Second Class, First Class, Same Day |
| Customer ID / Customer Name | Customer identifiers |
| Segment | Consumer, Corporate, Home Office |
| Country / City / State / Postal Code / Region | Geographic fields |
| Product ID / Category / Sub-Category / Product Name | Product hierarchy |
| Sales / Quantity / Discount / Profit | Transaction metrics |

At raw scale, the dataset spans **5,009 unique orders**, **9,994 order line items**, **793 unique customers**, across **3 product categories**, **17 sub-categories**, and **4 regions**.

Two supporting sheets accompany the Orders data in the workbook:
- **Returns** — a list of order IDs that were later returned (296 unique orders)
- **People** — the regional manager responsible for each of the 4 regions

## C. Cleaning Process

The version of this dataset published in `data/superstore_raw_uncleaned.csv` reflects the kind of messiness a raw export from an order-management or e-commerce system typically contains. The following issues were identified and resolved during cleaning:

1. **Duplicate records** — exact duplicate rows were identified and removed.
2. **Missing values** — nulls in `Postal Code`, `Customer Name`, `Ship Date`, `Discount`, and `City` were investigated and handled (imputed where a reliable rule existed, e.g. postal code from city/state; dropped or flagged where not).
3. **Inconsistent date formats** — `Order Date` and `Ship Date` arrived in a mix of formats (`MM/DD/YYYY`, `DD-MM-YYYY`, `YYYY/MM/DD`, `DD Mon YYYY`) and were standardized to a single date type.
4. **Inconsistent text formatting** — categorical fields (`Region`, `Category`, `Sub-Category`, `Ship Mode`, `Segment`, `Country`) had mixed casing, leading/trailing whitespace, and typos (e.g. "Wast" → "West", "Tecnology" → "Technology", "USA" / "US" / "united states" → "United States") and were standardized to consistent capitalization and spelling.
5. **Mixed data types** — some `Sales` values were stored as currency-formatted strings (e.g. `"$261.96"`) rather than numbers, and were converted to a consistent numeric type.
6. **Data-entry errors** — a small number of negative `Quantity` and `Sales` values (impossible for completed orders) were reviewed and corrected.
7. **Header standardization** — inconsistent column headers (extra spaces, mixed casing, e.g. `"Ship  Date"`, `"segment"`, `"Sub-Category "`) were renamed to a clean, consistent naming convention.
8. **Blank rows** — fully empty rows left over from the export were dropped.

**Feature engineering** — once the data was clean, the following fields were derived to support the pivot analysis and dashboard:

- `DAY`, `MONTH`, `YEAR` — extracted from `Order Date`
- `WEEKDAY` — day of week the order was placed
- `NO. OF DAYS TO SHIP` — `Ship Date` minus `Order Date`
- `GOOD & BAD SALES` — flags orders as profitable or loss-making
- `NORMAL OR BULK ORDER` — flags orders above a quantity threshold as bulk
- `HIGH VAL. & NORM. CUST.` — flags customers above a lifetime-sales threshold as high-value

## D. Final Result

The cleaned dataset (**Orders** sheet) contains **9,994 fully populated rows across 30 columns** — the original 21 raw fields plus 9 engineered analysis fields — with zero missing values and consistent formatting throughout.

From this cleaned data, the **PivotCharts** sheet builds pivot tables covering:
- Sales by region
- Sales by region × category
- Sales by month (seasonality)
- Orders by category × shipment mode
- Sales by sub-category
- Top 10 products by sales

These feed directly into the **Report** sheet — a single-page dashboard with 6 KPI cards and 6 charts (bar, line, horizontal bar, and radar) summarizing the business at a glance.

## E. Outcome

- Delivered a fully cleaned, analysis-ready dataset with reliable, standardized fields suitable for further modeling or reporting.
- Produced a reusable pivot-table layer that can be refreshed as new orders are added.
- Built a single-page executive dashboard (KPI cards + 6 charts) that surfaces regional, seasonal, and category-level performance without needing to open the raw data.
- Established a clear, repeatable cleaning process (documented above) that could be reapplied to future data drops from the same source system.

## F. Insights

**Overall performance**
- Total Sales: **$2,297,200.86** | Total Profit: **$286,397.02** (a ~12.5% overall margin)
- Average sale value: **$229.86** across 9,994 line items / 5,009 orders / 793 customers

**Regional performance**
- **West** is the top-performing region by sales (**$725,458**), followed by **East** (**$678,781**), **Central** (**$501,240**), and **South** (**$391,722**) — West outsells South by roughly 85%.

**Category & sub-category performance**
- **Technology** leads both in sales (**$836,154**) and profit (**$145,455**), followed by **Office Supplies** (**$719,047** sales / **$122,491** profit).
- **Furniture** generates the second-highest sales (**$741,999**) but by far the lowest profit (**$18,451**) — its margin is a fraction of the other two categories.
- **Tables** is the single biggest profit drag in the entire dataset, running a **net loss of ‑$17,725**, despite steady sales — a strong candidate for pricing or discount-policy review.
- **Phones** ($330,007) and **Chairs** ($328,449) are the top-selling sub-categories; the **Canon imageCLASS 2200 Advanced Copier** is the single best-selling product (~$61,600).

**Customer segments**
- **Consumer** is the largest segment by sales (**$1,161,401**, ~51% of total), ahead of **Corporate** (**$706,146**) and **Home Office** (**$429,653**).

**Seasonality**
- Sales are strongly seasonal: **January and February are the slowest months** (~$95K and ~$60K), while **September, November, and December are the strongest** (~$308K, ~$352K, and ~$325K respectively) — consistent with back-to-school and holiday-season buying patterns.

**Shipping**
- **Standard Class** is used for the majority of orders (5,968 of 9,994, ~60%), with **Same Day** shipping the least common (543 orders, ~5%).

**Discounting & profitability**
- Discount and profit are **negatively correlated** (r ≈ ‑0.22) — heavier discounting tends to erode profit rather than grow it, reinforcing the case for reviewing discount policy on categories like Furniture and Tables.
- **1,871 of 9,994 line items (~19%) were sold at a loss**, concentrated disproportionately in the Furniture category.

**Returns**
- **296 unique orders were returned**, representing about **$180,504 in sales** and **$23,232 in profit** that was ultimately reversed — a useful segment for a follow-up analysis into *why* those orders were returned (product, region, or shipping-mode patterns).
