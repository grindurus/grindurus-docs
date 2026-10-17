# Deposit and mint

## `deposit(asset, amount, lock?, referrer)`

1. Pull `amount` of `asset` from the caller (native ETH = `address(0)` on EVM).
2. Credit **actually received** amount (fee-on-transfer safe).
3. Compute USD value via oracle: `usdValue(asset, received)`.
4. Mint GRAI via `previewDeposit` (book × mint-premium ask — see below).
5. Increase `totalValue` by the **full** deposit USD (`value`), even when the ask mints fewer shares.
6. Forward assets to **Grinders** reserve.

**No listed feed → no mint.** Paused feeds block **deposit only** (not claim or distribute).

## Book mint

Without premium (or in year 0), shares are minted at **book**:

```
book = value × totalSupply / totalValue     # or book = value when totalValue == 0
graiOut = book
```

First deposit (`totalValue == 0`) is 1:1 with USD value (1 GRAI ≈ $1 book). Yield inventory sitting on GRAI is **not** in `totalValue`.

## Mint premium (ask above book)

Over time the **mint ask** compounds so new depositors pay above book — existing shares become more expensive to dilute.

| Knob | Role |
| ---- | ---- |
| `mintPremiumAt` | Clock start (set at `initialize` / migrate). `0` disables premium. |
| `config.mintPremiumBpsPerYear` | Annual compound rate in bps. Default **500** = **+5%/year**. `0` = always par. |

Full years since the clock:

```
n = floor((now − mintPremiumAt) / 365 days)   # capped at 100
```

- **`n == 0`** (or rate/clock off) → mint at book (par).
- **`n ≥ 1`** → compound once per full year:

```
priceBps = 10_000 × (10_000 + rate)^n / 10_000^n
graiOut  = book × 10_000 / priceBps
```

Example at default **5%/year**:

| Years elapsed | `priceBps` | Mint vs book |
| ------------- | ---------- | ------------ |
| 0 | 10_000 | 100% (par) |
| 1 | 10_500 | ~95.24% of book |
| 2 | 11_025 | ~90.70% of book |

So after one full year, $100 of deposit still adds **$100** to `totalValue`, but mints only ~**95.24** GRAI instead of 100. The gap accrues to existing holders via a higher book per share.

Retarget rate with `setConfig(MINT_PREMIUM, rateBps)` (owner, while `GRINDING`). Setting rate to `0` restores book mint without clearing the clock.

## Referrer (first deposit only)

On the first mint for a locker, `referrer` binds permanently in Treasury:

| `referrer` | Effect |
| ---------- | ------ |
| `0` / self | Locker roots on itself |
| Another address | That address becomes upline for affiliate books |

See [Treasury and affiliates](https://docs.grindurus.xyz/protocol/grai/treasury-and-affiliates).

## Oracle listing

Owner lists assets with `setFeed`:

| Feed type | Source |
| --------- | ------ |
| Chainlink | Aggregator address |
| Pyth | Pyth contract + price id |
| Custom | View returning `(price, decimals, updatedAt)` |

While listed and **not** paused, only the pause flag may change. To replace: pause → rewrite feed → unpause.

## Previews

- `previewDeposit(asset, amount)` → USD `value` + expected `graiOut` (includes premium)
- Solana: `deposit` / `deposit_sol` with optional lock flag

## Blocked when

- Liquidation is open (`REDEMPTION` regime).
- Asset feed is paused.
- GRAI itself cannot be listed as collateral.
