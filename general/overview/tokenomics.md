# Tokenomics

This page is the high-level economics map. Detail lives under [GRAI](../../grai/overview.md), [GRS](../../grs/overview.md), and [Treasury / affiliates](../../grai/treasury-and-affiliates.md).

## Yield split (default GRAI config)

When custodians report profit via `distribute`:

| Cut | Default | Destination |
| --- | ------- | ----------- |
| **Dividend** | 50% (`dividendCutBps`) | Unvoted locked GRAI → `claim` / `claimAll` |
| **Treasury** | 50% (`treasuryCutBps`) | Treasury contract |

If nobody qualifies for dividends (`totalLocked == totalVoted`), the dividend cut goes to Treasury instead.

## Treasury → affiliates → beneficiar

From the treasury cut:

| Slice | Default | When |
| ----- | ------- | ---- |
| **Revenue share** | 5% of yield (`revenueShareBps`) | Paid to L1/L2 referrers on locker **`claim`** |
| **Remainder** | ~45% of gross yield | `Treasury.beneficiar` |

At launch `beneficiar` is typically `GRAI.owner()`. Target: **FeeVault** streaming to **GRS** stakers (`xGRS` / `veGRS`).

## Role ladder (GRAI)

```
holder → lock() → locker (unvoted) → vote() → voter ← bribe() ← briber
                      │                    │
                 earns dividends      no dividends; quorum seat
```

- Wallet GRAI earns **nothing** until locked.
- Only **unvoted** locked GRAI earns asset dividends.
- **Vote** is the price of a liquidation seat.

## GRS at a glance

- **1,000,000,000** fixed supply; single genesis on **home**.
- **15%** (150M) Token sales — public `buy` book, Instant gate.
- **20%** each: Investments, Affiliates, Team, Ecosystem, Foundation.
- Cap-table **`grant`** is `owner` on home; spokes revert `NotHome`.
- Votes target: home GRS + Governor + timelock controlling GRAI / Grinders / Treasury params.

Full bucket table: [GRS cap table](../../grs/cap-table.md).

## What GRS is not

- Not a deposit receipt (that is GRAI).
- Not a custodian operator license (Grinders NFT).
- Not the affiliate cashflow NFT (GRAI-TREASURY).
- Not minted from yield — fee surplus only.

## Invariants (short)

1. GRAI book (`totalValue`) moves on deposit / redeem, not on custodian trades until `distribute`.
2. Circulating GRS never exceeds 1B across home + spokes.
3. `totalVoted ≤ totalLocked ≤ totalSupply`.
4. Liquidation open is **2-of-2**: vote quorum + Grinders `confirmed`.
