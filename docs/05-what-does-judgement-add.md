# 5. What Does Judgement Add?

The core comparison is between two things: what raw quant facts buy on their own, and what quant facts plus human judgement about news, politics, technology and society buy together.

One reason to expect a clear answer is that markets absorb news quickly [15]. By the close, most of today's headlines are already in the price, and VIX already reflects some of the fear or excitement. So a computed mood from today's news may add little. If judgement adds value, it should come from looking forward, which is what a trader does and what a model reading today's headlines cannot do.

## 5.1 The experiment ladder

We add one kind of input at a time and record how much each step improves the prediction.

| # | Inputs to Model B | Question it answers |
|---|---|---|
| 1 | Quant facts only | What do raw numbers buy? (baseline) |
| 2 | + regime from Model A | Does knowing the background condition help? |
| 3 | + today's news mood from Model A | Does reading today's news help, or is it already priced in? |
| 4 | + true mood of the coming days | The most that judgement could ever add (upper limit) |
| 5 | + mood of the coming days, correct only p% of the time | How often must a trader be right? (break-even accuracy) |
| 6 | + our own mood calls, logged daily (optional) | Does a real person's judgement help? |

Rows 4 and 5 use news-based mood as a stand-in for human judgement, because no person labelled the last ten years. Row 6 is the only test with real human input. We plan to record our own mood call each evening for the rest of the semester, then compare those calls with what happened. The sample will be small, but it is real.

Plotting row 5 for every value of p from 0% to 100% gives a curve. Where the curve crosses the quant-only line is the break-even accuracy. Figure 2 shows the shape we expect; it contains no real data.

*Figure 2. Expected shape of the break-even curve (sketch only). Below the break-even point, the trader's input makes predictions worse; above it, better.*

## 5.2 How results will be judged

- **Time order only.** Walk-forward validation: train on earlier years, test on the years after. The final stretch of data is locked away and used once, at the very end.
- **Baselines first.** Markets rise on slightly more than half of all days, so "always predict up" already scores above 50%. Every model must beat it.
- **Several scores.** Accuracy alongside balanced accuracy, ROC-AUC and log loss, since accuracy alone hides poor probabilities.
- **Realistic expectations.** A direction accuracy of 52–56% on unseen data is normal and useful in finance [12]. Anything far above that usually means a bug or leakage.
- **Simple backtest.** Trade only when Model B is confident, include trading costs, and check whether the edge survives.
