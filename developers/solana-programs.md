# Solana programs

Primary tree: [grindurus-solana](https://github.com/grindurus/grindurus-solana)

## Programs

| Program | Role |
| ------- | ---- |
| **grai** | GRAI mint, oracles, deposit, lock/vote/bribe, liquidation |
| **grinders** | Custodian NFTs, allocate/deallocate, swap CPI, liquidate |
| **grs** | LayerZero OFT — home genesis 1B, sales, bridge |
| **lifi_custody** | PDA custody for LiFi swaps from Grinder adapter |
| **custom_price_feed** | Dev/test price feed |

Behavior mirrors EVM unless noted (e.g. Solana GRS has no on-chain `grant` cap table).

## GRS on Solana

| Instruction | Home | Spoke |
| ----------- | ---- | ----- |
| `init` | ✓ | ✓ |
| `mint_genesis` | once | reverts |
| `sale` / `publish_sale` | ✓ | `NotHome` |
| `buy` | ✓ | ✓ (after LZ publish) |
| `lz_receive` (sale payload) | `NotSpoke` | ✓ |

Decimals: **9** local, **6** shared on the wire.

## Devnet deploy

Migrations under `migrations/`:

```bash
cd grindurus-solana
anchor build
npx tsx migrations/...   # per-program scripts
```

See `migrations/README.md` and `programs/grai/README.md` for PDA layout and admin ix list.

## Tests

```bash
anchor test
cargo check -p grs
```

TypeScript: `tests/grs.t.ts`, `tests/grai.t.ts`.

## IDL in app

After program changes, rebuild IDL and sync to `grindurus-app/src/grai/idl/` for the app to read on-chain state.
