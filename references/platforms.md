# Platform & API Reference

## Launch Platforms

### Pump.fun
Memecoin factory with bonding curve pricing. Tokens launch with no initial liquidity pool — price follows a bonding curve until ~$69K market cap, then "graduates" to Raydium with auto-created LP.

- **Flow**: Create token → bonding curve trading → graduation → Raydium LP
- **Graduation threshold**: ~85 SOL in bonding curve (~$69K mcap)
- **Fee**: 1% on buys/sells during bonding curve phase
- **API**: `https://frontend-api-v3.pump.fun`
  - `GET /coins/latest` — newest tokens
  - `GET /coins/{mint}` — token details (market cap, volume, holder count)
  - `GET /coins/king-of-the-hill` — top trending
  - WebSocket: `wss://frontend-api-v3.pump.fun/socket.io` — real-time new token events
- **PumpSwap**: Pump.fun's native AMM for post-graduation trading

### Raydium LaunchLab
Raydium's native token launcher. Similar concept to Pump.fun but integrated into Raydium's ecosystem.

- **Flow**: Create token → LaunchLab bonding curve → auto-migrate to Raydium AMM pool
- **Configurable curves**: Linear, exponential, or custom
- **API**: `https://api-v3.raydium.io`
  - `GET /mint/ids` — token info by mint addresses
  - `GET /pools/info/ids` — pool data
  - WebSocket available for real-time pool creation events

### Metaplex
Full Solana token infrastructure + **Genesis Launch Pools** (fair launch system).

- **Token Metadata Program**: `metaqbxxUerdq28cj1RbAWkYQm3ybzjb6a8bt518x1s`
- Use to verify: token name, symbol, URI, update authority, whether metadata is mutable
- **Genesis Launch Pools**: Fair launch where users deposit SOL during a timed window and receive tokens proportional to their share of total deposits. No fixed price — market determines distribution.
- **Launch Phases**:
  1. **Deposit Phase** — users deposit SOL into the Launch Pool bucket
  2. **Crank Phase** — deposit period ends, on-chain behaviors triggered (SOL moves to unlocked bucket)
  3. **Claim Phase** — users claim their proportional token allocation
- **Key difference from Pump.fun**: No bonding curve. Fair distribution based on deposit share, not first-come-first-served pricing.
- **SDKs**: `@metaplex-foundation/genesis` (launch pools), `@metaplex-foundation/umi` (core framework), `@metaplex-foundation/mpl-toolbox`
- **Monitoring signals**:
  - Track deposit phase timing — participate during deposit window
  - Check total SOL deposited vs token allocation to estimate entry price
  - Monitor number of depositors (more depositors = healthier distribution)
- **API**: Query via Helius DAS (Digital Asset Standard) API
- **Docs**: https://www.metaplex.com/docs

### Bags (@BagsApp)
Presale and fair launch platform on Solana. Supports two models:
- **Presale**: Fixed price buy-in before launch, tokens distributed at TGE
- **Fair Launch**: Everyone gets same price, allocation proportional to contribution

- **Website**: bags.fm
- **API Base**: `https://public-api-v2.bags.fm/api/v1`
- **Auth**: Requires API key via `x-api-key` header. Get keys at [dev.bags.fm](https://dev.bags.fm) (up to 10 keys per user)
- **Rate limit**: 1,000 requests/hour per user/IP
- **Key endpoints**:
  - `GET /pools` — list all Bags pools with Meteora DBC and DAMM v2 pool keys
  - `GET /pools/{tokenMint}` — get specific pool by token mint
  - `POST /trade/quote` — get trade quote for a token
  - `POST /trade/swap` — create swap transaction (returns base58 serialized tx, ready to sign & send)
  - `GET /claimable-positions/{wallet}` — get claimable fee positions for a wallet
  - `POST /token/info` — create token info with image upload
  - `POST /token/launch` — create token launch transaction
- **Agent Auth**: Bags supports AI agent authentication via Moltbook verification flow (init → post verification → login → get JWT valid 365 days)
- **Docs**: https://docs.bags.fm

### Moonshot
DEX Screener's token launcher. Tokens created here appear natively on DEX Screener with boosted visibility.

- **Flow**: Create token → bonding curve → graduates to Raydium or Meteora
- **API**: Accessible via DEX Screener API (see below)
- Graduation triggers auto-LP creation

## Swap & Data APIs

### Jupiter (Swaps)
Solana's leading DEX aggregator. Routes across all major DEXs for best price. **Free, no API key.**

- **Quote**: `GET https://quote-api.jup.ag/v6/quote?inputMint={}&outputMint={}&amount={}`
- **Swap**: `POST https://quote-api.jup.ag/v6/swap` — returns serialized transaction
- **Price**: `GET https://price.jup.ag/v6/price?ids={mint}`
- Docs: https://station.jup.ag/docs/apis

### Helius (Token Data)
Solana RPC + enriched APIs. **Free tier: 100K credits/month.**

- **RPC**: `https://mainnet.helius-rpc.com/?api-key={KEY}`
- `POST /` with `getAsset` — full token metadata via DAS
- `POST /` with `getAssetsByOwner` — wallet holdings
- **Parsed transactions**: `GET https://api.helius.xyz/v0/transactions/?api-key={KEY}`
- Webhook support for real-time events
- Sign up: https://helius.dev

### Birdeye (Prices & Market Cap)
Token analytics and price data. **Free tier available.**

- **Price**: `GET https://public-api.birdeye.so/defi/price?address={mint}`
- **OHLCV**: `GET https://public-api.birdeye.so/defi/ohlcv?address={mint}&type=15m`
- **Token overview**: `GET https://public-api.birdeye.so/defi/token_overview?address={mint}`
- Header: `X-API-KEY: {key}` (free key from birdeye.so)
- Provides: price, market cap, volume, liquidity, holder count, trade count

### DEX Screener
Aggregated DEX data across chains. **Free, no API key.**

- **Token**: `GET https://api.dexscreener.com/latest/dex/tokens/{mint}`
- **Pairs**: `GET https://api.dexscreener.com/latest/dex/pairs/solana/{pairAddress}`
- **Search**: `GET https://api.dexscreener.com/latest/dex/search/?q={query}`
- **New tokens**: `GET https://api.dexscreener.com/token-profiles/latest/v1`
- Returns: price, volume, liquidity, pair age, price change %, txn counts
