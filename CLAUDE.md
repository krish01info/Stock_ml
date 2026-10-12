# Trading on Judgement — Claude instructions

## Current focus: Model A only

We are building **Model A (state detector)** only. Do not build Model B, the experiment ladder, the backtest or any UI. Model B is described below only so Model A produces what B will need.

## The project in brief

ML course project (classical ML only: scikit-learn, hmmlearn). Two models with a slot between them:

- **Model A (state detector)** labels every past trading day with a **regime** and a **mood**. It exists because Model B needs thousands of labelled days to train on, and no person recorded their view for the last ten years.
- **Model B (predictor)** takes quant facts (returns, volatility, volume, VIX, etc.) plus a market state (regime + mood) and returns P(up) over the next k trading days. At prediction time a trader types their own regime and mood in place of Model A's. If the trader has no view, Model A fills the slot.
- **Goal:** find the break-even accuracy, i.e. how often the trader's judgement must be right to beat quant facts alone.

What Model B needs from Model A:
- Labels that mean the same thing every time ("volatile" must always mean the same regime), because a trader will pick from the same names.
- **Two moods:** today's mood (quant version of B) and the mood of the coming k days (judgement version of B).

## Model A spec

### Regime (report 4.3, 6.2)

- Cluster days on daily returns, realized volatility and volume. About **three regimes**.
- Candidates: rule-based thresholds on volatility or VIX (baseline), Gaussian mixture model (number of regimes by BIC), Gaussian HMM via hmmlearn (only probabilities based on past data).
- Models do not name groups. Inspect which periods fall in each (does March 2020 land with other crises?) and name them afterwards: calm, volatile, crisis.
- A regime for day t uses only data up to day t.

### Mood (report 4.2, 4.4, 6.1)

For each day:
1. Collect headlines published before the market close.
2. Remove market-report headlines (e.g. "Sensex falls 500 points").
3. Score each headline positive / neutral / negative. Baseline: Loughran–McDonald word counts. Classifiers: TF-IDF + logistic regression, multinomial Naive Bayes or linear SVM, trained on Financial PhraseBank.
4. Combine into one daily number, older headlines counting less. Keep the headline count.
5. Group into levels (negative, neutral, positive).

Produce both:
- **Today's mood:** headlines up to the close of day t.
- **Coming-k-days mood:** headlines published over t+1 … t+k. This uses future news on purpose; it is what the trader forecasts.

Optional: separate mood per category (policy, company and technology, economy), at most two or three.

## Output format — PROPOSED, NOT YET AGREED

One table per training window, one row per trading day, saved to `data/processed/model_a/<window>.parquet`:

| Column | Meaning |
|---|---|
| `date` | Trading day t |
| `regime` | `calm` / `volatile` / `crisis` |
| `regime_prob_<name>` | Probability of each regime (GMM/HMM only) |
| `mood_today` | `negative` / `neutral` / `positive` |
| `mood_today_score` | Daily combined score before binning |
| `headlines_today` | Number of headlines used |
| `mood_next_k` | Level for headlines over t+1 … t+k |
| `mood_next_k_score` | Score before binning |
| `headlines_next_k` | Number of headlines used |

## Folder layout — PROPOSED, NOT YET AGREED

```
src/model_a/
  data.py       load prices, VIX, headlines
  regime.py     rule, GMM, HMM detectors
  mood.py       headline filter, scorers, daily aggregation
tests/          pytest; include leakage checks
notebooks/      exploration only, no logic that code depends on
data/raw/       downloads, git-ignored
data/processed/ outputs, git-ignored
```

## Rules (report 4.4, 5.2)

- Mood from news only, never from prices.
- Remove market-report headlines.
- Fit Model A on training years only. Regime for day t uses data up to t only.
- Split by time; never shuffle.
- Preprocessing lives in a pipeline refitted on each training window.

## Open decisions — ask, do not decide

Market (Nifty 50 or US stocks such as NVIDIA), headline source (Alpaca, GDELT or Kaggle), regime method, mood levels and categories, horizon k, and whether to start with regime or build both parts together. See `docs/07-open-decisions-and-limitations.md`.

## Where to read more

Human docs in `docs/`. Model A overview: `docs/model-a.md`. Open only the chapter you need.
