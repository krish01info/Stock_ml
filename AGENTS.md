# Project context for AI assistants

## Current focus: Model A only

We are working on **Model A (state detector)** only. Do not build Model B or the experiments yet. Before any Model A work, read `ai/model-a.md`.

**Trading on Judgement** — ML course project. Model A labels each past day with a regime (slow: calm/volatile/crisis) and mood (fast news reaction: positive/neutral/negative). Model B takes quant facts + market state and returns P(up) over the next k days. At prediction time a trader's regime and mood replace Model A's. Goal: find the break-even accuracy at which trader judgement beats quant facts alone.

Classical ML only (scikit-learn, hmmlearn). Full design: `README.md` and `docs/` — read only the chapter you need.

## Rules (from docs/04, section 4.4 and docs/05, section 5.2)

- Split by time. Never shuffle. Walk-forward validation; final stretch locked until the end.
- Mood from news only, never from prices. Remove market-report headlines.
- Model A fitted on training years only; a day's regime uses only data up to that day.
- Preprocessing lives in a pipeline refitted on each training window.
- Only deliberate leakage: the judgement version of Model B trains on mood from headlines over the next k days.
- Every model must beat "always predict up". Accuracy far above 52–56% likely means a bug or leakage.

Open decisions (market, horizon, mood levels, regime method, headline source): `docs/07-open-decisions-and-limitations.md`. Don't decide them in code without asking.
