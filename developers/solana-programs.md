# Solana programs

Primary tree: [grindurus-solana](https://github.com/grindurus/grindurus-solana)

## Programs

| Program | Role |
| ------- | ---- |
| **grai** | GRAI mint, oracles, deposit, lock/vote/bribe, liquidation |
| **grinders** | Custodian NFTs, allocate/deallocate, swap CPI, liquidate |
| **lifi_custody** | PDA custody for LiFi swaps from Grinder adapter |
| **custom_price_feed** | Dev/test price feed |

Behavior mirrors EVM unless noted.

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
```

TypeScript: `tests/grai.t.ts`.

## IDL in app

After program changes, rebuild IDL and sync to `grindurus-app/src/grai/idl/` for the app to read on-chain state.
