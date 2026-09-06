# Fundraising concepts ↔ GrindURUS docs

Mapping of classic startup / VC fundraising terms to what is (and is not) described in this documentation.

Sources: [GRS cap table](../grs/cap-table.md), [Token sales](../grs/token-sales.md), [GRS overview](../grs/overview.md), [GRS mechanics](../developers/mechanics/GRS.md), [General overview](overview.md), [Manifesto](manifesto.md), [GRAI liquidation](../grai/liquidation.md).

*Status as of docs (August 2026, pre-mainnet).*

---

## 1. Cap table

**В документации: есть (подробно).**

On-chain / planned genesis allocation of **1B GRS** (fixed supply), spent from contract inventory via `grant` / sales — not reminted.

| Group | Share | Buckets |
| ----- | ----- | ------- |
| **Investments** | 20% | Token sales 15% + Pre-seed 5% |
| **Affiliates & airdrops** | 20% | Revenue Share 15% + Airdrops 5% |
| **Team** | 20% | Core team 15% + Advisors 5% |
| **Ecosystem** | 20% | Growth Fund 10% + LP & MM 10% |
| **Foundation** | 20% | Long-term reserve 15% + Audits 3% + Legal 2% |

Gates: **Instant**, **Linear** (vest), **Proprietary** (ops allocation, no GRS vote on bucket). All `grant` calls are `onlyOwner` on home.

Charts: [grs.svg](../developers/mechanics/grs.svg), [grs-vesting.svg](../developers/mechanics/grs-vesting.svg).

---

## 2. Pre / Post-money

**В документации: нет терминов pre-money / post-money.**

Есть близкий якорь оценки через **FDV** (fully diluted valuation):

| Parameter | Value |
| --------- | ----- |
| Supply | 1B GRS |
| TGE / Pre-seed price | **$0.02 / GRS** |
| Implied FDV | **$20M** |

Это эквивалент «оценки по полной эмиссии», а не классическая формула:

- pre-money = valuation before new cash  
- post-money = pre-money + raise  

В docs не разделены pre- и post-money для Pre-seed / TGE / Late Sale. Raise sizes даны отдельно (см. §10 / Token sales).

---

## 3. Dilution

**В документации: явно не описано.**

Косвенно:

- Supply **fixed 1B** — после genesis **нет mint** новых GRS.
- Продажи и `grant` **тратят inventory** контракта, а не раздувают supply → классическая «dilution от нового раунда mint» здесь не применяется в том же виде, что у equity rounds.
- Free float растёт по мере unlock / sales / proprietary grants → **доля ликвидного** и **относительная доля держателя в circulating** меняются, но это не названо dilution.
- TokenSales on-chain **uncapped** для buyback recycle (релистинг уже существующих токенов), не новая эмиссия.

Отдельного раздела «dilution», waterfall ownership % после раунда или fully diluted ownership scenarios **нет**.

---

## 4. SAFE / Note / Equity

**В документации:**

| Instrument | Status |
| ---------- | ------ |
| **SAFE** | **Нет** |
| **Convertible Note** | **Нет** |
| **Equity (традиционный)** | **Нет** (нет shares / SPA / % компании) |
| **Token equity (GRS)** | **Есть** — GRS = *protocol equity* / governance |

Механика привлечения в docs:

1. **Pre-seed** — 5% (50M), **$1M USDC** @ $0.02, linear vest 24m after TGE  
2. **TGE sales** — 10% (100M), public `buy`, instant, ~$2M target (USDC/ETH/SOL rows)  
3. **Late Sale** — 5% (50M), after protocol anniversary, **discount** to market  

Это token sale / grant model, не SAFE/Note/equity paper.

---

## 5. Option pool

**В документации: нет термина «option pool».**

Ближайшие аналоги в cap table:

| Bucket | Share | Vesting |
| ------ | ----- | ------- |
| **Core team** | 15% (150M) | 12m cliff, 60m linear |
| **Advisors** | 5% (50M) | 6m cliff, 66m linear |

Также есть holder-level `vest` (любой holder может залочить свой GRS: cliff ≤ 365d, linear ≤ 4×365d) — это не employee option pool.

Отдельного «unallocated option pool», strike prices, ISO/NSO или board-approved pool refresh **нет**.

---

## 6. Liquidation preference

**В документации: нет VC liquidation preference** (1x non-participating, participating, seniority, etc.).

Есть **другой** термин — **GRAI fund liquidation** ([Liquidation](../grai/liquidation.md)):

- Режим фонда `GRINDING` → `REDEMPTION`
- Open = **2-of-2**: vote quorum + Grinders arm
- Holders `redeem` pro-rata of basket after delay  
- Это shutdown / redeem путь **фонда (GRAI)**, не preference equity holders при exit компании

Для **GRS investors** waterfall / preference stack при sale of company / protocol **не описан**.

---

## 7. Founder vesting

**В документации: есть как Team vesting** (не отдельный заголовок «Founder vesting»).

| Role | Allocation | Schedule |
| ---- | ---------- | -------- |
| **Core team** | 15% / 150M | **12m cliff**, then linear to **M72** (60m) |
| **Advisors** | 5% / 50M | **6m cliff**, then linear to **M72** (66m) |
| **Pre-seed** (investors) | 5% / 50M | No cliff, **24m linear** after TGE |

