# Protocol overview

GrindURUS turns pair volatility into yield. Architecturally the protocol is two modules:

| Module | Where | Job |
| ------ | ----- | --- |
| **GRAI** | **On-chain** | Store depositor capital as fund shares and **distribute** money (dividends, treasury cut, liquidation redeem). Accounting, locks, votes, bribes, claims — all on the GRAI contract. |
| **Grinders** | **Off-chain** (+ thin on-chain custody) | Make the **complex decisions** about how the fund’s money is managed: which pair, which venue, when to allocate / trade / take profit. Strategy lives off-chain; on-chain Grinders only holds reserve + custodian wallets that the bots drive. |

In short: **GRAI holds and pays out; Grinders decides and trades.**

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

### GRAI — on-chain fund share & money rails

**What:** Elastic USD book-priced token. Deposit listed assets → mint GRAI; lock for dividends; vote toward liquidation; claim yield; redeem in liquidation.

**Why it is here:** The **on-chain module for storing and distributing money**. Passiveors get a claim on the fund without running strategies. Book price (`totalValue`) is the fair mint/redeem anchor; every payout path (dividends, treasury, redeem) is settled by the GRAI contract.

→ [GRAI overview](grai/overview.md) · [mechanics](grai/mechanics.md)

### Grinders strategy — fund management brain

**What:** Grinder processes + adapters. Not only DIRECT / INVERSE trading — also DeFi **storage** (e.g. Aave) and **swaps / intents** (CoW, Jupiter, LiFi, CEX) against the linked custodian.

**Why it is here:** The module that decides how the fund’s money is managed — grind, park, route — with URUS algebra underneath. Latency and terminal APIs do not belong on-chain. On-chain only sees allocations and reported yield — not every fill.

→ [Strategy](grinders/strategy.md)

### Grinders (on-chain) — custody wallets for those decisions

**What:** Reserve that receives deposits, plus **GRINDERS NFTs** — one proxy wallet per custodian that holds allocated capital and can trade.

**Why it is here:** Thin on-chain shell so off-chain Grinders can move capital without rewriting GRAI’s book on every swap. Custodians hold inventory; GRAI book only updates on `deposit` / `distribute` / redeem / revive. NFT ownership is the keys to that wallet.

→ [Grinders overview](grinders/overview.md) · [mechanics](grinders/mechanics.md) · [strategy](grinders/strategy.md)

### Treasury — fee tree and affiliates

**What:** Companion of GRAI. Holds the treasury cut of `distribute`, pays L1/L2 referrers on `claim`, keeps a GRAI-TREASURY NFT tree; `poach` can buy an upline seat.

**Why it is here:** Growth distribution (affiliates) and protocol fee sink must not sit in the share token itself. Treasury isolates referral books and beneficiar routing from locker dividends.

→ [GRAI overview — Treasury](grai/overview.md#treasury-and-affiliates)

---

## How they fit

| Question | Component |
| -------- | --------- |
| Where is money stored / paid out on-chain? | **GRAI** |
| Who decides how the fund trades? | **Grinders** (off-chain) |
| Where does trading capital sit on-chain? | Grinders reserve → custodian NFT |
| How do I earn without trading? | Lock GRAI → **claim** dividends from `distribute` |
| Who gets protocol fees / referrals? | **Treasury** (+ beneficiar) |
| How does the fund shut down? | GRAI **vote** + Grinders heartbeat stale → liquidate → redeem |

Cross-chain note: GRAI is local per chain (no fund-share bridge).

---

## Read next

| Path | Start here |
| ---- | ---------- |
| Deposit & earn | [GRAI overview](grai/overview.md) |
| Custody & bots | [Grinders](grinders/overview.md) → [strategy](grinders/strategy.md) |
| Product narrative | [General overview](../general/overview.md) |
