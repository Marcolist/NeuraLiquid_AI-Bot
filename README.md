<p align="center">
  <img src="NeuraLiquid.png" alt="NeuraLiquid" width="280">
</p>

<h1 align="center">NeuraLiquid</h1>

<p align="center">
  <strong>Modular trading research and automation for Hyperliquid</strong>
</p>

<p align="center">
  AI-assisted analysis · Pattern strategies · Copy trading · Risk management · Web and ESP32 dashboards
</p>

---

## Overview

NeuraLiquid is a personal trading and research platform for Hyperliquid perpetuals, including supported markets on the xyz DEX. It combines market scanning, AI-assisted decisions, algorithmic pattern detection, wallet tracking, execution and trade diagnostics.

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
          Entry checks and conviction sizing
                    |
          Execution and position management
                    |
          Trade history and diagnostics
                    |
          Web dashboard · Telegram · ESP32 displays

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

## Execution, risk and diagnostics

The implementation includes:

- Configurable position, leverage, exposure and loss limits
- Exchange-side stop/target order support and trailing-stop logic
- Pattern-engine position recovery and stop-verification routines
- Cooldowns, time-based exits and consecutive-loss pauses
- Persistent trade and decision history in SQLite
- Maximum favorable/adverse excursion (MFE/MAE) and exit diagnostics
- Simulation, dry-run records and live-fill reconciliation for comparing expected and observed behavior

**Risk behavior depends on the module and configuration.** For example, the optional “Diamond Hands” mode changes stop-loss behavior for selected long positions. Simulated or dry-run outcomes are not equivalent to live execution, and exchange orders do not guarantee a fill at the trigger price.

## Interfaces

### Web dashboard

A Flask-based dashboard provides portfolio and market views, trade history, AI reasoning, pattern simulation, active-pair controls, copy-trading views, analysis and settings.

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
| Persistence | SQLite and structured logs |
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
