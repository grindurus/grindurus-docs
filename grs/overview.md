# GRS overview

**GRS** (*Grindurus Token*) is fixed-supply protocol equity and governance.

| | **GRAI** | **GRS** |
| --- | --- | --- |
| Role | Fund share | Protocol equity + gov |
| Supply | Elastic (NAV) | **1B** at genesis |
| Mint after TGE | Yes (deposits) | **No** |

Metadata: [grs.json](https://grindurus.xyz/grs.json) · ERC-1046 `tokenURI`

## Implementations

| Chain | Contract / program |
| ----- | ------------------ |
| EVM | `GRS.sol` — LayerZero **OFT**, non-upgradeable |
| Solana | `programs/grs` — OFT store, 9 local / 6 shared decimals |

## Home vs spokes

One **home** chain (Solana or Ethereum, fixed at TGE) mints the entire **1B once**. Every other deployment is a **spoke**: bridged representation via LayerZero, not a second genesis.

Changing home is a **migration** (new lockboxes), not a remint.

## Main surfaces

| Area | Functions | Who |
| ---- | --------- | --- |
| Cap table (home) | `grant`, `quoteGrant`, `getAllocations` | owner |
| Sales | `sale`, `previewBuy`, `buy`, `quoteSale` | owner / anyone |
| Vesting | `vest`, `release`, `getVestings` | holder / anyone |
| Bridge | `bridge`, `quoteBridge`, `getPeers` | holder |
| Votes (home) | `transfer`, `delegate` | holder |

Spokes: `NotHome` on cap table and `sale`. Sale LZ payload handled on spoke only (`NotSpoke` on home receive).

## Utility target

GRS governs protocol parameters and fee routing — not custodian keys:

- GRAI `owner` (config, feeds, wiring)
- Grinders `owner` (custodians, allocate policy)
- Treasury via `GRAI.owner()` (beneficiar, affiliate weights)

Target stack: **ERC20Votes** on home GRS + Governor + timelock.

Spec: [GRS mechanics](../developers/mechanics/GRS.md)

## Pages

- [Cap table](cap-table.md)
- [Bridge](bridge.md)
- [Token sales](token-sales.md)
