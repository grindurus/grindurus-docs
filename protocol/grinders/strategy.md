# Strategy

On-chain Grinders hold capital in custodian NFTs ([overview](overview.md)). The **strategy layer** lives off-chain: a Grinder process decides *what* to do with that capital and drives adapters against terminals and DeFi protocols. It is **not only** buy-low / sell-high trading — it also parks inventory in money markets and moves size through swaps / intents wherever liquidity is.

Reported profit still flows the same way:

```
Custodian → Grinders.distribute → GRAI.distribute → dividends + Treasury
```

## What this layer does

| Job | Meaning |
| --- | ------- |
| **Grind (trade)** | DIRECT / INVERSE market taking on a pair — grow quote or base from realized volatility |
| **Store (DeFi)** | Park idle or working capital in lending / yield venues (e.g. Aave) when the loop says hold, not flip |
| **Swap / route** | Move between assets via CEX, AMM, CoW intents, Jupiter, LiFi, … — same math, different rails |

One **Grinder** binds a custodian wallet to an **adapter**. The adapter is the plug for that venue or protocol. On-chain custodian kinds (`path`, `swap`, `cow`, `lifi`, `aave_v3`, …) are the wallets those adapters drive.

```
                    ┌─────────────────────────────────────┐
                    │           Grinder (off-chain)         │
                    │  GrindURUS DIRECT + INVERSE + ledger  │
                    └──────────────┬──────────────────────┘
                                   │ adapter
           ┌───────────────────────┼───────────────────────┐
           ▼                       ▼                       ▼
     Trade terminal          Swap / intent              DeFi store
     (CEX klines/API)        (CoW, Jupiter, LiFi)       (e.g. Aave)
           │                       │                       │
           └───────────────────────┴───────────────────────┘
                                   │
                                   ▼
                         Custodian NFT wallet
                         (on-chain Grinders)
```

## Core

This section is the **tip of the iceberg**: enough to see what GrindURUS *is* and how the stack is wired. The deep layer — full positional algebra, ledger invariants, and GrindURUS operation set — stays with **URUS** and will be published after the PhD work lands in 2027.

The core is **extended asset accounting through a ledger**.

A blockchain already keeps an asset ledger — balances, transfers, custody. The protocol applies the same idea to **pair `(asset, related price)`**, which generates **positional algebra**.

**URUS** is the code name for that positional algebra — **Ubiquitous Resource for Utilities and Securities** (developed by Vakhtanh Chikhladze, Founder).

**GrindURUS** is the strategy composition of many URUS operations and derived data structures. (Full spec after PhD.) It decides when to grind, when to store, and when to swap — not a single-venue bot script.

### Modes (grind)

| Mode | Action | Result |
| ---- | ------ | ------ |
| **DIRECT** | Buy dips, sell rallies | Quote asset grows (e.g. more USDC) |
| **INVERSE** | Sell peaks, rebuy dips | Base asset grows (e.g. more ETH) |

Both modes run on the **same pair** inside one **Grinder**. Thresholds and loop timing are configurable.

### Store and swap (beyond the loop)

| Path | Examples | Role |
| ---- | -------- | ---- |
| **Storage** | Aave V3 custodian / lending adapters | Earn or park while not actively grinding the pair |
| **Swaps** | Path/Swap custodians, Binance, Jupiter | Spot inventory moves for the grind loop |
| **Intents / solvers** | CoW, LiFi intents | Fill without maintaining an open order book on every venue |

Same URUS book underneath; adapters only change *how* the fill or deposit lands on the custodian.

### Grinder

A **Grinder** is the runtime unit:

- Initializes an **adapter** (terminal or DeFi connection).
- Runs **DIRECT** and **INVERSE** `GrindURUS` strategy instances.
- Can drive store / swap flows through the same adapter surface where implemented.
- Exposes an HTTP API (prices, balances, config, grind loop).
- Persists state under a UUID data directory.

Backtests use historical klines without touching GRAI.

## Infrastructure

The **[grindurus-offchain](https://github.com/grindurus/grindurus-offchain)** repo runs this stack.

### Layout

```
grindurus-offchain/
├── grindurus/               # Grinder, GrindURUS, GrindLedger
│   ├── core/                # Adapter, Ledger, Mode, URUS
│   └── adapters/            # Terminal + DeFi plugins
│       ├── binance/         # CCXT (ccxt:binance) — trade
│       ├── cow/             # CoW Protocol (eip155:cow) — intents / swap
│       ├── jupiter/         # Jupiter (solana:jupiter) — swap
│       ├── lifi_solana/     # LiFi routing (solana_lifi) — swap / bridge
│       ├── lifi_solana_intents/  # LiFi Intents [WIP]
│       ├── lifi/            # LiFi EVM [WIP]
│       └── velora/          # ParaSwap Delta [WIP]
├── docker/                  # Grinder images, Traefik
├── frontend/                # Ops UI (Vite + React)
├── analytics/               # Notebooks
└── test/                    # Backtests
```

On-chain counterparts for store/swap wallets live under Grinders custodian kinds (e.g. `AaveV3Custodian`, `PathCustodian`, `CoWCustodian`) — see [Grinders overview — Trade](overview.md#4-trade).

Grinder **discovers** adapters automatically: each subpackage under `grindurus/adapters/` registers an `Adapter` subclass and its terminal name.

### Terminals and protocols

| Adapter folder | Terminal id | Job | Status |
| -------------- | ----------- | --- | ------ |
| `binance` | `ccxt:binance` | Trade (CEX) | ready |
| `cow` | `eip155:cow` | Swap / intents | ready |
| `jupiter` | `solana:jupiter` | Swap | routing |
| `lifi_solana` | `solana_lifi` | Swap / route | routing |
| `lifi_solana_intents` | `solana_lifi_intents` | Intents | WIP |
| `lifi` | — | Swap / route (EVM) | WIP |
| `velora` | — | Swap | WIP |

Storage venues (e.g. Aave) are primarily wired through on-chain custodian kinds today; adapters that drive them sit in the same plugin model as they land.

### Grinder process

| Service | Port (default) | Role |
| ------- | -------------- | ---- |
| **Grinder** | 8001 | Strategy loop + adapter (HTTP API) |

### Backtest

Historical runs live under `grindurus-offchain/test/`:

- `backtest_direct.py` / `backtest_inverse.py` — single-mode URUS on klines
- `backtest_grinder.py` — full Grinder + backtest adapter

The public app exposes a paid backtest calculator at [grindurus.xyz/backtest](https://grindurus.xyz/backtest).

### Ecosystem services

| Repo | Role |
| ---- | ---- |
| `gateway` | Edge reverse proxy |
| `grindurus-backtest-service` | Backtest API + OHLCV/klines (`klines/`) |

---

## Read next

- [Grinders overview](overview.md) — on-chain reserve, allocate, custodians
- [Grinders mechanics](mechanics.md) — EVM entrypoints
- [GRAI overview](../grai/overview.md) — shares and payouts
- [General overview](../../general/overview.md) — manifesto → product
