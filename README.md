# CAR SALES DASHBOARD

## 📌 Project Overview:

- A comprehensive **Power BI Business Intelligence Dashboard** built to analyze vehicle sales performance through interactive visualizations, dynamic KPIs, and advanced DAX calculations. This project demonstrates the complete Power BI development workflow from data transformation and modeling to creating insightful dashboards that support data-driven business decisions.

- The dashboard is designed to help stakeholders monitor sales performance, compare historical trends, evaluate pricing strategies, and identify growth opportunities using real-time business metrics.

<br>

### The project contains two dashboard pages:

🔷 **Car Sales Dashboard | Overview**

🔶 **Car Sales Dashboard | Details**

---

## 🔍 Project Focus - What This Analysis Solves

- Track overall sales performance using YTD, MTD, and YoY KPIs.

- Compare current sales, average price, and cars sold with the previous year's performance.

- Analyze sales by body style, vehicle color, company, and dealer region.

- Identify weekly sales trends and high-performing sales periods.

- Provide transaction-level details for deeper analysis of individual car sales.

---

## 📌 Key terms to know before diving into the dashboard

- **YTD:** Year-to-Date - value accumulated from the beginning of the year up to the selected date.

- **MTD:** Month-to-Date - value accumulated from the beginning of the current month up to the selected date.

- **PYTD:** Previous Year-to-Date - value for the same period in the previous year.

- **YoY:** Year-over-Year - comparison of current performance with the previous year's performance.

- **KPI:** Key Performance Indicator used to measure business performance.

- **Time Intelligence:** DAX functionality used to compare and analyze data across different time periods.

- **Calendar Table:** A dedicated date table used for accurate time-based calculations.

- **Data Modeling:** Creating relationships between tables so that data can be analyzed correctly.

- **DAX:** Data Analysis Expressions used to create measures and calculations in Power BI.
  
---

## 📌 How Does This Dashboard Provide Valuable Insights?

The dashboard converts raw car sales data into a set of interactive reports that can be used to:

- Monitor current sales performance.

- Compare sales with the previous year.

- Track changes in average selling price.

- Analyze the number of cars sold.

- Identify weekly sales trends and peak sales periods.

- Compare sales across different body styles and colors.

- Analyze dealer-region performance.

- Compare company-wise sales contribution.

- Filter the report and investigate specific sales records.

---

## 🔷 Data Model View

A dedicated **Calendar Table** is used for Time Intelligence calculations. The Calendar Table is connected to the main `car_data` table using a **one-to-many (1:*) relationship**.

### Data Model Structure

- **Calendar Table** → One side (`1`)
  
- **car_data** → Many side (`*`)
  
- Relationship is created using the **Date** column.

<br>

<img width="1673" height="1246" alt="image" src="https://github.com/user-attachments/assets/ab419759-ef93-4a33-9a42-1e823b4259da" />


---

## 🔷 Data Transformation & Cleaning (Power Query)

The first step of the project is to **transform and clean the raw data using Power Query**.

### 🔶 Data Quality Check

- **Valid Values:** Verified that the data contains valid and correctly formatted values.
  
- **Error Values:** Identified and reviewed any errors present in the data.

- **Empty Values:** Checked for blank or missing values that may affect the analysis.

> **Important:** The **Date** column must contain **100% valid values** because it is used for Time Intelligence calculations.

### 🔶 Data Cleaning

- Identified and reviewed **errors and empty values**.

- Used **Replace Values** to correct inconsistent or incorrect data.

- Ensured the dataset was clean and consistent.

- Prepared the cleaned data for **Data Modeling and DAX calculations**.
  
---

# 📊 Dashboard 1: Car Sales Dashboard | Overview

The Overview page provides the main summary of the car sales analysis. It contains three major KPI sections followed by interactive charts and a company-wise sales table.

## KPI 1️⃣ Sales Overview
<p>
<img width="400" alt="Car Sales Dashboard Overview" src="https://github.com/user-attachments/assets/3e68de88-2507-4245-9eb6-d5ad2bf8bb44" />
<p>
  
**Requirement**

The Sales Overview section tracks:

- YTD Average Price
- MTD Average Price
- YOY Growth in Average Price
- Difference between YTD Average Price and PTYD Average Price


---

### Step 1 ➡️ Creating a Dynamic Calendar Table

The sales dataset may contain missing dates, which can lead to inaccurate Time Intelligence calculations. Creating a dedicated Calendar Table ensures continuous dates and enables functions like **YTD**, **MTD**, **PYTD**, and **YoY** to work correctly.

🔶 Go to **Modeling → New Table**:

