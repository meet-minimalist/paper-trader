# paper-trader

Published output of a systematic paper-trading experiment on Indian
equities. Updated automatically after each NSE close.

**Live page:** https://meet-minimalist.github.io/paper-trader/

```
index.html                       the page
payload.json                     the same data as JSON
data/prices_<index>/<year>.csv   daily OHLCV cache, reusable
```

Everything committed here is generated. The strategy itself lives in a
separate private repository; the daily job checks it out into
`engine-src/`, which `.gitignore` excludes and a guard step verifies is
never tracked, so no strategy code is committed here.

The workflow runs only on a schedule or a manual dispatch. It must stay
that way: GitHub withholds secrets from fork pull requests, and that is
what keeps the private-repo token out of an outsider's reach.

## Price data

`data/` is a plain-CSV daily OHLCV cache for the index being traded, split
one file per calendar year and sorted by date. Split-and-dividend adjusted,
sourced from Yahoo Finance, with dates normalised to the IST session.

Free to reuse:

```python
import pandas as pd, glob
df = pd.concat(pd.read_csv(f, parse_dates=["date"])
               for f in glob.glob("data/prices_nifty_total_market/*.csv"))
reliance = df[df.symbol == "RELIANCE.NS"].set_index("date").sort_index()
```

## Caveats

Paper trading only - no real orders are placed, and no money is at risk.
Fills are simulated at the next session's open after a signal, with
Zerodha delivery costs applied. Past results do not predict future
results. Nothing here is investment advice.
