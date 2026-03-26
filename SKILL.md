---
name: trade-strategy-skill
description: Build a personalized Solana trading strategy through guided conversation. Use when a user wants to trade tokens, set up a trading plan, snipe new launches, flip memecoins, copy whale wallets, or automate trading on Solana. Triggers on phrases like "I want to trade," "set up my strategy," "help me trade memecoins," "snipe new launches," or "make money trading."
---

# Trade Strategy Skill

Guide the user through a 5-stage multiple-choice conversation to build a clear, executable trading plan for Solana tokens. Save the plan as a JSON config. Follow it strictly as a constitution — never deviate from confirmed parameters.

## Core Rules

- Every question is multiple choice with plain language options.
- Every question includes: **"Let AI decide for me"** as the last option.
- Explain each option briefly before the user chooses.
- If user says "Let AI decide" — pick the most balanced/safe option and explain why.
- Never skip stages. Never start trading without a confirmed plan.
- If user wants to skip ahead, offer **Strategy Templates** (see below).

## Stage 1: Platform Selection 🏗️

Ask: "Where do you want to hunt for tokens?"

**Launch platforms to monitor** (multi-select):
- A) Pump.fun — new memecoin launches, bonding curve model
- B) Raydium LaunchLab — Raydium's native token launcher
- C) Metaplex — OG Solana token infrastructure
- D) Bags — presale & fair launch platform
- E) Moonshot — DEX Screener's launcher
- F) All of the above
- G) Let AI decide for me

**DEX for executing swaps:**
- A) Jupiter — aggregator, best prices across all DEXs
- B) Raydium direct
- C) PumpSwap
- D) Let AI decide for me

For platform-specific details and APIs, see `references/platforms.md`.

## Stage 2: Entry Strategy 🎯

Ask: "When should I buy, and how much?"

**Target market cap for entry:**
- A) Micro — $1K to $10K (highest risk, earliest entry)
- B) Early — $10K to $50K (high risk, early entry)
- C) Mid — $50K to $250K (moderate risk)
- D) Established — $250K to $1M (lower risk, later entry)
- E) Custom range — ask user to specify
- F) Let AI decide for me

**Buy amount per trade:**
- A) Micro — 0.05 to 0.1 SOL
- B) Small — 0.1 to 0.5 SOL
- C) Medium — 0.5 to 2 SOL
- D) Large — 2 to 5 SOL
- E) Custom amount
- F) Let AI decide for me

**Max concurrent positions:**
- A) 3 positions
- B) 5 positions
- C) 10 positions
- D) Unlimited
- E) Let AI decide for me

## Stage 3: Safety Filters 🛡️

Ask: "What should I avoid? These filters protect you from scams."

**Minimum liquidity:**
- A) Any (no filter)
- B) $5K+
- C) $10K+
- D) $50K+
- E) Let AI decide for me

**Dev wallet holding limit:**
- A) Skip if dev holds >5% of supply
- B) Skip if dev holds >10%
- C) Skip if dev holds >20%
- D) Don't filter by dev wallet
- E) Let AI decide for me

**Minimum holder count:**
- A) 10+ holders
- B) 50+ holders
- C) 100+ holders
- D) 500+ holders
- E) Let AI decide for me

**Rug pull protection** (multi-select):
- A) Skip if mint authority is NOT revoked
- B) Skip if freeze authority is enabled
- C) Skip if top wallet holds >25% of supply
- D) All of the above
- E) Let AI decide for me

For technical details on how to check these, see `references/safety-checks.md`.

## Stage 4: Take Profit Strategy 📈

Ask: "When do I cash out?"

**Profit-taking style:**
- A) Ladder — sell in portions at milestones
- B) Moon Bag — sell most, keep a small bag forever
- C) All-or-Nothing — single exit target (degen mode)
- D) Let AI decide for me

**If Ladder selected, ask ladder targets:**
- A) Sell 25% at 2x → 25% at 5x → 25% at 10x → hold 25%
- B) Sell 50% at 3x → 50% at 10x
- C) Sell 30% at 2x → 30% at 5x → 40% at 20x
- D) Custom ladder — ask user to specify multipliers and percentages
- E) Let AI decide for me

**If Moon Bag selected:**
- A) Sell 80%, keep 20% forever
- B) Sell 90%, keep 10% forever
- C) Custom split
- D) Let AI decide for me

**If All-or-Nothing selected:**
- A) Exit at 5x
- B) Exit at 10x
- C) Exit at 50x
- D) Exit at 100x
- E) Custom target
- F) Let AI decide for me

**Stop loss:**
- A) -30% (tight protection)
- B) -50% (moderate)
- C) -70% (loose)
- D) No stop loss (full degen)
- E) Let AI decide for me

## Stage 5: Review, Configure & Deploy 🚀

### Execution Mode

Ask: "How autonomous should I be?"

**Execution mode:**
- A) Alert Only — I find opportunities, you decide to buy/sell
- B) Semi-Auto — I buy automatically, ask your approval before selling
- C) Full Auto — I handle everything, you review reports
- D) Let AI decide for me

### Alert Channel Setup

Ask: "Where should I send you alerts?"

- A) Telegram — walk user through creating a bot via @BotFather or providing existing bot token
- B) Discord — ask for webhook URL or bot token
- C) WhatsApp — guide QR code connection (if using OpenClaw)
- D) In-session only — no external alerts
- E) Let AI decide for me

**Report frequency:**
- A) Every trade — notify on each buy/sell
- B) Daily summary — one report per day
- C) Weekly digest — one report per week
- D) Let AI decide for me

### Generate Trading Plan Summary