```DAX
Calendar Table =
CALENDAR(
    MIN(car_data[Date]),
    MAX(car_data[Date])
)
```
---

### Step 2 ➡️ Create Date Attributes

🔶 **Year**

```DAX
Year =
YEAR('Calendar Table'[Date])
```
<br> 

🔶 **Week**

```DAX
Week =
WEEKNUM('Calendar Table'[Date])
```
<br>

🔶 **Month**

```DAX
Month =
FORMAT('Calendar Table'[Date], "MMMM")
```

The Calendar Table is then connected to the `car_data` table using a **1:* relationship**.

---

### Step 3 ➡️ Data Modeling

Create a relationship between the Calendar Table and the Sales Table.

**Relationship**

| From | To | Relationship |
|-------|----|--------------|
| Calendar Table | Car Data | **One-to-Many (1:*)** |

- **1** → Calendar Table
- **\*** → Car Data (Fact Table)

This relationship enables all Time Intelligence functions to calculate correctly.

---

### Step 4 ➡️ Create KPI Measures

🔶 **YTD Total Sales**

```DAX
YTD Total Sales =
TOTALYTD(
    SUM(car_data[Price ($)]),
    'Calendar Table'[Date]
)
```
<br>

🔶 **MTD Total Sales**

```DAX
MTD Total Sales =
TOTALMTD(
    SUM(car_data[Price ($)]),
    'Calendar Table'[Date]
)
```
<br>

🔶 **MTD KPI**

```DAX
MTD KPI =
CONCATENATE(
    "MTD Total Sales : ",
    FORMAT([MTD Total Sales] / 1000000, "$0.00M")
)
```
<br>

🔶 **PYTD Total Sales**

PYTD is required before calculating the YoY growth and sales difference.

```DAX
PYTD Total Sales =
CALCULATE(
    SUM(car_data[Price ($)]),
    SAMEPERIODLASTYEAR('Calendar Table'[Date])
)
```
<br>

🔶 **Sales Difference**

```DAX
Sales Difference =
[YTD Total Sales] -
[PYTD Total Sales]
```
<br>

🔶 **YoY Sales Growth**

```DAX
YoY Sales Growth =
[Sales Difference] /
[PYTD Total Sales]
```

Format the measure as **Percentage (%)**.

<br>


🔶 **Sales Difference Colour**

This measure is used in **Callout Value → Conditional Formatting** to dynamically change the KPI color based on performance.

```DAX
Sales Diff Colour =
IF(
    [Sales Difference] > 0,
    "Green",
    "Red"
)
```

The result is used in the KPI card's conditional formatting:

🟢 Green → Positive Growth

🔴 Red → Negative Growth

---

## KPI 2️⃣ Average Price Analysis
<p>
<img width="400" alt="Car Sales Dashboard Overview" src="https://github.com/user-attachments/assets/0f5ff8bf-474a-4e33-a664-4f2cea988565" />
<p>

**Requirement**

The Average Price section tracks:

- YTD Average Price
- MTD Average Price
- YoY Growth in Average Price
- Difference between YTD Average Price and PYTD Average Price

<br>
<br>

🔶 **Average Price**

The base Average Price measure is created first.

```DAX
Avg Price =
SUM(car_data[Price ($)]) /
COUNT(car_data[Car_id])
```

<br>

🔶 **YTD Average Price**

```DAX
YTD Avg Price =
TOTALYTD(
    [Avg Price],
    'Calendar Table'[Date]
)
```

<br>

🔶 **PYTD Average Price**

```DAX
PYTD Avg Price =
CALCULATE(
    [Avg Price],
    SAMEPERIODLASTYEAR('Calendar Table'[Date])
)
```

<br>

🔶 **Average Price Difference**

```DAX
Avg Price Diff =
[YTD Avg Price] -
[PYTD Avg Price]
```

<br>

🔶 **Average Price Colour**

```DAX
Avg Price Colour =
IF(
    [Avg Price Diff] > 0,
    "Green",
    "Red"
)
```

This measure is used for conditional formatting of the Average Price KPI.

<br>

🔶 **YoY Average Price Growth**

```DAX
YoY Avg Price Growth =
[Avg Price Diff] /
[PYTD Avg Price]
```

Format the measure as **Percentage (%)**.

<br>

🔶 **MTD Average Price**

```DAX
MTD Avg Price =
TOTALMTD(
    [Avg Price],
    'Calendar Table'[Date]
)
```

<br>

🔶 **MTD Average Price KPI**

```DAX
MTD Avg Price KPI =
CONCATENATE(
    "MTD Avg Price : ",
    FORMAT([MTD Avg Price] / 1000, "$0.00K")
)
```

---

