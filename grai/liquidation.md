# Liquidation

While the fund is **GRINDING**, there is **no live redeem** to the protocol. Holders exit via secondary market, `unlock`, `bribe`, or **liquidation**.

## Regimes

| Regime | Meaning |
| ------ | ------- |
| **GRINDING** | Normal operation; deposits open (unless paused) |
| **REDEMPTION** | Liquidation open; redeem window active |

## Opening liquidation (2-of-2)

`liquidate()` requires **both**:

1. **GRAI** — vote quorum (`hasQuorum()`).
2. **Grinders** — heartbeat is stale (`!grinders.grinding()`), i.e. no operational activity for `grindingPeriod`.

Voters alone cannot force a sweep while Grinders is still actively operating.
Heartbeat is refreshed by operational calls (`allocate`, `deallocate`, `distribute`) and by GRAI via `heartbeat()` on `revive`.

Opening sets regime to **REDEMPTION** before custodian sweeps so nested `Grinders.liquidate` can require an open GRAI liquidation.

Hard sweep failure **rolls back** the regime change.

## Redeem window

After `liquidationPeriod`, holders **`redeem`**:

- Burns wallet and/or locked GRAI.
- Pro-rata share of `_redeemable` balances on GRAI (excludes dividend reserves).
- Grinders sweeps return custodian assets to GRAI first.

## Revive

After `liquidationPeriod + redeemPeriod`, anyone may call **`revive`**:

- Sends leftover redeemable balances to Grinders.
- Clears liquidation and returns to **GRINDING**.
- Does **not** reprice `totalValue` from leftover NAV (keeps ~$1/GRAI mint semantics when supply > 0).

Unclaimed dividend reserve stays on GRAI.

## Dead GRAI

Unlock penalties and orphan escrow sit as `balanceOf(GRAI) − totalLocked`. The **liquidation opener** scoops this dead inventory on open.

## Timeline (conceptual)

```
GRINDING ──vote quorum + stale Grinders heartbeat──► REDEMPTION
    ▲                                              │
    │                                              │ liquidationPeriod
    │                                              ▼
    └──────── revive (after + redeemPeriod) ◄── redeem window
```
