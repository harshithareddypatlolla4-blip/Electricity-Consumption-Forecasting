# AI-Based Electricity Consumption Forecasting System

A beginner-friendly Streamlit dashboard that explores historical electricity
consumption, evaluates a Random Forest regression model, and forecasts usage for
a selected future date. The project runs locally and does not require an API key.

## Features

- Upload a historical CSV file or use the included sample data.
- Preview cleaned data and handle missing or invalid values with clear messages.
- View total, average, maximum, and minimum consumption.
- Explore consumption history and actual-versus-predicted charts.
- Review MAE and R² scores on a time-ordered test set.
- Forecast consumption from a date and expected temperature.
- Get simple recommendations based on the dataset's high-use threshold.
- Save the trained model as `models/electricity_model.pkl`.

## Technologies used

- Python 3.11
- Streamlit
- Pandas and NumPy
- Scikit-learn
- Matplotlib
- Joblib

## Project structure

```text
Electricity_Consumption_Forecasting/
├── app.py
├── requirements.txt
├── README.md
├── .gitignore
├── data/
│   └── electricity_data.csv
└── models/
    └── electricity_model.pkl
```

If the sample CSV is missing, the app creates a two-year sample dataset on its
first run. The model file is created or refreshed whenever the app is run.

## Installation (Windows PowerShell)

Open PowerShell in the project folder and run:

```powershell
py -3.11 -m venv venv
.\venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

If PowerShell prevents environment activation, use the virtual environment's
Python directly instead:

```powershell
.\venv\Scripts\python.exe -m pip install --upgrade pip
.\venv\Scripts\python.exe -m pip install -r requirements.txt
```

## Run the application

From the project folder, run:

```powershell
streamlit run app.py
```

If you did not activate the environment, run:

```powershell
.\venv\Scripts\python.exe -m streamlit run app.py
```

Streamlit prints a local address in the terminal; open it in your browser.

## CSV format

The required column names are `Date` and `Electricity_Consumption`.
`Temperature` is optional; missing values are filled using the available
temperature median, or a neutral value when the entire column is unavailable.
`Day_of_Week` is accepted but recalculated from the date to keep it consistent.

Example:

```csv
Date,Temperature,Day_of_Week,Electricity_Consumption
2024-01-01,18.5,Monday,21.3
2024-01-02,19.2,Tuesday,22.1
```

Each consumption value is treated as the kWh used for that record (for example,
one day if each row represents a day).

## How the machine-learning model works

1. The app reads the uploaded or bundled CSV and checks the required columns.
2. It parses dates, fills missing numeric values, and derives day, month, year,
   and numeric day-of-week features.
3. The rows are sorted by date and split chronologically: the earliest 80% train
   the evaluation model, and the latest 20% are used for testing.
4. A `RandomForestRegressor` predicts the test period. MAE measures average
   prediction error in kWh; R² indicates how well predictions explain variation
   in the test data.
5. A new model is trained on all cleaned records and saved with Joblib. The
   selected forecast date and temperature are passed to this model.

The included data is synthetic example data, not a utility bill or a guarantee
of real-world accuracy. The model's estimates depend on the quality and range of
the historical data.

## Future improvements

- Add daily, weekly, and monthly forecast horizons with prediction intervals.
- Compare Random Forest with other regression models and a simple baseline.
- Include holidays, energy prices, occupancy, or appliance-level usage.
- Allow downloading forecast results as a CSV.
- Add more evaluation metrics and charts for different seasons.