## KPI 3️⃣ Cars Sold Metrics
<p>
<img width="400" alt="Car Sales Dashboard Overview" src="https://github.com/user-attachments/assets/442723d8-4aa4-4489-b6b7-7472045eac62" />
<p>
  
**Requirement**

The Cars Sold section tracks:

- YTD Cars Sold
- MTD Cars Sold
- YoY Growth in Cars Sold
- Difference between YTD Cars Sold and PYTD Cars Sold

<br>

🔶 **YTD Cars Sold**

```DAX
YTD Cars Sold =
TOTALYTD(
    COUNT(car_data[Car_id]),
    'Calendar Table'[Date]
)
```

<br>

🔶 **PYTD Cars Sold**

```DAX
PYTD Cars Sold =
CALCULATE(
    COUNT(car_data[Car_id]),
    SAMEPERIODLASTYEAR('Calendar Table'[Date])
)
```

<br>

🔶 **Cars Sold Difference**

```DAX
Cars Sold Diff =
[YTD Cars Sold] -
[PYTD Cars Sold]
```

<br>

🔶 **Cars Sold Colour**

```DAX
Cars Sold Colour =
IF(
    [Cars Sold Diff] > 0,
    "Green",
    "Red"
)
```

The measure is used for conditional formatting of the Cars Sold KPI.

<br>

🔶 **YoY Cars Sold Growth**

```DAX
YoY Cars Sold Growth =
[Cars Sold Diff] /
[YTD Cars Sold]
```

Format the measure as **Percentage (%)**.

<br>

🔶 **MTD Cars Sold**

```DAX
MTD Cars Sold =
TOTALMTD(
    COUNT(car_data[Car_id]),
    'Calendar Table'[Date]
)
```

<br>

🔶 **MTD Cars Sold KPI**

```DAX
MTD Cars Sold KPI =
CONCATENATE(
    "MTD Cars Sold : ",
    FORMAT([MTD Cars Sold] / 1000, "$0.00K")
)
```

---

## 🔷 Charts and Visualizations

### 1️⃣ YTD Sales Weekly Trend

**Chart Type: Area Chart**
<p>
  <img width="500" alt="Car Sales Dashboard Overview" src="https://github.com/user-attachments/assets/c9bdbdda-c4c3-4252-8911-80995d9e4492" />
<p>
  
**Fields**

- **X-Axis:** Week
- **Y-Axis:** Total Sales

A Year filter is applied using **Basic Filtering**, with **2023** selected for the analysis.
The X-Axis range is set to **54**, as the number of weeks in a year does not exceed 54.

<br>

🔶 **Total Sales**

```DAX
Total Sales =
SUM(car_data[Price ($)])
```
<br>

🔶 **Maximum Sales Point**

The following measure identifies the maximum weekly sales value.

```DAX
Max Point =
IF(
    MAXX(
        ALLSELECTED('Calendar Table'[Week]),
        [Total Sales]
    ) = [Total Sales],
    MAXX(
        ALLSELECTED('Calendar Table'[Week]),
        [Total Sales]
    ),
    BLANK()
)
```

The `Max Point` measure is added to the data labels to highlight the highest sales point on the chart.

<br>

**Formatting**

- Removed gridlines.
- Applied dashboard color formatting.
- Adjusted the X-Axis range.
- Highlighted the maximum sales point.

---

### 2️⃣ YTD Total Sales by Body Style
<p>
  <img width="300" alt="Car Sales Dashboard Overview" src="https://github.com/user-attachments/assets/1ee52791-8a56-447d-bf1a-b979c251b52c" />
<p>
    
**Chart Type: Donut Chart**

**Fields**

- **Legend:** Body Style
- **Values:** YTD Total Sales

The chart shows the contribution of different vehicle body styles to total YTD sales.

**Formatting**

- Applied consistent colors.
- Adjusted labels and legend.
- Formatted the visual to match the dashboard theme.

---

### 3️⃣ YTD Total Sales by Color
<p>
  <img width="300" alt="Car Sales Dashboard Overview" src="https://github.com/user-attachments/assets/7469ef3b-ade4-4339-9f1e-0598dd4afb81" />
<p>
  
**Chart Type: Stacked Column Chart**

**Fields**

- **X-Axis:** Color
- **Values:** YTD Total Sales

The chart compares YTD sales across different vehicle colors.

### Formatting

- Applied color formatting.
- Adjusted labels and axes.
- Removed unnecessary gridlines.
- Maintained consistency with the overall dashboard design.

---

### 4️⃣ YTD Cars Sold by Dealer Region

**Chart Type: Map**

<p>
  <img width="500" alt="Car Sales Dashboard Overview" src="https://github.com/user-attachments/assets/01165942-eb9f-4b55-99df-773fe00fc230" />
