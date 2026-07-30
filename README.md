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
