# Protocol overview

GrindURUS turns pair volatility into yield. The **protocol** is the on-chain fund layer that holds capital, pays depositors, and routes fees — plus the off-chain Grinders that actually trade.

```
depositors ──► GRAI ──► Grinders reserve ──► custodian NFTs ──► off-chain Grinder
                 │              │                                      │
                 │              └── allocate / deallocate               │
                 │                                                     │
                 ◄────────────── distribute(yield) ◄───────────────────┘
                 │
                 ├── dividends → lockers (claim)
                 └── treasury cut → Treasury → affiliates + beneficiar
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

---

## How they fit

| Question | Component |
| -------- | --------- |
| Where do I put capital? | **GRAI** `deposit` |
| Who holds and trades it? | **Grinders** reserve → custodian NFT ← **off-chain** Grinder |
| How do I earn without trading? | Lock GRAI → **claim** dividends from `distribute` |
| Who gets protocol fees / referrals? | **Treasury** (+ beneficiar) |
| How does the fund shut down? | GRAI **vote** + Grinders heartbeat stale → liquidate → redeem |

Cross-chain note: GRAI is local per chain (no fund-share bridge).

---

## Read next

| Path | Start here |
| ---- | ---------- |
| Deposit & earn | [GRAI](grai/overview.md) → [lock / dividends](grai/lock-vote-and-dividends.md) |
| Custody & bots | [Grinders](grinders/overview.md) → [off-chain](grinders/off-chain.md) |
| Product narrative | [General overview](../general/overview.md) |
