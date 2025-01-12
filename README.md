# Market Analytics

A set of Jupyter Notebooks and Python tools to quantitatively (and sometimes qualitatively) analyze the cryto markets.

## Orderbook Delta
A trading bot for this strategy is written in Rust [here](https://github.com/dineshpinto/orderbook-delta-bot).

The idea behind this is the concept of _mean reversion_. We look for large deviations in the volume delta of BTC-PERP 
on FTX at depth=1.  These deviations could be caused by over-enthusiastic and over-leveraged market participants (speculation, are reasons important?).

We counter-trade those deviations, and enter short/long positions based on triggers given by a large delta (> 2 SDs) 
from a (10-20) period rolling bollinger band.

The full analysis is in [notebooks/OrderbookDeltaAnalyzer.ipynb](notebooks/OrderbookDeltaAnalyzer.ipynb)

Interactive image [here](images/orderbook_delta_analyzer.html) (may need to be downloaded as GitHub limits hosted file size)

![orderbook_delta_analyzer.png](images/orderbook_delta_analyzer.png)


### Live orderbook visualizer
Built with Dash and Plotly, you can also visualize the orderbook delta live:

[orderbook_delta_visualizer](https://user-images.githubusercontent.com/15251343/173228676-470de060-7ab9-4742-b69f-7b04068ec6fe.mov)

#### To run the visualizer
```shell
python src/orderbook_delta_visualizer.py
```

#### To adjust the visualizer parameters or strategy
+ All strategies and parameters are stored in `src/orderbook_delta_strategies.py`
+ Modify the strategy by implementing a new class inheriting from the `orderbook_delta_strategies/BaseStrategy` abstract base class 
+ Point to the new strategy by modifying the parameters stored in the dataclass `orderbook_delta_strategies/Parameters`
+ You can modify parameters when `src/orderbook_delta_visualizer.py` is running, it will restart automatically

### Limitations
+ This model is not based on a detailed analysis of market microstructure and order-book dynamics, it is simply an obervation based on very limited experimentation
+ To understand market microstucture and order-book dynamics, an ab-intio theoretical analysis is required. Quant arXiv has some nice papers on this:
  + [Trade arrival dynamics and quote imbalance in a limit order book](https://arxiv.org/pdf/1312.0514.pdf)
  + [Continuous-time Modeling of Bid-Ask Spread and Price Dynamics in Limit Order Books](https://arxiv.org/pdf/1310.1103.pdf)
  + [How markets slowly digest changes in supply and demand](https://arxiv.org/pdf/0809.0822.pdf)
+ The data is certainly over-fit
+ Needs to tested on significantly more data

### TODO
+ Extract probabilities to use as inputs for Kelly criterion
+ Use multiple statical models to predict market moves (in progress, needs to be formalized)
  - [x] ARIMA
  - [x] Observed Components
  - [x] ML time series analysis
+ Perform spectral analysis for market timing


## Crypto and its correlation to US Fed actions
![crypto_interest_rate_comparison.png](images/crypto_interest_rate_comparison.png)

![crypto_interest_rate_correlation.png](images/crypto_interest_rate_correlation.png)

![crypto_asset_holdings_repo_comparison.png](images/crypto_asset_holdings_repo_comparison.png)


## Open interest and funding rates
We use a metric derived from the product of the open interest, future dilution and funding rate, normalized by the market cap. 
The metrics name is FOM, derived from **F**unding rate, **O**pen Interest and **M**arket cap. 
The FOM metric indicates whether futures market participants hold a strong bias, generally for smaller cap projects 
trading on popular derivatives exchanges. 
The direction of the bias is indicated by the funding rate. The variation of the FOM with different projects is shown 
in this image below.

The top 5 percentile of projects with the highest FOM (on 2022-04-23) are shown in the table below.
The higher the FOM, the larger the relative position futures participants have taken.

| Token | FOM  | Direction | 
|-------|------|-----------|
| LOOKS | 7.6μ | Short     | 
| GMT   | 2.1μ | Short     | 
| CRV   | 1.9μ | Short     |
| SRM   | 0.9μ | Short     |
| RAY   | 0.7μ | Short     |
| BIT   | 0.6μ | Short     |

The full analysis is in [notebooks/FuturesOpenInterestAnalyzer.ipynb](notebooks/FuturesOpenInterestAnalyzer.ipynb)

![fom_metric.png](images/fom_metric.png)

### Limitations:
1. While this does give the prevailing sentiment among the futures market participants,
it does not provide any information about timeframes or price levels. 
2. Building on point 1, the main limitation is the lack of historical data. Tracking the buildup of shorts/longs over time is important,
both from the perspective of fitting it to some model and the price levels at which they enter.
3. In very certain cases, the market participants may simply be hedging their positions due to some upcoming event, 
perhaps this could be filtered out by a simple social media volume tracker...

## Hawkes Processes
The full analysis is in [notebooks/HawkesProcesses.ipynb](notebooks/HawkesProcesses.ipynb)

## CFMMs
The full analysis is in [notebooks/CFMM.ipynb](notebooks/CFMM.ipynb)

## Bitcoin Market Analysis

Understanding market cycles using basic mathematics is a fun (and probably incorrect!) exercise. Here we use simple geometric progressions on the length of Bitcoin bull and bear 
cycles to estimate the length of future cycles. This simple model is able to accurately predict a 26 month bull cycle peaking in April 2021, followed 
by a 3 month bear cycle. It further predicts 10 month bull cycle ending in **July 2022**, after which the model starts predicting a bear cycle length tending to 0.

We speculate that the bear cycle length is likely due to the failure of the model. 
However, if this is not the case, then possibly a large event has led to hyperbitcoinization, leading to only bull cycles. 
Given the nature of the geometric progression however, this will not last forever, and the bull cycle length will also tend to 0 with time.

The full analysis is in [notebooks/BitcoinGeometricProgression.ipynb](notebooks/BitcoinGeometricProgression.ipynb)

![BLX_2021-12-29_14-32-48_ba615.png](images/BLX_2021-12-29_14-32-48_ba615.png)

## Project Valuation
Finding undervalued projects based on metrics such as fully diluted valuation, current valuation and percent since ATH.

The full analysis is in [notebooks/ProjectValuation.ipynb](notebooks/ProjectValuation.ipynb)


## Installation
1. Create the conda environment from file (for Mac M1)
```shell
conda env create --file conda-env.yml
```
3. Activate environment 
```shell
conda activate market_analytics
```
4. Add environment to Jupyter kernel 
```shell
python -m ipykernel install --name=market_analytics
```
5. Install jupyter lab extensions for plotly 
```shell
jupyter labextension install jupyterlab-plotly
```
6. Explore the various Jupyterlab Notebooks under `notebooks/`
```shell
jupyter lab
```


### Export conda environment
```shell
conda env export --no-builds | grep -v "^prefix: " > conda-env.yml
```

## Disclaimer
This project is for educational purposes only. You should not construe any such information or other material as legal, tax, investment, financial, or other advice. Nothing contained here constitutes a solicitation, recommendation, endorsement, or offer by me or any third party service provider to buy or sell any securities or other financial instruments in this or in any other jurisdiction in which such solicitation or offer would be unlawful under the securities laws of such jurisdiction.

If you plan to use real money, use at your own risk.

Under no circumstances will I be held responsible or liable in any way for any claims, damages, losses, expenses, costs, or liabilities whatsoever, including, without limitation, any direct or indirect damages for loss of profits.
