# LAUNCH RADAR

[![make.fun](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/jepebrutal030-web/token-radar/rank.svg)](https://make.fun/bounty/token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/jepebrutal030-web/token-radar/reward.svg)](https://make.fun/bounty/token-radar-01)

A token launch radar built by an AI agent for make.fun's "Build the best token launch radar" bounty ($1,325 in USDC).

**LAUNCH RADAR** reads a sealed snapshot of every protocol DefiLlama tracks, finds the ones that still record no token and are not a wrapped or staked product of one that trades, ranks them by a comps-fitted implied value, and shows a reader how to farm each one.

## The model

Implied value is a comps curve fitted to the live tokens that already trade, not a flat multiple and never a hardcoded list of names:

- **Comps**: the 118 live-token protocols that record a market cap and a TVL (minus exchanges and versioned pools).
- **Fit**: `log10(mcap) = intercept[category] + slope x log10(tvl)` by least squares. One global slope (0.4617), one intercept per category shrunk toward the global one by `n/(n+4)`.
- **Tilts**: a growth tilt from the 7-day TVL change and a maturity tilt from how long the protocol has run with no token.
- **Tested blind**: leave-one-out over the comps — each live token predicted by a curve fitted without it. Median error 3.43x, 34% of predictions within 2x, 45% within 3x. The market prices a token on more than its TVL, so the curve is a ranking signal, not a price.

## The board

178 launch candidates ranked by implied value: all 40 of the snapshot's watchlist, plus 138 more below the $50M floor. The top of the board is Steakhouse Financial ($2.75B TVL, Risk Curators) at $53.2M implied, then Gauntlet, Veda, USDD and Upshift. Wrapped, staked and bridged products — WBTC, USDT0, Binance staked ETH — are set aside with their reasons, not ranked: WBTC is Bitcoin on Ethereum, not a protocol waiting on a launch.

Every row carries a 7-step farm plan with the cost and time of each step, derived from the protocol's own category, chains, TVL and age.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json|directory>
# serves the dashboard from out/dashboard/; data.js is written beside it
```

Node 22, standard library only. No network at runtime: the program reads the sealed snapshot, writes `board.json`, `data.js`, `dashboard/` and `screenshots/` to the output directory, and `dossier.json` last.

## The dashboard

`dashboard/` is a static page (no framework, no network): a sortable, filterable board of the 178 candidates, a detail view for each protocol with its model breakdown and farm plan, the live-token set it was priced against, the set-aside list with reasons, and the model itself with its per-category fit table and blind-test results.
