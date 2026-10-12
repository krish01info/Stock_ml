# 1. Introduction and Problem Definition

## 1.1 Two ways to trade

Stock prices move for many reasons: company results, interest rates, elections, wars, new products and plain fear or excitement. Traders deal with this in two broad ways.

A discretionary trader reads that NVIDIA has announced a new product, decides that the announcement matters, and buys. A quant trader runs a model that has studied years of price data and buys only when today's numbers resemble past situations in which prices rose. The table below compares the two.

| | Discretionary trader | Quant model |
|---|---|---|
| Good at | Understanding news and context: a new product, a policy change, an election result | Remembering how the market behaved in thousands of past situations, without emotion |
| Weak at | Recalling history precisely; staying consistent; avoiding over-optimism | Understanding events it has never seen in its data; reading the news in context |
| Typical question | "This announcement is big. Should I buy?" | "Prices behaved like this 300 times before. What usually happened next?" |

The two strengths fit together. The trader is good at forecasting what kind of situation the market is heading into; the model is good at knowing what usually happens in that kind of situation. Our project connects them: the trader supplies the situation, and the model supplies the history.

## 1.2 What already exists

Researchers have combined human judgement with models before, in four main ways.

| Approach | What it does | What it leaves out |
|---|---|---|
| Automatic news sentiment as a model input [6][9] | The model scores today's news itself and uses the score as a feature. | No human view of the future. Today's news is also largely priced in by the time the market closes [15]. |
| Black–Litterman investor views [1] | Blends an investor's numeric views ("NVIDIA will rise 8%") with market data to decide how much of each stock to hold. | Views must be exact return numbers, not qualitative views like "volatile and negative". It decides portfolio weights, not whether a price will rise. It does not measure how good the views need to be. |
| Judgemental adjustment of forecasts [2][3] | A person adjusts a model's forecast after the model has made it. | The model never learns how the market behaves in the situation the person describes. Mostly studied for sales and supply chains, not stock prices. |
| Expert judgement in machine learning [5] | Blends an expert's rule with a model's output, trusting the expert more when a case looks unlike the training data. | Not designed for markets. The judgement is a fixed rule, not a forecast of the coming situation. |

What we did not find is a system in which a trader's qualitative, forward-looking judgement is an input the model has been trained to understand, together with a measurement of how accurate that judgement must be to be useful. Our search was limited to what a course project allows, so this gap should be read as "uncommon" rather than "never done".

## 1.3 Problem definition

In plain words: given what the numbers say about the market today and a trader's forecast of the market state over the coming days, estimate the probability that the price will be higher a few days from now. Then find the lowest accuracy of that forecast at which the combined system beats the numbers alone.

Written a little more precisely, for each trading day t:

- x = the quant facts known at the close of day t
- s = the market state (regime and mood) over the next k days, as forecast by the trader
- y = 1 if the price k days later is higher than today's closing price, otherwise 0

**Goal 1.** Learn a model that estimates P(y = 1 | x, s): the chance of a rise, given the numbers and the trader's view.

**Goal 2.** Find the smallest judgement accuracy p at which this model beats a model that uses x alone. We call this the break-even accuracy.

## 1.4 Research questions

1. **What do quant facts alone buy?** How well can price direction be predicted from market numbers only? This is our baseline.
2. **Does the market state help when a model computes it?** Does adding regime and today's news mood improve the prediction, or is that information already priced in?
3. **What is perfect judgement worth?** If we knew the true mood of the coming days, how much better would the prediction be? This is the upper limit.
4. **What is the break-even accuracy?** How often must a trader's judgement be right before it beats quant facts alone?

Which market we study and how many days ahead we predict are still open. Chapter 7 lists every open decision.
