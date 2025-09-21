# Modern-Trade-and-General-Trade-Common-Route-Sales-Analysis_PowerBI-and-Python
### This is a work project to show the outline &amp; depth of my work. The data used/shown here are dummy data to protect the privacy &amp; integrity of the company. The actual project is used by the top management to monitor the impact of a new MT outlets opening on the GT's sales of that same route and I help them to make decisions according to that.

# 📈 Impact of MT Outlet Openings on GT Sales
## This project was conducted for a consumer goods company with a dual sales model: General Trade (GT) via traditional outlets and Modern Trade (MT) via large-format retail stores. With ~36M sales records across 46K outlets, management wanted to assess whether opening new MT outlets was cannibalizing GT sales, especially in metro/divisional regions.

The analysis combined Power BI dashboards and Python statistical tests to measure the sales impact of MT openings on GT performance.

## Insights & Recommendations provided on:

1. Outlet Productivity: Tracking billed vs non-billed outlets, productive vs non-productive trends.

2. Sales Trends: Monitoring SKU category sales (value & quantity), YOY growth, and seasonal peaks.

3. SO Performance: Evaluating coverage, LPC, strike rate, visit%, and delivery%.

4. Behavioral Insights: Monitoring order frequency, repeat purchase, and location accuracy to detect inefficiencies.

## Data Structure & Model

1. Fact Table: public orders (36M rows).

2. Dimension Tables:
public outlets (46K records).
MT outlets (100 records).
GT and MT combined outlets (48K records).
Calendar table for time-based measures.

The schema follows a snowflake design, with public orders at the center connected to MT Outlets, Public Outlets, Combined Outlets, and Calendar.


## Relationships:
Public Order table is the main fact table connected with MT outlets table on outlet_name, MT Outlets table is connected with Public Outlets table on route, MT Outlets table is again conncted with GT and MT Combined Outlets table on outlet_code, calendar is connected with order table on order_date.

## 🔎 Executive Summary

The dashboard became a central sales intelligence hub, delivering:

  Executive Summary of Findings

📌 Metro outlets: GT sales dropped sharply (~ -23%) after MT openings.

📌 Divisional districts: Similar decline (~ -25%) in GT sales, whereas non-divisional areas remained stable or positive.

📌 Memo behavior: Memo growth was not sufficient in metros to offset GT losses, but in non-metros it ensured growth.

📌 Dhaka: Most negatively impacted, with GT sales decline of -20% to -40%.

## 🔎 Business Decision:

📌 Halt new MT openings in Dhaka/divisional metros.

📌 Focus expansion in non-metro/non-divisional regions.

📌 Nurture existing MT outlets with assortment optimization and damage/return monitoring.114% sales growth in Nivea’s Q1 2025 winter season.


## 📈 Insights Deep Dive (Key Categories)

1. Outlet Status (Metro vs Non-Metro): Metro GT growth significantly lower.

2. Division Status: Divisional GT sales dropped, non-divisional stable.

3. Memo Behavior: In non-metros, memo growth → GT growth; in metros, memo growth ≠ GT growth.

4. Dhaka-specific: GT sales most severely affected post MT opening.

✅ Recommendations

1. Strategic Expansion: Focus MT growth outside Dhaka.

2. Nurture Existing MTs: Improve assortment planning & reduce return rates.

3. Protect GT in Metros: Enhance promotional activities to prevent customer shift.1. Outlet Productivity → Non-productive outlets identified quickly; trend monitoring improved coverage.



## Detailed Page-by-Page Explanation:


### 📊 Page 1: Outlet Productivity Report

![Image Alt](https://github.com/RAAD07/NiveaSecondarySales_PowerBIProject/blob/191f51242140244e6dc199727910ec68d364cd18/Page1_Nivea%20Outlet%20Productivity%20Report%202024%20-%202025.png)

### 📊 PowerBI Report: GT–MT Cannibalization Dashboard

Filters: District, Outlet Status (Metro/Non-Metro).

Cards: Total GT outlets in MT routes.

Matrix Visuals (per MT outlet):

1. MT outlet name, opening month, GT route, # of GT outlets.

2. GT Sales (Before vs After Opening) → DAX measures using CALCULATE, EOMONTH, and USERELATIONSHIP.

3. Memo Count (Before vs After) → DISTINCTCOUNT of order_id.

4. Growth% for Sales & Memo.

5. MT sales (first 6 months), Route’s total sales, Route Growth%, Gap.

Sample DAX – GT Sales (Before)
GT Sales (Before) = 
VAR SelectedRoute = SELECTEDVALUE('MT Outlets'[GT Route])
VAR OpeningDate = SELECTEDVALUE('MT Outlets'[Opeing Month])
VAR StartDate = EOMONTH(OpeningDate, -4) + 1
VAR EndDate = EOMONTH(OpeningDate, -1)
RETURN
    CALCULATE(
        SUM('public orders'[sub_total]),
        FILTER(
            'Calendar',
            'Calendar'[Date] >= StartDate &&
            'Calendar'[Date] <= EndDate
        ),
        USERELATIONSHIP('public orders'[route_name], 'MT Outlets'[GT Route]),
        'public orders'[state] = "Done"
    )

## 📈 Statistical Analysis in Python

### 1. GT Growth% by Outlet Status (Metro vs Non-Metro)

Visualized with boxplots (Seaborn).

T-test on GT Growth% between Metro vs Non-Metro.

from scipy.stats import ttest_ind
metro_gt = df[df['Outlet Stetus']=="Metro"]['GT Growth%']
non_metro_gt = df[df['Outlet Stetus']=="Non-Metro"]['GT Growth%']
t_stat, p_val = ttest_ind(metro_gt, non_metro_gt, equal_var=False)
print(t_stat, p_val)


➡️ Result: Metro avg GT growth ~ -23%, p ≈ 0.065 → borderline significant.

### 2. GT Growth% by Division Status (Divisional vs Non-Divisional)

Created new column Division Status.

Outlier removal via IQR filtering.

T-test between Divisional vs Non-Divisional.
➡️ Result: Divisional avg GT growth ~ -25%, p = 0.038 → statistically significant.

### 3. Scatterplot Analysis

Memo Count Growth% vs GT Growth% for Metro & Non-Metro (without outliers).

Regression trendline added (sns.regplot).
➡️ Finding: Memo growth drove positive GT growth in Non-Metro, but in Metro GT sales still declined despite memo growth.



![Image Alt](https://github.com/RAAD07/NiveaSecondarySales_PowerBIProject/blob/40d20f10edf074f4130acc713b618176753d767f/Page2_Nivea%20Sales%20Value%20%26%20QTY.png)

Visuals:

Donut – SKU category-wise % sales (value).

Line – SKU category-wise monthly sales trend (quantity).

