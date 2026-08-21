# Mechanics

Canonical on-chain specs derived from EVM implementations. Solana programs mirror the same behavior unless noted.

| Spec | Contract / program |
| ---- | ------------------- |
| [GRAI](https://docs.grindurus.xyz/developers/mechanics/grai) | `GRAI.sol`, `Treasury.sol` |
| [Grinders](https://docs.grindurus.xyz/developers/mechanics/grinders) | `Grinders.sol`, custodian proxies |
| [GRS](https://docs.grindurus.xyz/developers/mechanics/grs) | `GRS.sol`, `programs/grs` |

## Diagrams

| Asset | Description |
| ----- | ----------- |
| [protocol.svg](protocol.svg) / [protocol.png](protocol.png) | Locker, voter, briber, referrer, poacher |
| [grs.svg](grs.svg) | Cap table groups |
| [grs-vesting.svg](grs-vesting.svg) | Vesting schedule |
| [bribe-amount-vs-voted.svg](bribe-amount-vs-voted.svg) | Bribe ask vs voted share |

Source code: [grindurus-evm](https://github.com/grindurus/grindurus-evm) · [grindurus-solana](https://github.com/grindurus/grindurus-solana)
