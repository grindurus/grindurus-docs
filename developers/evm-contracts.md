# EVM contracts

Primary tree: [grindurus-evm](https://github.com/grindurus/grindurus-evm)

## Core contracts

| Contract | Upgrade | Role |
| -------- | ------- | ---- |
| `GRAI.sol` | UUPS | Fund share, oracle router, lock/vote/bribe/liquidation |
| `Grinders.sol` | UUPS | NFT registry, reserve, allocate/deallocate |
| `Treasury.sol` | — | Referrals, poach, beneficiar, cashflow NFTs |
| `GRS.sol` | **None** | LayerZero OFT, cap table, sales, vest |
| `Custodian.sol` | Per-wallet proxy (no upgrade) | Base custodian wallet |

Custodian kinds live under `src/custodians/` (Swap, CoW, LiFi, …).

## Deploy

CREATE3 scripts in `script/`:

| Script | Purpose |
| ------ | ------- |
| `Deploy.s.sol` | GRAI + Grinders |
| `5_DeployGRS.s.sol` | GRS OFT (`HOME=true/false`) |

GRS env: `HOME`, `lzEndpoint`, `delegate`.

## Tests

```bash
cd grindurus-evm
forge test
forge test --match-path "test/GRS*.t.sol"
```

Key integration: `test/GRAILifecycle.t.sol`, `test/TreasuryReferrals.t.sol`, `test/TreasuryPoach.t.sol`.

## Access control

- **Ownable2Step** on GRAI and Grinders.
- GRAI: `renounceOwnership` disabled.
- Production: multisig / timelock on both; GRS `proprietor` → gov timelock after migration.

## Docs in repo

Mechanics specs live under Protocol: [GRAI](https://docs.grindurus.xyz/protocol/grai/mechanics), [GRS](https://docs.grindurus.xyz/protocol/grs/mechanics), [Grinders](https://docs.grindurus.xyz/protocol/grinders/mechanics) (+ diagrams `protocol.svg`, `grs.svg`, …).
