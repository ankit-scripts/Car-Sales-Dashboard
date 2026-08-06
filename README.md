# Car-Sales-Dashboard

# Step 1: Data Transformation & Cleaning (Power Query)

The first step in this project is to import the dataset into **Power Query** and perform data quality checks to ensure the data is accurate and analysis-ready.

### Data Quality Validation

After loading the dataset, verify the quality of each column by checking:

- **Valid** values
- **Error** values
- **Empty (Null)** values

> **Important:** The **Date** column should always have **100% Valid** values since it is the foundation for time intelligence calculations such as **YTD, MTD, and PYTD**.

### Data Cleaning

To improve data consistency and eliminate unwanted values:

- Used the **Replace Values** function to clean and standardize the data.
- Replaced incorrect or inconsistent values where required.
- Ensured the dataset was clean before proceeding to data modeling and DAX calculations.

---

### Power Query Features Used

- Data Quality Check (Valid, Error & Empty)
- Replace Values
- Data Cleaning
- Data Validation

  # 📊 KPI 1: Sales Overview Dashboard

## 📌 Problem Statement

The objective of this KPI is to provide **real-time insights into sales performance** by tracking key business metrics. These KPIs help stakeholders monitor sales trends, compare current performance with previous periods, and make data-driven business decisions.

### Business Requirements

The Sales Overview section includes the following Key Performance Indicators (KPIs):

- 📈 Year-to-Date (YTD) Total Sales
- 📅 Month-to-Date (MTD) Total Sales
- 📊 Year-over-Year (YoY) Sales Growth (%)
- 🔄 Difference between YTD Sales and Previous Year-to-Date (PYTD) Sales

---

# Step 1: Creating a Dynamic Calendar Table

Time Intelligence calculations in Power BI require a dedicated **Calendar (Date) Table**.

> **Why is a Calendar Table required?**
>
> The sales dataset may contain missing dates, which can lead to inaccurate Time Intelligence calculations. Creating a dedicated Calendar Table ensures continuous dates and enables functions like **YTD**, **MTD**, **PYTD**, and **YoY** to work correctly.

### Create Calendar Table

Go to **Modeling → New Table** and create the following table.

```DAX
Calendar Table = CALENDAR(MIN(car_data[Date]), MAX(car_data[Date]))
```

---

## Step 2: Create Date Attributes

Add the following calculated columns to the Calendar Table.

### Year

```DAX
Year = YEAR('Calendar Table'[Date])
```

### Week Number

```DAX
Week = WEEKNUM('Calendar Table'[Date])
```

### Month Name

```DAX
Month = FORMAT('Calendar Table'[Date], "MMMM")
```

---

# Step 3: Data Modeling

Create a relationship between the Calendar Table and the Sales Table.

### Relationship

| From | To | Relationship |
|-------|----|--------------|
| Calendar Table | Car Data | **One-to-Many (1:*)** |

- **1** → Calendar Table
- **\*** → Car Data (Fact Table)

This relationship enables all Time Intelligence functions to calculate correctly.

---

# Step 4: Create KPI Measures

## 1️⃣ Year-to-Date (YTD) Total Sales

Calculates cumulative sales from the beginning of the year to the selected date.

```DAX
YTD Total Sales = TOTALYTD(SUM(car_data[Price ($)]), 'Calendar Table'[Date])
```

---

## 2️⃣ Month-to-Date (MTD) Total Sales

Calculates cumulative sales for the current month.

```DAX
MTD Total Sales = TOTALMTD(SUM(car_data[Price ($)]), 'Calendar Table'[Date])
```

### MTD KPI Label

A formatted KPI label for better dashboard presentation.

```DAX
MTD KPI = CONCATENATE("MTD Total Sales : ", FORMAT([MTD Total Sales] / 1000000, "$0.00M"))
```

---

## 3️⃣ Previous Year-to-Date (PYTD) Sales

This measure serves as the foundation for calculating **YoY Growth** and **Sales Difference**.

```DAX
PYTD Total Sales = CALCULATE(SUM(car_data[Price ($)]), SAMEPERIODLASTYEAR('Calendar Table'[Date]))
```

