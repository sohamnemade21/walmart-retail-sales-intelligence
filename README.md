# Walmart Retail Sales Analysis

### PostgreSQL + SQL + Python \| End-to-end data analytics project

> An independent retail-sales analysis project using a third-party
> Walmart-themed transaction dataset. Python is used for data inspection
> and preparation; PostgreSQL is used to store the cleaned records and
> answer business questions with SQL.

------------------------------------------------------------------------

## 1. Project at a glance

This project explores transaction-level retail data to understand sales
activity, customer payment preferences, product-category ratings, branch
performance, and estimated profit.

The workflow is:

1.  Inspect the raw CSV and understand its structure.
2.  Clean duplicate and incomplete records.
3.  Convert price, date, time, and numeric fields into analysis-ready
    types.
4.  Derive transaction revenue and estimated profit.
5.  Load the cleaned records into PostgreSQL.
6.  Answer business questions using SQL aggregations, CTEs, window
    functions, date functions, and conditional logic.

**Primary database:** PostgreSQL\
**Supporting tools:** Python, pandas, SQLAlchemy, Jupyter Notebook,
pgAdmin 4\
**Optional comparison:** MySQL query script is included, but PostgreSQL
is the recommended execution path for this repository.

### What this project is --- and is not

This is an analysis of the supplied Kaggle dataset. It is not an
official Walmart internal dataset, a live Walmart data feed, or a
production retail forecasting system. The results describe the records
in this dataset only and should not be presented as company-wide Walmart
facts.

------------------------------------------------------------------------

## 2. Dataset

The repository currently contains two CSV files:

  -----------------------------------------------------------------------------
  File                       Purpose                             Observed shape
  -------------------------- --------------------- ----------------------------
  `Walmart.csv`              Raw input                 10,051 rows × 11 columns

  `walmart_clean_data.csv`   Cleaned/exported           9,969 rows × 11 columns
                             dataset supplied in   
                             this repository       
  -----------------------------------------------------------------------------

The supplied raw file has 10,051 rows, 31 missing values in
`unit_price`, 31 missing values in `quantity`, and 51 duplicate rows.
The supplied cleaned CSV has 9,969 rows, no missing values, and no
duplicate rows.

The dataset contains the following fields:

  -----------------------------------------------------------------------
  Column                  Meaning                 Expected analytical
                                                  type
  ----------------------- ----------------------- -----------------------
  `invoice_id`            Transaction/invoice     Integer or text
                          identifier              identifier

  `Branch`                Branch code             Text

  `City`                  City associated with    Text
                          the branch              

  `category`              Product category        Text

  `unit_price`            Price per unit; raw     Decimal
                          values include a `$`    
                          symbol                  

  `quantity`              Units purchased         Integer

  `date`                  Transaction date,       Date
                          represented as          
                          `DD/MM/YY` in the       
                          supplied CSV            

  `time`                  Transaction time        Time

  `payment_method`        Payment method used     Text

  `rating`                Customer rating         Decimal

  `profit_margin`         Margin rate supplied in Decimal
                          the dataset             
  -----------------------------------------------------------------------

