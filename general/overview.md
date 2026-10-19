# Overview

GrindURUS is **extended accounting** with an on-chain fund layer (**GRAI**). See [Protocol overview](../protocol/overview.md), [Grinders off-chain](../protocol/grinders/off-chain.md), [GRAI](../protocol/grai/overview.md), [Treasury](../protocol/grai/treasury-and-affiliates.md).

## Two layers

**Off-chain (protocol repo)** — trading bots called **Grinders**. Each Grinder runs **GrindURUS** strategy in two modes on one pair:

- **DIRECT** — buy low, sell high → grow the quote asset (e.g. USDC)
- **INVERSE** — sell high, buy low → grow the base asset (e.g. ETH)

Adapters connect to Binance, CoW Protocol, LiFi, Jupiter, and other terminals. **Boss** starts and monitors Grinder containers.

**On-chain (evm + solana repos)** — the fund:


| Contract / program | Token or NFT      | Role                         |
| ------------------ | ----------------- | ---------------------------- |
| GRAI               | GRAI (6 decimals) | USD book-priced share        |
| Grinders           | GRINDERS NFT      | Custodian wallets + reserve  |
| Treasury           | GRAI-TREASURY NFT | Affiliate tree + fee routing |




## Typical holder path

1. Deposit USDC (or another listed asset) into **GRAI**.
2. Receive GRAI at the current book (`totalValue`).
3. Optionally **lock** GRAI to earn asset dividends on the unvoted portion.
4. Custodians trade allocated capital; yield returns via `**distribute**`.
5. **Claim** dividends, or exit via secondary market / unlock / liquidation redeem.



## Architecture



### End-to-end flow

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
              └─ remainder ──► beneficiar (net profit)
```



### Protocol map

![GrindURUS protocol map: actors, contracts, and yield cuts](../protocol/protocol.png)

Source: [protocol.svg](../protocol/protocol.svg) · PNG: [protocol.png](../protocol/protocol.png).

GRAI actors: locker, voter, briber, referrer, poacher


| Actor                  | Role                                                               |
| ---------------------- | ------------------------------------------------------------------ |
| **Depositor / holder** | Mints GRAI; may lock in the same tx                                |
| **Locker**             | Escrowed GRAI; earns dividends on `locked − voted`                 |
| **Voter**              | Adds to liquidation quorum; **no** asset dividends on voted amount |
| **Briber**             | Buys out voted GRAI for `settlementAsset`                          |
| **Referrer**           | Upline seat in Treasury tree; paid on downstream claims            |
| **Poacher**            | Buys a locker’s upline link for GRAI (`poach`)                     |
| **Grinders owner**     | Allocates capital, registers custodians, arms liquidation          |




### Networks

The same logic is implemented on **EVM** (Solidity, UUPS where noted) and **Solana** (Anchor). Each chain runs its own GRAI fund (local NAV — no fund-share bridge).

### Repositories

See [Repositories](../developers/repositories.md) for the full map of `grindurus-offchain`, `grindurus-evm`, `grindurus-solana`, `grindurus-app`, and related trees.

## Tokenomics



### Yield split (default GRAI config)

When custodians report profit via `distribute`:


| Cut          | Default                | Destination                                |
| ------------ | ---------------------- | ------------------------------------------ |
| **Dividend** | 50% (`dividendCutBps`) | Unvoted locked GRAI → `claim` / `claimAll` |
| **Treasury** | 50% (`treasuryCutBps`) | Treasury contract                          |


If nobody qualifies for dividends (`totalLocked == totalVoted`), the dividend cut goes to Treasury instead.

### Revenue flow

Per **100%** of reported yield (illustrative; GRAI defaults above): **Dividends 50%** and **Treasury 50%** at `distribute`. On locker `claim`, the treasury cut splits into **Affiliates 5%** of gross (L1/L2 **80/20** → **4%** / **1%**) and **Gross Profit 45%** to `beneficiar` (net profit retained / ops).

```mermaid
---
config:
  sankey:
    showValues: true
    linkColor: gradient
    suffix: "%"
---
sankey-beta

Revenue 100%,Dividends 50%,50
Revenue 100%,Treasury 50%,50
Treasury 50%,Affiliates 5%,5
Treasury 50%,Gross Profit 45%,45
Affiliates 5%,L1 referrer 4%,4
Affiliates 5%,L2 referrer 1%,1
Gross Profit 45%,Net Profit 45%,45
```




### Treasury → affiliates → beneficiar

From the treasury cut:


| Slice             | Default                         | When                                          |
| ----------------- | ------------------------------- | --------------------------------------------- |
| **Revenue share** | 5% of yield (`revenueShareBps`) | Paid to L1/L2 referrers on locker `**claim`** |
| **Remainder**     | ~45% of gross yield             | `Treasury.beneficiar` — **net profit**        |


At launch `beneficiar` is typically `GRAI.owner()`.

### Role ladder (GRAI)

```
                        poacher
                           │
                        poach()
                           ▼
holder → lock() → locker (unvoted) → vote() → voter ← bribe() ← briber
                      │                    │
                 earns dividends      no dividends; quorum seat
```

- Wallet GRAI earns **nothing** until locked.
- Only **unvoted** locked GRAI earns asset dividends.
- **Vote** is the price of a liquidation seat.
- **Poacher** buys the locker’s upline seat (`poach`) for GRAI — rewrites the referrer tree, not the cashflow NFT.



### Invariants (short)

1. GRAI book (`totalValue`) moves on deposit / redeem, not on custodian trades until `distribute`.
2. `totalVoted ≤ totalLocked ≤ totalSupply`.
3. Liquidation open is **2-of-2**: vote quorum + Grinders `confirmed`.
