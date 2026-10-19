# Data Cleaning and Missing Data Handling in R

## Overview

This experiment demonstrates how to clean datasets in R by identifying invalid values, handling missing data, validating results, and exporting cleaned files. It also compares loop-based and vectorized operations to show different ways of processing data in R.

Two example datasets are used: a Heart Disease dataset and an Adult Census Income dataset. If a dataset cannot be downloaded, the notebook attempts to create a sample dataset so the code can demonstrate the workflow.

## Objectives

- Load datasets and inspect data quality.
- Identify invalid, extreme, and missing values.
- Use loops and `if-else` statements to apply cleaning rules.
- Handle warnings and errors with `tryCatch()`.
- Compare loop-based and vectorized outlier detection.
- Understand the difference between `NA`, `NaN`, `NULL`, and blank strings.
- Apply missing-data strategies such as replacing blanks, removing affected rows, and median imputation.
- Validate and export cleaned datasets.

## Packages Used

- **`dplyr`** — data manipulation.
- **`naniar`** — summaries and visualizations of missing data.
- **`skimr`** — descriptive summaries for data validation.
- **`microbenchmark`** — repeated timing comparisons between functions.

The notebook installs packages that are not already available and loads them before running the experiment. It also uses `set.seed(42)` to make random selection reproducible in the same setup.

## What the Notebook Does

### 1. Cleans blood pressure data

The notebook loads the Heart Disease dataset from the UCI repository. It deliberately introduces examples of incorrect data, including negative blood pressure values, missing values, and unusually high values.

A custom function named `clean_bp()` checks each value using a loop and conditional statements:

- Missing values remain `NA`.
- Negative values are changed to `NA`.
- Values greater than 250 are capped at 250.
- Other values are retained.

The cleaned values are stored in a new column, so the original blood pressure column remains available for comparison.

### 2. Handles calculation errors

The notebook uses `tryCatch()` to make calculations safer.

- `safe_mean_bp()` calculates the mean while handling warnings or errors, including the case where all values are missing.
- `safe_ratio()` calculates the ratio of cholesterol to cleaned blood pressure and handles missing values, division by zero, and invalid results.

The calculated ratios are stored in a new column.

### 3. Compares loops and vectorized operations

Outlier detection is implemented in two ways:

- A loop checks each blood pressure value individually.
- A vectorized expression checks all values at once.

The notebook checks whether both methods return the same results, then compares their execution times using `system.time()` and `microbenchmark()`.

This demonstrates that vectorized expressions can reduce the overhead of processing values one at a time. Actual timings depend on the dataset, operation, and system.

### 4. Handles different forms of missing or invalid data

The notebook loads the Adult Census Income dataset and introduces examples of missing or invalid values, including:

- `NA`, which represents a missing value.
- `NaN`, which represents “Not a Number.”
- Blank strings, such as an empty category value.
- An impossible age value of `999`.

It demonstrates `is.null()`, `is.na()`, and `is.nan()` and uses `naniar::miss_var_summary()` to summarize missing values.

### 5. Applies cleaning strategies

Different problems are handled in different ways:

- Impossible ages of `999` are converted to `NA`.
- Blank `workclass` values are replaced with `"Unknown"`.
- Rows with `NaN` in `hours_per_week` are removed.
- Missing ages are filled with the median using a custom `impute_median()` function.

The notebook then uses `complete.cases()`, `miss_var_summary()`, `vis_miss()`, and `skimr::skim()` to inspect the cleaned dataset.

### 6. Validates and exports the results

The notebook checks the cleaned blood pressure values for remaining missing, negative, or unusually high values. It also checks the cleaned census data for impossible ages, missing ages, and blank workclass values.

Finally, it exports the resulting datasets to CSV files:

- `cleaned_heart_data.csv`
- `cleaned_adult_data.csv`

## Key R Concepts

- Conditional statements: `if`, `else if`, and `else`
- Loops: `for`
- User-defined functions
- Error and warning handling with `tryCatch()`
- Vectorized logical operations
- Missing-value checks: `is.na()`, `is.nan()`, and `is.null()`
- Median imputation
- Complete-case checking with `complete.cases()`
- Data summaries and missingness visualization
- Execution-time comparison
- CSV export with `write.csv()`

## How to Run

1. Open `R_PROG_EXP2.ipynb` in an environment that supports R notebooks.
2. Ensure that an internet connection is available if you want to download the original datasets.
3. Run the notebook cells in order.
4. Review the validation summaries and missing-data visualization.
5. Check the working directory for the two exported CSV files.

## Notes

- The notebook modifies selected values intentionally to demonstrate cleaning techniques; it does not represent the original datasets without alteration.
- If a dataset download fails, the notebook attempts to use a synthetic dataset. Some later steps expect columns from the original datasets, so the fallback path may require adjustments.
- The cleaning rules are examples for this experiment and should not automatically be treated as clinical or domain-specific rules for real-world data.
- Some missing values may remain in columns that are not explicitly cleaned. Review the validation output before assuming the entire dataset has no missing data.

## Conclusion

The experiment shows how R can be used to identify and handle invalid data, manage different forms of missing values, protect calculations with error handling, compare loop-based and vectorized approaches, and validate and export cleaned datasets.