On-chain: Linear gate via `grant` + `release`; no `revoke` in GRS mechanics.

Имя основателя встречается в [Protocol overview](../protocol/overview.md) (Vakhtanh Chikhladze, Founder) — **без** персонального vesting schedule отдельно от Core team bucket.

Double-trigger acceleration, good/bad leaver, repurchase rights — **не описаны**.

---

## 8. Board & voting rights

**В документации: частично (on-chain governance), без классического Board of Directors.**

### Есть

- **GRS votes** on home: `transfer` / `delegate` (ERC20Votes target); spoke GRS не голосует до bridge.
- Target Governor params: proposal **0.1%** (1M GRS), quorum **4%**, delay **48h** params / **7d** upgrades.
- GRS governs **protocol parameters and fee routing**, not custodian keys.
- Launch path: team multisig → timelock (`GRS.proprietor`) → steady state via GRS vote.
- Contract `owner` / Ownable2Step surfaces: GRAI, Grinders, Treasury wiring.

### Нет

- Board seats, observer rights, protective provisions  
- Investor veto / consent rights list  
- Corporate board composition / voting thresholds off-chain  

---

## 9. Term sheet

**В документации: нет.**

Нет term sheet, SPA, SAFT, investment agreement summary или списка commercial terms (valuation, board, prefs, pro-rata, MFN, etc.).

Ближайший «commercial» артефакт — **Raise plan** в [Token sales](../grs/token-sales.md) (цены, %, unlock, chain rows).

---

## 10. Use of funds + milestones

**В документации: частично.**

### Есть (use / destination hints)

**Target treasury mix after Pre-seed + TGE** (~$4M framing in raise diagram):

| Asset | ~Amount | Source |
| ----- | ------- | ------ |
| USDC | ~$2M | Pre-seed $1M + TGE USDC rows ~$0.5M×2 |
| ETH | ~$0.5M | TGE row 2 |
| SOL | ~$0.5M | TGE row 3 |

**Foundation / ops buckets** (token allocation, not cash budget):

- Long-term reserve 15%  
- Audits & Bug Bounty 3%  
- Legal 2%  
- Growth Fund, LP & MM, Revenue Share, Airdrops  

Ongoing: fee surplus → intended **GRS buybacks** → TokenSales resale at discount.

### Нет

- Детальный **cash** use of funds (% product / hiring / marketing / runway months)  
- Fundraising **milestones** (KPI gates, tranche releases, Series A triggers)  
- Late Sale raise size (явно: *depends on spot and discount*)

---

## 11. Runway / burn

**В документации: нет** (как finance metrics: monthly burn, runway months, cash vs opex).

Не путать с:

- **Token burn** при bridge / OFT hops (`grs/bridge.md`, sales LZ publish)  
- Unlock / circulating release schedules  

Числового burn rate команды или runway после Pre-seed/TGE **нет**.

---

## 12. Fundraising narrative

**В документации: частично (product / thesis narrative, не investor deck story arc).**

### Есть

- [Manifesto](manifesto.md): volatility → structural alpha; on-chain hedge fund; GRAI vs GRS roles; who it is for (investors, private clients, agents, builders).  
- [Overview](overview.md): architecture, yield split, tokenomics invariants.  
- [Token sales raise plan](../grs/token-sales.md): Pre-seed → TGE → Late Sale + buyback recycle story.  
- Positioning: GRS = protocol equity / fee layer; GRAI = fund capital share.

### Нет

- Готового fundraising narrative deck (problem → solution → traction → ask → use of funds)  
- Traction metrics / AUM targets / competitive landscape как investor section  
- Explicit «why now / why this round» investment memo  

---

## Summary matrix

| # | Concept | In docs? | Notes |
| - | ------- | -------- | ----- |
| 1 | Cap table | **Yes** | Full 1B GRS bucket table + gates |
| 2 | Pre/Post-money | **No (terms)** | FDV $20M / $0.02 used instead |
| 3 | Dilution | **No** | Fixed supply; unlock/float only implied |
| 4 | SAFE / Note / Equity | **Token equity only** | No SAFE/Note/corp equity |
| 5 | Option pool | **Analog only** | Team + Advisors buckets |
| 6 | Liquidation preference | **No** | Only GRAI fund liquidation (different) |
| 7 | Founder vesting | **Yes (Team)** | Core 12m/60m; Advisors 6m/66m |
| 8 | Board & voting | **Partial** | On-chain GRS gov; no Board |
| 9 | Term sheet | **No** | — |
| 10 | Use of funds + milestones | **Partial** | Treasury mix + token buckets; no cash plan / KPIs |
| 11 | Runway / burn | **No** | — |
| 12 | Fundraising narrative | **Partial** | Manifesto + raise plan; no full investor narrative |

---

## Primary doc links

- [GRS cap table](../grs/cap-table.md)  
- [Token sales / raise plan](../grs/token-sales.md)  
- [GRS mechanics](../developers/mechanics/GRS.md)  
- [Manifesto](manifesto.md)  
- [GRAI liquidation](../grai/liquidation.md) (not LP preference)  
