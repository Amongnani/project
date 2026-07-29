# Retail Demand Forecasting Model

Predicts short-term product demand from historical sales data so category
and inventory teams can plan stock levels more effectively.

## Tech Stack
- **Python** (Pandas, Scikit-learn) — feature engineering and model training
- **SQL** — sales data storage and feature aggregation queries
- **Power BI** — forecast vs. actual visualization for stock planning

## Project Structure
```
retail-demand-forecasting/
├── sql/
│   ├── schema.sql            # Product / store / sales tables
│   └── feature_query.sql     # Weekly aggregation & rolling-average query
├── python/
│   ├── generate_sample_data.py  # Creates a synthetic sales dataset
│   ├── features.py              # Lag, rolling-average & seasonality features
│   ├── train_model.py           # Trains & evaluates the forecasting model
│   ├── forecast_next_week.py    # Produces next-week demand forecast
│   └── requirements.txt
├── data/                      # Generated CSVs (sample data, forecasts)
└── powerbi/
    └── README.md               # How to connect Power BI to the exports
```

## How It Works
1. **Data**: Weekly per-product sales history (`generate_sample_data.py` creates
   a realistic synthetic dataset with trend, seasonality, and noise so the
   project runs end-to-end without a live database).
2. **Feature Engineering** (`features.py`): builds lag features (previous 1/2/4
   weeks), a trailing 4-week rolling average, and cyclical week-of-year
   seasonality signals.
3. **Model** (`train_model.py`): a `RandomForestRegressor` trained on the
   engineered features, evaluated with MAE and MAPE, and saved to
   `model.joblib`.
4. **Forecast** (`forecast_next_week.py`): loads the trained model and scores
   the latest feature row per product to produce a next-week demand forecast.
5. **Power BI**: `train_model.py` exports `data/forecast_vs_actual.csv` and
   `forecast_next_week.py` exports `data/next_week_forecast.csv` — both are
   plain CSVs that Power BI can load directly (Get Data → Text/CSV) to build
   the forecast-vs-actual and next-week-demand visuals.

## Running It
```bash
cd python
pip install -r requirements.txt
python generate_sample_data.py     # 1. create sample sales history
python train_model.py              # 2. train model + evaluate + export for Power BI
python forecast_next_week.py       # 3. produce next week's forecast per product
```

## Sample Output
```
Test MAE:  10.18 units
Test MAPE: 13.44%

 product_id           product_name    category  predicted_units_next_week
          2         Cotton T-Shirt     Apparel                        198
          1       Wireless Earbuds Electronics                        135
          3 Stainless Water Bottle        Home                        109
          5      Bluetooth Speaker Electronics                         84
          4               Yoga Mat      Sports                         64
```

## Notes
- `sql/schema.sql` and `sql/feature_query.sql` show how the same
  feature engineering would be done against a real MySQL/SQL Server
  database; the Python pipeline mirrors that logic against the CSV
  export so the project can be run and demoed without database access.
