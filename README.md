<p align="center">
  <img src="NeuraLiquid.png" alt="NeuraLiquid" width="280">
</p>

<h1 align="center">NeuraLiquid</h1>

<p align="center">
  <strong>Modular trading research and automation for Hyperliquid</strong>
</p>

<p align="center">
  AI-assisted analysis · Machine learning · Pattern strategies · Copy trading · Backtesting · Hardware dashboards
</p>

---

## Overview

NeuraLiquid is a personal trading and research platform for Hyperliquid perpetuals, including supported markets on the xyz DEX. It combines market scanning, AI-assisted decisions, algorithmic pattern detection, wallet tracking, execution, multiple learning models, shadow trading and trade diagnostics.

The project has evolved beyond its original three-agent Bull/Bear debate design. The current implementation includes an AI coin selector, a portfolio-manager analysis path, a configurable signal aggregator and conviction-based position sizing. Pattern trading, copy trading and a separate algorithmic scalper can operate as distinct modules.

**This repository is the public project overview. It contains documentation and the logo, not the bot source code or an installable release. The implementation remains private.**

## System at a glance

```text
Market data and context
Hyperliquid · Binance · News · Order flow
                    |
          Market scan and candidate selection
                    |
          AI selector + Portfolio Manager
                    ^
                    |
          Signal aggregator
          Pattern signals + Copy-trader signals
                    |
          ML entry gate + conviction sizing
                    |
          Execution and position management
                    |
          Trade history, shadow outcomes and diagnostics
                    |
          Web dashboard · Telegram · ESP32 displays

Learning and research alongside execution:
Candle model · Trade-outcome classifier · Pattern ML · Backtesting

Separate configurable modules:
Pattern engine · Copy-trading engine · Algorithmic scalper
```

This is a conceptual overview. Each trading module has its own configuration and execution path; they do not all pass through the AI portfolio manager.

## AI-assisted decision pipeline

- **Market scan:** ranks candidates using market activity, funding, technical context and supported xyz markets.
- **AI coin selector:** narrows the candidate list before deeper analysis.
- **Portfolio Manager:** evaluates bullish and bearish arguments and returns a structured buy, sell or hold decision.
- **Signal aggregation:** can add pattern detections and tracked-wallet signals to the analysis. These sources can be enabled independently.
- **Conviction-based sizing:** combines supporting evidence and warnings to adjust position size, alongside entry checks and configured limits.
- **Analysis controls:** configurable gates, caching and fast/deep cycles reduce repeated work. The older dual-perspective analysis path is also retained.

The implementation supports a Claude CLI backend and an Anthropic API backend. Model access, usage limits and costs depend on the selected provider configuration; this overview makes no zero-cost or unlimited-usage claim.

## Trading and research modules

### Pattern engine

The pattern engine implements 15 detectors spanning market-structure patterns and classical technical setups:

- Fair Value Gap, Break of Structure and Change of Character
- Order Block, Liquidity Sweep and Equal-High/Low Sweep
- FVG Inversion, Premium/Discount Order Block, Breaker Block and Mitigation Block
- Session Breakout, 1-2-3 Reversal and Turtle Soup
- Double Top/Bottom and Triple Top/Bottom

The surrounding workflow includes simulation, configurable dry/live operation, per-pair statistics and optional ML-based confidence estimates. Long and short setups can be tracked separately by pattern, market and timeframe.

### Copy trading

A separate module discovers and tracks Hyperliquid wallets, evaluates their trading history and supports signal-only, selective-copy and mirror modes. Configuration includes wallet selection, position limits and optional Kelly-based sizing.

Tracked-wallet signals can also be used as context for the AI pipeline without enabling automatic copying.

### Algorithmic scalper

A separate non-LLM scalper evaluates short-cycle signals such as exhaustion, RSI reversals and order-book imbalance. It has its own execution settings, loss limits and dry-run option.

### Market and news context

Analysis modules cover technical indicators, multiple timeframes, funding, open-interest changes, order-book conditions, whale activity and Binance cross-exchange context.

News processing combines crypto and business sources, asset-specific headlines, sentiment and macro-event context. AI-assisted sentiment analysis has a keyword-based fallback.

## Machine learning and feedback

Machine learning is a separate layer alongside the LLM analysis. NeuraLiquid contains several complementary models and statistical filters rather than a single generic AI score.

### Historical-candle learning

A feature-based trainer learns from historical candles fetched from Binance, with Hyperliquid fallback and a separate path for supported xyz markets.

- Extracts RSI, EMA trend, ATR-based volatility, MACD, VWAP position, volume and trading-session features
- Labels historical entries using take-profit, stop-loss and a maximum holding horizon (triple-barrier-style labeling)
- Builds direction-specific feature statistics for crypto long and short setups; the current xyz training path is long-only
- Produces a weighted estimate for new setups that feeds into conviction-based sizing
- Compares stop-loss/take-profit combinations by historical win rate and estimated expected value
- Persists the learned feature model and exposes training status through the dashboard
- Supports manual training and automatic training during configured no-trade periods

### Trade-outcome meta-model

A separate **scikit-learn pipeline with StandardScaler and LogisticRegression** learns from eligible closed trades in the local database.

