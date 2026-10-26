# Retail Sales Forecasting: Walmart Store Sales

Forecasting weekly sales for **45 stores × 81 departments** (421,570 store-department-week records, Feb 2010 – Oct 2012), with a clear answer to one question: **does a machine learning model actually beat a simple "same week last year" forecast?**

## Business question
A retailer must decide, weeks ahead, how much stock and staff each store and department needs. Too little stock loses sales, and too much ties up cash. This project builds and honestly tests a 26-week-ahead forecast.

## Data
Three files from the Walmart Store Sales Forecasting dataset (Kaggle):
- `train.csv`: weekly sales per store and department
- `stores.csv`: store type (A/B/C) and size
- `features.csv`: temperature, fuel price, CPI, unemployment, promotion markdowns

## What I did
1. **Cleaning:** merged the 3 files, checked duplicates (0), set 1,285 negative sales rows (0.3%, returns) to 0 for training, filled missing markdown data with 0 (it only exists from Nov 2011), and filled weather/economy gaps per store.
2. **EDA and statistics:** trend, seasonality, holiday lift, store type and size, top departments, Spearman correlations, Mann-Whitney test.
3. **Leakage-free features:** every "past sales" feature is at least 26 weeks old (or one year old), matched by date, so nothing from the future leaks in.
4. **Time-aware split:** trained on Feb 2010 – Apr 2012, tested on the last 26 weeks (May – Oct 2012). Never shuffled.
5. **Models:** 3 baselines vs HistGradientBoosting vs Random Forest, scored with MAE, RMSE, WAPE and the competition's holiday-weighted WMAE.
6. **Rolling backtest:** repeated on 3 separate 26-week windows to check the result is stable.
7. **Error analysis, feature importance, safety-stock planning.**

## Results

### Hold-out (last 26 weeks)
| Model | WAPE % | MAE |
|---|---|---|
| **Random Forest** | **9.62** | 1,524 |
| HistGradientBoosting | 10.64 | 1,686 |
| Seasonal naive × growth | 10.76 | 1,704 |
| Seasonal naive (same week last year) | 10.86 | 1,721 |
| Store-Dept mean | 14.73 | 2,335 |

### Rolling backtest (average of 3 windows)
| Model | WAPE % |
|---|---|
| Seasonal naive × growth | 11.6 |
| Seasonal naive | 11.6 |
| HistGradientBoosting | 12.3 |
| Random Forest | 12.7 |

### Company-total forecast (hold-out)
Random Forest 1.77% · Holt-Winters 2.03% · Seasonal naive 2.42% · HistGradientBoosting 2.76%

## Key findings
1. **Peak demand is the week before Christmas, not "Christmas week".** The biggest sales weeks are 24 Dec 2010 and 23 Dec 2011, and the data does not flag them as holidays. The official "Christmas" week (end of December) is actually 7.7% *below* a normal week.
2. **Thanksgiving is the strongest flagged holiday: +41%** vs a normal week. Super Bowl +3.6%, Labor Day about 0%.
3. **The ML model's edge is small and not stable.** On the last 26 weeks Random Forest has 11% lower error than "same week last year" (9.6% vs 10.9%). Across 3 backtest windows, the simple seasonal baseline was best on average (11.6% vs 12.3–12.7%).
4. **The best forecasts come from past sales.** The top drivers are department, recent sales level and last year's sales. Weather, fuel price and unemployment matter very little.
5. **Totals are far easier than details.** The company-total forecast is off by only about 2%, while store-department forecasts are off by about 10%.
6. **Safety stock:** store-level actual sales reach about 1.06× the forecast in 1 week out of 10, so that is a sensible stock buffer. The hold-out forecast was biased by about +0.8% (slightly over-forecasting).

## Recommendations
- Plan inventory and staff around the **pre-Christmas and Thanksgiving peaks**. Add the week before Christmas as an explicit feature.
- Use the ML model alongside the "same week last year" baseline. Do not replace the baseline until the model wins consistently.
- Order using the **safety-stock multiplier**, not a single forecast number.
- Retrain monthly and alert if model error drifts above the baseline's.

## Limitations
- Temperature, CPI and fuel price are assumed known at forecast time.
- With 26-week lags the model cannot react to recent shocks. A shorter horizon would allow better accuracy.
- Only about 2.7 years of history limits how well yearly patterns can be learned.
- This is US Walmart data, used as a practice dataset.

## Files
- `retail_sales_forecasting.ipynb`: full analysis
- `reports/figures/`: all charts (the dashboard is `00_dashboard.png`)
- `reports/model_comparison_holdout.csv`, `reports/backtest_results.csv`: result tables
- `data/`: the three source CSV files

## Run it
```
pip install pandas numpy matplotlib seaborn scipy scikit-learn statsmodels
```
Then open the notebook and use **Run All** (about 4 minutes).