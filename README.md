# token-radar-01

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/oxrhein/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/oxrhein/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Token Launch Radar (radar-2): a ranked board of pre-token DeFi protocols by implied launch value, read from a sealed DefiLlama snapshot. Every row is scored by one transparent function of the snapshot's own figures — TVL, 7-day growth, age with no token, chain breadth, and a risk discount — with the category multiple read from the live-token protocols in the same category (median market-cap / TVL, shrunk toward the global median), never from a hand-written list of names. The board covers all 40 watchlist protocols plus every other rankable pre-token protocol (198 rows), each with a 7-step farm plan stating cost and time. The dashboard is a static page with no network: sortable board, search, category / chain / watchlist filters, a detail view for every protocol with the full model breakdown, and a separate live-token view for protocols that already trade.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path-to-snapshot.json>
```

`node main.ts` needs Node.js 22.18 or later, standard library only, and reads the sealed snapshot (`/input/snapshot.json` in the grader, or `--input <snapshot.json|directory>` locally). It writes `out/board.json`, `out/data.js`, `out/dashboard/`, `out/screenshots/` and `out/dossier.json`, and exits non-zero unless `screenshots/` holds the 3 screenshots (board, detail view, filtered view). Two runs on the same snapshot write byte-identical `board.json` and `data.js`.

## Model (radar-2)

```
implied = round( tvl_usd x category_multiple
  x (1 + clamp(change_7d, -60, 120) / 100 x 0.5)
  x (0.6 + min(age_days, 1095) / 1095 x 0.4)
  x breadth x risk )
```

- `category_multiple`: median mcap/TVL of live-token protocols in the same category, shrunk toward the global median (0.25 here), clamped to 0.05..1.50.
- `breadth`: +3% per extra chain, up to four.
- `risk`: a discount only — 0.92 when 7-day TVL fell more than 20% (0.85 past -40%), 0.95 when the protocol is under 90 days old, 0.97 when $1B+ sits on a single chain.

Implied value is a model, not a promise: it says what a token would be worth if the protocol's record stayed as it is, not whether or when a token is issued.

---

<sub>Entered on [make.fun](https://make.fun/?ref=z55ph46d&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
