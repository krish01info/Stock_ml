# Methodology

## 1. Problem definition

For each trading day *t*:

- **x** — the quant facts known at the close of day *t*
- **s** — the market state (regime, mood) over the next *k* trading days, as forecast by the trader
- **y** — the direction label for the move from the close of *t* to the close of *t + k* (see §1.1)

**Goal 1.** Learn a model that estimates P(y = 1 | x, s).

**Goal 2.** Find the smallest judgement accuracy *p* at which this model beats a model that uses **x** alone: the **break-even accuracy**. Report it with a confidence interval, not as a single number (see [experiments.md](experiments.md)).

### 1.1 Target definition

A move of +0.01% is not meaningfully "up". Two options, to be decided (see [decisions.md](decisions.md)):

| Option | Definition | Trade-off |
|---|---|---|
| Plain direction | y = 1 if close(t+k) > close(t) | Simple; noisy labels near zero |
| Dead zone | y = 1 if return > +δ, y = 0 if return < −δ, drop the day otherwise (e.g. δ = 0.2%) | Cleaner labels; fewer training days |

For single stocks, consider using return in excess of the index so the model is not just learning market direction.

### 1.2 What the state means — regime and mood are both forecasts

The trader is forecasting the state over the *coming* k days. Model B must be trained on labels with the same meaning, so both components need an explicit definition:

| Component | Training label (judgement version) | Training label (quant version) | What the trader enters |
|---|---|---|---|
| Mood | Mood measured from headlines published over t+1 … t+k | Mood from headlines up to the close of t | Forecast of coming-days mood |
| Regime | Regime of day t, computed from data up to t | Same | **Their view of the current regime** |

Regime is slow (weeks to months), so the current regime is a reasonable proxy for the regime over the next k days. The trader is asked for the *current* regime, and that is what Model B is trained on. Only mood is forward-looking. This must be stated on any interface the trader sees.

## 2. The two-model system

**Model A (state detector)** labels every past day with a regime and a mood. It exists only because Model B needs thousands of labelled days to train on.

**Model B (predictor)** takes quant facts and a market state and returns P(up over k days). It does not care who filled the state slot, provided the labels mean the same thing they meant in training (§5).

Workflow:

1. Collect ~10 years of daily prices, volume, VIX and headlines.
2. Model A labels every past day with regime and mood.
3. Model B learns from those days: given quant facts and state, what happened over the next k days?
4. At prediction time, the trader enters a regime and mood. Model B returns P(up) and a fan chart.
5. If the trader has no view, Model A fills the slot (pure quant mode).

## 3. Quant facts

Computed at the close of each day from data available at that close. Model B always receives them.

| Group | Features | What it tells the model |
|---|---|---|
| Trend | Returns over 1, 5, 20 days; distance from 20-day mean price | Direction and stretch |
| Volatility | Realized vol over 5 and 20 days; VIX level; 5-day VIX change | Size of swings, nervousness |
| Volume | Volume ÷ 20-day average volume | Unusual participation |
| Selling pressure | (close − low) ÷ (high − low) | Who controlled the close |
| Calendar | Trading days to next central bank meeting / inflation release | Scheduled shocks ahead |

VIX and recent returns already carry some mood. Whether a separate mood adds anything beyond them is part of the research question (ladder row 3).

## 4. Mood

Built from news only, never from prices.

1. **Collect** headlines with exact publish timestamps.
2. **Filter** headlines that report price moves rather than cause them (see §4.1).
3. **Score** each headline positive / neutral / negative:
   - Baseline: Loughran–McDonald word counts.
   - Main: TF-IDF + logistic regression (or Naive Bayes / linear SVM) trained on Financial PhraseBank.
4. **Aggregate** to one daily number with recency decay. Keep the headline count as a separate feature.
5. **Discretise** into levels by **fixed quantiles of the training-period distribution** (e.g. bottom 30% = negative, top 30% = positive). Thresholds are fitted on training years only and frozen.

Optional: tag headlines by category (policy, company/technology, economy) with a separate mood per category. Limit to 2–3 categories; each one splits the data further.

### 4.1 Filtering price-report headlines

A keyword filter ("falls", "surges", "points", "Sensex", "Nifty", index names) catches obvious cases but misses indirect ones such as "investors rattled as…" or "chipmakers rally on…". These matter far more for **future** mood than for today's mood (see §4.2). The filter must be:

- documented as a fixed list in config,
- evaluated by hand on a random sample of ~200 kept headlines, reporting the share that are really price reports.

### 4.2 Future mood and price contamination

The judgement version of Model B trains on mood from headlines published over t+1 … t+k. Much of that news is *caused by* price moves in the same window. Left unchecked, this makes the upper limit (ladder row 4) measure the price partly through the news, which inflates every point on the break-even curve.

Mitigations, in order of preference:

1. **Pre-open headlines only.** For each future day, use only headlines published before that day's market open. News written after the open can react to that day's move.
2. **Exogenous categories only.** Restrict future mood to categories that are not reactions to price: policy decisions, earnings releases, product announcements, scheduled macro data.
3. **Contamination check (always report).** Correlation between future mood and the same window's return, compared with the correlation between today's mood and the next window's return. A much larger same-window correlation means the future mood is partly reading the price.

Row 4 must be described as a loose ceiling in the final report.

## 5. Regime

Found by clustering daily returns, realized volatility and volume.

| Method | Notes |
|---|---|
| Rule-based | e.g. volatile when VIX above a threshold. Baseline. |
| GMM | Treats each day independently. Number of regimes chosen by BIC. |
| Gaussian HMM | Adds persistence. Use **filtered** probabilities (forward algorithm, data up to t only), never smoothed (Viterbi or forward-backward over the full sample). |

About three regimes, named after inspection (e.g. calm, volatile, crisis), checked against known periods such as March 2020.

### 5.1 Label alignment across refits

Each walk-forward window refits the regime model, and cluster indices are arbitrary. Cluster "0" in one window may be cluster "2" in the next. After every fit, **reorder clusters by their mean realized volatility** (lowest = calm). Without this, "calm" means different things in different folds.

## 6. Making the trader's input mean the same as the training labels

Model B learned "positive" as "classifier score in the top 30% of training days". A trader's "positive" may be looser or stricter. To keep them on the same scale:

- Show the trader the definition in plain words ("positive = a news week better than about 70% of weeks in the last ten years").
- Provide reference examples: a handful of past weeks for each level, with sample headlines.
- In the real-human test, compare the trader's level distribution with the training distribution and report any skew.

## 7. Model B outputs

- **P(up over k days)** — the main output. Must be calibrated (see [experiments.md](experiments.md)).
- **Fan chart** — the spread of k-day returns. Two candidate methods; pick one and state it:
  - *Analogue days:* distribution of realised k-day returns on past days with the same state and similar quant facts (nearest neighbours).
  - *Quantile regression:* predict the 10th, 50th and 90th percentiles directly.

Magnitude prediction is optional and should be cut if classification work runs late.
