# 2. Literature Review

The work closest to ours falls into three groups: combining people with models, measuring news mood, and detecting market regimes. The table lists the papers we used; the notable ones are discussed below it.

| # | Study | What it did | What we take from it |
|---|---|---|---|
| 1 | Black & Litterman (1992) | Blended investors' numeric views with market data to build portfolios. | Human views can be combined with a model in a principled way. |
| 2 | Blattberg & Hoch (1990) | Compared statistical models with managers' forecasts in five business settings. | Model plus manager beat either one alone, every time. |
| 3 | Fildes et al. (2009) | Studied over 60,000 forecasts that people adjusted in supply-chain companies. | Human adjustments helped on average but leaned towards optimism. |
| 4 | Dietvorst et al. (2018) | Experiments on when people accept forecasts made by algorithms. | People use a model more when they can adjust it, which supports a human input slot. |
| 5 | Park et al. (2023) | Blended expert judgement with a model's prediction, weighted by how unusual each case is. | A general method for expert input; not built for markets. |
| 6 | Tetlock (2007) | Measured pessimism in a daily Wall Street Journal column. | Media pessimism predicted short-term price pressure that later reversed. |
| 7 | Loughran & McDonald (2011) | Built a word list for finance text. | General negative-word lists misread finance words such as "liability". Our word-list baseline. |
| 8 | Malo et al. (2014) | Released Financial PhraseBank: about 4,800 finance news sentences labelled by sentiment. | Training data for our headline classifier. |
| 9 | Bollen et al. (2011) | Measured public mood from Twitter and used it to predict a US index. | Mood can carry signal, but a very high reported accuracy on a short test calls for caution. |
| 10 | Hamilton (1989) | Introduced models in which the economy switches between hidden states. | The basis for using HMMs to find regimes. |
| 11 | Kritzman et al. (2012) | Used HMMs to detect market regimes and adjust investment strategies. | Regime labels from HMMs are useful in practice. |
| 12 | Gu, Kelly & Xiu (2020) | Compared machine learning models for predicting US stock returns. | Trees beat linear models, but predictability is small. Sets realistic expectations. |
| 13 | López de Prado (2018) | Book on machine learning for finance. | Time-ordered testing methods that prevent leakage. |

## Combining people and models

Blattberg and Hoch [2] found that a simple 50/50 blend of a model's forecast and a manager's forecast beat either one alone across five business settings. Fildes and colleagues [3] looked at tens of thousands of real adjustments and found that people improve forecasts on average, but their upward adjustments were often wrong. Dietvorst and colleagues [4] showed that people are more willing to use a model when they are allowed to adjust it. Together these suggest that human input helps, that it has predictable biases, and that a system people can steer is one they will actually use.

## Human views in finance

Black–Litterman [1] is the best-known way to combine investor views with market data. It needs views as exact numbers and uses them to set portfolio weights, which is a different job from ours. Our system accepts simple qualitative views and predicts direction, and we measure how good those views must be.

## News mood and regimes

Tetlock [6] showed that the tone of financial news relates to next-day prices, and Loughran and McDonald [7] showed that finance text needs its own word list. Most of this work measures today's news, which markets absorb quickly [15]. On regimes, Hamilton [10] and Kritzman and colleagues [11] show that markets behave differently in different hidden states and that HMMs can detect them. We use both ideas as building blocks. What we add is the step of letting a person forecast the coming state, and measuring what that forecast is worth.
