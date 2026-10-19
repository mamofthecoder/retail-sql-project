# Retail Sales Analysis with SQL and SQLite

A reproducible SQL portfolio project analysing synthetic retail sales across customers, products, categories, cities, and time. The analysis focuses on 2026 and demonstrates relational queries, data validation, window functions, and business interpretation.

**Tools:** SQLite · Python · pandas · Jupyter Notebook  
**2026 revenue:** R4,012,700 · **2026 orders:** 238 · **Customers ordering in 2026:** 81 of 100

> All data is synthetic. Findings demonstrate analytical methods and describe the generated scenario; they are not evidence of real South African market behaviour.

## Project objectives

- Validate order totals against their underlying order items.
- Measure monthly revenue, order volume, average order value, and month-on-month change.
- Compare product and category performance by revenue and units sold.
- Analyse customer participation and revenue differences across cities.
- Rank customers within cities and assess revenue concentration.
- Identify available stock with no sales during 2026.

## Database structure

| Table | Rows | Main fields |
|---|---:|---|
| Customers | 100 | Customer_ID, Customer_Name, City |
| Products | 12 | Product_ID, Product_Name, Category, Unit_Price, Stock_Quantity |
| Orders | 500 | Order_ID, Customer_ID, Order_Date, Total_Amount |
| Order_Items | 1,237 | Order_Item_ID, Order_ID, Product_ID, Quantity, Unit_Price |

The database contains simulated orders from 2025–2026. Analysis uses the range `2026-01-01` inclusive to `2027-01-01` exclusive.

Relationships:

- One customer can have many orders (`Customer_ID`).
- One order can have many order items (`Order_ID`).
- One product can appear in many order items (`Product_ID`).

Primary keys, foreign keys, required fields, and numeric checks are defined in the setup. SQLite foreign-key enforcement is enabled on the notebook connection.

## Key findings

### Monthly performance

September generated the highest monthly revenue at **R570,800**. November had the highest average order value at **R23,758.82**, but fewer orders than September: 17 versus 26.

The largest revenue decrease in rand occurred from April to May: **R296,150 (61.49%)**. The largest percentage decrease occurred from November to December: **67.68%**, representing R273,350. Percentage changes depend on the previous month's revenue, so the largest monetary and percentage decreases need not coincide.

### Product and category performance

**Laptop** led product revenue at **R2,352,000**, from 196 units. **Office Chair** led units sold at 205, illustrating why sales volume and revenue should be assessed separately.

| Category | Revenue | Units sold | Revenue share | Average selling price per unit |
|---|---:|---:|---:|---:|
| Electronics | R2,944,300 | 553 | 73.37% | R5,324.23 |
| Furniture | R776,850 | 528 | 19.36% | R1,471.31 |
| Accessories | R291,550 | 695 | 7.27% | R419.50 |

Electronics generated the highest revenue despite Accessories selling more units, reflecting its higher quantity-weighted average selling price.

### Customer and city performance

**81 customers placed at least one order in 2026; 19 did not.**

| City | Total customers | Customers with orders | Orders | Revenue | Customer order rate | Average order value |
|---|---:|---:|---:|---:|---:|---:|
| Cape Town | 29 | 24 | 76 | R1,474,850 | 82.76% | R19,405.92 |
| Johannesburg | 27 | 21 | 52 | R972,700 | 77.78% | R18,705.77 |
| Durban | 24 | 20 | 64 | R919,950 | 83.33% | R14,374.22 |
| Pretoria | 20 | 16 | 46 | R645,200 | 80.00% | R14,026.09 |

Cape Town generated more revenue than Durban through both more orders and a higher average order value, despite Durban having a higher customer order rate.

Pretoria's top two customers contributed approximately **37.52%** of its revenue, indicating customer concentration within the simulated dataset. This percentage is a revenue share, not a formal risk score.

### Stock without sales

| Product | Units in stock | Value at current selling price |
|---|---:|---:|
| Standing Desk | 8 | R36,000 |
| Printer | 10 | R30,000 |
| **Total** | **18** | **R66,000** |

These products were deliberately excluded from generated orders. Their zero sales are a designed scenario, not evidence of weak demand. The R66,000 is stock valued at selling prices, not inventory cost or guaranteed future revenue.

## SQL skills demonstrated

- Multi-table joins, including `LEFT JOIN` to retain customers and products without sales.
- Aggregation, conditional counts, distinct order counts, and `COALESCE`.
- CTEs to separate filtering, summaries, and window calculations.
- `LAG` for monthly comparisons and `DENSE_RANK` for rankings within cities.
- Partitioned window totals for city-specific revenue shares.
- `NOT EXISTS` to identify products without sales in the period.
- Date-range filtering, `strftime`, percentage calculations, `ROUND`, and `NULLIF`.

## Validation and interpretation

- All 500 order totals reconcile with summed item revenue; the reconciliation query returns no mismatches or orders without item totals.
- Monthly, product, category, customer, and city revenue totals are checked for agreement at **R4,012,700** for 2026.
- Customer counts reconcile: **81 with orders + 19 without orders = 100**.
- Revenue shares are calculated before filtering ranked customers so denominators include the entire relevant city.
- Category order counts use distinct order IDs. They should not be added across categories because one order can contain multiple categories.
- Order-level totals are not summed after joining to item rows, avoiding inflated revenue from repeated order totals.

## Run the project

1. Download [retail_sql_project_presented.ipynb](retail_sql_project_presented.ipynb) into a project folder.
2. Install the notebook dependencies:

   ```bash
   python -m pip install pandas jupyterlab
   ```

3. Start JupyterLab:

   ```bash
   jupyter lab
   ```

4. Open the notebook and run all cells from top to bottom.

Python's `sqlite3`, `random`, and `datetime` modules are part of the standard library. The bundled SQLite must support window functions (SQLite 3.25.0 or later).

The notebook creates `retail_sql_project.db` in its working directory and generates data using **seed 42**. It inserts data only when all four tables are empty; if any rows already exist, generation is skipped. To reproduce the original dataset, run it in a fresh folder without an existing database file. No CSV download or separate database server is required.

## Repository files

| File | Purpose |
|---|---|
| README.md | Project overview, findings, limitations, and instructions |
| retail_sql_project_presented.ipynb | Database setup, reproducible generation, SQL analyses, and saved outputs |
| retail_sql_project.db | Generated locally when the notebook runs; optional to include in the repository |

Keep the notebook beside this README so the link above works.

## Limitations

- The entire dataset is synthetic and covers the full simulated 2026 year, including dates that were in the future when it was created. It is not a year-to-date report.
- Customers 91–100 and products 11–12 were intentionally excluded from generated orders.
- Unit prices are fixed per product. Discounts, returns, taxes, shipping charges, costs, and profit are not modelled.
- Stock quantities are a separate snapshot and are not reduced by simulated sales. They do not represent a reconstructed historical inventory balance.
- Month-on-month comparisons use the previous observed month. All twelve months exist here; missing months would require a calendar table or equivalent treatment.
- Customer order rates measure participation among listed customers, not acquisition conversion or retention.

## Author

Ofentse Matlawe
