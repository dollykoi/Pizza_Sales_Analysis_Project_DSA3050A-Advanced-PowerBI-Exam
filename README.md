# DSA 3050A Advanced Power BI Examination
## Pizza Sales Analytics Solution

**Student:** Claire Mwarari  
**Admission Number:** 669470  
**Course:** DSA 3050A - Business Intelligence and Analytics  
**Institution:** USIU-Africa  
**Examination:** End Semester Exam  
**Submission Date:** April 2026

---

## Table of Contents
1. [Project Overview](#project-overview)
2. [Problem Statement](#problem-statement)
3. [Dataset Description](#dataset-description)
4. [Tools Used](#tools-used)
5. [Steps Followed](#steps-followed)
   - [Part A: Data Acquisition and Understanding](#part-a-data-acquisition-and-understanding)
   - [Part B: Data Cleaning and Transformation](#part-b-data-cleaning-and-transformation)
   - [Part C: Data Modeling](#part-c-data-modeling)
   - [Part D: DAX Measures and Calculated Columns](#part-d-dax-measures-and-calculated-columns)
   - [Part E: Dashboard Design and Visualization](#part-e-dashboard-design-and-visualization)
   - [Part F: Analytical Insights and Recommendations](#part-f-analytical-insights-and-recommendations)
6. [Repository Structure](#repository-structure)
7. [Key DAX Measures](#key-dax-measures)
8. [Dashboard Features](#dashboard-features)
9. [Key Insights](#key-insights)
10. [Challenges Encountered](#challenges-encountered)
11. [Conclusion](#conclusion)
12. [Academic Integrity Statement](#academic-integrity-statement)

---

## Project Overview

This repository contains a complete Power BI analytical solution developed for **Plato's Pizza**, a high-volume artisanal pizzeria. The project transforms 49,574 raw transactional records into business intelligence using a Star Schema model, advanced Power Query transformations, DAX calculations, and an interactive three-page dashboard.

The solution enables stakeholders to identify sales trends, optimize inventory management, understand customer purchasing patterns, and increase Average Order Value (AOV) through data-backed decisions.

---

## Problem Statement

Despite steady sales, Plato's Pizza management lacked a data-driven understanding of:

- **Peak operational periods** — When is the kitchen under maximum pressure?
- **Product portfolio performance** — Which of the 32 SKUs drive profit vs. dead weight?
- **Customer purchasing behavior** — Are customers buying single items or large group orders?
- **Revenue trends** — How does performance vary by month, day, and hour?

**Primary Objective:** Develop a comprehensive Power BI solution that transforms raw transactional data into actionable insights for operational efficiency and profitability maximization.

---

## Dataset Description

| Attribute | Value |
|-----------|-------|
| **Source** | [Kaggle - Pizza Sales Dataset](https://www.kaggle.com/datasets/mexwell/pizza-sales) |
| **Total Records** | 49,574 line-item transactions |
| **Unique Orders** | 21,350 transactions |
| **Date Range** | January 1, 2015 – December 31, 2015 |
| **Pizza Varieties** | 32 unique pizzas across 4 categories |
| **Sizes Available** | S, M, L, XL, XXL |

### Variables in Dataset
| Column | Description | Data Type |
|--------|-------------|-----------|
| `order_id` | Unique identifier for each order (one order may contain multiple pizzas) | Text |
| `date` | Order date in ISO 8601 format (YYYY-MM-DD) | Date |
| `time` | Order time in 24-hour format (hh:mm:ss) | Time |
| `name` | Short name for the pizza (e.g., `classic_dlx`, `thai_ckn`) | Text |
| `size` | Pizza size: S, M, L, XL, XXL | Text |
| `type` | Category: classic, chicken, supreme, veggie | Text |
| `price` | Selling price in USD | Currency |

### Pizza Categories and Full Names
- **Classic:** classic_dlx, big_meat, pepperoni, hawaiian, pep_msh_pep, ital_cpcllo, napolitana, the_greek
- **Chicken:** thai_ckn, bbq_ckn, southw_ckn, cali_ckn, ckn_pesto, ckn_alfredo
- **Supreme:** brie_carre, calabrese, soppressata, sicilian, ital_supr, peppr_salami, prsc_argla, spinach_supr, spicy_ital
- **Veggie:** mexicana, four_cheese, five_cheese, spin_pesto, veggie_veg, green_garden, mediterraneo, spinach_fet, ital_veggie

---

## Tools Used

| Tool / Technology | Purpose |
|-------------------|---------|
| **Power BI Desktop** | Primary development and visualization environment |
| **Power Query (M Language)** | Data extraction, cleaning, transformation, and derived column creation |
| **DAX (Data Analysis Expressions)** | Measure creation, time intelligence, and calculated columns |
| **GitHub** | Version control, documentation, and reproducibility |
| **Microsoft Word** | Examination report documentation |

---

## Steps Followed

### Part A: Data Acquisition and Understanding

- **Domain:** Retail / Food & Beverage Analytics
- **Business Problem:** Plato's Pizza requires optimization of operational efficiency and profitability through data-driven insights.
- **Key Tables Identified:** Sales_Details (transactional), Orders (order headers), Pizzas (product attributes), Pizza_Types (descriptive metadata), Calendar (time intelligence)

**Suitability for Advanced Analysis:**
- **Relational Complexity:** 4 interrelated tables forming a Star Schema
- **Volume:** 49,574 rows (exceeds 9,000-row requirement)
- **Variable Mix:** Categorical (category, size, type) and numerical (price, quantity implicit)
- **Time Intelligence:** Dual `date` and `time` fields enable granular peak-hour and monthly trend analysis

---

### Part B: Data Cleaning and Transformation in Power Query

1. **Import:** Loaded CSV using Text/CSV connector
2. **Removed Artifact Columns:** Eliminated "Unnamed: 0" and "X" index columns generated during export
3. **Data Type Corrections:**
   - `date` → Date type
   - `time` → Time type
   - `price` → Fixed Decimal Number (Currency)
   - `order_id` → Text type
4. **Derived Columns Created:**
   - **Year:** `Date.Year([date])`
   - **Month:** `Date.Month([date])`
   - **Quarter:** `Date.QuarterOfYear([date])`
   - **DayOfWeek:** `Date.DayOfWeekName([date])`
   - **Hour:** `Time.Hour([time])`
   - **Revenue:** `[price]` (quantity implicit as 1)
5. **Star Schema Construction:**
   - **Fact_Sales:** Transaction grain (49,574 records)
   - **Dim_Pizza:** Product dimension with `PizzaFullName` and `SizeSort` (1-5) columns
   - **Dim_Date:** Calendar dimension with `MonthName`, `IsWeekend`, and `FiscalQuarter`
   - **Dim_Orders:** Order-level aggregation with `OrderTotal`, `ItemCount`, `AvgPricePerItem`, and `OrderSizeCategory`
6. **Disabled Intermediate Query:** Prevented loading of raw transformation query to reduce memory consumption

**Total Transformation Steps:** 12 meaningful steps documented in Power Query Applied Steps pane.

---

### Part C: Data Modeling

Implemented a **Star Schema** with one fact table and three dimension tables.

| Table | Role | Primary Key | Description |
|-------|------|-------------|-------------|
| **Fact_Sales** | Fact Table | order_details_id | Transaction records (49,574 rows). Contains foreign keys and measures. |
| **Dim_Pizza** | Dimension | pizza_name | 32 unique pizzas. Descriptive attributes: category, size, full name, sort order. |
| **Dim_Date** | Dimension | date | 358 unique dates. Time intelligence support: Year, Month, Quarter, DayOfWeek, IsWeekend. |
| **Dim_Orders** | Dimension | order_id | 21,350 unique orders. Aggregated metrics: OrderTotal, ItemCount, AvgPricePerItem, OrderSizeCategory. |

**Relationships Configured:**

| From | To | Column | Cardinality | Cross-Filter |
|------|-----|--------|-------------|--------------|
| Fact_Sales | Dim_Pizza | pizza_name | Many-to-One (*:1) | Single (Dim → Fact) |
| Fact_Sales | Dim_Date | date | Many-to-One (*:1) | Single (Dim → Fact) |
| Fact_Sales | Dim_Orders | order_id | Many-to-One (*:1) | Single (Dim → Fact) |

**Optimizations:**
- Marked `Dim_Date` as official date table
- Hidden foreign key columns in Fact_Sales from report view
- Configured `SizeSort` to ensure S→M→L→XL→XXL ordering in visuals

---

### Part D: DAX Measures and Calculated Columns

Created **8 DAX measures** and **2 calculated columns**.

#### Measures

| Measure | Formula | Purpose |
|---------|---------|---------|
| **Total Revenue** | `SUM(Fact_Sales[Revenue])` | Baseline financial performance across any filter context |
| **Total Orders** | `DISTINCTCOUNT(Fact_Sales[order_id])` | Customer transaction volume |
| **Average Order Value** | `DIVIDE([Total Revenue], [Total Orders], 0)` | Mean spending per transaction |
| **Total Pizzas Sold** | `COUNTROWS(Fact_Sales)` | Operational throughput at line-item level |
| **YTD Revenue** | `TOTALYTD([Total Revenue], Dim_Date[date])` | Running cumulative revenue from Jan 1 |
| **Category Rank** | `RANKX(ALL(Dim_Pizza[category]), [Total Revenue], , DESC, Dense)` | Dynamic competitive positioning of 4 categories |
| **MoM Growth %** | `VAR c = [Total Revenue] VAR p = CALCULATE([Total Revenue], PREVIOUSMONTH(Dim_Date[date])) RETURN DIVIDE(c-p, p, 0)` | Month-over-month trend analysis (critical as dataset is single-year) |

#### Calculated Columns

| Column | Table | Formula | Purpose |
|--------|-------|---------|---------|
| **Order Size Category** | Dim_Orders | `SWITCH(TRUE(), [ItemCount]=1, "Single Item", [ItemCount]<=3, "Small Order (2-3)", [ItemCount]<=6, "Medium Order (4-6)", "Large Order (7+)")` | Customer behavioral segmentation |
| **Revenue** | Fact_Sales | `[price]` | Explicit revenue measure (quantity=1 implicit) |

---

### Part E: Dashboard Design and Visualization

Three-page interactive dashboard with consistent blue/gray professional theme.

#### Page 1: Executive Summary
- **KPI Cards:** Total Revenue, Total Orders, Average Order Value, Total Pizzas Sold.
- **Line Chart:** Monthly Revenue Trend (`MonthName` vs. `Total Revenue`).
- **Clustered Bar Chart:** Revenue by Category (`category` vs. `Total Revenue`).
- **Slicers:** Year (2015), MonthName, Category — synced across all pages.
![alt text](<screenshots/dashboards/page 1 executive summary.png>)

#### Page 2: Detailed Analysis
- **Drill-Down Matrix:** Rows = `category` → `pizza_name`; Values = `Total Revenue`, `Total Orders`, `Total Pizzas Sold`.
- **Stacked Column Chart:** Size distribution (`size` vs. `Total Pizzas Sold`) by category, sorted by `SizeSort`.
- **Clustered Bar Chart:** Order behavior (`OrderSizeCategory` vs. `Total Orders`).
- **Top 10 Table:** Revenue-ranked pizzas with green-gradient conditional formatting.
![alt text](<screenshots/dashboards/page 2 detailed analysis.png>)

#### Page 3: Insights and Performance Monitoring
- **Area Chart:** YTD Revenue accumulation (`MonthName` vs. `YTD Revenue`).
- **Clustered Column Chart:** Weekday pattern (`DayOfWeek` vs. `Total Revenue`) with average line.
- **Heat Map Matrix:** `DayOfWeek` (rows) × `Hour` (columns) × `Total Revenue` (red-gradient conditional formatting).
- **Donut Chart:** Weekend vs. Weekday split (`IsWeekend` vs. `Total Revenue`).
- **Rank Table:** Category performance verification (`category`, `Total Revenue`, `Category Rank`).
![alt text](<screenshots/dashboards/page 3 insights monitoring.png>)

---

### Part F: Analytical Insights and Recommendations

#### Key Insights
1. **Classic Category Concentration Risk:** Classic pizzas drive ~32–35% of revenue, creating portfolio imbalance vulnerability.
2. **Weekday Troughs:** Tuesday/Wednesday revenue falls below weekly average despite fixed operating costs.
3. **Peak Hour Congestion:** 12:00–13:00 and 18:00–19:00 drive majority of revenue; 14:00–17:00 significantly underutilized.
4. **Large Order Value:** Orders with 7+ items represent <9% of volume but generate ~2.5x AOV, indicating an underserved high-value segment.
5. **Long Tail Bloat:** Bottom 10 SKUs contribute minimally while increasing operational complexity.

#### Actionable Recommendations
1. **Implement "Afternoon Value Window" (14:00–17:00)** with targeted discounts to smooth demand curve.
2. **Launch "Party Pack" Catering Bundles** for 7+ item orders to intentionally capture high-value group dining.
3. **Execute Menu Rationalization** — phase out bottom 8–10 SKUs and introduce 2–3 targeted Veggie innovations to reinvigorate the lowest-performing category.

---

## Repository Structure

```
Pizza_Sales_Analysis_Project/
├── data/
│   └── A_year_of_pizza_sales_from_a_pizza_place_872_68.csv
├── powerbi/
│   └── pizza-sales-analysis-project.pbix
├── screenshots/
│   ├── dashboards/
│   │   ├── page 1 executive summary.png
│   │   ├── page 2 detailed analysis.png
│   │   └── page 3 insights monitoring.png
│   ├── data_cleaning_and_transformations/
│   │   ├── Dim_Date/
│   │   ├── Dim_Orders/
│   │   ├── Dim_Pizza/
│   │   ├── Fact_Sales/
│   │   └── main_table/
│   ├── data_modeling/
│   └── DAX_measures/
├── report/
│   └── DSA 3050A End Semester Exam.pdf
└── README.md
```

---

## Key DAX Measures

### Full DAX Code

**Total Revenue**
```dax
Total Revenue = SUM(Fact_Sales[Revenue])
```

**Total Orders**
```dax
Total Orders = DISTINCTCOUNT(Fact_Sales[order_id])
```

**Average Order Value**
```dax
Average Order Value = DIVIDE([Total Revenue], [Total Orders], 0)
```

**Total Pizzas Sold**
```dax
Total Pizzas Sold = COUNTROWS(Fact_Sales)
```

**YTD Revenue**
```dax
YTD Revenue = TOTALYTD([Total Revenue], Dim_Date[date])
```

**Category Rank**
```dax
Category Rank = RANKX(ALL(Dim_Pizza[category]), [Total Revenue], , DESC, Dense)
```

**MoM Growth %**
```dax
MoM Growth % = 
VAR CurrentMonth = [Total Revenue]
VAR PrevMonth = CALCULATE([Total Revenue], PREVIOUSMONTH(Dim_Date[date]))
RETURN DIVIDE(CurrentMonth - PrevMonth, PrevMonth, 0)
```

---

## Dashboard Features

| Feature | Implementation | User Benefit |
|---------|-----------------|--------------|
| **Star Schema Model** | One fact table, three dimensions | Fast query performance; intuitive filtering |
| **Time Intelligence** | YTD, MoM Growth, FiscalQuarter | Trend analysis without complex manual calculations |
| **Drill-Down Matrix** | Category → Pizza Name hierarchy | Noise reduction; focused exploration |
| **Synced Slicers** | Cross-page synchronization | Consistent filter context across all report pages |
| **Conditional Formatting** | Color gradients on revenue metrics | Rapid visual identification of performance drivers |
| **Interactive Tooltips** | Custom tooltip details on visuals | Enhanced user context without cluttering dashboard |

---

## Challenges Encountered

| Challenge | Solution Applied |
|-----------|------------------|
| CSV export artifact columns | Removed "Unnamed: 0" and "X" in Power Query first transformation step |
| Size ordering defaulted alphabetically | Created `SizeSort` numeric column (1=S, 2=M, 3=L, 4=XL, 5=XXL) |
| Order-level metrics unavailable at line-item grain | Created `Dim_Orders` via Power Query Group By on `order_id` |
| Single-year dataset prevented YoY comparison | Implemented MoM Growth % using `PREVIOUSMONTH()` as time intelligence alternative |
| Intermediate query consumed memory | Disabled loading of raw transformation query from final model |

---

## Conclusion

This Power BI transforms raw transactional pizza sales data into strategic business intelligence through data modeling, advanced DAX calculations, and a user-centric dashboard design. The Star Schema architecture ensures query efficiency and analytical flexibility, while the three-page dashboard serves distinct stakeholder needs — from executive KPI monitoring to operational drill-downs and strategic insight generation.

The identified opportunities around off-peak demand smoothing, catering segment cultivation, and menu rationalization provide Plato's Pizza with concrete, data-backed pathways to improved profitability and operational efficiency.

### Future Enhancements

- Integration of real-time POS data refresh
- Predictive forecasting using Power BI AI visualizations
- Customer demographic segmentation (if customer ID data becomes available)
- Competitive pricing analysis benchmarking
- Seasonal trend decomposition and forecasting

---

## Academic Integrity Statement

This submission represents my own original work completed for the **DSA 3050A Advanced Power BI End Semester Examination at USIU-Africa**.

**Dataset:** The dataset is publicly available from [Kaggle](https://www.kaggle.com/datasets/mexwell/pizza-sales) and is properly cited.

**Independent Work:** All Power Query transformations, data model relationships, DAX formulas, dashboard designs, and analytical insights were developed independently.

**AI Tool Usage:** No AI tool was used to replace analytical work, generate insights, or create visualizations that I did not independently verify.

