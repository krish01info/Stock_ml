# 6. Potential Models

> **Note on this chapter.** This chapter was prepared with the help of an AI assistant. We are not yet confident in every choice listed here. It needs more reading, discussion and testing before we commit to any of it, and the final set of models may differ.

Each part of the system has a few candidate models, starting from a simple baseline. Every model will be fitted with scikit-learn inside a pipeline that is refitted on each training window, so that scaling and other preparation steps never see test data.

## 6.1 Mood: headline sentiment classifier

| Candidate | Why consider it |
|---|---|
| Loughran–McDonald word counts | Simplest baseline; needs no training and is easy to explain. |
| TF-IDF + logistic regression | Strong classical text classifier; L1 or L2 regularization; its word weights can be inspected. |
| TF-IDF + multinomial Naive Bayes | Fast and works well with little data; a useful comparison. |
| TF-IDF + linear SVM | Often the most accurate classical text classifier. |
| FinBERT [14] (reference only) | A neural model outside the course scope; shows how far the classical models are from the state of the art. |

## 6.2 Regime detector

| Candidate | Why consider it |
|---|---|
| Rule-based (volatility or VIX thresholds) | Baseline that anyone can check by hand. |
| Gaussian mixture model | Clusters days by returns, volatility and volume; the number of regimes can be chosen with the BIC score, which rewards fit but penalises extra groups. |
| Gaussian HMM (hmmlearn library) | Adds memory from day to day, giving steadier regimes. Only the probabilities based on past data are used, to avoid leakage. |

## 6.3 Model B: price direction

| Candidate | Why consider it |
|---|---|
| "Always up" and logistic regression | Baselines. Logistic regression with L1 also shows whether mood survives as a useful feature. |
| Decision tree | Shows in plain yes/no rules how regime and mood combine. |
| Random forest | Ensemble of trees; handles interactions such as "negative mood matters more in volatile regimes". |
| Gradient boosting (scikit-learn, XGBoost or LightGBM) | Usually the strongest classical model on tables of numbers. |
| Voting or stacking ensemble | Combines the best two or three models above; required ensemble component. |
| Optional regression: ridge, lasso, gradient boosting regressor, quantile regression | Predicts the size of the move; quantile regression gives the lower and upper edges of the fan chart. |

## 6.4 How this meets the course requirements

| Requirement | Where it appears in the project |
|---|---|
| Exploratory data analysis | Return distributions in each regime; headline counts over time; share of up and down days; how mood and returns move together. |
| Feature derivation | Regime and mood are derived features, and justifying them is the core of the project. Quant facts are derived from raw prices. |
| Classification | Headline sentiment classifier; Model B's up/down prediction. |
| Regularization | L1 and L2 in logistic regression; depth and leaf-size limits for trees; early stopping in boosting. |
| Hyperparameter tuning | Search over regularization strength, tree depth, number of trees, learning rate and number of regimes, using walk-forward validation. |
| Ensemble techniques | Random forest, gradient boosting, and a voting or stacking ensemble. |
| Performance analysis | Metrics in Section 5.2, the experiment ladder, the break-even curve and the backtest. |