---

## 4️⃣ Sales Difference

Calculates the difference between current Year-to-Date Sales and Previous Year-to-Date Sales.

```DAX
Sales Difference = [YTD Total Sales] - [PYTD Total Sales]
```

---

## 5️⃣ Year-over-Year (YoY) Sales Growth

Measures the percentage growth in sales compared to the previous year.

```DAX
YoY Sales Growth = DIVIDE([Sales Difference], [PYTD Total Sales])
```

> **Note:** Format this measure as **Percentage (%)** in Power BI.

---

## 6️⃣ Sales Difference Color

Used for conditional formatting of KPI cards.

- 🟢 Green → Positive Growth
- 🔴 Red → Negative Growth

```DAX
Sales Diff Colour = IF([Sales Difference] > 0,"Green", "Red")
```

Apply this measure in **Callout Value → Conditional Formatting** to dynamically change the KPI color based on performance.

---

# 📈 KPI Summary

| KPI | Description |
|------|-------------|
| YTD Total Sales | Total sales from the beginning of the current year |
| MTD Total Sales | Total sales for the current month |
| PYTD Total Sales | Total sales for the same period in the previous year |
| Sales Difference | Difference between YTD and PYTD sales |
| YoY Sales Growth | Percentage growth compared to the previous year |

---

## 💡 Key Learning Outcomes

Through this KPI implementation, the following Power BI concepts were applied:

- Dynamic Calendar Table Creation
- Data Modeling (One-to-Many Relationship)
- Time Intelligence Functions
- `TOTALYTD()`
- `TOTALMTD()`
- `SAMEPERIODLASTYEAR()`
- `CALCULATE()`
- `FORMAT()`
- `DIVIDE()`
- `IF()`
- KPI Card Design
- Conditional Formatting
- Dynamic Business Metrics

---

# 📊 KPI 2: Average Price Analysis

## 📌 Problem Statement

The objective of this KPI is to analyze the **average selling price** of vehicles over different time periods. Tracking the average price helps identify pricing trends, evaluate revenue performance, and compare the current year's pricing strategy against the previous year.

### Business Requirements

The Average Price Analysis section includes the following KPIs:

- 💰 Year-to-Date (YTD) Average Price
- 📅 Month-to-Date (MTD) Average Price
- 📈 Year-over-Year (YoY) Growth in Average Price
- 🔄 Difference between YTD Average Price and Previous Year-to-Date (PYTD) Average Price

---

# Step 1: Create Base Measure

Before creating Time Intelligence measures, first calculate the **Average Selling Price**.

```DAX
Avg Price =
SUM(car_data[Price ($)])
    / COUNT(car_data[Car_id])
```

> **Note:** This measure calculates the average selling price by dividing the total sales amount by the total number of cars sold.

---

# Step 2: Create Time Intelligence Measures

## 1️⃣ Year-to-Date (YTD) Average Price

Calculates the cumulative average selling price from the beginning of the year to the selected date.

```DAX
YTD Avg Price =
TOTALYTD(
    [Avg Price],
    'Calendar Table'[Date]
)
```

---

## 2️⃣ Previous Year-to-Date (PYTD) Average Price

Calculates the average selling price for the same period in the previous year.

```DAX
PYTD Avg Price =
CALCULATE(
    [Avg Price],
    SAMEPERIODLASTYEAR('Calendar Table'[Date])
)
```

---

## 3️⃣ Difference Between YTD and PYTD Average Price

Measures the difference in average price between the current year and the previous year.

```DAX
Avg Price Diff =
[YTD Avg Price] -
[PYTD Avg Price]
```

---

## 4️⃣ Year-over-Year (YoY) Average Price Growth

Calculates the percentage increase or decrease in average selling price compared to the previous year.

```DAX
YoY Avg Price Growth =
DIVIDE(
    [Avg Price Diff],
    [PYTD Avg Price]
)
```

> **Note:** Format this measure as **Percentage (%)** in Power BI.

---

## 5️⃣ Month-to-Date (MTD) Average Price

