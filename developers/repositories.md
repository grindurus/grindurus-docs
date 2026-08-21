# Repositories

Grindurus is a **multi-repo product** under [github.com/grindurus](https://github.com/grindurus).

| Repository | Contents |
| ---------- | -------- |
| **grindurus-protocol** | GrindURUS strategy, Grinder, Boss, `adapters/`, Docker, Boss UI |
| **grindurus-evm** | Solidity: GRAI, Grinders, Treasury, GRS, custodians |
| **grindurus-solana** | Anchor: grai, grinders, grs, lifi_custody |
| **grindurus-app** | Protocol app ([app.grindurus.xyz](https://app.grindurus.xyz)) |
| **grindurus-landing** | Marketing site ([grindurus.xyz](https://grindurus.xyz)) |
| **grindurus-docs** | This GitBook (docs.grindurus.xyz) |
| **grindurus-ecosystem** | Gateway, klines, backtest service |

Local workspace often clones all trees as siblings under one `grindurus/` folder.

## Spec source of truth

| Doc | Path |
| --- | ---- |
| GRAI mechanics | [developers/mechanics/GRAI.md](https://docs.grindurus.xyz/developers/mechanics/grai) |
| GRS mechanics | [developers/mechanics/GRS.md](https://docs.grindurus.xyz/developers/mechanics/grs) |
| Grinders mechanics | [developers/mechanics/GRINDERS.md](https://docs.grindurus.xyz/developers/mechanics/grinders) |
| Capability checklist | `FUNCTIONALITY.md` (monorepo root) |
| Manifesto | [general/manifesto.md](https://docs.grindurus.xyz/general/manifesto) |

## GitBook sync

1. Connect **grindurus-docs** repo in GitBook → GitHub integration.
2. Point custom domain to **docs.grindurus.xyz**.
3. Edit markdown here; GitBook rebuilds on push to the synced branch.

Structure files:

- `SUMMARY.md` — navigation
- `.gitbook.yaml` — root + redirects
- `README.md` — home page
