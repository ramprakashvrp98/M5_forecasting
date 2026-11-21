# Walmart M5 Demand Forecasting (Python)

Forecasting weekly demand for the 20 best-selling food products in a Walmart store in Texas, and comparing a simple baseline against a seasonal model.

## Business question
How much of each product will sell in the coming weeks? Over-forecasting ties up cash in excess inventory. Under-forecasting causes empty shelves and lost sales. A reliable forecast is the starting point for safety stock and reorder decisions.

## Data
[M5 Forecasting Accuracy](https://www.kaggle.com/competitions/m5-forecasting-accuracy) on Kaggle: about 5 years of real daily Walmart sales (2011 to 2016) for 3,049 products across 10 stores.

This project uses store **TX_1**, category **FOODS**, and the **top 20 products** by total units sold.

> The data files are not included in this repo. Download `sales_train_evaluation.csv` and `calendar.csv` from Kaggle and put them in the same folder as the notebook.

## Approach
1. **Prepare the data:** reshape daily sales from wide to long, join the calendar, and sum daily units into Walmart weeks (Saturday to Friday). Incomplete weeks are removed so a partial week doesn't look like a sales drop.
2. **Explore patterns:** 4-week moving averages and seasonal decomposition (trend, yearly seasonality, noise).
3. **Hold out the last 12 weeks** as a test period the models never see.
4. **Forecast with two methods:**
   - **Moving average:** average of the last 4 weeks (baseline)
   - **Holt-Winters exponential smoothing:** damped trend plus a 52-week seasonal pattern
5. **Score each forecast** against actual sales with MAE, WAPE, MAPE and bias, and pick the better method for each product.

## Results
| Metric | Moving average | Holt-Winters |
|---|---|---|
| Average WAPE (20 SKUs) | 42.4% | 68.2% |
| Median WAPE (20 SKUs) | 26.3% | 32.0% |
| Products won | 13 of 20 | 7 of 20 |

Picking the better method for each product gives an average WAPE of **39.9%**.

**Key findings**
- The 4 highest-volume products (over 200 units a week) forecast at about **10% WAPE**. Accuracy falls as weekly volume drops.
- The simple moving average beat Holt-Winters on 13 of 20 products, showing how hard a baseline is to beat in stable periods.
- 4 products (FOODS_3_541, FOODS_3_808, FOODS_3_234, FOODS_3_635) had over 100% error because demand dropped sharply in the test period, likely stockouts or delistings. They need investigation, not just a better model.
- Holt-Winters tended to over-forecast (positive bias on most products), which would build excess inventory.
- Top seller FOODS_3_586 dropped about 25% in volume between 2012 and 2013, then stabilized, with a strong summer peak each year.

## Accuracy metrics
| Metric | Meaning |
|---|---|
| MAE | Average units missed per week |
| WAPE | Total miss ÷ total sales (main score; works with zero-sales weeks) |
| MAPE | Average % miss per week (skips zero-sales weeks) |
| Bias | Positive = forecast too high (excess stock); negative = too low (stockouts) |

## Next steps
- Add a seasonal naive method and test calendar effects (holidays, SNAP days) and price
- Use forecast error to set safety stock and reorder points by service level

## How to run
```
pip install -r requirements.txt
jupyter notebook M5_Week1_Demand_Forecasting.ipynb
```

## Tools
Python, pandas, statsmodels, matplotlib, Jupyter
