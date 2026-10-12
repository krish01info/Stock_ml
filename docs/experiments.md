# Experiments and Evaluation

## 1. The experiment ladder

One kind of input is added at a time, and the improvement from each step is recorded.

| # | Inputs to Model B | Question it answers |
|---|---|---|
| 1 | Quant facts only | What do raw numbers buy? (baseline) |
| 2 | + regime from Model A | Does knowing the background condition help? |
| 3 | + today's news mood from Model A | Does today's news help, or is it already priced in? |
| 4 | + true mood of the coming days | The most judgement could ever add (loose upper limit, see [methodology §4.2](methodology.md#42-future-mood-and-price-contamination)) |
| 5 | + coming-days mood from a simulated trader with accuracy *p* | How often must a trader be right? (break-even) |
| 6 | + our own logged mood calls | Does a real person's judgement help? (optional case study) |

Rows 1–5 use the same model family, hyperparameter search and splits, so differences come from the inputs only.

## 2. Simulated traders (row 5)

### 2.1 Defining accuracy

With three mood levels, a trader who guesses at random is still right one time in three. To avoid confusion, the simulation is parameterised by **effective accuracy** *a*: the share of days on which the entered mood equals the true future mood.

On each day, with probability *q* the trader enters the true mood; otherwise they draw from an error distribution. For a uniform error over all three levels, *a = q + (1 − q)/3*. All curves are plotted against *a*, never *q*. Chance level for three balanced levels is *a* = 1/3.

### 2.2 Two kinds of trader

Random errors partly cancel out; real errors do not. Fildes et al. (2009) found human adjustments lean optimistic, and traders tend to extrapolate recent moves. So two simulated traders are run:

| Trader | When wrong, enters… | What it represents |
|---|---|---|
| **Unbiased** | A level drawn uniformly from the wrong levels | Best case: errors are pure noise |
| **Biased** | Positive with extra weight (optimism), or the level matching the sign of the last 5-day return (extrapolation) | Realistic case: errors follow a pattern |

Both curves go in the report. The break-even accuracy for the biased trader is the more honest headline number.

### 2.3 The break-even curve

For each *a* on a grid from 1/3 to 1 (step 0.05):

1. Generate the simulated trader's mood for every test day, with 50 different random seeds.
2. Score Model B (judgement version) on the test set.
3. Average across seeds; keep the spread across seeds.

Plot score against *a*, with the quant-only score (row 1, or the best of rows 1–3) as a horizontal line. The break-even accuracy is where the curve crosses that line. Its uncertainty comes from the block bootstrap (§4.3).

## 3. Real-human test (row 6)

A small, real sample. Protocol, fixed before the first call:

- Each team member logs one call per trading day: regime (current) and mood (coming k days).
- Calls are written to `human_calls/` **before the next market open**, committed to git so the commit time is the timestamp.
- The mood definition and reference examples ([methodology §6](methodology.md#6-making-the-traders-input-mean-the-same-as-the-training-labels)) are fixed in advance and not changed.
- Nobody looks at Model B's output for that day before logging.

A semester gives roughly 40–60 calls per person. Present the result as a case study: each person's effective accuracy, where it falls on the break-even curve, and whether their errors look more like the unbiased or the biased trader. Do not claim statistical significance.

## 4. Evaluation protocol

### 4.1 Splits

- **Walk-forward only.** Train on earlier years, test on the following period, roll forward. Never shuffle.
- **Purge and embargo.** With a k-day horizon, the label of day *t* overlaps days *t+1 … t+k*. Remove from each training set every day whose label window overlaps the test period (purge), and leave a gap of at least *k* days after each test period before training resumes (embargo). See López de Prado (2018), ch. 7.
- **Locked final period.** The last stretch of data (e.g. the final 12 months) is never used for any choice and is scored once, at the end.
- All preprocessing (scalers, TF-IDF vocabulary, mood thresholds, regime models) is refitted inside each training window.

### 4.2 Metrics

| Metric | Why |
|---|---|
| Accuracy and balanced accuracy | Readable; balanced accuracy corrects for more up days than down days |
| ROC-AUC | How well up days are ranked above down days |
| Log loss | Punishes confident wrong probabilities |
| Calibration curve and Brier score | Whether P(up) = 0.6 means 60% |

Baselines every model must beat: "always up" and the up-day rate of the training window.

Realistic expectations: 52–56% direction accuracy on unseen data is normal and useful (Gu, Kelly & Xiu, 2020). Anything far above that should be treated as a bug or leakage until proven otherwise.

### 4.3 Statistical power and confidence intervals

Ten years is about 2,500 trading days. With overlapping 5-day labels that is closer to 500 independent observations, so AUC differences of 0.01–0.02 between ladder rows may be noise.

- Report every metric with a 95% confidence interval from a **block bootstrap** over test-period predictions (block length ≥ k, e.g. 20 days).
- For differences between ladder rows, bootstrap the **difference** on the same resampled blocks (paired), not each score separately.
- Report the break-even accuracy as a range: the values of *a* where the paired difference's interval crosses zero.

## 5. Backtest

A simple sanity check, not the main result.

- Trade only when Model B is confident (e.g. P(up) > 0.55 or < 0.45).
- Hold for k days; non-overlapping entries only.
- Include trading costs (e.g. 0.05% per side for an index, more for single stocks).
- Compare with buy-and-hold over the same locked period.

## 6. Exploratory analysis (before modelling)

- Return distribution in each regime.
- Headline count per day over time; gaps in coverage.
- Share of up and down days by year and regime.
- Today's mood vs next-k-day return, and future mood vs same-window return (the contamination check).
