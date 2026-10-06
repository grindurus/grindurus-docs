# GRS cap table

**1 billion GRS** minted once on **home** into contract inventory. `grant` spends buckets; `spent[bucket]` tracks usage.

## Groups (100%)

| Group | Share | GRS | Notes |
| ----- | ----- | --- | ----- |
| **Investments** | 20% | 200M | |
| → Token sales | 10% | 100M* | Instant — `buy` / optional `grant`. **TGE** @ $20M FDV (ETH+SOL rows). See [Token sales](https://docs.grindurus.xyz/grs/token-sales) |
| → IDOs | 5% | 50M* | Instant — Initial DEX Offerings. Display carve from the TokenSales soft plan (same on-chain inventory); listed via `sale` / `buy` |
| → Pre-seed | 5% | 50M | **$1M USDC** @ $0.02 ($20M FDV). Linear, 24m after TGE |
| **Affiliates & airdrops** | 20% | 200M | |
| → Revenue Share | 15% | 150M | Proprietary, ops |
| → Airdrops | 5% | 50M | Proprietary, 67 seasons |
| **Team** | 20% | 200M | |
| → Core team | 15% | 150M | 12m cliff, 60m linear |
| → Advisors | 5% | 50M | 6m cliff, 66m linear |
| **Ecosystem** | 20% | 200M | |
| → Growth Fund | 10% | 100M | Proprietary |
| → LP & MM | 10% | 100M | Proprietary, DEX inventory |
| **Foundation** | 20% | 200M | Not the GRAI fee Treasury |
| → Long-term reserve | 15% | 150M | Proprietary |
| → Audits & Bug Bounty | 3% | 30M | Proprietary |
| → Legal | 2% | 20M | Proprietary |

## Gate types

| Gate | Meaning | Caller |
| ---- | ------- | ------ |
| **Instant** | Full payout now (or OFT to spoke) | `owner` |
| **Linear** | Vesting schedule on home GRS | `owner` |
| **Proprietary** | Team / ops allocation; no GRS vote on bucket | `owner` |

All cap-table **`grant`** calls are **`onlyOwner`** on home.

## TGE free float (M0)

~**200M (20%)** immediately liquid if TGE sales, IDOs, and Foundation proprietary float are listed:

- **100M** Token sales (10% TGE rows)
- **50M** IDOs (5%)
- **50M** Foundation proprietary float

Pre-seed **50M** is locked (24m linear).

\* Genesis **150M** TokenSales soft plan = Token sales **100M** + IDOs **50M**. On-chain the bucket is uncapped so fee **buybacks** can re-enter escrow and be relisted at a **discount**. IDOs are **not** a separate on-chain `Bucket` — cap-table display only.

## Holder vesting

Any holder may **`vest`** their own GRS (home or spoke):

- Cliff ≤ 365 days; linear ≤ 4×365 days
- Instant vest reverts — use `transfer`
- **`release(id)`** is permissionless to beneficiary
- Unreleased vest **cannot bridge**

## Charts

Cap table visuals: [grs.svg](../developers/mechanics/grs.svg), [grs-vesting.svg](../developers/mechanics/grs-vesting.svg).
