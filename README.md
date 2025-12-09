
---

# 📊 **Global Layoffs Analysis (2020-2023) — SQL Project**

This project analyzes global layoff trends from 2020 to 2023 using SQL.
The workflow includes **data cleaning**, **standardization**, and **exploratory data analysis (EDA)** performed entirely in SQL.

The dataset is taken from Kaggle:
*“Layoffs Dataset” by Swaptr.*


---

# 🛠️ **Project Workflow**

This project consists of two main parts:

1. **Data Cleaning (SQL)**
2. **Exploratory Data Analysis (SQL)** 

Below is the full documentation for the **Data Cleaning** process.
<img width="962" height="259" alt="image" src="https://github.com/user-attachments/assets/b983653d-4541-438f-9fbf-6dfe06b05b73" />


---

# 🧹 **1. Data Cleaning**

The raw dataset contained inconsistencies, duplicates, missing values, and formatting issues.
A structured, step-by-step cleaning approach was used to create a clean and analysis-ready dataset.

---

## ✅ **Step 1 — Create a Staging Table**

A staging table (`layoffs_staging`) was created to ensure the **raw data remains untouched**:

* Cloned table schema
* Inserted raw data into the staging table
* All cleaning operations were done on the staging copy

This protects the original dataset and ensures reversible operations.

---

## ✅ **Step 2 — Identifying & Removing Duplicates**

Duplicate records were detected using `ROW_NUMBER()` with a `PARTITION BY` clause on:

* company
* location
* industry
* total_laid_off
* percentage_laid_off
* date
* stage
* country
* funds_raised_millions

Two approaches were explored:

### ✔️ Approach A: CTE-based duplicate removal

