# GRS cap table

**1 billion GRS** minted once on **home** into contract inventory. `grant` spends buckets; `spent[bucket]` tracks usage.

## Groups (100%)

| Group | Share | GRS | Notes |
| ----- | ----- | --- | ----- |
| **Investments** | 20% | 200M | |
| → Token sales | 15% | 150M | Instant — `buy` / optional `grant` |
| → Pre-seed | 5% | 50M | Linear, 24m after TGE |
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

~**200M (20%)** immediately liquid:

- 150M Token sales
- 50M Foundation proprietary float

## Holder vesting

Any holder may **`vest`** their own GRS (home or spoke):

- Cliff ≤ 365 days; linear ≤ 4×365 days
- Instant vest reverts — use `transfer`
- **`release(id)`** is permissionless to beneficiary
- Unreleased vest **cannot bridge**

## Charts

Cap table visuals: [grs.svg](../developers/mechanics/grs.svg), [grs-vesting.svg](../developers/mechanics/grs-vesting.svg).
