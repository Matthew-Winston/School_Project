Here is a descriptive, professional `README.md` template for your GitHub repository. It clearly explains the project's purpose, the data pipeline, and how to run the code, while completely removing any company-specific references.

-----

# Ski School Capture Rate Forecaster

## Overview

This repository contains an end-to-end machine learning pipeline built in R to forecast the **Capture Rate** at various ski resorts.

**Capture Rate** is defined as the percentage of total resort visitors who participate in a ski school lesson within a given timeframe. By accurately forecasting this metric at the monthly and resort level, operations and management teams can better optimize staffing, allocate resources, and anticipate ski school demand.

The model utilizes **XGBoost** (Extreme Gradient Boosting) to predict future capture rates based on historical transaction data, temporal features, and lagged volume metrics.

## Features

  * **Efficient Data Handling:** Uses the `arrow` package to read specific columns from large `.parquet` files, minimizing RAM usage.
  * **Automated Feature Engineering:** Aggregates raw transactional data into monthly summaries and automatically calculates historical lag features (e.g., 1-month lag for visits and capture rates).
  * **Robust ML Pipeline:** Implements an XGBoost regression model with hyperparameter tuning designed for smaller, seasonal datasets.
  * **Holdout Validation:** Automatically isolates the most recent two months of data to test the model against unseen realities.
  * **Automated Visualization:** Uses `ggplot2` to generate and save a comparative line chart of Actual vs. Predicted capture rates for the top 5 highest-volume resorts.

## Prerequisites

To run this project, you will need **R** (and preferably RStudio) installed on your machine. The script will automatically check for and install the following required packages:

  * `arrow` (Parquet file reading)
  * `dplyr`, `tidyr`, `lubridate` (Data manipulation and temporal parsing)
  * `xgboost` (Machine learning algorithm)
  * `ggplot2` (Data visualization)
  * `Metrics` (Model evaluation: RMSE, MAE)

## Data Requirements

The script expects a single Parquet file named `transactions_data.parquet` located in your data directory.

The dataset should contain guest transaction and scan event records. To function correctly, the file must include the following columns (case-insensitive):

| Column Name | Type | Description |
| :--- | :--- | :--- |
| `UNIVERSALSALEDATE` | Date / String | The standardized date of the transaction or scan event. |
| `RESORTNAME` | String | The name of the resort where the event occurred. |
| `SKISCHOOLDAYCOUNT` | Numeric | The count of ski school lesson days (units). Used to flag a lesson. |
| `LESSONTYPE` | String | The type of lesson taken (if applicable). Used as a secondary lesson flag. |

*Note: The script safely auto-fills missing numeric values to `0` and missing character values to `"unknown"`.*

## Usage

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/YourUsername/capture-rate-forecast.git
    cd capture-rate-forecast
    ```

2.  **Set up your data directory:**
    Place your `transactions_data.parquet` file into a local folder.

3.  **Update the file path:**
    Open the R script and update the `DATA_DIR` variable to point to the folder containing your parquet file:

    ```r
    DATA_DIR <- "/path/to/your/local/data/folder"
    ```

4.  **Run the script:**
    Execute the script from top to bottom. If running in RStudio, clearing your environment before a fresh run is highly recommended to prevent cached data issues.

## Project Pipeline

1.  **Data Ingestion:** Loads only the strictly necessary columns from the `.parquet` file to preserve memory.
2.  **Data Cleaning:** Standardizes column names, parses dates, and formats missing values.
3.  **Target Variable Creation:** Creates a binary `lesson_flag` if `SKISCHOOLDAYCOUNT > 0` or if a valid `LESSONTYPE` is present.
4.  **Aggregation:** Rolls up daily row-level data into `year_month` and `resortname` groupings, calculating the exact capture rate.
5.  **Feature Engineering:** Sorts the data chronologically and creates autoregressive lag features (`capture_rate_lag_1`, `visits_lag_1`).
6.  **Model Training:** Splits the data (holding out the last two months), converts to `xgb.DMatrix`, and trains an XGBoost model using `reg:squarederror`.
7.  **Evaluation:** Outputs the Root Mean Squared Error (RMSE) and Mean Absolute Error (MAE) for the test set.
8.  **Visualization:** Saves a high-resolution `.png` file comparing the model's predictions to reality for the top 5 resorts.

\*\*\* *Tip: You can easily add this to your repository by creating a new file named `README.md`, pasting this text, and committing the change.*
