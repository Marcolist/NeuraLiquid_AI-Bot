<p align="center">
  <img src="https://raw.githubusercontent.com/Marcolist/NeuraLiquid_AI-Bot/main/NeuraLiquid.png" alt="NeuraLiquid" width="280">
</p>

<h1 align="center">NeuraLiquid</h1>

<p align="center">
  <strong>AI-Powered Autonomous Trading Agent for Hyperliquid Perpetuals</strong>
</p>

<p align="center">
  Three AI minds. One decision. Zero API costs.
</p>

---

## What is NeuraLiquid?

NeuraLiquid is a fully autonomous trading system that uses **Claude AI** to trade perpetual futures on [Hyperliquid](https://hyperliquid.xyz). Instead of relying on a single strategy or a fixed set of rules, it deploys a **Dual-Perspective Signal Pattern** — two AI analysts with opposing biases debate every trade before a portfolio manager makes the final call.

It runs 24/7, manages its own risk, learns from its trades, and costs nothing to operate beyond a Claude subscription.

---

## The Core Idea: AI Debate Before Every Trade

Most trading bots follow rules. NeuraLiquid **thinks**.

Every potential trade goes through a structured AI debate:

| Role | Bias | Purpose |
|---|---|---|
| **Momentum Bull** | Aggressive | Finds early entries, momentum plays, trend continuations |
| **Risk Skeptic** | Conservative | Challenges every setup, spots overextended moves, protects capital |
| **Portfolio Manager** | Balanced | Synthesizes both perspectives, makes the final decision |

Both analysts receive the same market data — technicals, funding rates, open interest, order flow, news sentiment, whale activity — but interpret it through their respective lenses. The Portfolio Manager weighs both arguments and decides: trade, skip, or reduce size.

This isn't a gimmick. It's a systematic way to avoid the single biggest problem in algorithmic trading: **confirmation bias**.

---

## What Makes It Different

### Runs on Claude via OAuth — No API Costs

NeuraLiquid uses the Claude CLI with OAuth authentication. If you have a Claude Pro or Max subscription, the bot runs at **zero additional cost**. No pay-per-token billing, no API key management.

### 7-Stage Entry Filter Pipeline

A green light from the AI debate is just the beginning. Every trade must pass through seven independent filters before execution:

1. **Market Regime** — Is the broader market trending or ranging?
2. **Meta-Labeling** — What's the historical win rate for this exact setup?
3. **ML Model** — A self-trained model predicts win probability from candle patterns
4. **VWAP Confirmation** — Is price positioned correctly relative to volume-weighted average?
5. **Momentum & Volume** — Do RSI and volume confirm the direction?
6. **Whale Order Flow** — Are large players moving with or against the trade?
7. **Risk-Reward Check** — Do orderbook walls make the R:R ratio unacceptable?

Each filter can halve the position size or reject the trade entirely. The result: fewer trades, higher quality.

### Self-Learning ML Signal Filter

The bot trains its own machine learning model from historical data — no external libraries required. It uses triple-barrier labeling to evaluate whether past setups hit take-profit or stop-loss first, then applies those learnings to filter future entries. Training happens automatically during off-hours.

### Adaptive Everything

Nothing is static:

- **Leverage** adjusts to volatility (2x in chaos, up to 5x in calm markets)
- **Stop-Loss & Take-Profit** scale with ATR instead of using fixed percentages
- **Loop Interval** ranges from 60 seconds (high volatility) to 10 minutes (low volatility)
- **Position Size** scales with the Portfolio Manager's confidence (0.25x to 1.0x)

### Whale Tracking & Order Flow

A real-time whale watcher monitors:

- **Trade Flow** across top coins every 30 seconds, detecting buy/sell imbalances
- **Leaderboard Positions** from Hyperliquid's top 10 most profitable active traders
- **Large Trades** exceeding $10k that signal institutional movement

These signals feed directly into the AI debate as additional context.

### Cross-Exchange Intelligence

The bot pulls signals from Binance (no API key needed) to compare:

- Funding rate divergences between Hyperliquid and Binance
- Top trader long/short ratios for contrarian signals
- Volume confirmation across exchanges

### News Sentiment & Emergency Detection

A news agent continuously scans CoinDesk and CoinTelegraph for market-moving events. If it detects an emergency (hack, depeg, major exploit), it can trigger an immediate close of all positions.

---

## Risk Management

NeuraLiquid treats capital preservation as a first-class feature:

- **Account Drawdown Protection** — Hard stop at 15% drawdown, closes everything
- **Daily Loss Limit** — Configurable cap, triggers cooldown when hit
- **Diamond Hands Mode** — BTC, ETH, SOL longs are never sold at a loss
- **6 Exit Mechanisms** — Stop-loss, take-profit, trailing stops, partial exits, time-based ROI tables, and stuck position detection
- **Session Timing** — No new trades during thin market hours (configurable)
- **Cooldowns** — 30 min after stop-loss hits, 2h before re-entering the same coin
- **Correlation Check** — Prevents stacking correlated positions

---

## Full Control From Anywhere

### Web Dashboard

A real-time dashboard with full control:

- Live portfolio with P&L, open positions, entry prices, and stop levels
- Market scanner with top coins ranked by volume, funding, and open interest
- One-click Dual-Perspective analysis for any coin
- Manual order placement (long, short, close)
- Full reasoning chain for every trade decision
- Trade performance analytics (win rate, P&L, day vs night breakdown)
- ML model status, accuracy metrics, and manual training trigger
- Bot controls: start, stop, dry run, restart, close all

### Telegram Bot

Two-way Telegram integration for remote monitoring and control:

- **Alerts**: Trade opens/closes, stop-loss hits, daily loss warnings, errors
- **Commands**: Check status, view positions, close trades, start/stop the bot — all from your phone

### Hardware Display

For those who want a dedicated screen: a custom firmware for the ESP32 CYD (Cheap Yellow Display) shows account balance, daily P&L, open positions, and win rate on a 2.8" touchscreen with WiFi connectivity.

---

## Architecture

```
Market Data (Hyperliquid + Binance + News + Whale Flow)
                        │
                        ▼
        ┌───────────────────────────────┐
        │     Dual-Perspective Engine    │
        │                               │
        │  ┌─────────┐   ┌───────────┐  │
        │  │Momentum │   │   Risk    │  │
        │  │  Bull   │   │  Skeptic  │  │
        │  └────┬────┘   └─────┬─────┘  │
        │       └───────┬──────┘        │
        │               ▼               │
        │     ┌───────────────┐         │
        │     │   Portfolio   │         │
        │     │   Manager     │         │
        │     └───────┬───────┘         │
        └─────────────┼─────────────────┘
                      ▼
        ┌───────────────────────────────┐
        │   7-Stage Entry Filter        │
        │   Regime → Meta → ML → VWAP   │
        │   → Momentum → Flow → R:R     │
        └───────────────┬───────────────┘
                        ▼
        ┌───────────────────────────────┐
        │   Adaptive Execution          │
        │   Size · Leverage · SL · TP   │
        └───────────────┬───────────────┘
                        ▼
        ┌───────────────────────────────┐
        │   Risk Manager (continuous)   │
        │   SL/TP · Trailing · Partial  │
        │   ROI Table · Stuck · Stale   │
        └───────────────────────────────┘
```

---

## Tech Stack

| Component | Technology |
|---|---|
| Language | Python |
| AI Engine | Claude (Haiku for analysts, Sonnet for PM) |
| Exchange | Hyperliquid (Perpetual Futures) |
| Cross-Exchange Data | Binance (public endpoints) |
| ML | Custom feature-scoring model (zero dependencies) |
| Price Feed | WebSocket (real-time) with REST fallback |
| Dashboard | Flask + vanilla JS |
| Database | SQLite |
| Alerts & Control | Telegram Bot API |
| Hardware Display | ESP32-2432S028R (PlatformIO) |

---

## Status

NeuraLiquid is actively developed and running in production. The source code is in a private repository.

This project is a personal trading tool — not financial advice, not an investment product. Use at your own risk.

---

<p align="center">
  Built with Claude AI on Hyperliquid
</p>
