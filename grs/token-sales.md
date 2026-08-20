# Token sales

Public TGE float comes from the **TokenSales** bucket: **150M GRS**, Instant gate, no vest.

## Book model

Each sale row has:

| Field | Meaning |
| ----- | ------- |
| `id` | 1-based, assigned on `sale` |
| `asset` | Native (`0`) or ERC-20 / SPL mint as `bytes32` |
| `assetAmount` | Remaining quote asset for sale |
| `grsAmount` | Remaining GRS at this id |
| `recipient` | Payee for `buy`; empty → owner / admin |

Row **closes** when either remainder hits zero.

## Home lists, spoke sells

**Home** (owner):

```
sale(asset, assetAmount, grsAmount, recipient, dstEid)
```

| `dstEid` | Behavior |
| -------- | -------- |
| `0` | Local listing only; no LZ burn |
| ≠ `0` | Burns `grsAmount` from TokenSales inventory; LZ-publishes row to spoke |

**Spoke** `lzReceive`:

- Writes the sale row (`SaleAccepted`)
- Mints that GRS into **escrow** for buyers

Solana: `sale` appends locally; `publish_sale` performs the LZ hop (burn from `sale_escrow`).

## Buy

Anyone calls `buy(id, grsAmount, to)`:

1. Cost = `floor(grsAmount × assetAmount / remaining GRS)` (full remainder pays exact `assetAmount`).
2. Pay `recipient` in native / ERC-20 / SPL.
3. Receive GRS instantly from escrow — no vest.

Previews:

- EVM: `previewBuy(id, grsAmount)`
- Solana: `preview_buy(id, amount)`
- LZ fee quote: `quoteSale(asset, …, dstEid)` / `quote_sale(dst_eid, id)`

## Cross-chain test path

End-to-end check (devnet):

1. Deploy GRS home on Sepolia, spoke on Solana devnet.
2. Wire LayerZero peers.
3. Owner `sale(..., dstEid = solana_eid)` on home.
4. Wait for spoke `lz_receive` → row + escrow mint.
5. Buyer `buy` on Solana with SOL/USDC.

## Limits

- TokenSales cap **150M** total (`spent[TokenSales]` / `token_sales_spent`).
- `buy` never mints — only transfers from escrow.
- Do not `grant(TokenSales)` and LZ-publish the same GRS twice.

## App

GRS page: [app.grindurus.xyz](https://app.grindurus.xyz) — sales list, preview, buy (when deployments are wired).
