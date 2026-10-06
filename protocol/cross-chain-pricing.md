# Cross-chain markets

Build the market stack layer by layer. The point of the design is that **local GRAI never bridges**, yet **GRS** turns many local share markets into one **cross-chain secondary mesh**.

---

## Layer 1 — Local GRAI (TVL + yield)

On one chain, GRAI is a fund share with **local TVL** (deposits into that chain’s Grinders) and **local yield** (`distribute` → lockers / Treasury on that chain).

There is no live protocol redeem while the fund is grinding. Without a secondary market, exit is unlock (penalty), wait, or liquidation — not a simple sell.

---

## Layer 2 — GRAI / native (simple exit)

Add a DEX pool **GRAI / native** (ETH, SOL, …).

That gives a **simple exit from the protocol**: sell GRAI → gas asset, no need to wait for liquidation. The mid can trade rich or cheap vs book NAV; mint/deposit still uses oracles, not the pool.

Still **one chain**. GRAI_A and GRAI_B do not know each other.

```
Chain A                         Chain B
GRAI_A  ↔  ETH                  GRAI_B  ↔  SOL
   ↑ local TVL / yield             ↑ local TVL / yield
   ↑ easy exit via pool            ↑ easy exit via pool
         (no link between funds yet)
```

---

## Layer 3 — GRS / native + bridgeable GRS

On each chain add **GRS / native**. Make GRS an OFT: **bridge A ↔ B 1:1**.

Now each chain has a **GRAI / GRS hop** (via native or a direct route):

```
GRAI  ↔  native  ↔  GRS
```

Because GRS is the **same** token on both sides of the bridge, the hop composes into an **effective GRAI_A / GRAI_B** path:

```
GRAI_A → native_A → GRS  ──bridge──►  GRS → native_B → GRAI_B
```

That is cross-chain **arbitrage** (when one fund’s secondary is mispriced vs the other in GRS terms) and cross-chain **liquidity** (inventory can move as GRS; depth on one spoke supports pricing pressure on another). GRAI itself still never bridges — only the equity rail does.

```
Chain A                              Chain B
GRAI_A ↔ ETH ↔ GRS  ◄──── bridge ────►  GRS ↔ SOL ↔ GRAI_B
         │                                    │
    local exit                           local exit
         └──── implied GRAI_A / GRAI_B via GRS hop ────┘
```

Product context: [GRS overview](grs/overview.md).

| Layer | What you get |
| ----- | ------------ |
| GRAI alone | Local TVL + yield; hard exit |
| \+ GRAI / native | Simple local exit |
| \+ GRS / native + bridge | GRAI/GRS hop → effective **GRAI_A / GRAI_B**; cross-chain arb + shared equity liquidity |

---

## How the hop clears

| Leg | Clears across chains? |
| --- | --------------------- |
| GRAI book / dividends / liquidation | No — local |
| GRAI / native | No — local exit only |
| GRS / native | Local print; **yes** after bridge arb |
| GRAI_A ↔ GRAI_B (composed) | Yes — multi-hop via GRS, not an OFT of GRAI |

Fee buybacks buy GRS on the deepest GRS/native; bridge arb exports that bid to thinner spokes. Same 1B float, many local GRAIs.

---

## Example 1 — Local exit only (no mesh yet)

Alice deposited on Ethereum. She needs ETH today.

1. Sell GRAI_ETH → ETH on the GRAI/ETH pool.
2. Done. Solana’s GRAI never enters the story.

**Takeaway:** GRAI/native is the simple exit. It does not create cross-chain anything by itself.

---

## Example 2 — Quiet mesh (aligned hops)

Deep pools. For comparison assume 1 ETH ≈ 100 SOL.

| Market | Ethereum | Solana |
| ------ | -------- | ------ |
| GRAI / native | 0.00040 ETH | 0.040 SOL |
| GRS / native | 0.000010 ETH | 0.0010 SOL |
| Implied GRAI / GRS | **40** | **40** |

Hops match → no GRAI_A/GRAI_B arb through GRS. Each fund still has its own TVL and yield; prices just happen to line up.

---

## Example 3 — Cross-chain arb via the GRAI/GRS hop

Ethereum yield spikes; GRAI_ETH secondary bids up. Solana quiet.

| Market | Ethereum | Solana |
| ------ | -------- | ------ |
| GRAI / native | **0.00048** ETH | 0.040 SOL |
| GRS / native | 0.000010 ETH | 0.0010 SOL |
| Implied GRAI / GRS | **48** | **40** |

**Arb (effective GRAI_ETH → GRAI_SOL via GRS):**

1. Sell rich GRAI_ETH → ETH.
2. Buy GRS on Ethereum (or ETH → GRS).
3. `bridge` GRS → Solana.
4. Sell GRS → SOL; buy cheap GRAI_SOL (or `deposit` if book is better).

Capital left Ethereum’s fund share and entered Solana’s — **without** a GRAI bridge. GRS carried the inventory across. That is cross-chain liquidity: the Solana GRAI/SOL pool (or mint) absorbed flow that started as an Ethereum secondary sell.

Meanwhile **GRS/native arb** may also run (buy GRS where cheap, bridge, sell where rich) until equity mids converge. GRAI multiples need not converge — different TVL and yield streams.

---

## Example 4 — Buyback feeds the mesh

Ops buybacks GRS on deep Ethereum GRS/ETH. Arb bridges GRS toward Solana; GRS/SOL rises. Local GRAI/SOL may not move unless traders run the hop. Equity liquidity is **shared**; fund secondary liquidity stays **local** until someone hops.

---

## Example 5 — Thin spoke limits the hop

If Solana GRS/SOL is shallow, the GRAI_A/GRAI_B path dies in impact and bridge fees before size clears. Cross-chain arb only works inside:

```
edge  ≲  bridge_fee + swap fees + impact(size) + latency_risk
```

Seed GRS/native depth if the mesh is meant to carry real flow.

---

## What stays local on purpose

- **No GRAI OFT** — one fund’s TVL/liquidation must not drag another.
- Label tickers by chain (`GRAI-ETH`, `GRAI-SOL`).
- Report GRAI/GRS **per chain**; the composed GRAI_A/GRAI_B rate is an arb path, not a single oracle.

---

## Summary

1. **Local GRAI** = TVL + yield on that chain.  
2. **GRAI / native** = simple exit from the protocol.  
3. **GRS / native + bridgeable GRS** = GRAI/GRS hop on each chain.  
4. Compose hops across the bridge → **effective GRAI_A / GRAI_B** → cross-chain arbitrage and cross-chain liquidity, while each GRAI remains a separate fund.

Related: [Protocol overview](overview.md) · [GRS](grs/overview.md) · [Bridge](grs/bridge.md) · [Token sales / buyback](grs/token-sales.md) · [GRAI](grai/overview.md)
