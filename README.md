# Otaku x402 ElizaOS Plugin

AI-powered Solana token trading insights plugin for ElizaOS agents, with X402 micropayment support for data monetization.

## Overview

The Otaku x402 plugin enables ElizaOS AI agents to:
- 🤖 **Analyze Solana tokens** using real-time trading data from Otaku x402 traders
- 📊 **Provide buy/sell/hold recommendations** with confidence scores
- 🏆 **Track top traders** and their performance metrics
- 🔥 **Monitor hot tokens** with real-time volume and trade data
- 💰 **Monetize data access** via X402 micropayments protocol

## Features

### Actions

#### 1. `ANALYZE_TOKEN`
AI-powered token analysis with trading signals.

**Triggers**: `should I buy`, `analyze token`, `check $SYMBOL`

**Example**:
```
User: "Should I buy $BONK?"
Agent: Analyzes token using volume, trader interest, and momentum scores
       Provides BUY/HOLD/SELL recommendation with confidence level
```

#### 2. `GET_OTAKU_DATA`
Retrieve real-time market data from Otaku x402 traders.

**Triggers**: `show hot tokens`, `top traders`, `leaderboard`

**Example**:
```
User: "Show me today's hot tokens"
Agent: Returns top 10 hot tokens with trade counts and volumes
```

### Providers

#### `kolProvider`
Automatically fetches and provides Otaku x402 market context to the agent:
- Hot tokens (1h timeframe)
- Daily leaderboard (top 10 traders)
- Active traders
- Market summary

## Installation

### 1. Install the Plugin

```bash
npm install plugin-otaku-x402
```

### 2. Add to Your Eliza Character

```typescript
import { kolPlugin } from 'plugin-otaku-x402';

export const character = {
    name: "Trading Agent",
    plugins: [kolPlugin],
    settings: {
        OTAKU_API_URL: "https://api.otakux402.so"  // Optional, uses default if not set
    }
};
```

### 3. Environment Configuration

Add to your `.env` file:

```env
# Otaku x402 API Configuration
OTAKU_API_URL=https://api.otakux402.so

# X402 Micropayments (Optional - for data monetization)
X402_ENABLED=true
X402_PRICE_PER_REQUEST_USDC=0.001
X402_PAYMENT_ADDRESS=YOUR_SOLANA_WALLET_ADDRESS
```

## API Endpoints Used

The plugin connects to these Otaku x402 API endpoints:

- `GET /api/hot-tokens?timeframe=1h&limit=10` - Trending tokens
- `GET /api/leaderboard?timeframe=daily&limit=10` - Top traders
- `GET /api/traders?limit=10` - Active traders
- `GET /api/trades?limit=100` - Recent trading activity

All endpoints return real-time data scraped from otakux402.so.

## Usage Examples

### Token Analysis

```typescript
// User asks:
"Should I buy $POPCAT?"

// Agent responds with:
📊 Token Analysis: $POPCAT

🟢 Recommendation: BUY
Confidence: 87%

Metrics:
• Volume Score: 92/100
• Trader Interest: 85/100
• Momentum: 84/100
• Risk Level: LOW

Market Data:
• Total Trades: 156
• Volume: 245.67 SOL ($46,028.22)
• Timeframe: 1h

Reasoning:
Strong buy signal. High volume (156 trades), strong trader interest, and positive momentum.

Contract: `7GCihgDB8fe6KNjn2MYtkzZcRjQy3t9GHdC8uHYmW2hr`
```

### Market Data Retrieval

```typescript
// User asks:
"Who are the top traders today?"

// Agent responds with:
🏆 Top Traders (Daily)

1. **Cented**
   • PnL: +82.89 SOL
   • Win Rate: 55.8% (58W/46L)
   • Rank: #1

2. **Dior**
   • PnL: +76.34 SOL
   • Win Rate: 61.2% (52W/33L)
   • Rank: #2

... (continues for top 10)
```

## X402 Micropayments Integration

### For Data Providers (Monetize Your Data)

