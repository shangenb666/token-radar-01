# token-radar-01

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/shangenb666/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/shangenb666/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar that ranks 226 pre-token protocols by a comparable-multiples implied value: each protocol's TVL is priced off the median mcap/TVL of the live-token protocols in its own category, adjusted for seven-day growth, age with no token and chain breadth. The dashboard is a filterable board with a top-eight spotlight, a per-protocol detail view with the full value breakdown and a seven-step farm plan, a comparables table and a live model sandbox. No network, no framework: Node 22 and the sealed DefiLlama snapshot are all it needs.

The model, in one line: implied = TVL x comps x (1 + clamp(change_7d, -50, 150) / 100 x 0.6) x (0.6 + min(age_days, 1095) / 1095 x 0.4) x (1 + 0.05 x min(chains - 1, 4)), where comps is the category's median mcap/TVL among its live-token protocols, shrunk toward the global median and clamped to [0.05, 1.00].

## Run it

```sh
node main.ts
```

---

Entered on [make.fun](https://make.fun/?ref=xt6z1twn&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards
