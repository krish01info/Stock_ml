# 4. How Each Piece Is Built

Model B receives three kinds of input: quant facts, which are always computed; regime, which is computed or entered by the trader; and mood, which is computed or entered by the trader. This chapter explains each one, and then the rule that decides which mood is used in training.

## 4.1 Quant facts

Quant facts are the hard numbers, computed from market data at the close of each day. Model B always receives them.

| Group | Examples | What it tells the model |
|---|---|---|
| Trend | Returns over the last 1, 5 and 20 days; distance from the 20-day average price | Which way prices have been moving, and how far they have stretched |
| Volatility | Realized volatility over 5 and 20 days; VIX level and its 5-day change | How big the swings have been, and how nervous traders are |
| Volume | Today's volume divided by its 20-day average | Whether unusual numbers of people are trading |
| Selling pressure | Where the close sits between the day's high and low | Whether buyers or sellers were in control by the end of the day |
| Calendar | Days until the next central bank meeting or inflation report | Whether a scheduled shock is coming |

VIX and recent returns already carry some of the market's mood: when traders are frightened, VIX rises and prices fall. So part of our research question is whether a separate mood adds anything beyond what the numbers already show.

## 4.2 How mood is measured

Mood comes from news, using classical text classification. For each day:

1. Collect the headlines published before the market closed.
2. Filter out market-report headlines such as "Sensex falls 500 points". They describe the price move rather than cause it, and keeping them would let the model learn that falling prices predict falling prices.
3. Score each headline as positive, neutral or negative. The simple route counts words from the Loughran–McDonald finance word list. The stronger route trains a classifier (TF-IDF with logistic regression or Naive Bayes) on Financial PhraseBank. That classifier is a small machine learning task of its own.
4. Combine the day's scores into one number, with older headlines counting less. We also keep the number of headlines, since a day with 40 negative stories differs from a day with one.
5. Group the daily number into a few levels (negative, neutral, positive) so that a trader can pick one.

Traders think about politics, technology and the economy separately, so we may tag each headline by category (for example policy, company and technology, economy) and keep a separate mood for each. This gives the trader a richer way to express a view, but each extra category leaves less data per combination, so we would keep to two or three.

## 4.3 How regime is found

Regime comes from how the market behaved, using clustering on daily returns, realized volatility and volume. Two methods are candidates:

- A GMM groups days that look alike, treating each day separately.
- An HMM does the same but also uses the fact that regimes last: a calm market today is probably calm tomorrow. This usually suits markets better.

We plan to use about three regimes. The models do not name the groups; we look at which periods fall into each one (for example, whether the March 2020 crash lands in the same group as other crises) and name them afterwards, such as calm, volatile and crisis. Those names are the choices the trader sees. A regime for any day is worked out using only data up to that day. A simple rule-based version, such as "volatile when VIX is above a threshold", serves as a baseline.

## 4.4 Which mood goes into training

When a trader enters a mood, they are not describing today's news. They are forecasting the mood of the coming days: "this announcement will keep the news positive this week." So Model B is trained in two versions:

- **Quant version:** trained with today's mood, measured from headlines published up to today's close. This is all a model could know on the day.
- **Judgement version:** trained with the mood measured from the headlines that actually came out over the next k days. At prediction time the trader supplies this.

Using future headlines would normally be leakage. Here it is deliberate, because the future mood is exactly the thing the trader is forecasting, and measuring what that forecast is worth is the point of the project. The other leakage rules still apply in full:

- Mood is built from news only, never from prices.
- Market-report headlines are removed.
- Model A is fitted on training years only, and a regime uses only data up to its own day.
- Training and testing are split by time. The data is never shuffled.
