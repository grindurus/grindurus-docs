# Grinders overview

**Grinders** is the protocol’s **junior-capital vault and custodian registry**.

| Piece | Role |
| ----- | ---- |
| **Reserve** | Holds assets from `GRAI.deposit` until allocated |
| **GRINDERS NFT** | One ERC-721 / Metaplex NFT per custodian wallet |
| **Custodian proxy** | Per-NFT wallet that trades and returns yield |

GRAI book (`totalValue`) does **not** move when custodians trade — only when yield is **`distribute`**d or on deposit / redeem / revive.

## Capital path

```
GRAI.deposit → Grinders reserve
       │
       │ allocate(custodian, asset, amount)   [Grinders owner]
       ▼
Custodian wallet  ──trade──►  balances change
       │
       ├── distribute(yield) ──► GRAI.distribute
       └── deallocate(principal) ──► back to reserve
```

There is **no on-chain allocate ledger**. Track net issuance off-chain:

```
net ≈ Σ Allocate − Σ Deallocate
```

## Custodian kinds (EVM)

Registered under `keccak256("grindurus.custodian.<name>")`:

| Kind | Contract | Trading |
| ---- | -------- | ------- |
| `explicit_swap` | `SwapCustodian` | Router call + price limit |
| `cow` | `CoWCustodian` | CoW Protocol EIP-1271 |
| `lifi` | `LiFiCustodian` | LiFi routing (stub / WIP) |

Solana: swap CPI via Grinders program; Jupiter / LiFi custody paths in migrations.

## Roles

| Role | Powers |
| ---- | ------ |
| **Grinders owner** | `set`, `mint`, `allocate`, `deallocate`, `confirm` (liquidation arm) |
| **NFT owner** | Run swaps on that custodian |
| **Anyone** | — |

During liquidation: custodian trading blocked; `Grinders.liquidate` sweeps wallets to GRAI / reserve.

## NFT metadata

- Collection URI: ERC-1046 → `https://grindurus.xyz/metadata.json`
- Per-custodian: on-chain generated art (`GrinderArt` on EVM)

## App

[app.grindurus.xyz/grinders](https://app.grindurus.xyz/grinders) — custody balances, register, allocate (when wired).

Spec: [GRINDERS mechanics](https://docs.grindurus.xyz/developers/mechanics/grinders)
