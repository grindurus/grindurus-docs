# GRAI overview

**GRAI** (*Grinders Artificial Index*) is a USD-denominated fund-share token (6 decimals on-chain).

Depositors mint GRAI at **book value**:

```
graiOut = depositValue × totalSupply / totalValue
```

On the first deposit (`totalValue == 0`), mint is 1:1 with USD value (1 GRAI ≈ $1 book).

## What GRAI is

- A **claim on the fund basket** after liquidation redeem — not a live redeem while the fund is **GRINDING**.
- Priced by **oracles** per listed asset (Chainlink, Pyth, or custom feeds).
- **Non-rebasing** share: yield does not auto-compound; lockers **claim** asset dividends.

## What GRAI is not

- Not GRS (fixed 1B equity token).
- Not a yield vault with instant withdraw — see [Liquidation](https://docs.grindurus.xyz/protocol/grai/liquidation) for the shutdown path.

## Core flows

| Flow | Who | Summary |
| ---- | --- | -------- |
| `deposit` | Anyone | Asset → Grinders; mint GRAI; optional lock |
| `lock` / `unlock` | Holder | Escrow GRAI; unlock pays flat penalty to Grinders |
| `vote` | Holder | Quorum toward liquidation; auto-locks shortfall |
| `claim` | Locker | Asset dividends on unvoted locked GRAI |
| `distribute` | Anyone | Report yield; split dividend / treasury |
| `bribe` | Anyone | Buy voted GRAI for `settlementAsset` |
| `liquidate` → `redeem` → `revive` | Holders / anyone | Shutdown (quorum + stale Grinders heartbeat), pro-rata basket, restart |

## Implementations

| Chain | Contract / program |
| ----- | ------------------- |
| EVM | `GRAI.sol` (UUPS) |
| Solana | `programs/grai` |

Spec: [GRAI mechanics](https://docs.grindurus.xyz/protocol/grai/mechanics)

## Next pages

- [Deposit and mint](https://docs.grindurus.xyz/protocol/grai/deposit-and-mint)
- [Lock, vote, dividends](https://docs.grindurus.xyz/protocol/grai/lock-vote-and-dividends)
- [Liquidation](https://docs.grindurus.xyz/protocol/grai/liquidation)
- [Treasury and affiliates](https://docs.grindurus.xyz/protocol/grai/treasury-and-affiliates)
