# Architecture

## End-to-end flow

```
Depositor
    │ deposit(asset)
    ▼
  GRAI ──mint at book──► GRAI tokens
    │ assets
    ▼
 Grinders reserve
    │ allocate (owner)
    ▼
 Custodian NFT wallet ──trade──► yield on wallet
    │ distribute (owner reports profit)
    ▼
  GRAI.distribute
    ├─ dividendCut (~50%) ──► unvoted lockers (claim)
    └─ treasuryCut (~50%) ──► Treasury
              ├─ revenueShare (5% of yield) ──► affiliates on claim
              └─ remainder ──► beneficiar (target: GRS stakers)
```

## Protocol map

![GRAI actors: locker, voter, briber, referrer, poacher](../developers/mechanics/protocol.png)

| Actor | Role |
| ----- | ---- |
| **Depositor / holder** | Mints GRAI; may lock in the same tx |
| **Locker** | Escrowed GRAI; earns dividends on `locked − voted` |
| **Voter** | Adds to liquidation quorum; **no** asset dividends on voted amount |
| **Briber** | Buys out voted GRAI for `settlementAsset` |
| **Referrer** | Upline seat in Treasury tree; paid on downstream claims |
| **Poacher** | Buys a locker’s upline link for GRAI (`poach`) |
| **Grinders owner** | Allocates capital, registers custodians, arms liquidation |

## Networks

The same logic is implemented on **EVM** (Solidity, UUPS where noted) and **Solana** (Anchor). GRS uses **LayerZero OFT** for cross-chain representation: one **home** chain mints 1B; **spokes** bridge 1:1.

## Repositories

See [Repositories](../developers/repositories.md) for the full map of `grindurus-protocol`, `grindurus-evm`, `grindurus-solana`, `grindurus-app`, and related trees.
