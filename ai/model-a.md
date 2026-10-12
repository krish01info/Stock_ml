# Model A (state detector) — AI context

Current focus. Human version: `docs/model-a.md`.

Labels every past trading day with:
- **regime**: ~3 clusters on daily returns, realized vol, volume. Candidates: VIX/vol threshold rules (baseline), GMM (n by BIC), Gaussian HMM via hmmlearn (filtered probs only). Name clusters after inspection (calm/volatile/crisis; check March 2020).
- **mood**: headlines before close → drop market-report headlines → score pos/neu/neg (Loughran–McDonald baseline; TF-IDF + LR / NB / linear SVM trained on Financial PhraseBank) → recency-weighted daily score + headline count → bin into levels.
- Two mood outputs: today's mood (up to close of t) and coming-k-days mood (headlines over t+1..t+k, deliberate).

Rules: news-only mood; fit on training years only; regime for day t uses data ≤ t; time splits, never shuffle.

Open — ask, don't decide: market (Nifty 50 / US stocks), headline source (Alpaca / GDELT / Kaggle), regime method, mood levels, start order.