Calculates the average selling price for the current month.

```DAX
MTD Avg Price =
TOTALMTD(
    [Avg Price],
    'Calendar Table'[Date]
)
```

---

## 6️⃣ MTD Average Price KPI Label

Creates a formatted KPI label for displaying the Month-to-Date Average Price.

```DAX
MTD Avg Price KPI =
CONCATENATE(
    "MTD Avg Price : ",
    FORMAT([MTD Avg Price] / 1000, "$0.00K")
)
```

---

## 7️⃣ Average Price Conditional Formatting

Creates a measure to dynamically change the KPI color based on performance.

```DAX
Avg Price Colour =
IF(
    [Avg Price Diff] > 0,
    "Green",
    "Red"
)
```

### Conditional Formatting Logic

- 🟢 **Green** → Average Price has increased compared to the previous year.
- 🔴 **Red** → Average Price has decreased compared to the previous year.

Apply this measure to the **Callout Value → Conditional Formatting** option in the KPI Card visual.

---

# 📈 KPI Summary

| KPI | Description |
|------|-------------|
| Avg Price | Average selling price of all vehicles |
| YTD Avg Price | Average selling price from the beginning of the current year |
| PYTD Avg Price | Average selling price for the same period in the previous year |
| Avg Price Diff | Difference between current and previous year's average price |
| YoY Avg Price Growth | Percentage growth in average selling price |
| MTD Avg Price | Average selling price for the current month |

---

## 💡 Key Learning Outcomes

This KPI demonstrates the implementation of:

- Dynamic Average Price Calculation
- Time Intelligence using DAX
- `TOTALYTD()`
- `TOTALMTD()`
- `SAMEPERIODLASTYEAR()`
- `CALCULATE()`
- `DIVIDE()`
- `FORMAT()`
- `CONCATENATE()`
- `IF()`
- KPI Card Design
- Conditional Formatting
- Dynamic Business Metrics

---

# 📊 KPI 3: Cars Sold Analysis

## 📌 Problem Statement

The objective of this KPI is to analyze the **number of vehicles sold** across different time periods. Monitoring vehicle sales helps evaluate business performance, identify growth trends, and compare current sales with previous year's performance.

### Business Requirements

The Cars Sold Analysis section includes the following KPIs:

- 🚗 Year-to-Date (YTD) Cars Sold
- 📅 Month-to-Date (MTD) Cars Sold
- 📈 Year-over-Year (YoY) Growth in Cars Sold
- 🔄 Difference between YTD Cars Sold and Previous Year-to-Date (PYTD) Cars Sold

---

# Step 1: Create Time Intelligence Measures

## 1️⃣ Year-to-Date (YTD) Cars Sold

Calculates the total number of vehicles sold from the beginning of the year to the selected date.

```DAX
YTD Cars Sold =
TOTALYTD(
    COUNT(car_data[Car_id]),
    'Calendar Table'[Date]
)
```

---

## 2️⃣ Previous Year-to-Date (PYTD) Cars Sold

Calculates the total number of vehicles sold for the same period in the previous year.

```DAX
PYTD Cars Sold =
CALCULATE(
    COUNT(car_data[Car_id]),
    SAMEPERIODLASTYEAR('Calendar Table'[Date])
)
```

---

## 3️⃣ Difference Between YTD and PYTD Cars Sold

Measures the difference in the number of cars sold between the current year and the previous year.

```DAX
Cars Sold Diff =
[YTD Cars Sold] -
[PYTD Cars Sold]
```

---

## 4️⃣ Year-over-Year (YoY) Cars Sold Growth

Calculates the percentage increase or decrease in the number of vehicles sold compared to the previous year.

```DAX
YoY Cars Sold Growth =
DIVIDE(
    [Cars Sold Diff],
    [YTD Cars Sold]
)
```

> **Note:** Format this measure as **Percentage (%)** in Power BI.

---

## 5️⃣ Month-to-Date (MTD) Cars Sold

Calculates the total number of vehicles sold during the current month.

```DAX
MTD Cars Sold =
TOTALMTD(
    COUNT(car_data[Car_id]),
    'Calendar Table'[Date]
)
```

