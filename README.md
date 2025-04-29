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

