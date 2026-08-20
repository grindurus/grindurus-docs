# Bridge (home and spokes)

GRS uses **LayerZero OFT** for cross-chain transfers.

## Directions

| Direction | Home | Spoke |
| --------- | ---- | ----- |
| Home → spoke | Burn / lock from home inventory | Mint 1:1 |
| Spoke → home | — | Burn |
| Spoke → spoke | — | Burn on source, mint on dest |

## Accounting rules

1. `Σ spoke.supply + home.liquid + home.lockbox = 1B` (unreleased vest stays in lockbox).
2. Spoke mint authority is **only** the OFT adapter — not a second genesis.
3. Amount in = amount out; failed LZ messages do not mint.
4. New chain = new spoke + peer config — **zero** extra genesis.
5. **Votes** live on home (or EVM hub). Spoke GRS does not vote until bridged back.

## Decimals

| | Decimals |
| --- | -------- |
| EVM local | 18 |
| Solana local | 9 |
| Shared (LZ wire) | 6 |

Bridge is 1:1 in **GRS units**; dust rules apply on OFT send.

## API

| Function | Role |
| -------- | ---- |
| `bridge(dstEid, to, amountLD)` | Send GRS cross-chain |
| `quoteBridge(...)` | LZ fee quote |
| `getPeers()` | Configured remote OFTs |
| `setPeer` | Owner wires eid → peer bytes32 |

## Admin

- **Ownable2Step** on GRS (`transferOwnership` → `acceptOwnership`).
- OFT pause / fee withdraw per LayerZero adapter surface.

## Solana parity

`programs/grs`: `init_grs`, `mint_genesis` (home once), `lz_receive`, `send` — same home/spoke split.

Setup checklist for Sepolia ↔ devnet: see project `TODO.md` (deploy, wire peers, app config, test `sale` → `buy`).