---

## 6️⃣ MTD Cars Sold KPI Label

Creates a formatted KPI label for displaying Month-to-Date Cars Sold.

```DAX
MTD Cars Sold KPI =
CONCATENATE(
    "MTD Cars Sold : ",
    FORMAT([MTD Cars Sold] / 1000, "$0.00K")
)
```

---

## 7️⃣ Cars Sold Conditional Formatting

Creates a measure to dynamically change the KPI color based on sales performance.

```DAX
Cars Sold Colour =
IF(
    [Cars Sold Diff] > 0,
    "Green",
    "Red"
)
```

### Conditional Formatting Logic

- 🟢 **Green** → More vehicles sold compared to the previous year.
- 🔴 **Red** → Fewer vehicles sold compared to the previous year.

Apply this measure to the **Callout Value → Conditional Formatting** option in the KPI Card visual to dynamically change the KPI color.

---

# 📈 KPI Summary

| KPI | Description |
|------|-------------|
| YTD Cars Sold | Total number of vehicles sold from the beginning of the current year |
| PYTD Cars Sold | Total number of vehicles sold for the same period in the previous year |
| Cars Sold Diff | Difference between current year and previous year's vehicle sales |
| YoY Cars Sold Growth | Percentage growth in vehicles sold compared to the previous year |
| MTD Cars Sold | Total number of vehicles sold during the current month |

---

## 💡 Key Learning Outcomes

This KPI demonstrates the implementation of:

- Time Intelligence Functions
- `TOTALYTD()`
- `TOTALMTD()`
- `CALCULATE()`
- `SAMEPERIODLASTYEAR()`
- `COUNT()`
- `DIVIDE()`
- `FORMAT()`
- `CONCATENATE()`
- `IF()`
- KPI Card Design
- Conditional Formatting
- Dynamic Sales Performance Analysis

---

# 📈 Problem Statement 2: Dashboard Visualizations

## 📌 Problem Statement

The objective of this section is to build interactive visualizations that transform raw sales data into meaningful business insights. These charts enable stakeholders to monitor sales trends, compare product performance, analyze regional sales, and evaluate company-wise performance through an intuitive and interactive dashboard.

### Dashboard Requirements

The dashboard includes the following visualizations:

1. 📈 YTD Sales Weekly Trend
2. 🚘 YTD Total Sales by Body Style
3. 🎨 YTD Total Sales by Color
4. 🌍 YTD Cars Sold by Dealer Region
5. 🏢 Company-Wise Sales Trend

---

# 📊 Chart 1: YTD Sales Weekly Trend

## Objective

Visualize the **weekly sales trend** throughout the year to identify sales patterns, seasonal fluctuations, and peak-performing weeks.

### Visualization

- **Chart Type:** Area Chart

### Fields Used

| Field | Value |
|--------|-------|
| X-Axis | Week |
| Y-Axis | Total Sales |

---

## Apply Year Filter

To display sales for a specific year:

- Add **Year** to the **Visual Filter** pane.
- Select **Basic Filtering**.
- Choose **2023**.

This ensures the chart displays only **Year-to-Date (YTD)** sales for the selected year.

---

## Create Total Sales Measure

Before highlighting the highest sales point, create the following measure:

```DAX
Total Sales =
SUM(car_data[Price ($)])
```

---

## Highlight Maximum Sales Point

Create a measure to identify the highest weekly sales value.

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

Add this measure to the **Data Labels** field to automatically highlight the week with the highest sales.

---

## Formatting

- Set the **X-Axis Maximum** to **54** (maximum number of weeks in a year).
- Remove gridlines for a cleaner appearance.
- Apply professional color formatting.
- Enable data labels for the **Maximum Sales Point**.

---

# 🍩 Chart 2: YTD Total Sales by Body Style

## Objective

Analyze the contribution of each **vehicle body style** to the total Year-to-Date sales.

### Visualization

- **Chart Type:** Donut Chart

### Fields Used

| Field | Value |
|--------|-------|
| Legend | Body Style |
| Values | YTD Total Sales |

