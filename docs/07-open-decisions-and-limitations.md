# 7. Open Decisions and Limitations

## 7.1 Decisions still open

| Decision | Options | What will decide it |
|---|---|---|
| Market | Nifty 50, or a few US stocks such as NVIDIA | Whether we find headlines with exact publish times |
| Horizon | Next day, or next 5 days | A longer horizon gives mood time to matter but gives fewer separate test periods |
| Mood levels | Three levels, with or without news categories | How much data each combination leaves |
| Regime method | Rules, GMM or HMM | Which gives regimes that match known periods and stay stable |
| Headline source | Alpaca, GDELT or the Kaggle dataset | Coverage, publish times and cost |
| Real human test | Whether and how long we log our own calls | Time left in the semester |

## 7.2 Limitations

- **Judgement is simulated.** Most experiments use news-based mood as a stand-in for a person's view. Real traders may be biased in ways the simulation does not capture, such as over-optimism [3].
- **Small edges.** Price direction is hard to predict. We may find that judgement adds little or nothing; that is still a valid and reportable answer to the research question.
- **Few regimes.** Ten years contain only a handful of crises, so the model sees few examples of the rarest regime.
- **Change over time.** Relationships learned from the past may not hold in future markets.
- **Uneven news coverage.** Some days and some companies have far more headlines than others.
- **Not financial advice.** This is a learning project. Nothing in it should be used to trade real money without far more testing.
