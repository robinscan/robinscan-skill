# Robinscan Skill

Use this skill when the user asks about Robinhood Chain blockchain data, tokenized stocks, or wants to look up addresses/transactions/tokens on Robinhood Chain.

## API Base URL

All endpoints are at `https://robinscan.io`. No API key required.

## Available Endpoints

### Chain Stats
```
GET /api/stats
```
Returns: ROH price, gas prices, block time, total blocks/addresses/transactions, today's tx count.

### Activity Chart
```
GET /api/stats/chart
```
Returns: Last 14 days of daily transaction counts.

### Address Transactions
```
GET /api/address/{address}/txs?cursor={cursor}
GET /api/address/{address}/counters
```
- `address`: 0x-prefixed 40 hex chars
- `cursor`: optional pagination token from previous response
- Returns: paginated transaction list with hash, block, timestamp, from/to, value, fee, method, status

### Address Token Transfers
```
GET /api/address/{address}/transfers?cursor={cursor}&scope={scope}
```
- `scope=token`: list transfers of a specific token contract
- Returns: paginated transfer list with tx hash, log index, from/to, amount, token info

### Transaction Logs
```
GET /api/tx/{hash}/logs
```
- `hash`: 0x-prefixed 64 hex chars
- Returns: decoded event logs with method signatures, parameters, emitting contract

### Token Counters
```
GET /api/token/{address}/counters
```
Returns: total transfer count for a token contract.

### Token Transfers
```
GET /api/address/{address}/transfers?scope=token&cursor={cursor}
```
Returns: all transfers of a specific token.

### Stock Price
```
GET /api/stocks/{symbol}/bars?range={range}
```
- `symbol`: uppercase ticker (e.g. AAPL, TSLA)
- `range`: 1D, 5D, 1M, 6M, 1Y
- Returns: historical price bars and current quote with daily change

### Latest Blocks
```
GET /api/home/blocks
```
Returns: 12 most recent blocks with their transactions.

## Response Format

All endpoints return JSON. Paginated endpoints return:
```json
{
  "items": [...],
  "next": "base64url-cursor-or-null"
}
```

Pass the `next` value as the `cursor` parameter to get the next page.

## Error Handling

- `400`: Invalid address/hash format
- `502`: Upstream API unavailable (robinscan.io could not reach its backend)
- Responses with `{"error": "..."}` indicate a failure

## Example Workflows

1. **"What's the current gas price on Robinhood Chain?"** → Call `/api/stats`, extract `gas_prices`
2. **"Show me recent transactions for 0xabc...def"** → Call `/api/address/0xabc...def/txs`
3. **"How many transfers does this token have?"** → Call `/api/token/0x.../counters`
4. **"What's AAPL trading at?"** → Call `/api/stocks/AAPL/bars?range=1D`
5. **"Decode the logs on this transaction"** → Call `/api/tx/0x.../logs`
