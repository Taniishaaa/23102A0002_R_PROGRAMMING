# Retail Sales Data Cleaning and Analysis in R

## Overview

This experiment uses R to prepare retail sales data, combine information from multiple files, analyse sales performance, and store the results in an SQLite database.

The data represents retail transactions, products, and customers. The notebook can download the Online Retail dataset from the UCI Machine Learning Repository. If the download is unavailable, it creates sample data with a similar structure so the workflow can be demonstrated.

## Objectives

- Import data from CSV, JSON, and Excel files.
- Inspect datasets and identify missing values.
- Clean transaction, product, and customer records.
- Combine multiple data sources into one dataset.
- Calculate revenue and identify top products, customers, and countries.
- Classify customers into value segments.
- Store the integrated data in SQLite and retrieve results using SQL.

## Packages Used

- **`readr`** — read and write CSV files.
- **`jsonlite`** — read and write JSON files.
- **`readxl`** — import Excel files.
- **`writexl`** — export Excel files.
- **`dplyr`** — filter, transform, join, group, and summarise data.
- **`tidyr`** — data tidying tools.
- **`DBI`** and **`RSQLite`** — connect to SQLite and run SQL queries.
- **`lubridate`** — date and time utilities.

The notebook installs any required packages that are missing and loads them before processing the data.

## What the Notebook Does

### 1. Gets the data

The notebook attempts to download the Online Retail Excel dataset from the UCI repository.

It then creates three source files from the data:

- `transactions.csv` — invoice number, product code, customer ID, quantity, and invoice date.
- `products.json` — product code, description, and unit price.
- `customers.xlsx` — customer ID and country.

If the download fails, the notebook generates synthetic sample data and writes it to the same three files.

### 2. Imports and cleans the data

The three files are imported into R and inspected using functions such as `glimpse()` and missing-value counts.

The notebook cleans each dataset separately.

**Transactions**
- Removes exact duplicate rows.
- Removes rows with missing customer IDs or product codes.
- Removes transactions with zero or negative quantities.
- Converts invoice dates into R Date values.

**Products**
- Keeps one record per product code.
- Removes products with missing, zero, or negative prices.

**Customers**
- Keeps one record per customer ID.
- Removes records with a missing country.

These rules prepare the data for a sales-performance analysis. In particular, zero or negative quantities are excluded, so returns and cancellations are not included in this analysis.

### 3. Combines the data sources

The cleaned transaction data is joined with product and customer information.

- An **inner join** connects transactions to valid products. Transactions without a matching cleaned product are excluded because a reliable revenue value cannot be calculated for them.
- A **left join** adds customer information while initially retaining transaction rows that have no matching customer record.
- Revenue is calculated using:

  `Revenue = Quantity * UnitPrice`

The notebook checks for unmatched product and customer records. It then removes rows without a country because the later market analysis groups revenue by country.

### 4. Analyses sales and customers

The notebook calculates total sales revenue and produces several summaries.

- **Top 5 products:** products with the highest total revenue.
- **Top 5 countries:** countries with the highest total revenue.
- **Top 5 customers:** customers with the highest total purchase value.
- **Customer value segments:** customers are classified according to their total revenue:
  - Below 100 — Low Value
  - 100 to below 500 — Medium Value
  - 500 to below 2,000 — High Value
  - 2,000 or more — Premium

The number of customers in each segment is displayed, and the segment is added to the main sales dataset.

The notebook also compares countries by total revenue and number of distinct invoices to identify the highest- and lowest-revenue markets in the prepared dataset.

### 5. Stores and queries the data using SQL

The integrated dataset is saved to an SQLite database named `retail_analysis.sqlite`, in a table called `retail_sales`.

The notebook then runs SQL queries to retrieve:

- The top five customers by total revenue.
- Total revenue for each country, ordered from highest to lowest.

Finally, it closes the database connection.

## Files Created

Depending on the data source and successful execution, the notebook creates:

- `OnlineRetail_raw.xlsx` — downloaded source workbook, if the download succeeds.
- `transactions.csv` — transaction records.
- `products.json` — product records.
- `customers.xlsx` — customer records.
- `retail_analysis.sqlite` — integrated sales data stored in SQLite.

The notebook also prints analysis results in the output cells. It does not explicitly export the top-product, top-customer, or country summaries as separate files.

## How to Run

1. Open `R_PROG_EXP3.ipynb` in an environment that supports R code.
2. Ensure the required R packages can be installed.
3. Allow internet access if you want to download the original dataset.
4. Run the cells in order, because later steps use files created earlier.
5. Review the cleaning counts, revenue summaries, customer segments, and SQL query results.
6. Check the notebook's working directory for the generated files and SQLite database.

## Important Notes

- The notebook deliberately excludes zero and negative quantities. This is a chosen rule for analysing sales performance, not a general rule for every retail analysis, since negative quantities may represent genuine returns.
- The customer value thresholds are illustrative and are applied to the dataset's revenue values. They may need adjustment for another business or currency.
- Transactions without a valid product match are excluded, and rows without a country are removed before the sales analysis. These choices can affect the reported totals.
- If the notebook uses synthetic data because the download fails, the results will be sample results rather than findings from the original UCI dataset.
- The final results depend on the data available after cleaning and joining.

## Conclusion

This experiment demonstrates a retail data-analysis workflow in R: importing data from different file formats, cleaning inconsistent records, combining transaction, product, and customer information, calculating sales revenue, analysing product and customer performance, and saving the results in an SQLite database for SQL-based queries.
