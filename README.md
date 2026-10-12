# Machine Learning Course Project

# Trading on Judgement

A two-model system that combines a trader's view of the market with quant data to predict price direction

**Class:** BTech CSE 3

**Group number:**

**Group members:**

| No. | Name | Roll number |
|---|---|---|
| 1 | Ishan Batra | 240485 |
| 2 | Himank Swami | 240474 |
| 3 | Harsh Vardhan | 240467 |
| 4 | Koushik Araveti | 240512 |
| 5 | Krish Shrama | 240514 |

## Summary

Traders make decisions in two broad ways. Discretionary traders read the news, weigh what is happening in politics, technology and the economy, and decide for themselves. Quant traders let models built from past data decide. Each method has its own gaps: a trader cannot recall how the market behaved in every similar situation over the last ten years, and a model cannot read tomorrow's headlines.

This project builds a system that lets the two work together. A trader describes the market in two simple terms. The regime is the slow background condition of the market, such as calm or volatile. The mood is the fast reaction to news, such as positive or negative. A machine learning model combines that description with hard numbers about the market, such as recent price changes, trading volume and volatility, and returns the probability that the price will rise over the next few days.

A model cannot learn from human judgement directly, because nobody has written down their view of the market for every day of the past ten years. So the project uses two models. Model A reads ten years of prices and news headlines and labels every past day with a regime and a mood. Model B learns how prices behaved after each kind of day. When the system is used, the trader types in their own regime and mood in place of Model A's labels.

The central question is: how often does a trader's judgement need to be right before it improves on the model working alone? We answer it by testing Model B with three kinds of input: the model's own labels, perfect knowledge of the mood that actually followed, and a simulated trader who is right a chosen share of the time. The point where the simulated trader starts to help is the break-even accuracy.

Everything uses classical machine learning: text classification to measure news mood, clustering to find regimes, and classifiers such as logistic regression and random forests to predict price direction. Some choices are still open, including which market to study and how many days ahead to predict. The report marks these clearly.

## Contents

- [Word Definitions](docs/00-word-definitions.md)
- [1. Introduction and Problem Definition](docs/01-introduction-and-problem-definition.md)
  - 1.1 Two ways to trade
  - 1.2 What already exists
  - 1.3 Problem definition
  - 1.4 Research questions
- [2. Literature Review](docs/02-literature-review.md)
- [3. Proposed Methodology](docs/03-proposed-methodology.md)
  - 3.1 Data sources
  - 3.2 The two-model system
- [4. How Each Piece Is Built](docs/04-how-each-piece-is-built.md)
  - 4.1 Quant facts
  - 4.2 How mood is measured
  - 4.3 How regime is found
  - 4.4 Which mood goes into training
- [5. What Does Judgement Add?](docs/05-what-does-judgement-add.md)
  - 5.1 The experiment ladder
  - 5.2 How results will be judged
- [6. Potential Models](docs/06-potential-models.md)
  - 6.1 Mood: headline sentiment classifier
  - 6.2 Regime detector
  - 6.3 Model B: price direction
  - 6.4 How this meets the course requirements
- [7. Open Decisions and Limitations](docs/07-open-decisions-and-limitations.md)
  - 7.1 Decisions still open
  - 7.2 Limitations
- [8. Conclusion](docs/08-conclusion.md)
- [References](docs/references.md)
