# Decisions

Open decisions, the options, and what will decide each one. When a decision is made, move it to the log at the bottom with the date and the reason.

## Open

| Decision | Options | What will decide it |
|---|---|---|
| Market | Nifty 50, or a few US stocks such as NVIDIA | Whether headlines with exact publish times exist. Alpaca covers US stocks; no confirmed free Indian source yet. **Decide first** — it changes the headline source, mood categories and calendar features. |
| Horizon *k* | 1 day or 5 days | Longer gives mood time to matter but fewer independent test periods (see [experiments §4.3](experiments.md#43-statistical-power-and-confidence-intervals)) |
| Target | Plain direction, or dead zone of ±δ; raw or excess return | Label noise vs number of days ([methodology §1.1](methodology.md#11-target-definition)) |
| Mood levels | Three levels; with or without 2–3 news categories | How many days each regime × mood combination leaves |
| Future mood construction | Pre-open headlines only, exogenous categories only, or both | Result of the contamination check ([methodology §4.2](methodology.md#42-future-mood-and-price-contamination)) |
| Regime method | Rules, GMM or HMM | Match to known periods; stability |
| Headline source | Alpaca, GDELT or Kaggle | Coverage, publish times, cost, terms |
| Fan chart method | Analogue days or quantile regression | Simplicity vs fit; time available |
| Real human test | Whether to run it, and for how long | Time left in the semester |

## Decision log

| Date | Decision | Choice | Reason |
|---|---|---|---|
| 2026-10-12 | Regime meaning | Trader enters the **current** regime; only mood is forward-looking | Regime is slow; keeps training labels and trader input consistent |
| 2026-10-12 | Simulated traders | Run both an unbiased and a biased trader; plot against effective accuracy | Random errors understate the cost of real, patterned errors |
| 2026-10-12 | Evaluation | Purged walk-forward with embargo; block-bootstrap confidence intervals | Overlapping k-day labels; small number of independent periods |