---

## Formatting

- Apply an attractive color palette.
- Display percentage and value labels.
- Customize legend placement.
- Adjust donut size for improved readability.

---

# 📊 Chart 3: YTD Total Sales by Color

## Objective

Compare Year-to-Date sales across different vehicle colors.

### Visualization

- **Chart Type:** Stacked Column Chart

### Fields Used

| Field | Value |
|--------|-------|
| X-Axis | Color |
| Values | YTD Total Sales |

> **Note:** The X-Axis should contain the **Color** field to compare sales across vehicle colors.

---

## Formatting

- Apply custom colors.
- Enable data labels.
- Remove unnecessary gridlines.
- Adjust column spacing for better visibility.

---

# 🌍 Chart 4: YTD Cars Sold by Dealer Region

## Objective

Visualize the geographical distribution of vehicle sales across different dealer regions.

### Visualization

- **Chart Type:** Map

### Fields Used

| Field | Value |
|--------|-------|
| Legend | Dealer Region |
| Bubble Size | YTD Cars Sold |

---

## Formatting

- Adjust bubble size for better visibility.
- Apply suitable colors for dealer regions.
- Improve map readability using built-in formatting options.

---

# 📋 Chart 5: Company-Wise Sales Trend

## Objective

Provide a detailed company-wise sales summary for comparing business performance across different manufacturers.

### Visualization

- **Chart Type:** Table

### Columns

- Company
- YTD Average Price
- YTD Cars Sold
- YTD Total Sales
- %GT YTD Total Sales

---

## Display Grand Total Percentage

For **YTD Total Sales**:

- Open **Value Field Settings**.
- Select **Show Value As**.
- Choose **Percentage of Grand Total**.

This displays each company's contribution to the overall Year-to-Date sales.

---

## Display Units

Apply the following display units for better readability:

| Column | Display Unit |
|---------|--------------|
| YTD Avg Price | Thousands (K) |
| YTD Total Sales | Millions (M) |

---

## Formatting

- Apply professional color formatting.
- Center-align values where appropriate.
- Remove horizontal gridlines.
- Customize background color.
- Improve header styling and font formatting.

---

# 🎯 Dashboard Visuals Summary

| Visualization | Purpose |
|---------------|---------|
| Area Chart | Weekly YTD Sales Trend |
| Donut Chart | Sales Distribution by Body Style |
| Stacked Column Chart | Sales Comparison by Vehicle Color |
| Map | Regional Distribution of Cars Sold |
| Table | Company-Wise Sales Performance |

---

# 💡 Key Learning Outcomes

This visualization module demonstrates the implementation of:

- Area Charts
- Donut Charts
- Stacked Column Charts
- Map Visuals
- Table Visuals
- Visual-Level Filters
- Dynamic Data Labels
- DAX-Based Highlighting
- Conditional Formatting
- Data Label Customization
- Percentage of Grand Total
- Display Units (K & M)
- Professional Dashboard Formatting
- Interactive Business Reporting

---

# 🎛️ Dashboard Filters (Slicers)

## 📌 Objective

To improve dashboard interactivity and provide a better user experience, slicers have been added to enable users to dynamically filter the report based on different vehicle attributes. These slicers update all KPIs and visualizations in real time.

---

## Slicers Used

The dashboard includes the following **4 dropdown slicers**:

- 🚗 Body Style
- 🏪 Dealer Name
- ⚙️ Transmission
- 🔧 Engine

---

## Slicer Configuration

For each slicer:

1. Insert a **Slicer** visual.
2. Drag the required field into the slicer.
3. Navigate to:

   **Visual → Slicer Settings → Style → Dropdown**

4. Convert the slicer from **List** to **Dropdown** for a cleaner and more compact dashboard layout.

---

## Formatting

Apply consistent formatting across all slicers to maintain a professional dashboard appearance.

- Apply a consistent color theme.
- Adjust text alignment for better readability.
- Customize the background color.
- Format the title and border.
- Ensure all slicers have a uniform size and spacing.
- Position slicers vertically for easy navigation and filtering.

---
