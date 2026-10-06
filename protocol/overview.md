# Protocol overview

GrindURUS turns pair volatility into yield. The **protocol** is the on-chain fund + equity layer that holds capital, pays depositors, and routes fees — plus the off-chain Grinders that actually trade.

```
depositors ──► GRAI ──► Grinders reserve ──► custodian NFTs ──► off-chain Grinder
                 │              │                                      │
                 │              └── allocate / deallocate               │
                 │                                                     │
                 ◄────────────── distribute(yield) ◄───────────────────┘
                 │
                 ├── dividends → lockers (claim)
                 └── treasury cut → Treasury → affiliates + beneficiar
                                                      │
                                                      └── (ops) GRS buyback → TokenSales
```

Map: [protocol.svg](protocol.svg) · [protocol.png](protocol.png)

---

## Components

### GRAI — fund share

**What:** Elastic USD book-priced token. Deposit listed assets → mint GRAI; lock for dividends; vote toward liquidation.

**Why it is here:** Separates *passive capital* from trading ops. Depositors get a claim on the fund without running strategies. Book price (`totalValue`) is the fair mint/redeem anchor; secondary GRAI/ETH is optional.

→ [GRAI overview](grai/overview.md) · [mechanics](grai/mechanics.md)

### Grinders (on-chain) — custody vault

**What:** Reserve that receives deposits, plus **GRINDERS NFTs** — one proxy wallet per custodian that holds allocated capital and can trade.

**Why it is here:** Capital must live somewhere tradable without mixing into GRAI’s accounting every swap. Custodians move inventory; GRAI book only updates on `deposit` / `distribute` / redeem / revive. NFT ownership is the keys to that wallet.

→ [Grinders overview](grinders/overview.md) · [mechanics](grinders/mechanics.md)

### Grinders (off-chain) — trading runtime

**What:** Boss + Grinder containers + adapters (Binance, CoW, Jupiter, LiFi, …). Each process runs GrindURUS **DIRECT** / **INVERSE** on a pair and pushes profit into the linked custodian.

**Why it is here:** Strategy and terminal connectivity are off-chain by design (latency, APIs, PhD-depth URUS algebra). On-chain only sees allocations and reported yield — not every fill.

→ [Off-chain](grinders/off-chain.md)

### Treasury — fee tree and affiliates

**What:** Companion of GRAI. Holds the treasury cut of `distribute`, pays L1/L2 referrers on `claim`, keeps a GRAI-TREASURY NFT tree; `poach` can buy an upline seat.

**Why it is here:** Growth distribution (affiliates) and protocol fee sink must not sit in the share token itself. Treasury isolates referral books and beneficiar routing from locker dividends.

→ [Treasury and affiliates](grai/treasury-and-affiliates.md)

### GRS — protocol equity

**What:** Fixed **1B** supply, home genesis + LayerZero OFT spokes. Cap-table `grant` / vesting, public `sale` / `buy`, bridge. Not a second fund share.

**Why it is here:** Governance and value accrual for the *protocol* (ownership, fee destiny, TokenSales / buyback recycle), independent of elastic GRAI NAV. Bridgable so equity stays one float across chains while each chain keeps its own GRAI fund.

→ [GRS overview](grs/overview.md) · [cap table](grs/cap-table.md) · [token sales](grs/token-sales.md) · [bridge](grs/bridge.md) · [mechanics](grs/mechanics.md)

---

## How they fit

| Question | Component |
| -------- | --------- |
| Where do I put capital? | **GRAI** `deposit` |
| Who holds and trades it? | **Grinders** reserve → custodian NFT ← **off-chain** Grinder |
| How do I earn without trading? | Lock GRAI → **claim** dividends from `distribute` |
| Who gets protocol fees / referrals? | **Treasury** (+ beneficiar / buybacks) |
| What is protocol ownership? | **GRS** (not GRAI) |
| How does the fund shut down? | GRAI **vote** + Grinders heartbeat stale → liquidate → redeem |

**GRAI ≠ GRS.** GRAI scales with deposits; GRS does not. Yield accrues to locked GRAI and Treasury; buybacks may recycle GRS into TokenSales — they do not mint new equity.

Cross-chain note: GRS bridges 1:1; GRAI is local per chain. How the mesh works: [Cross-chain markets](cross-chain-pricing.md).

---

## Read next

| Path | Start here |
| ---- | ---------- |
| Deposit & earn | [GRAI](grai/overview.md) → [lock / dividends](grai/lock-vote-and-dividends.md) |
| Custody & bots | [Grinders](grinders/overview.md) → [off-chain](grinders/off-chain.md) |
| Equity & raise | [GRS](grs/overview.md) → [token sales](grs/token-sales.md) |
| Product narrative | [General overview](../general/overview.md) |
