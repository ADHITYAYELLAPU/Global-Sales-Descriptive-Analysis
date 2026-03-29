# Challenge 449 — Global Sales Analysis

> **Alteryx Weekly Challenge** | Descriptive Analysis | Multi-Currency Data Pipeline

---

## Problem Statement

A company tracks sales across **three global regions** (APAC, Europe, USA) and **five business segments** (Channel Partners, Enterprise, Government, Midmarket, Small Business). Sales figures are recorded in each country's **local currency**, and a separate exchange rate table maps them to euros.

**Two questions to answer:**

1. Build a clean summary table showing total EUR sales per region and segment for **2022 and 2023**.
2. Calculate **year-over-year (YoY) variation** per segment and identify which segments experienced **negative variation across all three regions**.

---

## Tools & Skills Used

| Tool | Purpose |
|---|---|
| **Join** | Merge sales data with exchange rate table |
| **Filter** | Remove null sale price records |
| **Formula** | Currency conversion + year extraction + YoY calculation |
| **Crosstab / Summarize** | Pivot years into columns |
| **Sort** | Order output by Segment and Region |
| **Unique** | Identify distinct declining segments |
| **Interactive Chart** | Visualize 2022 vs 2023 comparisons per region |
| **Render** | Export charts as PDF reports |

---

## Workflow Overview

```
[Sales Data] ──┐
               ├──► [Filter: Remove Nulls] ──► [Join: Exchange Rates]
[FX Rates]  ───┘         
                              │
                              ▼
                    [Formula: EURSales = Sales × Rate]
                    [Formula: Year = DateTimeYear(Date)]
                              │
                              ▼
                    [Summarize: Total EUR by Region + Segment + Year]
                    [Crosstab: Years → Columns (2022 | 2023)]
                    [Sort: Segment ASC, Region ASC]
                              │
                    ┌─────────┴──────────┐
                    ▼                    ▼
             [PART 1 OUTPUT]    [Formula: YoY = 2022 - 2023]
             Region | Segment   [Filter: 2023 < 2022]
             2022   | 2023      [Unique: by Segment]
                                         │
                                         ▼
                                  [PART 2 OUTPUT]
                                  Declining segments
```

---

## Part 1 — EUR Sales Summary Table

**Result: 15 records (3 regions × 5 segments)**

| Region | Segment | 2022 (€) | 2023 (€) |
|---|---|---|---|
| APAC | Channel Partners | 5,431,523.99 | 6,681,533.69 |
| APAC | Enterprise | 3,628,956.10 | 2,786,166.65 |
| APAC | Government | 11,530,750.10 | 14,685,752.02 |
| APAC | Midmarket | 7,576,854.57 | 6,971,593.72 |
| APAC | Small Business | 5,761,163.39 | 5,785,153.26 |
| Europe | Channel Partners | 16,477,394.63 | 16,297,522.04 |
| Europe | Enterprise | 15,236,444.47 | 10,500,638.78 |
| Europe | Government | 69,465,891.47 | 52,301,051.53 |
| Europe | Midmarket | 26,521,197.11 | 17,751,555.88 |
| Europe | Small Business | 10,847,044.51 | 15,627,839.43 |
| USA | Channel Partners | 5,613,400.37 | 6,681,533.69 |
| USA | Enterprise | 3,673,733.11 | 2,786,166.65 |
| USA | Government | 12,849,411.80 | 14,685,752.02 |
| USA | Midmarket | 7,743,178.46 | 6,971,593.72 |
| USA | Small Business | 6,145,936.29 | 5,785,153.26 |

> **Tip applied:** Records where `Sale Price = NULL` were removed using a Filter tool before joining with the exchange rate data. This prevented incorrect zero-value conversions from skewing totals.

---

## Part 2 — YoY Variation & Negative Segments

**Formula used:**
```
YoY Variation = [2022] - [2023]
Filter condition: [2023] < [2022]
```

**Segments with negative YoY variation across all three regions:**

| Region | Segment | 2022 (€) | 2023 (€) | YoY Variation |
|---|---|---|---|---|
| USA | Midmarket | 7,743,178.46 | 6,971,593.72 | **−771,584.74** |
| APAC | Enterprise | 3,628,956.10 | 2,786,166.65 | **−842,789.45** |

---

## Key Insights

### What the data tells us

**1. Two segments are declining — and it's not a regional problem, it's a segment problem.**

Both Midmarket (USA) and Enterprise (APAC) declined in 2023. The fact that these are in *different regions* points to a **segment-level issue** — not a regional economic factor. This would warrant a deeper diagnostic: are deal sizes shrinking? Is pricing uncompetitive for these buyer types?

**2. Europe Government had the largest absolute drop.**

At €69.5M → €52.3M, this is the single biggest contraction in the dataset — a €17.1M decline. However, since it only appears in Europe (not in all 3 regions), it did not meet the "negative across all regions" threshold.

**3. APAC Government was the standout performer.**

Growing from €11.5M to €14.7M (+27.4%), the Government segment in APAC is the strongest growth story. This could indicate successful public sector expansion or favourable government procurement conditions in the region.

**4. Small Business was mixed — growth in Europe, slight decline in USA and APAC.**

Not consistently negative across all three regions, so it didn't qualify — but worth monitoring in future periods.

---

## Visual Reports

Bar charts generated in Alteryx per region, comparing 2022 vs 2023 by segment:

> Charts were produced using the **Interactive Chart tool** (grouped bar, 2022 = blue, 2023 = orange) and exported via the **Render tool** as PDF.

---

## Repository Structure

```
Challenge-449-Global-Sales-Analysis/
│
├── README.md                          ← This file
├── Challenge_449_start_file.yxmd      ← Alteryx workflow
│
├── screenshots/
│   ├── workflow_overview.png          ← Full canvas screenshot
│   ├── result_part1_15records.png     ← Part 1 output table
│   └── result_part2_negative.png      ← Part 2 filtered output
│
└── visuals/

```

---

## How to Run

1. Open `Challenge_449_start_file.yxmd` in **Alteryx Designer**
2. Ensure both input files (Sales + Exchange Rates) are connected and paths are valid
3. Click **Run** — the workflow produces both Part 1 and Part 2 outputs
4. Charts will render in the Interactive Chart tools; use Render tool to export PDFs

---

## Analysis Type

This project covers **Descriptive Analysis** — summarizing what happened in 2022 and 2023 — with elements of **Diagnostic Analysis** in the YoY comparison step, identifying *which* segments underperformed and *why* that pattern is meaningful.

---

## About

**Certification level targeted:** Core → Advanced

**Skills demonstrated:** Data preparation, multi-currency joins, date parsing, pivot/crosstab, conditional filtering, report generation
