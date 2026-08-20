# Overview

GrindURUS is a **volatility harvesting** stack with an on-chain fund layer.

## Two layers

**Off-chain (protocol repo)** — trading bots called **Grinders**. Each Grinder runs **GrindURUS** in two modes on one pair:

- **DIRECT** — buy low, sell high → grow the quote asset (e.g. USDC)
- **INVERSE** — sell high, buy low → grow the base asset (e.g. ETH)

Adapters connect to Binance, CoW Protocol, LiFi, Jupiter, and other terminals. **Boss** starts and monitors Grinder containers.

**On-chain (evm + solana repos)** — the fund and governance:

| Contract / program | Token or NFT | Role |
| ------------------ | ------------ | ---- |
| GRAI | GRAI (6 decimals) | USD book-priced share |
| Grinders | GRINDERS NFT | Custodian wallets + reserve |
| Treasury | GRAI-TREASURY NFT | Affiliate tree + fee routing |
| GRS | GRS (1B cap) | Protocol equity |

## Typical holder path

1. Deposit USDC (or another listed asset) into **GRAI**.
2. Receive GRAI at the current book (`totalValue`).
3. Optionally **lock** GRAI to earn asset dividends on the unvoted portion.
4. Custodians trade allocated capital; yield returns via **`distribute`**.
5. **Claim** dividends, or exit via secondary market / unlock / liquidation redeem.

## GRAI vs GRS

| | **GRAI** | **GRS** |
| --- | --- | --- |
| Supply | Elastic (grows with deposits) | Fixed **1 billion** at genesis |
| Price anchor | USD book (`totalValue`) | Market + protocol fee claim |
| Use | Fund share, lock, vote, dividends | Equity, governance, fee routing target |

GRAI is the **deposit receipt** for the volatility fund. GRS is **protocol stock**, not a second fund share.
