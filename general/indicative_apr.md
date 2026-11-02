# Indicative APR

What the app shows next to deposit as **Indicative APR** (`5% – 83%` by default) — the same story as the info hint under the mint amount. It is a **modeled range**, not an on-chain rate and not a promise.

Landing plaque copy matches this page in short form: *Likely modeled APR range — from low vol to high vol*.

---

## What is Indicative APR

**Indicative APR** is a band of annualized yield outcomes under the GrindURUS / Grinder model.

| Claim | Meaning |
| ----- | ------- |
| About **19 out of 20** modeled future APR outcomes land in the band | Plain-language **~95% coverage** of the modeled future APR |
| **Low end** of the band | Quieter listed assets / lower realized swings |
| **High end** of the band | Higher market swings / more volatile listed assets |

So `5% – 83%` is read as: *most modeled APRs sit somewhere between these ends; quieter markets toward 5%, choppier markets toward 83%.*

It is **not**:

- the live dividend APR paid last epoch on-chain;
- a guaranteed floor or cap;
- a single “expected value” point you should treat as the forecast.

---

## How we estimate

Pipeline (same order as the in-app hint):

1. **Fit historical prices** for the relevant listed assets / pairs.
2. **Choose grinder parameters** (how the dual-sided / inventory strategy is run in simulation).
3. **Model how returns may unfold** with ML — a distribution over future returns given history and those parameters.
4. **Take the central band** that covers about **95%** of the modeled APR outcomes from that simulation.

In statistics terms (optional reading): that band is approximately a **95% prediction interval** for modeled APR — the central mass of the **predictive** APR under the model (roughly the 2.5% and 97.5% quantiles). The product UI keeps the plain “19 out of 20” wording; this paragraph is the precise name for the same object.

---

## Volatility → APR

For a selected deposit asset the UI shows something like:

```
ASSET  <volatility %>  →  sits inside  5% – 83%
```

Higher historical / displayed price volatility usually maps toward the **high** end of the indicative band (more swing-trading opportunity in the model). Lower volatility maps toward the **low** end. The asset’s own indicative figure still **sits inside** the published range; the range is the envelope across listed-asset regimes, not a separate promise per ticker.

---

## What it covers

The estimate is about yield modeled from **assets you receive from the index into your wallet** after participating (deposit / lock path) — i.e. the economic exposure the strategy harvests from listed inventory, not a staking APY on idle wallet GRAI.

On-chain cashflows when Grinders push profit remain the **~50% / ~50%** dividend / treasury split described in [Overview](overview.md#yield-at-a-glance-defaults). Indicative APR is a **forward model band**; distribute / claim are **realized** protocol events.

---

## Caveats

- **Model-based and indicative only** — not guaranteed.
- Outcomes **outside the model** (regime breaks, gaps, operational limits, thin liquidity) can land **outside** the band.
- The published interval **can be recalculated as the protocol runs** — new price history, live grinder performance, listed-set changes, oracles, strategy parameters, and refreshed ML fits all move the band. Treat `5% – 83%` as the **current** product figure, not a permanent constant.

---

## Where it appears

| Surface | What you see |
| ------- | ------------ |
| App — mint / deposit (`Indicative APR` + info) | Range + this explanation |
| Landing — grinders plaque (INDEXED YIELD) | `5%–83%` + *Indicative* + short desc |

---

## Read next

- [Overview](overview.md) — product path and yield split
- [GRAI overview](../protocol/grai/overview.md) — lock, distribute, claim
- [Strategy](../protocol/grinders/strategy.md) — what grinders do off-chain
