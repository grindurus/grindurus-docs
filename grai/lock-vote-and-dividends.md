# Lock, vote, and dividends

## Lock

`lock(graiAmount)` moves wallet GRAI into escrow on the GRAI contract.

- **Wallet GRAI earns no dividends** — only locked GRAI counts.
- New locks sync dividend debt so past index accrual is not diluted.

### Unlock

`unlock(graiAmount)` returns net GRAI to the wallet.

- Flat penalty (`unlockPenaltyBps`, default **1%**) is sent to **Grinders** (not Treasury, not left dead on GRAI).
- `previewUnlock` returns `(net, penalty)`.
- While penalty > 0, tiny unlock amounts below the dust floor revert.

## Vote

`vote(graiAmount)` adds to **liquidation quorum**.

- If wallet + locked balance is short, `vote` **auto-locks** from the wallet.
- **Voted GRAI leaves the dividend base** — only `locked − voted` earns payouts.
- Quorum: `totalVoted × BPS > totalSupply × quorumBps` (strict).
- New deposits dilute quorum (supply in denominator).

## Dividends

When `distribute(asset, amount)` runs, the **dividend cut** (default 50%) indexes into per-asset `accShare` for eligible lockers:

```
eligible = totalLocked − totalVoted
```

Lockers call `claim` / `claimAll` to receive listed assets.

| Case | Dividend cut goes to |
| ---- | -------------------- |
| Normal | Unvoted lockers (via index) |
| No eligible base | Treasury |
| Index increment rounds to zero | Remainder to Treasury |

Claims work **during** open liquidation (pay only from `totalClaimable` reserve).

## Bribe

`bribe(voter, graiAmount)` lets anyone buy **voted** GRAI for `settlementAsset`.

Price is dynamic vs **half-quorum**:

- Premium above par when votes are scarce
- Discount when votes exceed half-quorum
- Briber receives the full `graiAmount` to wallet

Blocked while liquidation is open.

## Summary table

| State | Dividends | Quorum |
| ----- | --------- | ------ |
| Wallet | No | No |
| Locked, unvoted | Yes | No |
| Voted (≤ locked) | No | Yes |