Its nine inputs include the Portfolio Manager's score and confidence, cyclical time-of-day and weekday features, trade direction, market type and leverage. The resulting win-probability estimate is used as an entry gate; rejected candidates can be recorded as shadow trades.

Training saves the model and metadata, including train/test accuracy, ROC AUC, Brier score, precision, recall and model coefficients. The main loop includes an automatic retraining check, and the dashboard provides model information and a manual training endpoint.

This model uses a stratified random train/test split. Its database query can include dry-run and shadow records as well as live trades; these metrics should not be read as live-only or walk-forward performance.

### Pattern-specific probability model

The pattern ML module learns feature-bucket statistics from historical pattern detections and forward evaluation.

Its **nine features** are pattern type, timeframe, signal-score bucket, volatility, RSI, trend, trading session, volume and long/short direction. Prediction uses exact feature matches where sufficient samples exist and progressively broader matches otherwise.

The current training module covers seven core detectors; the broader execution engine contains 15. Model state, trained market/timeframe combinations and sample counts are persisted and available through the dashboard.

### Statistical signal feedback

An additional meta-labeling filter analyzes past outcomes by confidence, consensus, trend, volatility, hour and market. It complements the learned models with interpretable historical signal statistics.

These layers serve different purposes: historical setup estimates, an entry classifier, pattern confidence and trade-history analysis. They are not all neural networks, and their probability estimates are not guarantees.

## Research, simulation and shadow trading

NeuraLiquid includes tools for investigating both accepted and rejected setups:

- **Historical backtesting:** strategy simulation with stop-loss, take-profit, trailing exits, equity curves and trade-level reports
- **Parameter sweeps:** comparisons of strategy settings and SL/TP combinations
- **Pattern simulation:** market/timeframe/direction-specific evaluation and active-pair management
- **Shadow positions:** selected rejected AI candidates are tracked as simulated trades with a rejection reason, allowing their subsequent outcomes to be inspected
- **Dry/live comparison:** persistent records support checking whether simulated behavior carries over to actual fills
- **MFE/MAE diagnostics:** measures favorable and adverse price excursions and how much of a favorable move an exit captured
- **Counterfactual tools:** dedicated analyses for entry timing and partial-bar confirmation, plus pair re-simulation

The code also contains a shadow-to-live promotion path under its entry conditions. Shadow tracking therefore forms part of the wider execution workflow, not just a reporting screen.

Training-sample win rates, random holdout metrics, backtest results and live realized outcomes are distinct measurements. This overview does not present them as interchangeable evidence of trading performance.

## Execution, risk and diagnostics

The implementation includes:

- Configurable position, leverage, exposure and loss limits
- Exchange-side stop/target order support and trailing-stop logic
- Pattern-engine position recovery and stop-verification routines
- Cooldowns, time-based exits and consecutive-loss pauses
- Pattern-pair trial sizing and promotion checks that compare simulation with recorded outcomes
- Independent long/short pair statistics and persistent re-entry cooldowns
- Persistent trade and decision history in SQLite
- Maximum favorable/adverse excursion (MFE/MAE) and exit diagnostics
- Simulation, dry-run records and live-fill reconciliation for comparing expected and observed behavior

**Risk behavior depends on the module and configuration.** For example, the optional “Diamond Hands” mode changes stop-loss behavior for selected long positions. Simulated or dry-run outcomes are not equivalent to live execution, and exchange orders do not guarantee a fill at the trigger price.

## Interfaces

### Web dashboard

A Flask-based dashboard provides portfolio and market views, trade history, AI reasoning, pattern simulation, active-pair controls, copy-trading views, analysis and settings. Dedicated endpoints expose historical-model training, pattern-model training, classifier metrics and shadow positions.

### Telegram

Two-way Telegram integration provides alerts and remote commands for status, positions and bot control.

### ESP32 displays

Hardware dashboards provide a dedicated view of account and trading status. The project includes both a CYD touchscreen implementation and an additional ESP32 pixel-dashboard implementation.

## Technology

| Area | Implementation |
|---|---|
| Core and trading modules | Python |
| AI integration | Claude CLI or Anthropic API |
| Exchange integration | Hyperliquid SDK, REST and WebSocket components |
| Market context | Hyperliquid, Binance and news sources |
| Dashboard | Flask, HTML and JavaScript |
| Machine learning | Feature-bucket models, scikit-learn logistic regression, NumPy and joblib |
| Research | Historical backtests, parameter sweeps, pattern simulation and shadow-trade analysis |
| Persistence | SQLite, JSON model stores, joblib and structured logs |
| Remote interface | Telegram Bot API |
| Hardware | ESP32, C++ and PlatformIO |

## Development status

NeuraLiquid is an evolving personal project with both research and live-execution capabilities. Feature availability in the code does not establish profitability, production readiness or the status of a particular running deployment.

This public overview was refreshed on **27 September 2026** against the current private implementation. It intentionally excludes private source code, credentials, account data and internal performance records.

There is no public installation package in this repository.

---

<p align="center">
  Built for exploring the connection between market data, automated decisions and practical trading infrastructure.
</p>
