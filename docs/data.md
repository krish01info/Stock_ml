# Data

Two candidate settings are still open: the **Nifty 50** (Indian index) or **a few US stocks such as NVIDIA**. See [decisions.md](decisions.md).

## Sources

| Source | Use | Market |
|---|---|---|
| Yahoo Finance via `yfinance` | Daily OHLC prices and volume; US VIX (`^VIX`) | Both |
| NSE India VIX history | Daily India VIX | India |
| NSE FII/DII activity | Daily foreign investor flows (possible extra input) | India |
| Alpaca News API | Timestamped US company headlines since 2015; also offers paper trading | US |
| GDELT Project | Timestamped global news; possible Indian and political headlines | Both |
| India News Headlines Dataset (Kaggle) | Times of India headlines with publish dates; fallback for Indian mood | India |
| Financial PhraseBank (Malo et al., 2014) | ~4,800 finance sentences labelled by sentiment; trains the headline classifier | — |
| Loughran–McDonald word list | Finance word list for the word-count baseline | — |

## Headline timestamp requirements

News is the hardest part of the data. For every headline we need:

| Field | Requirement |
|---|---|
| Publish time | To the minute, with time zone. A date alone is not enough: we must know whether it came out before the close (today's mood) and before the open (future mood, see [methodology §4.2](methodology.md#42-future-mood-and-price-contamination)). |
| Text | The headline itself; summary optional. |
| Ticker / entity tags | Needed for single stocks; optional for an index. |
| Source | To check coverage and remove duplicates syndicated across outlets. |

Alpaca meets these for US stocks. For India, no free source with exact times has been confirmed. If none is found, that likely decides the market in favour of US stocks.

## Coverage checks (run before committing to a source)

- Headlines per trading day over the full period: look for gaps, sudden jumps when the provider added sources, and weekends/holidays.
- Share of headlines with a usable timestamp.
- Share removed by the price-report filter.
- Duplicate rate across outlets.

## Storage conventions

- Raw downloads go to `data/raw/<source>/`, untouched, with the download date in the file name.
- Processed tables go to `data/processed/`, rebuilt by scripts, never edited by hand.
- `data/` is git-ignored. Each source gets a short note in this file on how to re-download it.
- All timestamps are stored in UTC and converted to the exchange time zone (IST or US Eastern) only when assigning headlines to trading days.
- One trading calendar per market is the single source of truth for which days exist.

## Licences and terms

Check and record the terms of use for each source before downloading at scale, particularly Alpaca, GDELT and the Kaggle dataset. Financial PhraseBank is released for research use.
