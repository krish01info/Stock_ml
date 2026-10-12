# Trading on Judgement

A two-model system that combines a trader's view of the market with quant data to predict price direction, and measures how accurate that view has to be before it helps.

BTech CSE 3 — Machine Learning course project.

| No. | Name | Roll number |
|---|---|---|
| 1 | Ishan Batra | 240485 |
| 2 | Himank Swami | 240474 |
| 3 | Harsh Vardhan | 240467 |
| 4 | Koushik Araveti | 240512 |
| 5 | Krish Sharma | 240514 |

> **Status:** design stage. This repository currently holds documentation only. No code has been written yet.

## The idea in one paragraph

Discretionary traders are good at reading news and forecasting what kind of situation the market is heading into. Quant models are good at remembering what usually happened in that kind of situation. This project connects the two. A trader describes the coming market state in two words: a **regime** (slow background condition, e.g. calm or volatile) and a **mood** (fast reaction to news, e.g. positive or negative). **Model B** combines that state with hard market numbers and returns P(up), the probability that the price will be higher in *k* trading days. Because nobody recorded their market view for every day of the last ten years, **Model A** labels every past day with a regime and mood from prices and headlines, so Model B has something to train on.

## The central question

**What is the break-even accuracy?** How often must a trader's judgement be right before adding it beats the quant model working alone?

The [experiment ladder](docs/experiments.md) answers this one input at a time:

1. Quant facts only (baseline)
2. + regime from Model A
3. + today's news mood from Model A
4. + true mood of the coming days (upper limit)
5. + mood of the coming days, correct only *p*% of the time (break-even curve)
6. + our own logged mood calls (real human test, optional)

## How the system fits together

```
            ┌──────────────── training ────────────────┐
 prices ──► │ Model A (state detector)                 │
 headlines► │   regime: rules / GMM / HMM              │──► regime + mood labels
            │   mood:   headline sentiment classifier  │        for every past day
            └──────────────────────────────────────────┘                │
                                                                        ▼
 quant facts (returns, volatility, VIX, volume, ...) ──────────► Model B (predictor) ──► P(up over k days)
                                                                        ▲
            at prediction time: trader's regime + mood ─────────────────┘
            (or Model A's labels if the trader has no view = pure quant mode)
```

## Documentation

| Document | What it covers |
|---|---|
| [docs/methodology.md](docs/methodology.md) | Problem definition, inputs, how quant facts, mood and regime are built, Model B |
| [docs/experiments.md](docs/experiments.md) | The experiment ladder, simulated traders, break-even curve, evaluation protocol |
| [docs/leakage.md](docs/leakage.md) | Every leakage rule, including the one deliberate exception, as a checklist |
| [docs/data.md](docs/data.md) | Data sources, headline timestamp requirements, storage conventions |
| [docs/models.md](docs/models.md) | Candidate models for each component, and how they meet course requirements |
| [docs/decisions.md](docs/decisions.md) | Open decisions, the options, and what will decide each one |
| [docs/limitations.md](docs/limitations.md) | Known limitations and threats to validity |
| [docs/references.md](docs/references.md) | Literature cited |

## Planned repository layout

This is the intended structure once implementation starts. None of it exists yet.

```
stock_ml/
├── README.md
├── docs/                  design documentation (this stage)
├── config/                market, horizon, mood levels, regime method
├── data/                  raw/ and processed/ (git-ignored)
├── src/tradejudge/
│   ├── data/              price, VIX, news loaders
│   ├── features/          quant facts
│   ├── mood/              headline filter, sentiment classifier, daily aggregation
│   ├── regime/            rule, GMM and HMM detectors, label alignment
│   ├── model_b/           direction classifiers and ensembles
│   ├── validation/        purged walk-forward splits, metrics, block bootstrap
│   └── experiments/       ladder, simulated traders, break-even curve, backtest
├── notebooks/             exploratory data analysis
├── human_calls/           timestamped log of our own daily mood calls
└── tests/                 leakage and causality tests
```

## Not financial advice

This is a learning project. Nothing in it should be used to trade real money.