**Data source:** [Walmart 10K Sales Dataset on
Kaggle](https://www.kaggle.com/datasets/najir0123/walmart-10k-sales-datasets)

Please retain the source attribution when redistributing or publishing
the dataset. Check the dataset page for its current license and usage
conditions before republishing the raw data.

### Important data interpretation

-   `total` is derived as `unit_price × quantity`; it represents
    transaction revenue/value in the dataset, not necessarily audited
    net revenue.
-   `profit_margin` is treated as a rate (for example, `0.48` means
    48%). Therefore estimated profit is calculated as
    `total × profit_margin`.
-   The dataset contains dates from 2019 through 2023. The
    year-over-year SQL question compares 2022 with 2023; it should not
    be interpreted as a general trend across all years.
-   The dataset's `invoice_id` values are unique in the supplied cleaned
    CSV, but do not assume that this is a globally unique Walmart
    invoice key outside this dataset.

------------------------------------------------------------------------

## 3. Repository structure

``` text
Walmart_SQL_Python-main/
├── Walmart.csv                     # Raw source data
├── walmart_clean_data.csv          # Supplied cleaned/exported data
├── project.ipynb                   # Python exploration, cleaning and loading workflow
├── PSQL Queries.sql                # PostgreSQL analysis queries
├── MySQL Queries.sql                # MySQL equivalents / comparison
├── Walmart Business Problems.pdf   # Business questions and their purposes
├── Walmart Project.png             # Project visual asset
├── walmart_project-piplelines.png  # Pipeline visual asset (filename as supplied)
├── requirements.txt                # Python dependencies (review before installing)
└── README.md
```

The current notebook is a working analysis notebook, not a fully
automated command-line pipeline. Some setup and connection cells need to
be configured for your own machine before running.

------------------------------------------------------------------------

## 4. Technology stack

  -----------------------------------------------------------------------
  Technology                          Role
  ----------------------------------- -----------------------------------
  Python                              Data inspection and transformation

  pandas                              CSV loading, missing-value checks,
                                      duplicate removal, feature creation

  SQLAlchemy                          Python-to-database connection and
                                      table loading

  PostgreSQL                          Persistent storage and analytical
                                      SQL

  pgAdmin 4                           Database administration, query
                                      execution, and table browsing

  Jupyter Notebook                    Reproducible exploration and
                                      documentation

  MySQL                               Optional second SQL dialect
                                      represented by `MySQL Queries.sql`
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 5. Setup and execution

### Prerequisites

-   Python 3.10 or later (use a version supported by all installed
    packages)
-   PostgreSQL Server installed and running
-   pgAdmin 4
-   Git (optional, for cloning)
-   The CSV files from this repository

### 5.1 Clone the repository

``` bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd Walmart_SQL_Python-main
```

Replace the placeholder with your own repository URL.

### 5.2 Create and activate a virtual environment

**Windows PowerShell**

``` powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
```

If PowerShell blocks activation, run this only for the current terminal
session and retry:

``` powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

### 5.3 Install dependencies

For the PostgreSQL workflow:

``` powershell
python -m pip install --upgrade pip
python -m pip install pandas sqlalchemy psycopg2-binary jupyter python-dotenv
```

For the optional MySQL workflow:

``` powershell
python -m pip install pymysql
```

If maintaining a dependency file, keep `requirements.txt` limited to
installable package names and version constraints. Do not put terminal
instructions or shell commands inside it.

### 5.4 Create the PostgreSQL database

In pgAdmin:

1.  Connect to your local PostgreSQL server.
2.  Right-click **Databases**.
3.  Select **Create → Database**.
4.  Set the name to `walmart_db`.
5.  Set the owner to your PostgreSQL user (commonly `postgres`).
6.  Save.

Alternatively, while connected to a maintenance database such as
`postgres`, run this as a standalone statement:

``` sql
CREATE DATABASE walmart_db;
```

Do not run `CREATE DATABASE` inside a transaction block.

### 5.5 Configure database credentials securely

Do not hard-code database passwords in a notebook or commit them to Git.

Create a local `.env` file in the project root:

``` env
PG_HOST=localhost
PG_PORT=5432
PG_DATABASE=walmart_db
PG_USER=postgres
PG_PASSWORD=replace_with_your_local_password
```

Add `.env` to `.gitignore`:

``` gitignore
.env
.venv/
__pycache__/
.ipynb_checkpoints/
```

Commit a `.env.example` file containing placeholders, never real
credentials.

Example SQLAlchemy connection:

``` python
import os
from dotenv import load_dotenv
from sqlalchemy import create_engine, URL

load_dotenv()

db_url = URL.create(
    drivername="postgresql+psycopg2",
    username=os.getenv("PG_USER"),
    password=os.getenv("PG_PASSWORD"),
    host=os.getenv("PG_HOST", "localhost"),
    port=int(os.getenv("PG_PORT", "5432")),
    database=os.getenv("PG_DATABASE", "walmart_db"),
)

engine = create_engine(db_url, pool_pre_ping=True)
```

`URL.create()` safely handles special characters in passwords.

### 5.6 Run the notebook

``` powershell
jupyter notebook
```

Open `project.ipynb` and run the cells in order. Confirm the CSV path
and database settings before running the database-loading cell.

------------------------------------------------------------------------

## 6. Data preparation methodology

The raw file should be processed in a deliberate and verifiable
sequence:

1.  **Load:** read `Walmart.csv` with pandas.
2.  **Profile:** inspect dimensions, column names, data types, summary
    statistics, missing values, and duplicate rows.
3.  **Remove duplicates:** identify exact duplicate records and remove
    them only after checking the effect on row count.
4.  **Handle missing values:** investigate missing `unit_price` and
    `quantity`; document why affected rows are removed or imputed.
5.  **Normalize price:** remove the currency symbol from `unit_price`
    and convert it to a numeric decimal type.
6.  **Normalize date and time:** parse `date` using the explicit format
    `%d/%m/%y` and parse `time` as a time value.
7.  **Normalize column names:** use consistent lowercase snake_case
    names. For example, rename `Branch` to `branch` and `City` to
    `city`.
8.  **Validate numeric fields:** ensure `quantity`, `rating`, and
    `profit_margin` have appropriate numeric types and sensible ranges.
9.  **Derive metrics:** calculate `total = unit_price * quantity` and
    `estimated_profit = total * profit_margin`.
10. **Export and load:** save a reproducible cleaned file and load it
    into PostgreSQL.
11. **Verify:** compare source and destination row counts, check nulls,
    and inspect sample rows.

### Example cleaning pattern

This is a reference pattern; compare its output with the notebook before
replacing existing cells:

``` python
import pandas as pd

df = pd.read_csv("Walmart.csv")

# Remove exact duplicate rows
df = df.drop_duplicates().copy()

# Convert price text such as "$74.69" to numeric
df["unit_price"] = (
    df["unit_price"]
      .astype("string")
      .str.replace("$", "", regex=False)
)
df["unit_price"] = pd.to_numeric(df["unit_price"], errors="coerce")

# Parse date and time
df["date"] = pd.to_datetime(
    df["date"], format="%d/%m/%y", errors="coerce"
)
df["time"] = pd.to_datetime(
    df["time"], format="%H:%M:%S", errors="coerce"
).dt.time

# Convert numeric columns
df["quantity"] = pd.to_numeric(df["quantity"], errors="coerce")
df["rating"] = pd.to_numeric(df["rating"], errors="coerce")
df["profit_margin"] = pd.to_numeric(
    df["profit_margin"], errors="coerce"
)

# Keep rows with required analytical fields
required = [
    "invoice_id", "Branch", "City", "category", "unit_price",
    "quantity", "date", "time", "payment_method",
    "rating", "profit_margin"
]
df = df.dropna(subset=required).copy()

# Standardize names
df.columns = df.columns.str.strip().str.lower()

# Derive metrics
df["total"] = df["unit_price"] * df["quantity"]
df["estimated_profit"] = df["total"] * df["profit_margin"]

df.to_csv("walmart_clean_data.csv", index=False)
```

**Validation warning:** the `walmart_clean_data.csv` currently supplied
in this repository still has dollar-formatted `unit_price` values stored
as text. Do not assume that file has numeric price values merely because
its filename contains the word `clean`. Re-run the cleaning steps and
verify `df.dtypes` before loading it.

------------------------------------------------------------------------

## 7. Database schema and loading

The current notebook writes the DataFrame to a table named `walmart` in
the `public` schema. A recommended PostgreSQL table structure is:

  Column             Suggested PostgreSQL type
  ------------------ ---------------------------
  invoice_id         BIGINT
  branch             VARCHAR(20)
  city               VARCHAR(100)
  category           VARCHAR(100)
  unit_price         NUMERIC(12, 2)
  quantity           INTEGER
  date               DATE
  time               TIME
  payment_method     VARCHAR(50)
  rating             NUMERIC(3, 1)
  profit_margin      NUMERIC(6, 4)
  total              NUMERIC(14, 2)
  estimated_profit   NUMERIC(14, 2)

The schema above is a recommended target design. The actual table
created by `pandas.DataFrame.to_sql()` may use different inferred types.

Example load:

``` python
df.to_sql(
    name="walmart",
    con=engine,
    schema="public",
    if_exists="replace",
    index=False,
    chunksize=1000,
    method="multi"
)
```

`if_exists="replace"` drops and recreates the destination table. Use it
only when overwriting the table is intended. For a repeatable
production-style pipeline, create the schema explicitly and use
controlled inserts/upserts instead.

### Verify the PostgreSQL load

Run these statements in pgAdmin's Query Tool while connected to
`walmart_db`:

``` sql
-- Confirm the active database
SELECT current_database();

-- List tables in the public schema
SELECT table_schema, table_name
FROM information_schema.tables
WHERE table_schema = 'public'
  AND table_type = 'BASE TABLE';

-- Count loaded records
SELECT COUNT(*) AS total_records
FROM public.walmart;

-- Preview records
SELECT *
FROM public.walmart
LIMIT 10;
```

If `public.walmart` does not exist, the table has not been created in
this database/schema. Check the Python import cell for errors and verify
that the notebook connection points to `walmart_db` on the same host and
port as pgAdmin.

------------------------------------------------------------------------

## 8. Business questions answered with SQL

The supplied business-problem document defines nine questions. The SQL
scripts contain queries intended to answer them.

  ------------------------------------------------------------------------
                            \# Business question     SQL concepts
  ---------------------------- --------------------- ---------------------
                             1 Which payment methods `GROUP BY`, `COUNT`,
                               are used, and how     `SUM`
                               many transactions and 
                               units are associated  
                               with each?            

                             2 Which product         `AVG`, `RANK`, window
                               category has the      functions
                               highest average       
                               rating in each        
                               branch?               

                             3 What is the busiest   Date functions,
                               day of the week for   `COUNT`, `RANK`
                               each branch by        
                               transaction count?    

                             4 How many units are    `SUM`, `GROUP BY`
                               sold through each     
                               payment method?       

                             5 What are the minimum, `MIN`, `MAX`, `AVG`
                               maximum, and average  
                               ratings for each      
                               category in each      
                               city?                 

                             6 Which product         Derived metrics,
                               categories generate   `SUM`, sorting
                               the highest estimated 
                               profit?               

                             7 What is the most      CTE, `RANK`,
                               frequently used       aggregation
                               payment method in     
                               each branch?          

                             8 How are transactions  `CASE`, time
                               distributed across    extraction,
                               morning, afternoon,   aggregation
                               and evening shifts by 
                               branch?               

                             9 Which branches have   CTEs, joins, date
                               the largest revenue   extraction,
                               decline from 2022 to  percentage change
                               2023?                 
  ------------------------------------------------------------------------

### Metric definitions

-   **Transactions:** number of rows, assuming one row represents one
    transaction.
-   **Units sold:** sum of `quantity`.
-   **Revenue/value:** sum of `unit_price × quantity`, or sum of the
    derived `total` column.
-   **Estimated profit:** sum of `total × profit_margin`.
-   **Revenue decline percentage:**
    `(2022 revenue − 2023 revenue) / 2022 revenue × 100`.

For the last metric, branches with no 2022 revenue or no 2023 records
need explicit handling. A missing year is not automatically equivalent
to zero revenue.

### SQL dialect note

`PSQL Queries.sql` is intended for PostgreSQL; `MySQL Queries.sql` is
intended for MySQL. Date parsing, time extraction, quoting, and some SQL
syntax differ between the two engines. Do not run one dialect's script
unchanged in the other database.

Before publishing, validate every query against the final table schema
and add a short interpretation of each result. A query running
successfully is not, by itself, a business insight.

------------------------------------------------------------------------

## 9. Current project status and known limitations

The files supplied with this repository establish the following:

-   A raw CSV and a cleaned/exported CSV are present.
-   A Jupyter notebook performs initial exploration, duplicate removal,
    missing-value removal, price conversion, revenue derivation, and
    database loading.
-   Separate PostgreSQL and MySQL query scripts are present.
-   A PDF lists nine business questions and their business purposes.

The following should be addressed before calling this a polished
portfolio project:

1.  The notebook contains hard-coded database credentials in connection
    examples. Replace them with environment variables and remove any
    real credentials from notebook outputs and Git history if they were
    committed.
2.  The supplied cleaned CSV still stores `unit_price` with `$` symbols.
    Rebuild and validate the cleaned output.
3.  Standardize column names consistently. The raw data contains
    `Branch` and `City` with uppercase initials, while SQL queries refer
    to lowercase names.
4.  The notebook's connection test only creates an SQLAlchemy engine
    object; it does not prove the database connection works. Execute a
    real query such as `SELECT 1`.
5.  The PostgreSQL query script should be checked for PostgreSQL syntax
    and explicit subquery aliases.
6.  The year-over-year query assumes both 2022 and 2023 exist for a
    branch. Make missing-year behavior explicit.
7.  Add documented validation checks and actual findings. The repository
    does not currently provide a verified results narrative or a
    dashboard.

------------------------------------------------------------------------

## 10. How to make this project more original and portfolio-worthy

Do not try to make the project look original through decorative README
badges or a different color scheme. Make the analytical work more
rigorous.

### A. Build a reproducible ETL pipeline

Move the cleaning logic out of the notebook into a Python module, for
example:

``` text
src/
├── extract.py
├── transform.py
├── load_postgres.py
└── validate.py
```

The pipeline should be runnable from a clean environment and should
report input rows, removed duplicates, removed/imputed nulls, output
rows, and load status.

### B. Add data-quality reporting

Create a validation report with:

-   Duplicate count before and after cleaning.
-   Null count by column.
-   Invalid date and time counts.
-   Non-numeric price count.
-   Negative or zero quantity checks.
-   Rating range checks.
-   Revenue reconciliation checks.

Store the report as `reports/data_quality_report.csv` or Markdown.

### C. Improve the SQL analysis

Add questions that are not already in the supplied nine-question brief:

1.  Monthly revenue and month-over-month growth.
2.  Revenue and units sold by category.
3.  Average transaction value by branch.
4.  Top five branches by revenue and units sold.
5.  Payment-method contribution to revenue, not just transaction count.
6.  Weekday versus weekend sales comparison.
7.  Rating versus sales-volume analysis by category.
8.  Revenue concentration: percentage of total revenue contributed by
    the top branches.
9.  Branch-category performance matrix.
10. Outlier detection for unusually high-value transactions.

Clearly label new questions as your own extensions.

### D. Add analytical interpretation

For each important query, document:

-   Business question.
-   Why the metric matters.
-   SQL approach.
-   Result (computed from your database).
-   Business interpretation.
-   Limitation or caveat.

Avoid inventing findings. Record actual query outputs and explain what
they mean.

### E. Add indexes and compare query performance

Test whether indexes help common filtering and grouping workloads. For
example, test an index on `date` or a composite index based on a query's
actual filter pattern. Use `EXPLAIN ANALYZE` to compare plans and
execution times; do not claim performance improvements without
measurements.

### F. Add a dashboard only after SQL is correct

A small Power BI, Tableau, or Streamlit dashboard could show:

-   Total revenue/value
-   Estimated profit
-   Number of transactions
-   Units sold
-   Monthly trend
-   Branch comparison
-   Category performance
-   Payment-method split

The dashboard should use the PostgreSQL database or a documented
exported result, not manually typed values.

### G. Make the repository reproducible

A stronger final layout would be:

``` text
walmart-retail-analysis/
├── data/
│   ├── raw/
│   └── processed/
├── notebooks/
│   └── exploratory_analysis.ipynb
├── src/
│   ├── extract.py
│   ├── transform.py
│   ├── load_postgres.py
│   └── validate.py
├── sql/
│   ├── schema.sql
│   ├── exploratory_queries.sql
│   ├── business_analysis.sql
│   └── performance_checks.sql
├── reports/
│   ├── data_quality_report.md
│   └── findings.md
├── screenshots/
├── .env.example
├── .gitignore
├── requirements.txt
└── README.md
```

Do not create empty folders just to imitate this structure. Add them
when the corresponding work exists.

------------------------------------------------------------------------

## 11. Recommended next milestones

-   [ ] Confirm the cleaned CSV has numeric `unit_price`, parsed dates,
    and consistent column names.
-   [ ] Remove credentials from the notebook and Git history if
    necessary.
-   [ ] Create a repeatable PostgreSQL schema and import script.
-   [ ] Verify row counts and data-quality checks after loading.
-   [ ] Validate all nine supplied SQL questions.
-   [ ] Add at least five original analytical questions.
-   [ ] Save query outputs and write evidence-based findings.
-   [ ] Add query performance measurements.
-   [ ] Add dashboard screenshots if a dashboard is implemented.
-   [ ] Update this README with actual execution instructions and
    measured results.

------------------------------------------------------------------------

## 12. Author and attribution

**Project:** Walmart Retail Sales Analysis\
**Author:** Soham Nemade\
**GitHub:** [sohamnemade21](https://github.com/sohamnemade21)

**Dataset attribution:** [Walmart 10K Sales Dataset ---
Kaggle](https://www.kaggle.com/datasets/najir0123/walmart-10k-sales-datasets)

This repository is an educational and portfolio analysis project built
from a third-party dataset. It is not affiliated with, endorsed by, or
an official product of Walmart.

------------------------------------------------------------------------

## 13. License

No license for this project or the redistributed dataset was established
from the supplied files. Before adding a license, choose one for your
original code and verify the dataset's separate license and
redistribution terms. A code license does not automatically grant rights
to redistribute third-party data.
