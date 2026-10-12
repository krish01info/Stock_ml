# Word Definitions

The report uses some terms from finance and from machine learning, they are explained here.

## Markets and trading

| Term | Meaning |
|---|---|
| Stock | A small share of ownership in a company, bought and sold on a stock exchange. |
| Closing price | The price at which a stock last traded at the end of a trading day. |
| Return | The percentage change in price over a period. A move from 100 to 102 is a 2% return. |
| Stock index | One number that tracks a basket of stocks. The Nifty 50 tracks 50 large Indian companies; the S&P 500 tracks 500 large US companies. |
| Trading volume | The number of shares bought and sold in a day. |
| Discretionary trading | Trading based on a person's own judgement: reading the news, sensing the market and deciding. |
| Quant trading | Trading based on rules or models built from data, with no personal judgement at the moment of the trade. |
| Bullish / bearish | Expecting prices to rise / expecting prices to fall. |
| Volatility | How much prices swing up and down. Realized volatility is measured from how much prices actually moved in recent days. |
| VIX and India VIX | An index that measures how much traders expect prices to swing over the next 30 days, worked out from the prices of options (contracts whose price depends on how much movement traders expect). It rises when traders are nervous, which is why it is called the "fear index". VIX covers the US market; India VIX covers the Indian market. |
| FII flows | Money that foreign institutional investors (large overseas funds) put into or take out of Indian stocks each day. |
| Central bank (RBI, Fed) | The institution that sets a country's interest rates: the Reserve Bank of India and the US Federal Reserve. An interest rate is the cost of borrowing money. |
| Priced in | News is priced in when the price has already moved to reflect it, so the news no longer helps predict the next move. |
| Backtest | Replaying a trading strategy on past data to see how it would have performed, including trading costs (the fees paid on every trade). |
| Paper trading | Trading with pretend money in real time to test a strategy without risking real money. |

## Our system

| Term | Meaning |
|---|---|
| Regime | The slow background condition of the market, lasting weeks or months, for example calm, volatile or crisis. |
| Mood | The market's reaction to news right now. It changes quickly, often daily, for example positive, neutral or negative. |
| Market state | Regime and mood together, for example "calm regime, positive mood". |
| Quant facts | Numbers computed directly from market data, such as recent returns, volatility, volume and VIX. Model B always receives them. |
| Model A (state detector) | Our first model. It labels every past day with a regime and a mood. |
| Model B (predictor) | Our second model. Given quant facts and a market state, it gives the probability that the price will rise. |
| Human override | The trader typing in their own regime and mood in place of Model A's labels. |
| Horizon | How many trading days ahead the prediction looks, for example 1 day or 5 days. |
| P(up) | Shorthand for "the probability that the price will be higher at the end of the horizon". |
| Fan chart | A chart of possible future price paths: a middle line and a shaded band that widens the further ahead it looks. |
| Simulated trader | A computer stand-in for a human that picks the correct mood a chosen share of the time and a random mood otherwise. |
| Break-even accuracy | How often a trader's judgement must be right before adding it beats the model working alone. |

## Machine learning and text

| Term | Meaning |
|---|---|
| Model | A program that learns patterns from past examples and applies them to new cases. |
| Training / test data | Training data is what the model learns from. Test data is kept aside and used only to check how well the model works on cases it has not seen. |
| Feature | One input number the model looks at, such as the last 5 days' return. |
| Label | The answer attached to a past example so a model can learn from it, such as "price went up". |
| Classification / regression | Classification predicts a category (up or down). Regression predicts a number (a 1.2% move). |
| Baseline | A deliberately simple method, such as "always predict up", that a real model must beat to be worth anything. |
| Overfitting | When a model memorises noise in its training data and then does badly on new data. |
| Regularization | A penalty added during training that keeps a model simple, to reduce overfitting. L1 can switch useless features off entirely; L2 shrinks all of them. |
| Hyperparameter | A setting chosen before training rather than learned, such as the maximum depth of a decision tree. |
| Ensemble | Combining several models so that their individual errors partly cancel out. |
| Walk-forward validation | A way of testing on time-ordered data: train on the past, test on the period that follows, then move forward and repeat. The data is never shuffled. |
| Leakage | When information from the future reaches the model during training by mistake, making results look far better than they really are. |
| NLP | Natural language processing: getting computers to work with human text. |
| Sentiment analysis | Scoring a piece of text as positive, neutral or negative. |
| Word list (lexicon) | A fixed list of positive and negative words. A headline is scored by counting them. |
| TF-IDF | A way to turn text into numbers. Each word gets a weight that is high when it appears often in one headline but rarely across all headlines. |
| Clustering | Grouping data points that look alike without being told the groups in advance. |
| GMM (Gaussian mixture model) | A clustering method that assumes the data comes from a few overlapping bell-shaped groups, and gives each day a probability of belonging to each group. |
| HMM (hidden Markov model) | A model that assumes the market is always in one of a few hidden conditions that tend to persist, and works out from the data which one it is probably in. Unlike a GMM, it remembers which condition the market was in yesterday. |
| Logistic regression | A classifier that combines features in a weighted sum and turns the result into a probability between 0 and 1. |
| Naive Bayes | A simple classifier that uses how often each word appears in positive and negative examples, combined with Bayes' rule of probability. |
| Decision tree | A model that predicts by asking a sequence of yes/no questions about the features. |
| Random forest | An ensemble of many decision trees, each trained on a random sample of the data, whose votes are averaged. |
| Gradient boosting | An ensemble that adds decision trees one at a time, each one correcting the mistakes of those before it. |
| SVM (support vector machine) | A classifier that finds the boundary between two classes with the widest possible gap on either side. |
| Accuracy, ROC-AUC, log loss | Ways to score a classifier. Accuracy is the share of correct calls. ROC-AUC runs from 0.5 (guessing) to 1 (perfect) and measures how well the model ranks up days above down days. Log loss punishes confident wrong probabilities. |
| Calibration | Whether a model's probabilities can be taken at face value: when it says 60%, the event should happen about 60% of the time. |
| API | A web service that a program can call to download data. |
