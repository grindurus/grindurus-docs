# Protocol overview

## Core

This section is the **tip of the iceberg**: enough to see what GrindURUS *is* and how the off-chain stack is wired. The deep layer — full positional algebra, ledger invariants, and GrindURUS operation set — stays with **URUS** and will be published after the PhD work lands in 2027.

The core is **extended asset accounting through a ledger**.

A blockchain already keeps an asset ledger — balances, transfers, custody. The protocol applies the same idea to **pair `(asset, related price)`**, which generates **positional algebra**.

**URUS** is the code name for that positional algebra — **Ubiquitous Resource for Utilities and Securities** (developed by Vakhtanh Chikhladze, Founder).

## Strategy

**GrindURUS** is the strategy that has a composition of many URUS operations and derived data structures. (Full spec after PhD.)

### Modes

| Mode | Action | Result |
| ---- | ------ | ------ |
| **DIRECT** | Buy dips, sell rallies | Quote asset grows (e.g. more USDC) |
| **INVERSE** | Sell peaks, rebuy dips | Base asset grows (e.g. more ETH) |

Both modes run on the **same pair** inside one **Grinder**. Thresholds and loop timing are configurable.

### Grinder

A **Grinder** is the runtime unit:

- Initializes an **adapter** (terminal connection).
- Runs **DIRECT** and **INVERSE** `GrindURUS` strategy instances.
- Exposes an HTTP API (prices, balances, config, grind loop).
- Persists state under a UUID data directory.

**Boss** spawns Grinder containers, proxies requests, and tracks health. In dev, source under `grindurus/` is bind-mounted with hot reload.

### Where yield goes

Grinders in production connect to **custodian wallets** on-chain (Grinders NFTs). Reported profit flows:

```
Custodian → Grinders.distribute → GRAI.distribute → dividends + Treasury
```

Off-chain backtests use historical klines without touching GRAI.

## Infrastructure

The **[grindurus-protocol]** repo runs the off-chain stack.

### Layout

```
grindurus-protocol/
├── grindurus/               # Grinder, GrindURUS, GrindLedger
│   ├── core/                # Adapter, Ledger, Mode, URUS
│   └── adapters/            # Terminal plugins
│       ├── binance/         # CCXT (ccxt:binance)
│       ├── cow/             # CoW Protocol (eip155:cow)
│       ├── jupiter/         # Jupiter (solana:jupiter)
│       ├── lifi_solana/     # LiFi routing (solana_lifi)
│       ├── lifi_solana_intents/  # LiFi Intents [WIP]
│       ├── lifi/            # LiFi EVM [WIP]
│       └── velora/          # ParaSwap Delta [WIP]
├── docker/                  # Boss, Grinder images, Traefik
├── frontend/                # Boss UI (Vite + React)
├── analytics/               # Notebooks
└── test/                    # Backtests
```

Grinder **discovers** adapters automatically: each subpackage under `grindurus/adapters/` registers an `Adapter` subclass and its terminal name.

### Terminals

| Adapter folder | Terminal id | Status |
| -------------- | ----------- | ------ |
| `binance` | `ccxt:binance` | ready |
| `cow` | `eip155:cow` | ready |
| `jupiter` | `solana:jupiter` | routing |
| `lifi_solana` | `solana_lifi` | routing |
| `lifi_solana_intents` | `solana_lifi_intents` | WIP |
| `lifi` | — | WIP |
| `velora` | — | WIP |

### Boss and Grinder

| Service | Port (default) | Role |
| ------- | -------------- | ---- |
| **Boss** | 8000 | Create/stop grinders, proxy API, SSE streams |
| **Grinder** | 8001 | Strategy loop + adapter |

Each grinder is reachable at `grinder-<id>.<boss>.localhost` behind Traefik in local Docker.

### Backtest

Historical runs live under `grindurus-protocol/test/`:

- `backtest_direct.py` / `backtest_inverse.py` — single-mode URUS on klines
- `backtest_grinder.py` — full Grinder + backtest adapter

The public app exposes a paid backtest calculator at [app.grindurus.xyz/backtest](https://app.grindurus.xyz/backtest).

### Ecosystem services

| Repo | Role |
| ---- | ---- |
| `grindurus-ecosystem/grindurus-gateway` | Edge reverse proxy |
| `grindurus-ecosystem/grindurus-klines-service` | Candle data |
| `grindurus-ecosystem/grindurus-backtest-service` | Backtest API |

On-chain fund and token detail: [General overview](../general/overview.md) · [GRAI](../grai/overview.md) · [Grinders](../grinders/overview.md).
