# Statistical arbitrage on PepsiCo and Coca-Cola

A pairs-trading experiment in a Jupyter notebook. I downloaded daily closing prices for PepsiCo (PEP) and Coca-Cola (KO), tested whether the two series are cointegrated, and backtested a simple mean-reversion strategy on the spread between them. It uses yfinance, pandas, NumPy, statsmodels and matplotlib.

## How it works

1. Download daily closing prices for PEP and KO from 2015-01-01 to 2023-01-01 with `yfinance`.
2. Run the Engle-Granger cointegration test (`statsmodels.tsa.stattools.coint`), which applies an augmented Dickey-Fuller test to the residuals of a regression between the two series.
3. Define the spread as PEP minus KO and convert it to a z-score using the mean and standard deviation of the whole period.
4. Generate signals: go long the spread (buy PEP, sell KO) when the z-score is below -1, go short the spread when it is above 1, and close the position when the z-score returns to between -0.5 and 0.5.
5. Backtest the signals day by day starting from 100,000 of capital, marking the open position to market each day, and plot the portfolio value.

## Results

| Measure | Value |
|---|---|
| Cointegration test statistic | -3.83 |
| Cointegration p-value | 0.0124 |
| Starting portfolio value | 100,000.00 |
| Final portfolio value (2022-12-30) | 100,004.34 |

The p-value is below 0.05, so the test rejects the null hypothesis of no cointegration for this period. The backtest trades one share of each stock per position, so the profit is tiny relative to the starting capital.

## Repository layout

| Path | What it is |
|---|---|
| `statistical_arbitrage.ipynb` | Data download, cointegration test, spread and z-score plots, signals and backtest |
| `requirements.txt` | Python packages used by the notebook |

## Running it

```
pip install -r requirements.txt jupyter
jupyter notebook statistical_arbitrage.ipynb
```

The notebook needs an internet connection because `yfinance` downloads the price data when it runs.

## Limitations

The z-score uses the mean and standard deviation of the full 2015 to 2022 period, so every trading decision uses information from the future; a rolling window would be needed for a fair backtest. The spread is a plain price difference with no hedge ratio, and the backtest ignores transaction costs, short-selling costs and position sizing. The cointegration test is also run on the same data the strategy is tested on.
