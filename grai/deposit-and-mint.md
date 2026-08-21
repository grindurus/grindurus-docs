# Deposit and mint

## `deposit(asset, amount, lock?, referrer)`

1. Pull `amount` of `asset` from the caller (native ETH = `address(0)` on EVM).
2. Credit **actually received** amount (fee-on-transfer safe).
3. Compute USD value via oracle: `usdValue(asset, received)`.
4. Mint GRAI to the depositor (or escrow if `lock = true` in the same call).
5. Increase `totalValue` by deposit USD.
6. Forward assets to **Grinders** reserve.

**No listed feed → no mint.** Paused feeds block **deposit only** (not claim or distribute).

## Referrer (first deposit only)

On the first mint for a locker, `referrer` binds permanently in Treasury:

| `referrer` | Effect |
| ---------- | ------ |
| `0` / self | Locker roots on itself |
| Another address | That address becomes upline for affiliate books |

See [Treasury and affiliates](https://docs.grindurus.xyz/grai/treasury-and-affiliates).

## Oracle listing

Owner lists assets with `setFeed`:

| Feed type | Source |
| --------- | ------ |
| Chainlink | Aggregator address |
| Pyth | Pyth contract + price id |
| Custom | View returning `(price, decimals, updatedAt)` |

While listed and **not** paused, only the pause flag may change. To replace: pause → rewrite feed → unpause.

## Previews

- `previewDeposit(asset, amount)` → USD value + expected `graiOut`
- Solana: `deposit` / `deposit_sol` with optional lock flag

## Blocked when

- Liquidation is open (`REDEMPTION` regime).
- Asset feed is paused.
- GRAI itself cannot be listed as collateral.
