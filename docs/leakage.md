# Leakage Rules

Leakage is when information from the future reaches the model during training, making results look far better than they are. In finance it is the most common reason a model that looks good on paper fails in practice. Every rule below should become an automated test once code exists.

## The one deliberate exception

The **judgement version** of Model B is trained on mood measured from headlines published over days t+1 … t+k. This is future information on purpose: it is exactly what the trader is forecasting, and measuring what that forecast is worth is the point of the project.

The exception covers future **mood** only. Nothing else may look ahead, and future mood itself must be built to limit price contamination ([methodology §4.2](methodology.md#42-future-mood-and-price-contamination)).

## Checklist

### Features

- [ ] Every quant fact for day *t* uses only data available at the close of *t*.
- [ ] Rolling windows are trailing (e.g. `rolling(20)` ending at *t*), never centred.
- [ ] VIX and India VIX are aligned to the same trading calendar as prices; no forward-filling from a later date.
- [ ] Calendar features use the schedule as published at the time, not revised dates.

### Mood

- [ ] Mood is built from news only, never from prices.
- [ ] Today's mood uses only headlines timestamped before the close of *t*, in the exchange's time zone.
- [ ] Price-report headlines are removed by the documented filter.
- [ ] Future mood uses pre-open headlines (or exogenous categories) per methodology §4.2.
- [ ] The sentiment classifier is trained on Financial PhraseBank only, never on our own headlines labelled by price outcome.
- [ ] Mood level thresholds are fitted on training years only and frozen for the test period.

### Regime

- [ ] The regime model is fitted on training years only.
- [ ] A day's regime uses only data up to that day: filtered HMM probabilities, never smoothed or Viterbi over the full sample.
- [ ] Cluster labels are aligned after every refit (ordered by mean volatility).

### Splits

- [ ] Data is split by time, never shuffled.
- [ ] Training days whose k-day label window overlaps the test period are purged.
- [ ] An embargo of at least k days follows each test period.
- [ ] Scalers, TF-IDF vocabulary and all other preprocessing are fitted inside each training window (scikit-learn `Pipeline`).
- [ ] The locked final period is used once, at the end.

### Sanity checks

- [ ] Shuffle the labels and retrain: AUC should fall to about 0.5.
- [ ] Shift every feature forward by one day (so it uses tomorrow's data): if performance jumps, the pipeline was respecting time; if it does not change, check for a bug.
- [ ] Any test accuracy above ~60% is investigated before it is reported.
