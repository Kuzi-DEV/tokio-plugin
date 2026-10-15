# TokIO for Claude: is your backtest real?

TokIO checks trading research for the three things that make most backtests lie:
**luck** (trying many ideas and keeping the best), **market drift** (a long-only rule
"works" because the market rose) and **lookahead** (a signal that peeks at the future).
Claude does the research in code; TokIO checks every result before Claude reports it.

What makes it different: **it counts every test.** The server keeps a ledger of every
strategy variant checked in your session, and each verdict is corrected for all of them.
If Claude tries fifteen ideas and reports the one that "worked", the verdict already
knows about the other fourteen.

## Install

Requires [uv](https://docs.astral.sh/uv/) (which provides `uvx`).

```
claude plugin install tokio-ai --marketplace Kuzi-DEV/tokio-plugin
```

The first start downloads `tokio-ai` and about 40 dependencies (a few minutes on a slow
connection). If Claude Code reports that the `tokio` server failed to start, wait a minute
and run `/mcp` to reconnect; later starts are instant.

Then ask, for example: *"Test 5, 20 and 50 day momentum on QQQ with TokIO against holding QQQ."*

## What's inside

- **MCP server** (`tokio`), from the open-source [tokio-ai](https://github.com/Kuzi-DEV/Tokio-ai)
  Python package, version pinned to 0.7.0 and started with `uvx`. Tools:
  - `get_prices`: daily price history for a ticker, saved as a CSV.
  - `backtest_positions`: backtest daily positions with next-day fills and costs,
    tested against holding the market by default, plus a lookahead check.
  - `check_backtest`: Deflated Sharpe, HAC p-values, Romano-Wolf correction for grids,
    probability of backtest overfitting, robustness checks.
  - `check_contracts`: exact test for prediction-market bets (Kalshi/Polymarket style).
  - `vol_surface`: implied-volatility surface with an interactive 3D plot.
  - `put_call`: daily Cboe put/call volume since 2006.
  - `session_trials` / `reset_session`: show or clear the session ledger.
- **Skill** (`honest-backtest`): tells Claude to pass every variant tried, test against the
  market, and quote verdicts as written.

## What it runs, sends and fetches

- Runs locally on your machine via `uvx`, which downloads `tokio-ai` 0.7.0 and its
  dependencies from PyPI the first time.
- Fetches public market data over HTTPS: Yahoo Finance (`query1.finance.yahoo.com`, price
  history and the 13-week T-bill rate) and Cboe (`cdn.cboe.com`, delayed option quotes and
  daily put/call statistics).
- Reads the CSV files you or Claude point it at, and writes its outputs (CSVs and HTML
  plots) to `~/.tokio_ai/cache`. The 3D plot HTML loads plotly.js from `cdn.plot.ly` when you open it.
- Sends nothing else anywhere: no accounts, no API keys, no telemetry.

The statistics, their measured false-positive rates and the case studies are documented in
the [main repository](https://github.com/Kuzi-DEV/Tokio-ai). TokIO is research software,
not investment advice.

## License

MIT