(Attempted but MySQL doesn't support deleting directly from CTE)
<img width="1457" height="273" alt="image" src="https://github.com/user-attachments/assets/3181a05a-1380-4ab2-b954-ffefbfe30bb0" />


### ✔️ Approach B:

A more reliable method was implemented:

1. Created a new table `layoffs_staging2`
2. Added a `row_num` column using `ROW_NUMBER()`
3. Deleted all rows where `row_num >= 2`
4. Dropped the `row_num` column after cleanup

This fully removed all true duplicates while preserving the first valid record.
<img width="1097" height="139" alt="image" src="https://github.com/user-attachments/assets/d10757ad-5c9c-4d72-ba91-2e8c369ef904" />
<img width="427" height="47" alt="image" src="https://github.com/user-attachments/assets/2c6cfde6-6545-4d4e-8729-8f851e42ca59" />



---

## ✅ **Step 3 — Standardizing Data**

Several inconsistencies were corrected to ensure uniformity across categories.

### 🔹 **3.1 Fixing Blank & NULL Industry Values**


* Empty strings were converted to `NULL`
* Missing industries were backfilled by matching company names
* If a company had at least one valid industry, the NULL rows were updated

This automated backfilling avoided manual corrections.


<img width="291" height="110" alt="image" src="https://github.com/user-attachments/assets/6183c676-c3b2-4138-aa2a-6f96f1362769" />
<img width="322" height="152" alt="image" src="https://github.com/user-attachments/assets/ffd0f4f2-51fb-41b3-8662-31460f9aee6b" />


---

### 🔹 **3.2 Standardizing Industry Names**

Inconsistent crypto labels were standardized:

* "CryptoCurrency" → "Crypto"
* "Crypto Currency" → "Crypto"

 
<img width="118" height="66" alt="image" src="https://github.com/user-attachments/assets/317d4050-1b72-4962-ab11-b4c8b0e1ae3a" />


<img width="547" height="91" alt="image" src="https://github.com/user-attachments/assets/670aaaa9-1582-4645-a34c-983bbfe25c55" />

---

### 🔹 **3.3 Cleaning Country Names**

Some rows contained `"United States."` (with trailing period).


Used:

```
TRIM(TRAILING '.' FROM country)
```

to standardize the country values.

<img width="221" height="44" alt="image" src="https://github.com/user-attachments/assets/ccd8c07e-71f6-425c-8dee-e9e39499fc0f" />
<img width="430" height="54" alt="image" src="https://github.com/user-attachments/assets/5b50ed2a-0141-46ca-bf82-1b2582386062" />


---

### 🔹 **3.4 Converting Date Format to DATE Type**

The `date` field was stored as **text ("MM/DD/YYYY")**.



Steps:

1. Converted text → date using `STR_TO_DATE()`
2. Updated column type to SQL `DATE`

This enables proper date-based analysis during EDA.

<img width="90" height="212" alt="image" src="https://github.com/user-attachments/assets/0cba2ee8-a394-47ac-a9d5-d6ef17c49647" />
<img width="419" height="63" alt="image" src="https://github.com/user-attachments/assets/af5bcd8e-b33f-46de-9a8b-0e631337611f" />
<img width="87" height="191" alt="image" src="https://github.com/user-attachments/assets/c92d2a3e-eea6-4d42-a4e9-2b4ef1138d89" />



---

## ✅ **Step 4 — Handling NULL Values**

Columns such as:

* `total_laid_off`
* `percentage_laid_off`
* `funds_raised_millions`

had valid NULL values representing missing or undisclosed information.

These NULLs were kept intentionally because:

* They preserve original meaning
* They are useful in EDA (e.g., filtering rows with incomplete information)

---

## ✅ **Step 5 — Removing Useless Rows**

Rows where **both**:

* `total_laid_off IS NULL`
* `percentage_laid_off IS NULL`

were deleted because they provide no value for analysis.


 <img width="546" height="213" alt="image" src="https://github.com/user-attachments/assets/9a25ae1c-8edb-4b61-bf3f-ba1f003b9b55" />
 <img width="387" height="79" alt="image" src="https://github.com/user-attachments/assets/f02f8b5a-bbf8-4b04-9e41-eb0a22d10b22" />



---

## 📁 **Final Cleaned Dataset**

After cleaning, `layoffs_staging2` contains:

* No duplicates
* Standardized industries and countries
* Proper DATE format
* Filled missing industry values
* Only meaningful rows retained

  <img width="1103" height="213" alt="image" src="https://github.com/user-attachments/assets/14afbcf1-7ade-4e1e-b813-ae8066e4408c" />


This dataset is now ready for the **Exploratory Data Analysis (EDA)** phase.

---

---

# 🔍 **2. Exploratory Data Analysis (EDA)**

After cleaning and standardizing the dataset, the next step was to perform **Exploratory Data Analysis (EDA)** to identify patterns, trends, and anomalies in global layoffs from 2020–2023.

The EDA was performed entirely in SQL and includes both **simple summaries** and **advanced analytical queries** using window functions and CTEs.

---

# 📌 **Overview of EDA Goals**

The analysis focuses on answering the following questions:

* Which companies had the highest layoffs?
* What industries were most impacted?
* How did layoffs differ across countries and locations?
* What percentage of companies experienced complete (100%) layoffs?
* How have layoffs trended over months and years?
* Which companies dominated layoffs each year?
* What is the cumulative (rolling) number of layoffs over time?

---

# 📊 **2.1 Basic Summary Metrics**

### ✔ Maximum layoffs in a single event

Identified the largest individual layoff event across the dataset.

### ✔ Percentage of workforce laid off

Calculated:

* Maximum layoff percentage
* Minimum non-null layoff percentage

This highlighted companies that laid off **100% of their workforce**, indicating shutdowns or bankruptcies.

### ✔ Companies that laid off 100% of employees

A query filtered on `percentage_laid_off = 1`, showing mostly startups that completely ceased operations.

Sorting by funding revealed:

* Companies with *significant funding* that still collapsed
* Examples include highly funded EV and media startups

---

# 📌 **2.2 Aggregated Layoff Insights**

These queries used `GROUP BY` to explore layoffs across dimensions.

### ✔ Companies with largest single-day layoffs

Sorted by `total_laid_off` to identify major events.

### ✔ Companies with the highest total layoffs (2020–2023)

Summed layoffs across all events, showing long-term impact.

### ✔ Layoffs by location

Reveals which cities were most affected during the period.

### ✔ Layoffs by country

Shows cross-country impact and highlights the most affected regions.

### ✔ Layoffs by year

Grouped by `YEAR(date)` to understand year-on-year trends.

### ✔ Layoffs by industry

Shows industries with the highest labor impact:

* Tech
* Retail
* Crypto
* Transportation
* Healthcare

### ✔ Layoffs by funding stage

Analyzed startup ecosystem impact:

* Seed
* Series A/B/C
* Pre-IPO
* Post-IPO

---

# 🏆 **2.3 Advanced EDA (CTEs + Window Functions)**

This section includes more sophisticated analysis using:

* Common Table Expressions (CTEs)
* `DENSE_RANK()`
* Rolling totals
* Monthly aggregation

---

## 🔹 **A. Top 3 Companies With Most Layoffs Each Year**

A two-level CTE approach was used:

1. **CTE 1:** Calculate total layoffs per company per year
2. **CTE 2:** Rank companies within each year using `DENSE_RANK()`
3. Filter top 3 per year

This highlights:

* Year-wise major contributors to layoffs
* Changing patterns over time (e.g., COVID impact, tech downturn, market corrections)

<img width="1132" height="400" alt="image" src="https://github.com/user-attachments/assets/1d7b8278-a6a3-4f73-aa43-c6b7395db727" />
<img width="315" height="281" alt="image" src="https://github.com/user-attachments/assets/9f5a41a9-801a-49e7-86c0-3e5768d93d47" />



---

## 🔹 **B. Rolling Total Layoffs (Month-by-Month)**

A monthly summary was created using `SUBSTRING(date,1,7)` to extract `YYYY-MM`.

A rolling sum was then calculated using:

```
SUM(total_laid_off) OVER (ORDER BY dates ASC)
```

This shows:

* The cumulative global layoff burden over time
* Trend lines for market stress
* Peaks during COVID waves and post-2022 tech downturn

<img width="720" height="266" alt="image" src="https://github.com/user-attachments/assets/5507a81e-c421-40f8-8365-2b9b14eb952e" />
<img width="225" height="448" alt="image" src="https://github.com/user-attachments/assets/83557e31-5de2-4666-9238-4f648b3338ee" />


---

# 📈 **2.4 Key Insights (Summary of EDA Findings)**


* Major layoffs peaked in **2022 and early 2023**, driven largely by tech.
* Several companies completely shut down (100% layoffs).
* The **United States** had the highest total layoffs.
* Tech, Retail, and Crypto industries were the most heavily impacted.
* Funding stage analysis showed significant layoffs even in **late-stage and well-funded companies**.
* Rolling totals indicated a consistent upward trend during major global events such as:

  * COVID-19
  * Post-pandemic restructuring
  * Global recession fears (2022–2023)

---

# 🧩 **Final Notes**

Both **data cleaning** and **EDA** were executed entirely in SQL, following a structured analytics approach:

1. Import
2. Clean
3. Standardize
4. Aggregate
5. Analyze
6. Interpret

This repository provides a complete SQL-based analytical workflow—suitable for data analysis portfolios and learning projects.

---


# 👤 **Author**

**Vishnu Nair**
Aspiring Data Analyst | SQL • Power BI • Excel • Python 

📧 **Email:** [vishnunairofficial2004@gmail.com]
🔗 **LinkedIn:** (www.linkedin.com/in/vishnu-nair-b6a764341)

---
