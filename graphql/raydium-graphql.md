# Raydium GraphQL

## Overview

Raydium's own REST API (api-v3.raydium.io) does not expose a GraphQL endpoint. GraphQL access to Raydium on-chain program state is provided by **Shyft** (shyft.to), a Solana indexing platform that parses and indexes Raydium program accounts (AMM v4 LiquidityPoolv4, CLMM PoolState, farm vaults, etc.) and exposes them via a Hasura-style GraphQL API with filtering, ordering, and pagination.

## Endpoint

```
https://programs.shyft.to/v0/graphql?api_key={SHYFT_API_KEY}
```

Method: `POST`  
Content-Type: `application/json`

## Authentication

An API key from [shyft.to](https://shyft.to) is required. Pass it as a URL query parameter (`api_key=`). Free-tier keys are available with rate limits. A paid plan is needed for production throughput.

## Supported Raydium Types (via Shyft)

| GraphQL Type | Description |
|---|---|
| `Raydium_LiquidityPoolv4` | AMM v4 pool accounts (constant-product) |
| `Raydium_PoolState` | CLMM concentrated-liquidity pool state |
| `Raydium_PersonalPositionState` | CLMM LP position NFT data |
| `Raydium_ProtocolPositionState` | CLMM protocol-level position tracking |
| `Raydium_TickArrayState` | CLMM tick array for price range management |
| `Raydium_FarmState` | Yield farm reward pools |
| `Raydium_FarmerState` | Per-wallet farm stake accounts |

## Example Query — AMM v4 Pool by ID

```graphql
query GetPool($poolId: String!) {
  Raydium_LiquidityPoolv4(where: { pubkey: { _eq: $poolId } }) {
    pubkey
    baseMint
    quoteMint
    lpMint
    baseVault
    quoteVault
    lpVault
    openOrders
    targetOrders
    marketId
    marketProgramId
    baseDecimal
    quoteDecimal
    tradeFeeNumerator
    tradeFeeDenominator
    swapFeeNumerator
    swapFeeDenominator
    swapBaseInAmount
    swapBaseOutAmount
    swapQuoteInAmount
    swapQuoteOutAmount
    lpReserve
    poolOpenTime
    status
    state
    depth
    nonce
    owner
  }
}
```

## Notes

- The Shyft GraphQL interface is a Hasura-style API where each Raydium account type is a top-level query field.
- Filtering uses Hasura comparison expressions (`_eq`, `_in`, `_gte`, `_lte`, `_like`).
- Raydium does not operate or endorse the Shyft indexer; it is a third-party service.
- For aggregate analytics (TVL, volume, APY) Raydium's own REST API at `api-v3.raydium.io` is the canonical source.
- CLMM pool state (tick spacing, sqrt price, fee tiers, liquidity) is available under `Raydium_PoolState`.
- On-chain swap execution goes through Raydium's Solana programs directly, not via any API or GraphQL layer.

## References

- Shyft documentation: https://docs.shyft.to/
- Shyft GraphQL console: https://programs.shyft.to/
- Raydium REST API docs: https://api-v3.raydium.io/docs/
- Raydium developer docs: https://docs.raydium.io/
- Community example (Shyft + Raydium AMM v4 swap): https://github.com/0xatillaa/shyft-api-raydium-swap
