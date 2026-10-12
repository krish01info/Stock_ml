# Model A — State Detector

Current focus of the project. This page collects everything the report says about Model A in one place. Sources: Chapters 3, 4, 6 and 7.

## What Model A does

Model A reads ten years of prices and headlines and labels every past day with a **regime** and a **mood**. It exists because we need thousands of past days labelled with a market state to train Model B, and no person has recorded their view for every day of the last ten years. (Section 3.2)

| Output | Meaning |
|---|---|
| Regime | The slow background condition of the market, lasting weeks or months, for example calm, volatile or crisis. |
| Mood | The market's reaction to news right now. It changes quickly, often daily, for example positive, neutral or negative. |

When the trader has no view, Model A fills the slot for Model B instead. This is the pure quant mode. (Section 3.2, step 5)

## Part 1 — Regime detector

Regime comes from how the market behaved, using clustering on daily returns, realized volatility and volume. (Section 4.3)

| Candidate | Why consider it (Section 6.2) |
|---|---|
| Rule-based (volatility or VIX thresholds) | Baseline that anyone can check by hand. |
| Gaussian mixture model | Clusters days by returns, volatility and volume; the number of regimes can be chosen with the BIC score, which rewards fit but penalises extra groups. |
| Gaussian HMM (hmmlearn library) | Adds memory from day to day, giving steadier regimes. Only the probabilities based on past data are used, to avoid leakage. |

- We plan to use about **three regimes**.
- The models do not name the groups. We look at which periods fall into each one (for example, whether the March 2020 crash lands in the same group as other crises) and name them afterwards, such as calm, volatile and crisis. Those names are the choices the trader sees.
- A regime for any day is worked out using only data up to that day.

## Part 2 — Mood detector

Mood comes from news, using classical text classification. For each day (Section 4.2):

1. Collect the headlines published before the market closed.
2. Filter out market-report headlines such as "Sensex falls 500 points". They describe the price move rather than cause it.
3. Score each headline as positive, neutral or negative.
4. Combine the day's scores into one number, with older headlines counting less. Also keep the number of headlines.
5. Group the daily number into a few levels (negative, neutral, positive) so that a trader can pick one.

Optional: tag each headline by category (for example policy, company and technology, economy) and keep a separate mood for each. Keep to two or three categories.

| Candidate scorer | Why consider it (Section 6.1) |
|---|---|
| Loughran–McDonald word counts | Simplest baseline; needs no training and is easy to explain. |
| TF-IDF + logistic regression | Strong classical text classifier; L1 or L2 regularization; its word weights can be inspected. |
| TF-IDF + multinomial Naive Bayes | Fast and works well with little data; a useful comparison. |
| TF-IDF + linear SVM | Often the most accurate classical text classifier. |
| FinBERT (reference only) | Outside the course scope; shows how far the classical models are from the state of the art. |

The classifier is trained on **Financial PhraseBank**.

### Two moods for training (Section 4.4)

- **Today's mood:** from headlines published up to today's close. Used by the quant version of Model B.
- **Mood of the coming k days:** from the headlines that actually came out over the next k days. Used by the judgement version of Model B. This is deliberate, because it is exactly what the trader forecasts.

## Data Model A needs (Section 3.1)

| Source | Use |
|---|---|
| Yahoo Finance via yfinance | Daily prices and volume; US VIX |
| NSE India VIX history | Daily India VIX |
| Alpaca News API | Timestamped US company headlines since 2015 |
| GDELT Project | Timestamped global news |
| India News Headlines Dataset (Kaggle) | Times of India headlines with publish dates |
| Financial PhraseBank | Labelled sentences for training the headline classifier |
| Loughran–McDonald word list | Word list for the baseline scorer |

We need to know when each headline was published, so the model never uses a headline that appeared after the market closed.

## Rules (Section 4.4)

- Mood is built from news only, never from prices.
- Market-report headlines are removed.
- Model A is fitted on training years only, and a regime uses only data up to its own day.
- Training and testing are split by time. The data is never shuffled.

## Open questions before building

From Section 7.1, plus where to start:

| Question | Options | Status |
|---|---|---|
| Market | Nifty 50, or a few US stocks such as NVIDIA | Open |
| Headline source | Alpaca, GDELT or the Kaggle dataset | Open |
| Regime method | Rules, GMM or HMM — one first, or all side by side | Open |
| Mood levels | Three levels, with or without news categories | Open |
| Where to start | Regime part first (needs prices only), or both parts together | Open |
