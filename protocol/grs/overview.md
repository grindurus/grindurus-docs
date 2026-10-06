# GRS overview

**GRS** (*Grindurus Token*) is the **bridgeable equity leg** of a distributed secondary market under many **local GRAI** funds.

Each chain runs its own GRAI (own NAV, deposits, dividends — **no** GRAI OFT). Beside it sit local pools **GRAI / ETH** and **GRS / ETH**. GRS bridges **1:1** via LayerZero OFT, so equity price pressure and buybacks can move across chains while fund shares stay isolated.

```
Chain A                          Chain B
────────                         ────────
GRAI_A  ↔  ETH                   GRAI_B  ↔  ETH     ← local funds
GRS     ↔  ETH                   GRS     ↔  ETH     ← same token
              └── GRS bridge (1:1) ──────────────────┘
```

**Why GRS exists this way:** one global float for protocol value and secondary depth; many local GRAIs for custody, yield, and liquidation. Without a bridgeable GRS, each spoke’s equity market would be a silo. Without local GRAI, one OFT share would couple every fund’s NAV.

Pricing and mesh examples: [Cross-chain markets](../cross-chain-pricing.md).

| | **GRAI** | **GRS** |
| --- | --- | --- |
| Role | Local fund share | Global bridgeable equity |
| Supply | Elastic (NAV) | Fixed **1B** at genesis |
| Cross-chain | Separate per chain | OFT 1:1 home ↔ spokes |
| Secondary | GRAI / ETH (local) | GRS / ETH (local pools, global arb) |

Metadata: [grs.json](https://grindurus.xyz/grs.json) · ERC-1046 `tokenURI`

## Implementations

| Chain | Contract / program |
| ----- | ------------------ |
| EVM | `GRS.sol` — LayerZero **OFT**, non-upgradeable |
| Solana | `programs/grs` — OFT store, 9 local / 6 shared decimals |

## Home vs spokes

One **home** chain (Solana or Ethereum, fixed at TGE) mints the entire **1B once**. Every other deployment is a **spoke**: bridged representation, not a second genesis. Changing home is a **migration** (new lockboxes), not a remint.

That home/spoke split is what makes the secondary market **distributed**: liquidity and sales can live on any peer; bridge arb ties GRS/ETH toward one world price.

## Main surfaces

| Area | Functions | Who |
| ---- | --------- | --- |
| Cap table (home) | `grant`, `quoteGrant`, `getAllocations` | owner |
| Sales | `sale`, `quoteBuy`, `buy`, `quoteSale` | owner / anyone — [Token sales](https://docs.grindurus.xyz/protocol/grs/token-sales) (TGE, IDOs, buyback resales) |
| Vesting | `vest`, `release`, `getVestings` | holder / anyone |
| Bridge | `bridge`, `quoteBridge`, `getPeers` | holder — the OFT path for secondary arb |
| Votes (home) | `transfer`, `delegate` | holder |

Spokes: `NotHome` on cap table and `sale`. Sale LZ payload accepted on spoke only.

Fee surplus can **buy GRS on any deep pool** → TokenSales → resale at a discount; after arb the same float feels it everywhere.

## Governance target

GRS is also the equity / governance claim (parameters, fee destiny) — not custodian keys:

- GRAI `owner` (config, feeds, wiring)
- Grinders `owner` (custodians, allocate policy)
- Treasury via `GRAI.owner()` (beneficiar, affiliate weights)

Spec: [GRS mechanics](https://docs.grindurus.xyz/protocol/grs/mechanics)

## Pages

- [Cap table](https://docs.grindurus.xyz/protocol/grs/cap-table)
- [Bridge](https://docs.grindurus.xyz/protocol/grs/bridge)
- [Token sales](https://docs.grindurus.xyz/protocol/grs/token-sales)
- [Cross-chain markets](https://docs.grindurus.xyz/protocol/cross-chain-pricing)
