# Residual Alpha Long-Short Strategy

## Overview

This project implements a market-neutral, residual-based long–short equity strategy with adaptive alpha weighting, volatility targeting, and stop-loss risk control.

The system isolates stock-specific returns by removing index exposure and combines multiple short-horizon statistical signals into a dynamically adjusted alpha engine. The strategy is backtested using realistic portfolio construction logic, turnover control, and volatility scaling.

---

## Strategy Architecture

The strategy follows a structured pipeline:

1. Data Collection  
2. Residual Return Computation  
3. Alpha Factor Construction  
4. Adaptive Signal Conditioning  
5. Portfolio Construction  
6. Risk Management  
7. Performance Evaluation  

---

## Core Assumptions

- Daily rebalancing or periodic rebalancing (configurable).
- No transaction costs explicitly modeled (turnover penalty used as proxy).
- Volatility estimated using rolling standard deviation.
- Stop-loss levels are volatility-based.
- Capital is fully deployable and integer share constraints apply.
- Residual returns are approximated by subtracting index returns from stock returns.

---

## Data Preparation

- Historical adjusted close prices downloaded via Yahoo Finance.
- Missing values forward-filled.
- Daily returns computed.
- Residual returns calculated as:

    residual_return = stock_return − index_return

This isolates stock-specific movement from market beta exposure.

---

## Alpha Factors

Three short-horizon cross-sectional factors are constructed:

### 1. Mean Reversion
Deviation from short-term moving average normalized by rolling standard deviation.

### 2. Residual Momentum
Rolling mean of residual returns to capture stock-specific trend persistence.

### 3. Statistical Arbitrage Signal
Residual return relative to its longer-term rolling mean to detect temporary mispricing.

All signals are cross-sectionally z-scored to ensure comparability.

---

## Alpha Engine

The composite alpha is built using weighted blending:

- 15% Mean Reversion  
- 50% Residual Momentum  
- 35% Statistical Arbitrage  

The combined signal is:
- Cross-sectionally normalized
- Smoothed via exponential moving average
- Volatility scaled
- Clipped to control extremes

---

## Regime-Adaptive Logic

The system tracks rolling signal performance:

- If recent alpha × future residual returns turn negative,
  the signal is flipped.
- Signals inconsistent with short-term trend confirmation are suppressed.
- Volatility scaling ensures risk-adjusted exposure.

This creates a self-correcting alpha engine that adapts to changing micro-regimes.

---

## Portfolio Construction

At each rebalance:

- Top N stocks selected for long positions.
- Bottom N stocks selected for short positions.
- Weights scaled inversely to rolling volatility.
- Turnover penalty reduces excessive reallocation.
- Portfolio scaled to target volatility.
- Integer share sizing applied.

---

## Risk Management

- Volatility-based stop-loss per position:
  
  stop_distance = multiplier × rolling_volatility × price

- Long positions exit if price falls below stop.
- Short positions exit if price rises above stop.
- Portfolio exposure capped via scaling factor.

No take-profit logic is applied — risk control is strictly downside-based.

---

## Performance Metrics

The system evaluates:

### Return Metrics
- Total Return
- CAGR
- Final Portfolio Value

### Risk Metrics
- Annualized Volatility
- Sharpe Ratio
- Sortino Ratio
- Max Drawdown
- Calmar Ratio

### Trade Diagnostics
- Win Rate
- Profit Factor
- Average Gain / Loss
- Skewness
- Kurtosis

Equity curve and drawdown plots are generated for visual inspection.

---

## Observed Characteristics (Typical Behavior)

- Low volatility profile
- Controlled drawdowns
- Moderate Sharpe ratio
- Low win rate but positive expectancy
- Positive skew with fat tails

The system behaves like a conservative statistical arbitrage framework rather than a high-beta growth strategy.

---

## Limitations

- No explicit transaction cost modeling.
- Slippage not incorporated.
- Residual return approximation is simplified (true beta regression not applied).
- Liquidity constraints not modeled.
- Short borrow cost not included.
- Backtest assumes perfect execution at close price.

---

## Disclaimer

This project is for research and educational purposes only.  
Past performance does not guarantee future results.  
Not investment advice.

---
