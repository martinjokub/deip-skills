# Financial Setup

Configure currencies and financial settings for accurate cost tracking.

## Base Currency

The workspace base currency is used for:
- Displaying all costs in a consistent format
- Converting item prices to base currency
- Calculating totals and summaries

Set via `update_workspace_settings`:
```json
{ "workspace_id": "uuid", "settings": { "baseCurrency": "EUR" } }
```

## Exchange Rates

FX rates define conversion to base currency. Format: `{ currency: rate_to_base }`

Example for EUR base:
```json
{
  "fx": {
    "EUR": 1,
    "USD": 0.92,
    "GBP": 1.17
  }
}
```

Conversion formula:
```
Amount in Base = Item Amount × fx[item_currency]
```

## Managing Currencies

### Add Currency
```json
{
  "workspace_id": "uuid",
  "currency_code": "JPY",
  "exchange_rate": 0.0061
}
```

### Remove Currency
```json
{
  "workspace_id": "uuid",
  "currency_code": "JPY"
}
```

### List Currencies
Returns all configured currencies with their exchange rates.

## Financial Settings

| Setting | Description | Default |
|---------|-------------|---------|
| `baseCurrency` | Base currency for all calculations | EUR |
| `expectedTools` | Expected tool count (for comparison) | null |
| `expectedAnnualCost` | Expected annual cost (for comparison) | null |
| `amortizeOneTimeYears` | Years to spread one-time costs | 3 |

## One-Time Cost Amortization

One-time costs (perpetual licenses, setup fees) are spread over `amortizeOneTimeYears`:

```
Annual Cost = One-Time Amount ÷ amortizeOneTimeYears
Monthly Cost = Annual Cost ÷ 12
```

## Best Practices

1. Set base currency to your primary reporting currency
2. Update FX rates periodically for accuracy
3. Use `expectedTools` and `expectedAnnualCost` for budget tracking
4. Consider amortization period for major capital expenses
