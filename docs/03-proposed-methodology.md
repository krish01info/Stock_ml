# 3. Proposed Methodology

This chapter covers the data we plan to use and the two-model system. Chapter 4 then explains how each piece is built.

## 3.1 Data sources

We are considering two example settings: an Indian index (the Nifty 50) and a few US stocks such as NVIDIA. The final choice is still open, so the table lists sources for both.

| Source | Use |
|---|---|
| Yahoo Finance via yfinance | Daily prices and volume for the Nifty 50, US stocks and the US VIX. |
| NSE India VIX history | Daily India VIX values. |
| NSE FII/DII activity | Daily foreign investor flows, a possible extra input for Indian mood. |
| Alpaca News API | Timestamped US company headlines since 2015; Alpaca also offers paper trading. |
| GDELT Project | Timestamped global news, a possible source of Indian and political headlines. |
| India News Headlines Dataset (Kaggle) | Times of India headlines with publish dates, a fallback for Indian mood. |
| Financial PhraseBank | Finance sentences labelled positive, neutral or negative, for training the headline classifier. |
| Loughran–McDonald word list | Finance word list for the simple scoring baseline. |

News is the hardest part. We need to know when each headline was published, so the model never uses a headline that appeared after the market closed. The Alpaca source has exact times for US stocks. For India we have not yet confirmed a free source with exact times, and this may decide which market we choose.

## 3.2 The two-model system

The system has two models with a slot between them. The slot holds the market state: a regime and a mood. Either Model A or the trader can fill it.

**Model A (state detector)** reads ten years of prices and headlines and labels every past day with a regime and a mood. It exists because we need thousands of past days labelled with a market state to train Model B, and no person has recorded their view for every day of the last ten years.

**Model B (predictor)** takes the market state plus today's quant facts and returns P(up) over the next k days. It can also estimate how big the move is likely to be. Model B does not care who filled the slot, as long as "volatile" and "negative" mean the same thing they meant in training.

The system runs in five steps:

1. Collect about ten years of daily prices, volume, VIX and news headlines.
2. Model A labels every past day with a regime and a mood.
3. Model B learns from those past days: given the quant facts and the state, what happened to the price over the next k days?
4. When the system is used, the trader reads the news and enters a regime and mood. Model B returns P(up), and a fan chart of how prices moved after similar past days.
5. If the trader has no view, Model A fills the slot instead. This is the pure quant mode, and comparing the two modes shows whether the trader's view added anything.

### Examples

The numbers below are made up to show the kind of answer the system gives. They are not results.

| Situation | What the trader enters | What Model B weighs | Example answer |
|---|---|---|---|
| NVIDIA announces a new product in a quiet market. | Regime calm, mood very positive. | Past calm periods with strongly positive news, plus NVIDIA's current trend and volume. | P(up over 5 days) = 0.61; fan chart from −3% to +6%. |
| The RBI raises interest rates unexpectedly. | Regime volatile, mood negative. | How the Nifty behaved after negative surprises in volatile periods. | P(up) = 0.41, with a wider fan chart because volatile periods swing more. |
| The trader feels positive, but the market shows heavy selling. | Regime calm, mood positive. | Heavy selling in the quant facts pulls the answer down. | P(up) = 0.50: the view and the data cancel out. |
| No news worth acting on. | Nothing. | Model A supplies today's regime and mood. | Pure quant answer, e.g. P(up) = 0.53. |

*Figure 1. System architecture. Model A derives regime and mood labels; Model B predicts. At prediction time the trader's view can replace Model A's.*
