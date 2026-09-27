# Monte Carlo Simulation of AAPL Using Geometric Brownian Motion
### Estimation of Value at Risk (VaR) and Conditional Value at Risk (CVaR)

This project simulates the one-year price distribution of Apple Inc. (AAPL) using Geometric Brownian Motion (GBM) and estimates downside risk through Value at Risk and Conditional Value at Risk at the 5% level.

## Objectives

1. To estimate the drift and volatility of AAPL from historical price data
2. To simulate future price paths using the exact solution of the GBM stochastic differential equation
3. To quantify downside risk using VaR and CVaR (Expected Shortfall)
4. To visualize the evolution of simulated price paths and their distribution over time

## Data

- **Source:** Yahoo Finance, retrieved via the `yfinance` library
- **Asset:** Apple Inc. (AAPL)
- **Period:** Five years of daily closing prices prior to the execution date

## Methodology

**1. Parameter estimation**

Daily log returns are calculated as r_t = ln(P_t / P_{t-1}) and annualized assuming 252 trading days:

- Annual drift: μ = mean(r_t) × 252
- Annual volatility: σ = std(r_t) × √252

**2. Price simulation**

Prices follow the GBM process dS/S = μ dt + σ dW. Paths are generated with the exact solution derived from Itô's Lemma:

S(t) = S₀ · exp((μ − σ²/2)t + σW(t))

The exact solution was selected over the Euler-Maruyama discretization because it avoids cumulative discretization error and guarantees non-negative prices. The Wiener process is initialized at W(0) = 0 so that every path begins at the current price.

| Parameter | Value |
|---|---|
| Time horizon | 1 year |
| Time step | 1 trading day (1/252 year) |
| Number of paths | 5,000 |
| Random seed | 42 (for reproducibility) |

**3. Risk measurement**

- **VaR (5%):** the 5th percentile of simulated terminal prices, expressed as a percentage loss from S₀
- **CVaR (5%):** the average of all terminal prices at or below the VaR threshold, representing the expected loss in the worst 5% of scenarios

## Results

Results at the time of execution:

| Measure | Value |
|---|---|
| Initial price (S₀) | 270.23 USD |
| Annual drift (μ) | 14.71% |
| Annual volatility (σ) | 27.37% |
| VaR 5% | 193.28 USD (28.48% loss) |
| CVaR 5% | 173.16 USD (35.92% loss, based on 250 paths) |
| Average terminal price | 310.97 USD |
| Terminal price range | 119.19 – 860.50 USD |

There is a 5% probability that the AAPL price will fall below 193.28 USD within one year. In those worst-case scenarios, the average loss is approximately 35.92%. As expected, CVaR exceeds VaR because it captures the severity of losses beyond the VaR threshold.

## Visualization

An interactive Plotly animation displays a sample of 30 simulated paths together with the evolving distribution of prices. Frames are generated every three trading days to maintain rendering performance.

## Limitations

- **Constant parameters:** GBM assumes constant drift and volatility, whereas actual volatility changes over time.
- **Normality assumption:** Log returns are assumed to be normally distributed. Actual equity returns exhibit fat tails, so extreme losses may be underestimated.
- **Historical estimation:** Drift and volatility are estimated from the past five years and may not reflect future market conditions.
- **Time dependence:** Because the data window ends on the execution date, results change each time the notebook is run.

## Tools

Python (NumPy, pandas, yfinance, Plotly)
