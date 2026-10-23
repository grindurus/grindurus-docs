# Grinders overview

**Grinders** is the on-chain custody shell for fund capital. It holds the **reserve**, mints **GRINDERS** custodian NFTs (one wallet each), and moves money in and out of those wallets. It does **not** pick trades or venues — that is off-chain. See [Protocol overview](../overview.md) · [Strategy](strategy.md). Full write-up: [Grinders mechanics](mechanics.md).

| Chain | Contract / program |
| ----- | ------------------- |
| EVM | `Grinders.sol` + `Custodian` kinds (UUPS) |
| Solana | Grinders / custody programs |

GRAI book (`totalValue`) does **not** move when custodians trade. Book moves on deposit / redeem / revive; yield enters GRAI only when someone **`distribute`**s it.

```
GRAI.deposit ──► Grinders reserve ──allocate──► Custodian NFT wallet
                                                   │ trade (off-chain / NFT owner)
                      ◄──deallocate──┘             │
                      ◄──distribute(yield)─────────┘ ──► GRAI.distribute
```

---

## 1. Reserve

Deposits land here. Idle assets sit on Grinders until a toreador allocates them to a custodian. Unlock fees and poach fees also arrive as **GRAI tokens** on Grinders — ordinary free GRAI, not yield and not part of the allocate trail.

```
Users ──deposit──► GRAI ──assets──► Grinders reserve
Users ──unlock──► GRAI ──penalty GRAI──► Grinders (free GRAI balance)
```

---

## 2. Mint and register

Owner registers a custodian **kind** (implementation), then `mint`s a proxy wallet + NFT, or `register`s a pre-deployed proxy. NFT owner = who may trade that wallet. Pair (`base` / `quote`) is set at mint or via `setAssets`.

```
Owner ──set(kind, impl)──► Grinders
Owner ──mint(kind, nftOwner, base, quote)──► Custodian proxy + GRINDERS NFT
```

---

## 3. Allocate

Toreador moves reserve inventory into a registered custodian. Emits `Allocate`. There is **no** on-chain allocate ledger — track net off-chain.

```
Reserve ──allocate(custodian, asset, amount)──► Custodian wallet
                                                      │
                                                      └── event Allocate
```

```
net ≈ Σ Allocate − Σ Deallocate   (per custodian, per asset)
```

---

## 4. Trade

NFT owner runs the kind-specific API (swap, CoW order, …). Balances of base / quote / ETH change on the wallet. Profit stays on the custodian until someone reports it.

| Kind (label family) | Contract | Trading |
| ------------------- | -------- | ------- |
| `…path` | `PathCustodian` | Router calldata + price limit |
| `…swap` | `SwapCustodian` | Swap path helper |
| `…cow` | `CoWCustodian` | CoW Protocol EIP-1271 |
| `…lifi` | `LiFiCustodian` | LiFi routing |
| `…aave_v3` | `AaveV3Custodian` | Aave V3 supply / borrow |

```
Custodian wallet ──trade──► new balances (still on wallet)
```

---

## 5. Distribute

Toreador reports **yield** from a custodian into GRAI. That refreshes the Grinders heartbeat and runs GRAI’s 50/50 cut (dividends / treasury). Mis-reporting principal as yield is a governance concern — there is no on-chain profit oracle.

```
Custodian ──Grinders.distribute──► GRAI.distribute
                                      ├── Dividends ~50%
                                      └── Treasury ~50%
```

---

## 6. Deallocate

Toreador pulls inventory from a custodian back to the reserve. Not capped by prior allocates — after swaps the token or size may differ. Blocked while GRAI liquidation is open.

```
Custodian ──deallocate──► Grinders reserve
                               │
                               └── event Deallocate
```

---

## 7. Heartbeat

Ops activity keeps Grinders “alive.” GRAI can open liquidation only when vote quorum is met **and** Grinders looks stale.

```
heartbeatAt = last allocate / deallocate / distribute / GRAI.heartbeat (revive)
grinding()  = now ≤ heartbeatAt + grindPeriod     // default 7 days
```

```
Alive (grinding)     ████████████░░░░  within grindPeriod
Stale (!grinding)    ░░░░░░░░░░░░░░░░  GRAI may open if also quorum
```

---

## 8. Liquidation sweeps

After GRAI opens **REDEMPTION**, anyone may page `Grinders.liquidate`. Custodian trading / distribute / deallocate are blocked. Keepers pull wallets and idle reserve onto GRAI for redeem.

```
GRAI.liquidate (quorum + !grinding)
        │
        ▼
Grinders.liquidate(fromId, toId)
        ├── fromId < toId  → page custodian wallets → GRAI
        └── fromId ≥ toId  → sweep idle listed balances on Grinders → GRAI
```

---

## Read next

- [Grinders mechanics](mechanics.md) — same sections, expanded (formulas, examples, EVM)
- [Strategy](strategy.md) — grind, store, swap; adapters
- [GRAI overview](../grai/overview.md) — shares, dividends, liquidation open
- [Protocol overview](../overview.md) — GRAI vs Grinders
