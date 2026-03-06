# Financial Performance Dashboard — Power BI

This report was built to answer a question every sales organization eventually has to face: *are we actually hitting our numbers, or are we just close enough to ignore the gap?*

Using 14 months of sales data spanning January 2023 to February 2024, I designed a financial performance dashboard in Power BI that tracks $19M in revenue against set targets, breaks down individual sales rep performance, and identifies exactly where — and why — the business fell short. The report was built entirely from scratch, including 13 custom DAX measures covering core KPIs, time intelligence, and dynamic visual logic.

---

## 📸 Dashboard Preview

![Financial Performance Dashboard](screenshot.png)

---

## 📌 Key Findings

- The business recorded **$19M in actual revenue** against a **$19M target** — a figure that looks balanced on the surface but conceals a **-$367K variance** when examined month by month
- Monthly sales targets were met in only **2 out of 14 months**, pointing to a forecasting model that consistently overestimates the team's output
- Revenue and targets both trended upward through **September 2023**, then reversed sharply in Q4 — a decline that was never recovered, suggesting a structural shift rather than a temporary dip
- Only **4 of 12 sales reps** exceeded their individual targets. The remaining 8 underperformed, with the worst variance sitting at **-11.6%** — a gap large enough to materially affect overall results
- **YTD performance stands at -4.4%**, meaning the business entered the next financial cycle already carrying a deficit with no corrective action yet visible in the data

---

## 🛠️ Tools & Techniques

**Power BI Desktop**
The entire report was designed, modelled, and published within Power BI Desktop. Layouts were structured to prioritise executive readability — KPI cards at a glance, trend analysis in the centre, and individual performance breakdowns below.

**DAX — Data Analysis Expressions**
13 measures were written to drive every calculation in the report. These include base aggregations, percentage calculations, time intelligence functions, and dynamic display logic. Variables (VAR / RETURN) were used throughout to make measures readable, reduce redundant evaluation, and separate calculation logic from output formatting — a practice that reflects production-level DAX writing, not just functional code.

**Time Intelligence**
DATESYTD was used alongside CALCULATE to build year-to-date measures for actual sales, targets, variance, and variance percentage. These measures respond dynamically to slicer selections, allowing stakeholders to assess performance at any point in the year without manual recalculation.

**Data Modelling**
The report is built on a structured data model with clearly defined relationships between the Actual sales table, the Targets table, and a Calendar dimension table. Measures are written against this model rather than calculated columns, keeping the model clean and the file performant.

**Conditional Formatting & Dynamic Visuals**
A Target Status measure (returning 1 or -1) drives colour-coded conditional formatting across the report — green for months that hit target, red for misses. Variance labels are formatted dynamically using VAR to assign emoji indicators, giving the report a polished, at-a-glance readability without relying on static colour rules.

**Slicers & Interactive Filtering**
Month-level and rep-level slicers allow users to drill into specific time periods or individuals without needing a separate page — keeping the report dense with insight but clean in navigation.

---

## 🧮 DAX Measures

```dax
-- ── CORE METRICS ──────────────────────────────────────────────────────────

-- Aggregates total actual sales from the Actual table
Total Sales Actual = SUM(Actual[Sales])

-- Aggregates total sales target from the Targets table
Total Sales Target = SUM(Targets[Sales])

-- Calculates the absolute gap between actual and target
Variance = [Total Sales Actual] - [Total Sales Target]

-- Calculates variance as a percentage of target using safe division
Variance % = DIVIDE([Variance], [Total Sales Target])


-- ── TIME INTELLIGENCE (YTD) ───────────────────────────────────────────────

-- Year-to-date actual sales scoped to the calendar date context
YTD Sales Actual = CALCULATE([Total Sales Actual], DATESYTD('Calendar'[Date]))

-- Year-to-date target sales
YTD Sales Target = CALCULATE([Total Sales Target], DATESYTD('Calendar'[Date]))

-- Year-to-date variance in absolute value
YTD Variance = CALCULATE([Variance], DATESYTD('Calendar'[Date]))

-- Year-to-date variance as a percentage
YTD Variance % = CALCULATE([Variance %], DATESYTD('Calendar'[Date]))


-- ── DYNAMIC VISUALS & CONDITIONAL FORMATTING ──────────────────────────────

-- Returns 1 or -1 to drive red/green conditional colour rules across visuals
Target Status = IF([Variance] > 0, 1, -1)

-- Uses VAR to separate formatting logic from calculation
-- Displays variance % alongside a dynamic green or red emoji indicator
Variance Pct Label = 
    VAR up = "🟢"
    VAR down = "🔴"
    VAR formatted_var_pct = FORMAT(ABS([Variance %]), "0.0%")
RETURN
    formatted_var_pct & " " & IF([Variance %] > 0, up, down)

-- Uses VAR to store a filtered table of months where actual exceeded target
-- COUNTROWS then returns the total count as a scalar value
Months Target Reached = 
    VAR months_with_target_reached = FILTER(ALL('Calendar'), [Variance] > 0)
RETURN
    COUNTROWS(months_with_target_reached)

-- Uses VAR to capture total month count, then builds a dynamic string
-- that updates automatically as filters change — no hardcoded values
Trend Chart Title = 
    VAR month_count = COUNTROWS('Calendar')
RETURN    
    "We met target for " & [Months Target Reached] & " out of " & month_count & " months"
```

---

## 📂 Files

| File | Description |
|------|-------------|
| `Financial Kpis.pbix` | Full Power BI report file |
| `screenshot.png` | Dashboard preview image |

---

## 💡 Business Recommendations

The data makes three things clear. First, the forecasting process needs to be revisited — targets were missed 12 out of 14 months, which is not a performance problem, it is a planning problem. Second, the Q4 2023 decline warrants a dedicated investigation. A drop of that scale and consistency does not happen by accident, and understanding whether it was market-driven, seasonal, or internal is critical before setting targets for the next cycle. Third, with 8 of 12 reps below target, the performance spread across the team is wide enough that structured coaching — particularly for the reps at -6% and below — could meaningfully close the gap without requiring additional headcount.

---

*Financial Performance Dashboard | Power BI, DAX | Data Analytics Portfolio*
