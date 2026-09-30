# Financial Fields

Detailed guide to pricing and cost fields in DEIP items.

## Core Pricing Fields

| Field | Type | Description |
|-------|------|-------------|
| `priceValue` | number | Base price amount |
| `currency` | string | Currency code (EUR, USD, GBP, etc.) |
| `billingCycle` | string | Billing frequency |
| `quantity` | number | Number of units/seats/licenses |
| `discountPct` | number | Discount percentage (0-100) |
| `vatPct` | number | VAT/tax percentage |
| `isPaid` | boolean | Whether item is paid (vs free) |
| `include` | boolean | Include in calculations |

## Billing Cycles

| Cycle | Annual Multiplier |
|-------|-------------------|
| `monthly` | × 12 |
| `quarterly` | × 4 |
| `annual` | × 1 |
| `weekly` | × 52 |
| `daily` | × 365 |
| `biennial` | × 0.5 |
| `one_time` | ÷ amortizeOneTimeYears |
| `on_demand` | × 12 (estimated) |

## Final Price Formula

```
Final Price = (priceValue × quantity) × (1 + vatPct/100) × (1 - discountPct/100)
```

## Currency Conversion

All amounts display in workspace base currency:
```
Amount in Base = Amount × fx[itemCurrency]
```

## One-Time Cost Amortization

One-time costs spread over `settings.amortizeOneTimeYears`:
```
Annual Cost = One-Time Amount ÷ amortizeOneTimeYears
```

## Payment Tracking

| Field | Description |
|-------|-------------|
| `nextPaymentDate` | When next payment is due |
| `lastPaymentDate` | When last payment was made |
| `paymentMethod` | How payment is made |
| `accountNumber` | Account/reference number |

## Example Configurations

### Monthly SaaS
```json
{
  "priceValue": 10,
  "currency": "USD",
  "billingCycle": "monthly",
  "quantity": 50,
  "isPaid": true
}
// Annual cost: $10 × 50 × 12 = $6,000
```

### Annual License with Discount
```json
{
  "priceValue": 1200,
  "currency": "EUR",
  "billingCycle": "annual",
  "quantity": 1,
  "discountPct": 20
}
// Annual cost: €1,200 × (1 - 0.20) = €960
```

### One-Time Purchase
```json
{
  "priceValue": 9000,
  "currency": "USD",
  "billingCycle": "one_time",
  "quantity": 1
}
// Annual cost (3yr amortize): $9,000 ÷ 3 = $3,000/year
```

## Include Flag

Set `include: false` to exclude items from:
- Total cost calculations
- Summary statistics
- Budget comparisons

Useful for:
- Legacy/deprecated tools
- Free alternatives being evaluated
- Tools being phased out
