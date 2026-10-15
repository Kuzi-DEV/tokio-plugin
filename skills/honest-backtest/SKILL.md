---
name: honest-backtest
description: Use when the user asks whether a trading strategy, signal, indicator, backtest or prediction-market betting record works, or asks you to research, test or compare trading ideas. Runs every result through TokIO's checks so luck, market drift and lookahead aren't reported as edge.
---

# Honest backtesting with TokIO

The TokIO tools check results; you do the research in code as usual. Follow these rules.

## Before you say anything "works"

1. Get data with `get_prices` (any Yahoo ticker) and load the CSV in your own code.
2. Build positions for EVERY variant you try (each parameter set is a variant) and save them as
   one CSV: a date column, then one column per variant, the position decided at that day's close
   (1 long, -1 short, 0 flat).
3. Run `backtest_positions`. It fills on the next day, charges costs, and by default tests against
   holding the market at the same average exposure. A long-only rule that only "works" because the
   market rose will fail here, and that is the correct answer. Use `vs="cash"` only when the user
   asks whether it made money at all.
4. For returns you computed yourself, run `check_backtest` with one column per variant.
5. For prediction-market bets, run `check_contracts` with the REAL fill prices paid.

## Reporting

- Quote TokIO's first verdict line as written. "NOT SIGNIFICANT" is the result, not "promising".
- The server counts every variant checked in the session and corrects later verdicts for all of
  them. Don't work around it by testing ideas one at a time or by calling `reset_session` to start
  over; reset only when the user starts a genuinely new question.
- If the user asks how many things were tried, call `session_trials`.
- Say what the strategy returned AND what simply holding the asset returned over the same dates.

## Other tools

- `vol_surface`: implied-volatility surface for a US ticker or index, with an interactive 3D plot file.
- `put_call`: daily Cboe put/call volume since 2006.
