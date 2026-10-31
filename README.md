# Cottonil Dashboard — Project README & Executive Summary

Oct 29, 2026 · @Ahmed

This project delivers a sales, branch, product-profitability, and inventory intelligence dashboard for Cottonil, built in Power BI on a cleaned 9-table SQL data model spanning 2018–2026.

## Project Overview

**Objective:** Translate Cottonil's raw transactional data into an interactive Power BI dashboard that surfaces sales performance, branch efficiency, product/discount behavior, and inventory allocation issues — with clear, data-backed recommendations for management.

**Scope (Tier 1 / MVP):** Sales Performance, Branch Performance, Product Profitability & Discounts, and Inventory Intelligence. Customer segmentation and 15+ additional requested modules (supply chain, ESG, store expansion, marketing ROI, etc.) were scoped out of this phase and deferred to Phase 2 (see Limitations).

**Timeframe:** 8-day sprint, delivered ahead of the Oct 30 deadline.

**Tools:** MySQL (data cleaning, joins, aggregation) → Power BI (modeling, DAX measures, visualization).

## Data & Methodology

**Source:** 9 of Cottonil's 19 available tables were ingested (branches, sales\_orders, sales\_order\_items, sales\_payments, customers, products\_catalog, monthly\_stock, sales\_returns, warehouses), selected because they directly support the Tier 1 scope. \~3 million rows total.
**link for dashboard** https://drive.google.com/drive/folders/1Qzum48Mm-D8xo1HbSi9fny-Oze8kadQZ?usp=drive_link
**Data quality issues found and fixed:**

| Issue | Fix |
| --- | --- |
| Arabic-text CSVs failed UTF-8 import (encoding/BOM errors) | Re-imported via `LOAD DATA INFILE` with explicit `utf8mb4` charset |
| ID columns (branch\_id, sku\_id, etc.) auto-typed as INT, truncating codes like "BR001" to 0 | Re-created tables with explicit `VARCHAR` typing before reload |
| 19% of `sales_orders` rows (103K of 540K) had a blank `customer_id` | Retained in revenue totals, recoded to `'UNKNOWN'`, excluded from customer-level segmentation |

**Modeling:** Built a star-schema style model — a cleaned `fact_sales` table (order items joined to orders), with daily aggregate tables (`agg_daily_branch_sales`, `agg_daily_product_sales`, `agg_channel_sales`) feeding the dashboard rather than raw 1M+ row tables, plus a `dim_date` calendar table in Power BI for year-over-year comparisons.

**Validation:** `fact_sales` revenue (841,274,176.32 EGP) was reconciled exactly against `sales_orders` totals before any dashboard work began.

## Executive Summary — Key Findings

Total revenue analyzed: **841.27M EGP** net (879.20M EGP gross) across 2018–2026, with a blended margin of **38.34%**.

| # | Finding | Evidence |
| --- | --- | --- |
| 1 | Discounts are not tied to product performance | Correlation between unit sales and discount/unit is only moderate (0.47); products with near-identical sales volumes receive discounts up to 20x apart (e.g. 2.33 vs 52.30 EGP/unit) |
| 2 | Wholesale is the most profitable channel per transaction | AOV = 15.53K EGP, far above Retail POS and E-commerce |
| 3 | Retail POS carries the heaviest discount burden | 18M EGP in discounts — the highest of the three channels |
| 4 | E-commerce is underperforming and under-invested | Lowest discounts (4.73M EGP) but also by far the lowest net sales (105M EGP) — a channel with untapped growth potential |
| 5 | Saad Zaghloul branch outperforms via volume, not pricing | Nearly double the orders of the next branch (13,521 vs 6,932) at a comparable AOV and margin (\~38.3%) — success is site/operational, not a distinct sales model |
| 6 | A handful of categories drive most revenue | Men boxers, Men tops, and Socks dominate gross sales (Pareto concentration across 139 active SKUs) |
| 7 | Inventory allocation contradicts demand | The fastest-selling categories (Men boxers, Men tops, Socks) have the *lowest* stock-to-sales ratios (12–29), while slow-moving categories (Women bottoms, Accessories) are overstocked (ratio 454–462) — signaling stockout risk on bestsellers and tied-up capital in slow stock |

## Dashboard Pages

| Page | What it shows |
| --- | --- |
| Overview | Top-level KPIs (Total Revenue, Margin, Discount, Margin %, YoY Growth), revenue trend over time, revenue split by sales channel, top branches |
| Sales | Gross vs. net sales trend, AOV, discount totals by channel, with the channel-efficiency narrative (Wholesale vs. Retail POS vs. E-commerce) |
| Branch Performance | Revenue ranking by branch, a Revenue × Margin % scatter plot to spot high-volume/low-margin outliers, AOV and discount % by branch (table) |
| Product | Discount-per-unit vs. units-sold scatter plot (the core discount-inconsistency finding), Pareto chart of gross sales by product, revenue by category |
| Inventory | Stock-to-sales ratio by category, stock and sold quantity by branch, flagging the demand/allocation mismatch |

## Recommendations

1. **Standardize discount policy.** Introduce a rule tying discount depth to product performance (e.g. clearance-only discounts for slow movers) rather than the current near-arbitrary allocation.
2. **Reallocate inventory to match demand.** Increase stock on Men boxers, Men tops, and Socks (highest velocity, lowest ratio) and reduce replenishment on Women bottoms and Accessories (overstocked) to cut stockout risk and free up working capital.
3. **Invest in Wholesale.** Highest AOV and transaction efficiency — a strong candidate for expanded account management or volume incentives.
4. **Audit Retail POS discounting.** Highest absolute discount spend relative to its net/gross gap; review discount approval rules at the POS level.
5. **Treat E-commerce as a growth gap, not a cost center.** Low discount spend hasn't translated into volume — investigate conversion, pricing, or marketing spend on this channel specifically.
6. **Use Saad Zaghloul as a site-selection case study**, not a sales-model template, when evaluating future branch locations.

## Limitations & Next Phase

**Known data limitations:**

- 19% of orders carry no customer\_id (recoded as "Unknown") — customer-level segmentation is directional only, not exhaustive.
- Inventory analysis uses `monthly_stock` snapshots, not real-time stock movement.

