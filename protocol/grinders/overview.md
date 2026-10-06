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
| **Grinders owner** | `set`, `mint`, `allocate`, `deallocate`, `distribute`, `setGrindPeriod` |
| **NFT owner** | Run swaps on that custodian |
| **GRAI** | `heartbeat` (on `revive`) |

During liquidation: custodian trading blocked; `Grinders.liquidate` sweeps wallets to GRAI / reserve.

## Unlock penalties

`GRAI.unlock` sends the flat unlock fee (`unlockPenaltyBps`) as **GRAI tokens** to the Grinders contract. Grinders holds that balance as ordinary ERC20 inventory (not yield, not allocate ledger). It is separate from junior capital assets.

## Heartbeat gate

Grinders no longer uses a manual `confirm` flag for liquidation.
Instead, liquidation depends on an implicit heartbeat:

- `heartbeatAt` stores the last operational activity timestamp.
- `grindingPeriod` defines how long Grinders is considered active.
- `grinding()` is true while `block.timestamp <= heartbeatAt + grindingPeriod`.
- GRAI can open liquidation only when quorum is reached **and** `!grinding()`.

## NFT metadata

- Collection URI: ERC-1046 → `https://grindurus.xyz/metadata.json`
- Per-custodian: on-chain generated art (`GrinderArt` on EVM)

## App

[app.grindurus.xyz/grinders](https://app.grindurus.xyz/grinders) — custody balances, register, allocate (when wired).

Spec: [GRINDERS mechanics](https://docs.grindurus.xyz/protocol/grinders/mechanics)

Trading stack (Boss, adapters, GrindURUS): [Off-chain](https://docs.grindurus.xyz/protocol/grinders/off-chain)
