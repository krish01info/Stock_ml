# Candidate Models

Every model is fitted with scikit-learn (or a compatible library) inside a `Pipeline` that is refitted on each training window, so scaling and other preprocessing never see test data. Each component starts from a simple baseline.

> These are candidates, not commitments. Final choices are made by the validation results and recorded in [decisions.md](decisions.md).

## Mood: headline sentiment classifier

Trained on Financial PhraseBank; applied to our headlines.

| Candidate | Why consider it |
|---|---|
| Loughran–McDonald word counts | No training; easy to explain; baseline |
| TF-IDF + logistic regression | Strong classical text classifier; L1/L2 regularisation; inspectable word weights |
| TF-IDF + multinomial Naive Bayes | Fast; works with little data |
| TF-IDF + linear SVM | Often the most accurate classical text classifier |
| FinBERT (reference only) | Neural; outside course scope; shows the gap to state of the art |

Evaluate on a held-out PhraseBank split **and** on ~200 of our own headlines labelled by hand, since PhraseBank sentences differ from news headlines.

## Regime detector

| Candidate | Why consider it |
|---|---|
| Rule-based (volatility or VIX thresholds) | Baseline anyone can check by hand |
| Gaussian mixture model | Clusters days by returns, volatility, volume; number of regimes by BIC |
| Gaussian HMM (`hmmlearn`) | Day-to-day persistence gives steadier regimes; filtered probabilities only |

Judge by: whether regimes match known periods (e.g. March 2020 lands with other crises), how stable they are (average regime length), and whether return distributions differ across regimes.

## Model B: price direction

| Candidate | Why consider it |
|---|---|
| "Always up" | Baseline |
| Logistic regression (L1/L2) | Baseline; L1 shows whether mood survives as a useful feature |
| Decision tree | Plain yes/no rules showing how regime and mood combine |
| Random forest | Captures interactions such as "negative mood matters more in volatile regimes" |
| Gradient boosting (scikit-learn, XGBoost or LightGBM) | Usually strongest on tabular data |
| Voting or stacking ensemble | Combines the best two or three; required ensemble component |
| Optional: ridge, lasso, gradient boosting regressor, quantile regression | Size of the move; quantile regression gives fan chart edges |

Probabilities from trees and boosting are often poorly calibrated. Apply isotonic or Platt calibration inside the training window if the calibration curve shows it is needed.

## Hyperparameter search

Search inside walk-forward validation (never on the locked period) over:

- regularisation strength (C, α)
- tree depth, minimum leaf size, number of trees
- learning rate and early stopping (boosting)
- number of regimes
- mood level thresholds and recency decay

Use the **same** search budget for every ladder row so rows are comparable.

## How this meets the course requirements

| Requirement | Where it appears |
|---|---|
| Exploratory data analysis | Return distributions by regime; headline counts over time; up/down share; mood vs returns |
| Feature derivation | Regime and mood are derived features; quant facts derived from raw prices |
| Classification | Headline sentiment classifier; Model B up/down |
| Regularisation | L1/L2 logistic regression; depth and leaf limits; early stopping |
| Hyperparameter tuning | Walk-forward search over the parameters above |
| Ensemble techniques | Random forest, gradient boosting, voting/stacking |
| Performance analysis | Metrics, experiment ladder, break-even curve, backtest |