The Otaku x402 plugin supports X402 protocol to monetize data access:

```typescript
// Enable X402 in your API server
const x402Config = {
    enabled: true,
    price_per_request_usdc: 0.001,  // $0.001 per request
    payment_address: "YOUR_SOLANA_WALLET",
    accepted_tokens: ["USDC", "SOL"]
};
```

### For AI Agents (Pay for Data)

Agents automatically handle X402 micropayments when accessing Otaku x402 data:

1. Agent detects HTTP 402 response
2. Checks price and creates payment transaction
3. Sends USDC payment to provider's wallet
4. Retries request with payment proof
5. Receives data

**Cost Example**:
- 1000 token analyses = $1.00 USDC
- Perfect for high-frequency trading agents

## Data Sources

All data is collected in real-time from **otakux402.so**:

- **Trading Activity**: Scraped every 60 seconds
- **Leaderboard**: Updated every 5 minutes (configurable)
- **Token Addresses**: Extracted via Helius RPC
- **Trader Stats**: Win rate, PnL, trade counts

## Trading Signal Algorithm

The plugin uses a 3-metric scoring system:

### 1. Volume Score (0-100)
```
score = (token_volume / max_volume_in_period) * 100
```

### 2. Trader Interest Score (0-100)
```
score = min((trade_count / 100) * 100, 100)
```

### 3. Momentum Score (0-100)
```
score = min((recent_trades / 20) * 100, 100)
```

### Overall Signal
```
overall = (volume + trader_interest + momentum) / 3

if overall >= 70: BUY
else if overall >= 40: HOLD
else: SELL
```

## Risk Assessment

- **HIGH RISK**: < 10 trades
- **MEDIUM RISK**: 10-50 trades
- **LOW RISK**: > 50 trades

## Development

### Build

```bash
npm install
npm run build
```

### Test

```bash
npm test
```

### Watch Mode

```bash
npm run dev
```

## Architecture

```
ELIZA_PLUGIN/
├── src/
│   ├── actions/
│   │   ├── analyzeToken.ts      # AI token analysis
│   │   └── getOtaku x402Data.ts        # Data retrieval
│   ├── providers/
│   │   └── kolProvider.ts       # Market data provider
│   ├── types.ts                 # TypeScript definitions
│   └── index.ts                 # Plugin export
├── package.json
├── tsconfig.json
└── README.md
```

## API Response Types

### HotToken
```typescript
interface HotToken {
    symbol: string;
    token_address: string;
    trade_count: number;
    total_volume_sol: number;
    total_volume_usd: number;
    timeframe: string;
}
```

### LeaderboardTrader
```typescript
interface LeaderboardTrader {
    id: number;
    trader_name: string;
    wallet_address: string;
    ranking_position: number;
    pnl_sol: number;
    pnl_usd: number;
    wins: number;
    losses: number;
    timeframe: string;
}
```

### TradingSignal
```typescript
interface TradingSignal {
    action: 'BUY' | 'HOLD' | 'SELL';
    confidence: number; // 0-100
    token: string;
    token_address: string;
    reasoning: string;
    metrics: {
        volume_score: number;
        trader_score: number;
        momentum_score: number;
        risk_level: 'LOW' | 'MEDIUM' | 'HIGH';
    };
}
```

## Roadmap

- [ ] X402 payment implementation
- [ ] Historical data analysis
- [ ] Backtesting framework
- [ ] Multi-timeframe analysis
- [ ] Advanced ML models for predictions
- [ ] Webhook notifications for signals
- [ ] Portfolio tracking

## License

MIT

## Support

For issues or questions:
- Open an issue on GitHub
- Contact: support@kol.ai

## Disclaimer

This plugin provides AI-generated analysis based on real-time trading data. **Always DYOR (Do Your Own Research)** before making any investment decisions. The creators are not responsible for any trading losses.

Trading cryptocurrencies carries significant risk. Only invest what you can afford to lose.

---

Built with ❤️ for the ElizaOS and Solana communities.
