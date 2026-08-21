# GrindURUS strategy

**GrindURUS** is positional algebra applied to market making: profit from **range**, not from forecasting direction.

## Modes

| Mode | Action | Result |
| ---- | ------ | ------ |
| **DIRECT** | Buy dips, sell rallies | Quote asset grows (e.g. more USDC) |
| **INVERSE** | Sell peaks, rebuy dips | Base asset grows (e.g. more ETH) |

Both modes run on the **same pair** inside one **Grinder**. Thresholds and loop timing are configurable.

## Grinder

A **Grinder** is the runtime unit:

- Initializes an **adapter** (terminal connection).
- Runs **DIRECT** and **INVERSE** `GrindURUS` instances.
- Exposes an HTTP API (prices, balances, config, grind loop).
- Persists state under a UUID data directory.

**Boss** spawns Grinder containers, proxies requests, and tracks health. In dev, source under `grindurus/` and `adapters/` is bind-mounted with hot reload.

## URUS

**URUS** is the underlying practical positional framework (developed by Vakhtanh Chikhladze). GrindURUS is the trading strategy built on it

## Where yield goes

Grinders in production connect to **custodian wallets** on-chain (Grinders NFTs). Reported profit flows:

```
Custodian → Grinders.distribute → GRAI.distribute → dividends + Treasury
```

Off-chain backtests use historical klines (Binance adapter) without touching GRAI.

## Learn more

- [Trading infrastructure](https://docs.grindurus.xyz/protocol/trading-infrastructure) — adapters and terminals
- [Grinders overview](https://docs.grindurus.xyz/grinders/overview) — on-chain custodians
