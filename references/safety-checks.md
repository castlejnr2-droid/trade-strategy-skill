# Safety Checks Reference

## On-Chain Checks

### Mint Authority
**Risk**: If active, the creator can mint unlimited tokens → instant dilution/rug.

**Check**: Query the token's mint account via RPC:
```
POST https://mainnet.helius-rpc.com/?api-key={KEY}
{"jsonrpc":"2.0","id":1,"method":"getAccountInfo","params":["{mintAddress}",{"encoding":"jsonParsed"}]}
```
Look at `result.value.data.parsed.info.mintAuthority`:
- `null` → ✅ Revoked (safe)
- Any pubkey → ⚠️ Active (risky)

### Freeze Authority
**Risk**: If active, the creator can freeze any holder's tokens — they can't sell.

**Check**: Same `getAccountInfo` call. Look at `result.value.data.parsed.info.freezeAuthority`:
- `null` → ✅ Revoked (safe)
- Any pubkey → ⚠️ Active (risky)

### Top Holder Concentration
**Risk**: If a few wallets hold most supply, a single sell = price crash.

**Check via Helius**:
```
POST https://mainnet.helius-rpc.com/?api-key={KEY}
{"jsonrpc":"2.0","id":1,"method":"getTokenLargestAccounts","params":["{mintAddress}"]}
```
Returns top 20 holders with balances. Calculate % of total supply.

**Thresholds**:
- Top holder >25% of supply → ⚠️ High risk
- Top 5 holders >50% combined → ⚠️ Concentrated
- Top 10 holders >70% combined → ⚠️ Very concentrated

Exclude known addresses: LP pools, burn addresses, program PDAs.

### Dev Wallet Detection
**Heuristics**:
1. Query token metadata → find `updateAuthority` (usually the deployer)
2. Check if `updateAuthority` wallet holds a significant % of supply
3. Check first few transactions of the mint — the wallet that created it is the dev
4. Cross-reference with top holders list

**Red flags**:
- Dev wallet holding >10% post-launch
- Multiple wallets funded from same source holding collectively >15%
- Dev selling within first hour of launch

### Liquidity Lock Status
**Risk**: If LP tokens aren't locked/burned, the creator can pull liquidity at any time (rug pull).

**Check**:
1. Find the LP token mint for the trading pair
2. Check if LP tokens were sent to a burn address (`1111111111111111111111111111111111`)
3. Or check known lockers — though Solana has fewer formal lockers than EVM

**Pump.fun graduated tokens**: LP is auto-created and LP tokens are burned on graduation → generally safe.

**Quick check via Birdeye**:
```
GET https://public-api.birdeye.so/defi/token_overview?address={mint}
```
The `liquidity` field shows current pool liquidity. A sudden drop = LP pulled.

### Known Scammer Wallets
Maintain a local watchlist. Sources:
- **RugCheck.xyz**: `https://api.rugcheck.xyz/v1/tokens/{mint}/report` — automated risk score
- Community-maintained lists on GitHub
- Previous rugs tracked in your own `performance.json`

**Pre-trade flow**:
1. Get deployer wallet from token creation tx
2. Check deployer against watchlist
3. Check if deployer has created multiple tokens (serial launcher = higher risk)
4. Query deployer's transaction history — look for pattern of create → rug → repeat

## Composite Safety Score

Combine checks into a single pass/fail before each trade:

| Check | Weight | Pass | Fail |
|-------|--------|------|------|
| Mint authority revoked | Required | null | Any pubkey |
| Freeze authority revoked | Required | null | Any pubkey |
| Top holder <25% | High | <25% | ≥25% |
| Dev wallet within limit | High | Within plan % | Exceeds plan % |
| Liquidity above minimum | High | ≥ plan minimum | Below plan minimum |
| Holder count above minimum | Medium | ≥ plan minimum | Below plan minimum |
| Not on scammer watchlist | Required | Not found | Found |

**Required** checks must pass — no override. **High/Medium** checks follow plan settings (user may have set permissive thresholds).
