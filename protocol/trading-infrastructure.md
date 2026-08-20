# Trading infrastructure

The **grindurus-protocol** repo runs the off-chain stack.

## Layout

```
grindurus-protocol/
├── grindurus/          # Grinder, GrindURUS, Adapter, Ledger
├── adapters/           # Terminal plugins
│   ├── binance/        # CCXT (ccxt:binance)
│   ├── cow/            # CoW Protocol (eip155:cow)
│   ├── jupiter/        # Jupiter (solana:jupiter)
│   ├── lifi_solana/    # LiFi routing (solana_lifi)
│   ├── lifi_solana_intents/  # LiFi Intents [WIP]
│   └── velora/         # ParaSwap Delta [WIP]
├── docker/             # Boss, Grinder images, Traefik
└── frontend/           # Boss UI (Vite + React)
```

Grinder **discovers** adapters automatically: each subpackage under `adapters/` registers an `Adapter` subclass and its terminal name.

## Terminals

| Adapter folder | Terminal id | Status |
| -------------- | ----------- | ------ |
| `binance` | `ccxt:binance` | ready |
| `cow` | `eip155:cow` | ready |
| `jupiter` | `solana:jupiter` | routing |
| `lifi_solana` | `solana_lifi` | routing |
| `lifi_solana_intents` | `solana_lifi_intents` | WIP |
| `velora` | — | WIP |

## Boss and Grinder

| Service | Port (default) | Role |
| ------- | -------------- | ---- |
| **Boss** | 8000 | Create/stop grinders, proxy API, SSE streams |
| **Grinder** | 8001 | Strategy loop + adapter |

Each grinder is reachable at `grinder-<id>.<boss>.localhost` behind Traefik in local Docker.

## Backtest

Historical runs live under `grindurus-protocol/test/`:

- `backtest_direct.py` / `backtest_inverse.py` — single-mode URUS on klines
- `backtest_grinder.py` — full Grinder + backtest adapter

The public app exposes a paid backtest calculator at [app.grindurus.xyz/backtest](https://app.grindurus.xyz/backtest).

## Ecosystem services

| Repo | Role |
| ---- | ---- |
| `grindurus-ecosystem/grindurus-gateway` | Edge reverse proxy |
| `grindurus-ecosystem/grindurus-klines-service` | Candle data |
| `grindurus-ecosystem/grindurus-backtest-service` | Backtest API |

## Further detail

Full detail will be published after the infrastructure audit.
