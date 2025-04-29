# electricity-demand-thesis
Code and data for Master's thesis on electricity demand elasticity estimation using DML

## Data Description

The `data` folder contains the sample data file and the notebook `processing_data.ipynb`, which contains the data preparation steps.

The original dataset used for my master thesis contained **2,432,890 rows**. However, due to confidentiality restrictions (e.g., EEX price data) and reliance on external APIs, the full dataset cannot be shared. Moreover, GitHub storage limitations prevent uploading such a large file.

Instead, a **sample dataset** is provided in this repository, created by randomly selecting **1,500 rows** from the original data.

### Variables:

- **PriceArea**: ENTSO-E bidding zone identifier.
- **ConsumptionValue**: Electricity load from ENTSO-E (in MW).
- **DayAheadPriceEUR**: Day-ahead electricity prices reported by ENTSO-E (in Euro).
- **WeatherWindSpeed**: Wind speed data from NASA.
- **WeatherTemperature**: Surface temperature from NASA (in Kelvin).
- **WeatherTemperature_F**: Temperature converted to Fahrenheit. (Transformation shown in `processing_data.ipynb`.)
- **Solar_MWh**: Solar generation reported by ENTSO-E (in MWh).
- **Wind_Offshore**: Offshore wind generation reported by ENTSO-E (in MWh).
- **tempbin**: Categorical temperature bins created from `WeatherTemperature_F`.
- **tempbin2**: Squared temperature variable.
- **block**: Categorical variable grouping hours into time blocks (e.g., morning, afternoon).
- **Price_Area**: Corresponds to the gas hub from EEX.
- **Price_gas**: Natural gas price from EEX.
- **Price_coal**: Coal price (API 2 CIF ARA) obtained from Investing.com.
- **Price_EUA**: EU Emissions Allowances price from Investing.com.
- **IsHoliday**: Dummy variable indicating public holidays (created using the Python `holidays` package).

### Notes:

- All variable creation, cleaning, and preprocessing steps can be found in the `processing_data.ipynb` notebook.

## Model_1: Naive Model Estimation

The folder `Model_1` contains the file `naive_model.ipynb`.

This notebook implements the following steps:

### Outlier Removal
- 21 rows identified as outliers from previous model runs are removed.

### Normalization
- A normalized electricity demand variable is created by dividing consumption by the mean demand within each bidding zone and year.

### Feature Engineering
- Interactions are created between solar generation and hour blocks, and between temperature and hour blocks.
- Offshore and onshore wind generation are combined into a single `Total_Wind_Generation` variable.

### Model Estimation
- The model is estimated using the `dml_rolling_window` function.
  - Hyperparameters were selected manually based on previous grid search results optimized for this model specification.
  - The function also allows you to either **automatically tune** hyperparameters or **manually set them**.

### Double Machine Learning Output
- The model estimates a DML with the following:
  - **Outcome variable**: `consumption_normalized`
  - **Treatment variable(s)**: `DayAheadPriceEUR`
  - **Instrument variable(s)**: `Total_Wind_Generation`
  - **Cluster variable(s)**: `Country` (for robust standard errors)
  - **Covariates**:  
    - Dummies for hour of the week
    - Fixed effects for bidding zones (`PriceArea`)
    - Interactions
    - Fossil fuel prices (`Price_gas`, `Price_coal`, `Price_EUA`)
    - Month dummies

### Prediction Outputs
- First 500 predicted vs actual values for demand, based on controls estimated via XGBoost.
- Test errors (MAE, RMSE, R²) separately calculated for each rolling window for:
  - Learner \( l \) (outcome vs controls)
  - Learner \( r \) (treatment vs controls)
  - Learner \( m \) (instrument vs controls)

### Overall Metrics
- Aggregated Train MAE, RMSE, R² across all training windows.
- Aggregated Test MAE, RMSE, R² across all testing windows.

### Benchmark Traditional IV Model
- A standard IV model with the same specification is also estimated for comparison.

### Feature Selection Variant
- Another model is estimated using only the top 50 most important features (based on feature importance from XGBoost).
- The corresponding IV model based on feature selection is also provided.

### Alternative Normalization
- An additional model specification normalizes demand by peak demand per bidding zone per year.

## Model_2: Conditional Estimator

The folder `Model_2` contains the notebook `lags.ipynb`.

This notebook tests a **Conditional Model** that accounts for autocorrelation by including lags of the instrument variable.

### Main Steps:
- Created **50 lags** of the instrument (`Total_Wind_Generation`).
- Included these lagged variables in the **covariate set**.
- Ran the Double Machine Learning model using the same general specifications and variables as in Model_1.

### Additional Models Provided:
- **Traditional IV model** using the extended covariate set (with lags).
- **Feature selection model**, where only the most important features are kept based on feature importance analysis.