<p>

**Fields**

- **Legend:** Dealer Region
- **Bubble Size:** YTD Cars Sold

The map provides a geographical view of cars sold across different dealer regions.

### Formatting

- Adjusted bubble size for visibility.
- Applied appropriate colors.
- Formatted the map for better readability.

---

### 5️⃣ Company-Wise Sales Trend

**Chart Type: Table**

<p>
  <img width="600" alt="Car Sales Dashboard Overview" src="https://github.com/user-attachments/assets/72de8221-0bdc-4841-b5ed-ca8dc1c8ad83" />
<p>
  
**Columns**

- Company
- YTD Avg Price
- YTD Cars Sold
- YTD Total Sales
- %GT YTD Total Sales

The table provides a company-wise comparison of sales performance.

### Percentage of Grand Total

YTD Total Sales is displayed as a percentage of the grand total to show each company's contribution to overall sales.

### Display Units

- **YTD Avg Price:** Thousands
- **YTD Total Sales:** Millions

### Formatting

- Adjusted column alignment.
- Removed horizontal gridlines.
- Changed the background color.
- Applied consistent formatting.
- Adjusted display units for easier reading.

---

# 📊 Dashboard 2: Car Sales Dashboard | Details

- The Details page provides a transaction-level view of the car sales data.

- It is designed for users who want to move from the high-level KPIs and charts on the Overview page to individual sales records.

## 🔷 Detailed Sales Grid

### Chart Type: Matrix

<p>
  <img width="1000" alt="Car Sales Dashboard Overview" src="https://github.com/user-attachments/assets/a7536ea3-01bc-4877-b60e-49c5dffbb726" />
<p>
  
**Information Included**: The Matrix visual contains relevant information for individual car sales, including:

- Car Model
- Body Style
- Color
- Sales Amount
- Dealer Region
- Dealer Name
- Engine
- Transmission
- Date

This provides a detailed view of the underlying sales transactions.

## 🔷 Data Bars for Total Sales

Data Bars are applied to the **Total Sales** column to make it easier to compare sales values visually.

### Configuration

1. Select the Matrix visual.
2. Open **Conditional Formatting**.
3. Select **Data Bars**.
4. Apply Data Bars to the Total Sales field.

Higher sales values are represented by longer data bars, making differences between transactions easier to identify.

## 🔷 Details Page Formatting

The Matrix visual was formatted to maintain consistency with the Overview page.

- Adjusted column alignment.
- Removed gridlines.
- Changed the background color.
- Adjusted column widths.
- Applied consistent visual formatting.

---

# 🔷 Dashboard Navigation & Filters

To enhance user experience and improve report navigation, interactive **Navigation Buttons** and **Dropdown Slicers** have been added to the dashboard. These features allow users to seamlessly switch between report pages and dynamically filter data based on their business requirements.

🔶 **Navigation Buttons**

Two navigation buttons are provided:

- **Overview**
- **Details**

The buttons allow users to move between the summary dashboard and the detailed sales page.

The navigation area is formatted with different states to make the active page clear.


🔶 **Dashboard Filters**

The dashboard contains four slicers:

1. **Body Style**
2. **Dealer Name**
3. **Transmission**
4. **Engine**

These filters allow users to narrow down the dashboard based on specific vehicle and dealer attributes.

---

## 📌 Key Learning Outcomes

- Data cleaning and transformation using Power Query.
- Building a dedicated Calendar Table for Time Intelligence.
- Creating one-to-many relationships through data modeling.
- Writing DAX measures for YTD, MTD, PYTD, YoY, and variance analysis.
- Creating dynamic KPI cards with conditional formatting.
- Building interactive charts, maps, tables, and Matrix visuals.
- Using `CALCULATE()`, `TOTALYTD()`, `TOTALMTD()`, `SAMEPERIODLASTYEAR()`, `SUM()`,
  `COUNT()`, `MAXX()`, `DIVIDE()`, `FORMAT()`, `IF()`, and `ALLSELECTED()`.
- Applying Data Bars and other visual formatting techniques.
- Creating interactive slicers and page navigation.
- Designing separate summary and transaction-level dashboard pages.
- Turning raw sales data into an interactive business reporting solution.

---

#Dashboard View:

### Overview:

<img width="2715" height="1520" alt="image" src="https://github.com/user-attachments/assets/aef4cda9-8ba3-421b-8481-421af8a0283e" />

---
### Details View:

<img width="2412" height="1346" alt="image" src="https://github.com/user-attachments/assets/cc1d55eb-96fd-4cf6-8dad-b124cd767ec0" />

---
