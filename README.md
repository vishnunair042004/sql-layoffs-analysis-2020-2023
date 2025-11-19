
---

# 📊 **Global Layoffs Analysis (2020–2023) — SQL Project**

This project analyzes global layoff trends from 2020 to 2023 using SQL.
The workflow includes **data cleaning**, **standardization**, and **exploratory data analysis (EDA)** performed entirely in SQL.

The dataset is taken from Kaggle:
*“Layoffs Dataset (2022–2023)” by Swaptr.*
https://www.kaggle.com/datasets/swaptr/layoffs-2022

---

# 🛠️ **Project Workflow**

This project consists of two main parts:

1. **Data Cleaning (SQL)**
2. **Exploratory Data Analysis (SQL)** *(will be added soon)*

Below is the full documentation for the **Data Cleaning** process.

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

### ✔️ Approach B:

A more reliable method was implemented:

1. Created a new table `layoffs_staging2`
2. Added a `row_num` column using `ROW_NUMBER()`
3. Deleted all rows where `row_num >= 2`
4. Dropped the `row_num` column after cleanup

This fully removed all true duplicates while preserving the first valid record.

---

## ✅ **Step 3 — Standardizing Data**

Several inconsistencies were corrected to ensure uniformity across categories.

### 🔹 **3.1 Fixing Blank & NULL Industry Values**

* Empty strings were converted to `NULL`
* Missing industries were backfilled by matching company names
* If a company had at least one valid industry, the NULL rows were updated

This automated backfilling avoided manual corrections.

---

### 🔹 **3.2 Standardizing Industry Names**

Inconsistent crypto labels were standardized:

* "CryptoCurrency" → "Crypto"
* "Crypto Currency" → "Crypto"

---

### 🔹 **3.3 Cleaning Country Names**

Some rows contained `"United States."` (with trailing period).
Used:

```
TRIM(TRAILING '.' FROM country)
```

to standardize the country values.

---

### 🔹 **3.4 Converting Date Format to DATE Type**

The `date` field was stored as **text ("MM/DD/YYYY")**.

Steps:

1. Converted text → date using `STR_TO_DATE()`
2. Updated column type to SQL `DATE`

This enables proper date-based analysis during EDA.

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

---

## 📁 **Final Cleaned Dataset**

After cleaning, `layoffs_staging2` contains:

* No duplicates
* Standardized industries and countries
* Proper DATE format
* Filled missing industry values
* Only meaningful rows retained

This dataset is now ready for the **Exploratory Data Analysis (EDA)** phase.

---

# 🔍 **2. Exploratory Data Analysis (EDA)**

*(To be added once EDA queries are shared.)*

The EDA section will include:

* Layoffs by year
* Layoffs by country
* Layoffs by industry
* Layoffs by company
* Funding vs layoffs
* Stage-wise analysis
* Month-over-month trends

---

# 📦 **Repository Structure (Recommended)**

```
📁 global-layoffs-sql-analysis
│
├── README.md
├── data/
│   ├── layoffs_raw.csv
│   └── layoffs_cleaned.csv
│
├── sql/
│   ├── 1_data_cleaning/
│   └── 2_eda/
│
└── screenshots/
```

---

If you want, I can now also create the:

✔ **EDA README section**
✔ **SQL file names & descriptions**
✔ **Full repository README.md combining cleaning + EDA**

Just share your EDA SQL queries next.