After all questions are answered, generate and display a complete summary:

```
═══════════════════════════════════════
       TRADING PLAN SUMMARY
═══════════════════════════════════════

PLATFORMS: [selected platforms]
DEX: [selected DEX]

ENTRY CRITERIA:
  Market Cap Target: [range]
  Buy Amount: [amount] SOL per trade
  Max Positions: [number]

SAFETY FILTERS:
  Min Liquidity: [value]
  Dev Wallet Limit: [value]
  Min Holders: [value]
  Rug Protection: [active filters]

TAKE PROFIT:
  Style: [ladder/moon bag/all-or-nothing]
  Targets: [specific targets]
  Stop Loss: [value]

EXECUTION:
  Mode: [alert/semi-auto/full-auto]
  Alerts: [channel]
  Reports: [frequency]

RISK METRICS:
  Max Capital at Risk: [calculated]
  Est. Trade Frequency: [calculated]
═══════════════════════════════════════
```

Ask: "Review your plan above. Want to change anything? If it looks good, reply **Confirm** to activate."

- If user wants changes → go back to the relevant stage question.
- If user confirms → save plan as `trading-plan.json` (see `scripts/trading-plan-template.json` for schema) and activate.

## Strategy Templates

If user wants to skip questions or asks for a template, offer these pre-built plans:

**🐌 Conservative Sniper**
- Platforms: All
- DEX: Jupiter
- Entry: $50K–$250K mcap, 0.1 SOL per trade, max 3 positions
- Safety: $10K+ liquidity, dev <5%, 100+ holders, all rug protection on
- Take Profit: Ladder (25% at 2x/5x/10x, hold 25%)
- Stop Loss: -30%
- Mode: Alert Only

**😈 Degen Mode**
- Platforms: Pump.fun + Moonshot
- DEX: Jupiter
- Entry: $1K–$10K mcap, 0.05 SOL per trade, max 10 positions
- Safety: Any liquidity, dev <20%, 10+ holders, mint authority check only
- Take Profit: All-or-Nothing at 50x
- Stop Loss: None
- Mode: Full Auto

**🐋 Whale Copy**
- Platforms: All
- DEX: Jupiter
- Entry: Follow whale's entry (any mcap), 0.5 SOL per trade, max 5 positions
- Safety: $5K+ liquidity, all rug protection on
- Take Profit: Moon Bag (sell 80%, keep 20%)
- Stop Loss: -50%
- Mode: Semi-Auto
- Special: Requires whale wallet addresses to monitor

Each template is fully customizable — after loading, walk through each parameter and ask if user wants to adjust.

## Post-Deployment Behavior

Once plan is confirmed and active:

### Constitution Rule
The trading plan is the AI's constitution. Never deviate from confirmed parameters. Never change position sizes, skip safety filters, or ignore take-profit levels without explicit user approval.

### Performance Tracking
Track every trade in `trading-log.json`:
- Entry price, exit price, P&L per trade
- Win rate (% of profitable trades)
- Total P&L in SOL
- Average hold time
- Best and worst trades
- Breakdown by platform (which launchpad performs best)

### Strategy Tuning
After 20+ trades, review performance and suggest adjustments:
- "Your stop loss at -30% triggered on X% of trades that later recovered. Consider -50%."
- "Platform X has a higher win rate than Platform Y. Consider focusing there."
- Present suggestions as options. Never auto-change the plan.

### Cooldown Timer
After 3 consecutive losses, enforce a cooldown:
- Pause trading for 2 hours (configurable)
- Notify user: "3 consecutive losses detected. Cooling down for 2 hours to prevent tilt trading."
- User can override with explicit command.

### Wallet Management
- Use a dedicated trading wallet separate from main wallet.
- Set a maximum budget. Never exceed it even in full-auto mode.
- Track remaining budget in trading-log.json.

### Plan Updates
User can say "update my strategy" or "change my plan" at any time to re-enter any stage. Save previous plan as backup before overwriting.

### Credentials Storage
Store all API keys and credentials at `trading-credentials.json` in the workspace:
```json
{
  "helius_api_key": "",
  "birdeye_api_key": "",
  "bags_api_key": "",
  "bags_jwt_token": "",
  "trading_wallet_private_key": "",
  "alert_config": {
    "telegram_bot_token": "",
    "telegram_chat_id": "",
    "discord_webhook_url": ""
  }
}
```
⚠️ Never log or share private keys. Load only when signing transactions.

## Example Prompts

Your human can trigger the skill with any of these:
- "I want to trade memecoins"
- "Set up my trading strategy"
- "Help me snipe new launches on Pump.fun"
- "I have 5 SOL, help me flip memecoins"
- "Show me my trading performance"
- "Update my strategy"
- "Pause trading" / "Resume trading"
- "Switch to alert-only mode"
- "What's my win rate?"
- "Change my take profit to ladder style"
- "Add Bags to my monitored platforms"
- "Show me my open positions"

You don't need to wait for a specific command — if the human asks anything trading-related, this skill activates.

## Channel Setup Guides

### Telegram Setup
1. Open Telegram, search for @BotFather
2. Send /newbot, follow prompts to name your bot
3. Copy the bot token BotFather gives you
4. Provide the token to the AI
5. AI will send test message to confirm connection

### Discord Setup
1. Go to Server Settings → Integrations → Webhooks
2. Create new webhook, select channel for alerts
3. Copy webhook URL
4. Provide URL to the AI

### WhatsApp Setup (OpenClaw)
1. AI generates QR code
2. Open WhatsApp → Settings → Linked Devices → Link a Device
3. Scan the QR code
4. AI sends test message to confirm
