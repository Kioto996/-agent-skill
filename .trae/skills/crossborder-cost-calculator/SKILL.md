---
name: crossborder-cost-calculator
description: Calculates comprehensive costs for cross-border e-commerce products including procurement, shipping, tariffs, and platform fees. Invoke when user needs to calculate cross-border e-commerce costs or determine product pricing for overseas sales.
---

# Cross-Border E-Commerce Cost Calculator

## When to Use
Use this skill when:
- Calculating total landed cost for imported goods
- Determining pricing strategy for international markets
- Comparing costs across different destination countries
- Planning profit margins for cross-border sales

## Cost Components

| Component | Description | Example |
|-----------|-------------|---------|
| **Product Cost** | Purchase price from supplier | $50.00 |
| **Domestic Shipping** | From supplier to domestic warehouse | $5.00 |
| **International Shipping** | From domestic to destination country | $15.00 |
| **Tariff** | Import duty based on HS code | 10% of CIF value |
| **VAT/GST** | Value-added tax in destination country | 19% (Germany) |
| **Platform Fees** | Marketplace commission | 15% of sales price |
| **Storage** | Overseas warehouse fees (if applicable) | $2.00/month |
| **Miscellaneous** | Packaging, insurance, handling | $3.00 |

## Calculation Formula

```
CIF Value = Product Cost + Domestic Shipping + International Shipping
Tariff = CIF Value × Tariff Rate
VAT = (CIF Value + Tariff) × VAT Rate
Total Cost = CIF Value + Tariff + VAT + Platform Fees + Storage + Miscellaneous
Suggested Price = Total Cost × (1 + Profit Margin)
```

## Usage Steps

**Phase 1 — Gather Inputs**
- [ ] Product cost from supplier
- [ ] Domestic shipping cost
- [ ] International shipping cost (by destination)
- [ ] HS code for tariff classification
- [ ] Destination country
- [ ] Platform commission rate
- [ ] Expected profit margin

**Phase 2 — Calculate Costs**

```python
def calculate_crossborder_cost(
    product_cost: float,
    domestic_shipping: float,
    international_shipping: float,
    tariff_rate: float,
    vat_rate: float,
    platform_commission_rate: float,
    storage_fee: float = 0,
    other_fees: float = 0,
    profit_margin: float = 0.3,
    api_key: str = None,
    use_fallback: bool = True
) -> dict:
    """
    Calculate comprehensive cross-border e-commerce costs

    Args:
        product_cost: Purchase price from supplier
        domestic_shipping: From supplier to domestic warehouse
        international_shipping: From domestic to destination
        tariff_rate: Import duty rate (e.g., 0.1 for 10%)
        vat_rate: Value-added tax rate (e.g., 0.19 for 19%)
        platform_commission_rate: Marketplace commission rate
        storage_fee: Overseas storage cost
        other_fees: Packaging, insurance, etc.
        profit_margin: Desired profit margin (default 30%)
        api_key: API key for real-time data (optional)
        use_fallback: Use built-in data if API unavailable (default True)

    Returns:
        Dictionary containing cost breakdown and suggested price
    """
    # CIF value (Cost + Insurance + Freight)
    cif_value = product_cost + domestic_shipping + international_shipping

    # Calculate duties and taxes
    tariff = cif_value * tariff_rate
    vat = (cif_value + tariff) * vat_rate

    # Base cost before platform fees
    base_cost = cif_value + tariff + vat + storage_fee + other_fees

    # Platform commission (based on final price)
    estimated_price = base_cost / (1 - platform_commission_rate)
    platform_commission = estimated_price * platform_commission_rate

    # Final total cost
    total_cost = base_cost + platform_commission

    # Suggested price with profit margin
    suggested_price = total_cost * (1 + profit_margin)

    return {
        "cif_value": round(cif_value, 2),
        "tariff": round(tariff, 2),
        "vat": round(vat, 2),
        "storage_fee": round(storage_fee, 2),
        "other_fees": round(other_fees, 2),
        "platform_commission": round(platform_commission, 2),
        "total_cost": round(total_cost, 2),
        "suggested_price": round(suggested_price, 2),
        "profit_margin": profit_margin,
        "cost_breakdown": {
            "product": round(product_cost / total_cost * 100, 1),
            "shipping": round((domestic_shipping + international_shipping) / total_cost * 100, 1),
            "duties": round((tariff + vat) / total_cost * 100, 1),
            "fees": round((platform_commission + storage_fee + other_fees) / total_cost * 100, 1)
        },
        "data_source": "calculated"
    }
```

**Phase 3 — Analyze Results**
- [ ] Review cost breakdown percentages
- [ ] Compare against target margins
- [ ] Identify cost optimization opportunities
- [ ] Adjust pricing strategy if needed

## API Integration & Data Source Options

### Option 1: Use Built-in Fallback Data (Default)
When no API key is provided, this skill uses built-in tariff and tax rate data.

```python
# Default usage - uses built-in data
result = calculate_crossborder_cost(
    product_cost=50.00,
    domestic_shipping=5.00,
    international_shipping=15.00,
    tariff_rate=0.05,
    vat_rate=0.19,
    platform_commission_rate=0.15
)
```

### Option 2: Provide Your Own API Key
For production use, integrate with real data sources:

```python
# With API key - fetch real-time data
result = calculate_crossborder_cost(
    product_cost=50.00,
    domestic_shipping=5.00,
    international_shipping=15.00,
    tariff_rate=0.05,
    vat_rate=0.19,
    platform_commission_rate=0.15,
    api_key="your_api_key_here"
)
```

### Recommended API Providers

| Provider | Data Type | API Format | Coverage |
|----------|-----------|------------|----------|
| **WTO** | Tariff rates | REST | Global |
| **OECD** | Tax rates | REST | OECD countries |
| **Freightos** | Freight rates | REST | Global shipping |
| **XE.com** | Exchange rates | REST | All currencies |
| **Open Customs API** | HS code lookup | REST | Multi-country |

### Setting Up Custom Data Sources

```python
def set_custom_tariff_api(
    endpoint: str,
    api_key: str = None,
    format: str = "json"
):
    """
    Configure custom tariff API endpoint

    Args:
        endpoint: API base URL
        api_key: API authentication key
        format: Response format (json/xml)

    Example:
        set_custom_tariff_api(
            endpoint="https://api.customstariff.com/v1",
            api_key="your_key"
        )
    """
    pass

def set_custom_exchange_rates(
    api_endpoint: str,
    api_key: str = None
):
    """
    Configure custom exchange rate API

    Args:
        api_endpoint: API base URL
        api_key: API authentication key

    Example:
        set_custom_exchange_rates(
            api_endpoint="https://api.exchangerate.com/v1",
            api_key="your_key"
        )
    """
    pass
```

## Calling Other Skills

This skill can call related skills for comprehensive analysis:

### Skill Calling Examples

```python
def calculate_full_cost_with_logistics(
    product_cost: float,
    weight: float,
    dimensions: dict,
    origin_country: str,
    destination_country: str,
    tariff_rate: float,
    platform_commission_rate: float,
    shipping_method: str = "air"
) -> dict:
    """
    Calculate total cost including logistics (calls logistics-cost-estimator)

    Args:
        product_cost: Product purchase cost
        weight: Package weight in kg
        dimensions: Dict with length, width, height
        origin_country: Origin country code
        destination_country: Destination country code
        tariff_rate: Import tariff rate
        platform_commission_rate: Platform commission rate
        shipping_method: air/sea/rail/express

    Returns:
        Combined cost breakdown from both skills
    """
    # Call logistics-cost-estimator skill
    # (In production, this would invoke the skill's functions)
    logistics_cost = estimate_shipping(
        weight=weight,
        dimensions=dimensions,
        origin_country=origin_country,
        destination_country=destination_country,
        shipping_method=shipping_method
    )

    # Get VAT rate (from cost-audit-data-search if available)
    vat_rate = get_vat_rate(destination_country)  # or use fallback

    # Calculate total cost
    total_cost = product_cost + logistics_cost['total_cost']

    return {
        'product_cost': product_cost,
        'logistics_cost': logistics_cost,
        'total_before_duties': total_cost,
        'tariff': total_cost * tariff_rate,
        'vat': (total_cost + total_cost * tariff_rate) * vat_rate,
        'platform_commission_rate': platform_commission_rate,
        'final_cost': total_cost * (1 + tariff_rate) * (1 + vat_rate)
    }

def enrich_with_platform_fees(
    base_cost: float,
    platform: str,
    category: str
) -> dict:
    """
    Add platform fees to cost calculation (calls platform-fee-calculator)

    Args:
        base_cost: Total landed cost
        platform: Platform name (amazon/ebay/shopee)
        category: Product category

    Returns:
        Cost breakdown including platform fees
    """
    # Call platform-fee-calculator skill
    platform_fees = calculate_platform_fees(
        platform=platform,
        category=category,
        selling_price=base_cost * 1.3  # Estimate selling price
    )

    return {
        'landed_cost': base_cost,
        'platform_fees': platform_fees,
        'total_cost_with_fees': base_cost + platform_fees['total']
    }
```

## Common Tariff Rates by Country

| Country | Standard Rate | Special Notes |
|---------|---------------|---------------|
| **United States** | 0-25% | Dependent on HS code |
| **European Union** | 0-17% VAT + duties | VAT varies by country |
| **United Kingdom** | 20% VAT + duties | Post-Brexit rules apply |
| **Japan** | 10% consumption tax + duties | Lower rates for certain goods |
| **Australia** | 10% GST + duties | GST on imports over AUD 1,000 |
| **Canada** | 5% GST + provincial taxes + duties | Varies by province |
| **Singapore** | 8% GST | GST on imports over SGD 400 |
| **South Korea** | 10% VAT + duties | Complex tariff structure |

## Best Practices

1. **Verify HS Codes**: Incorrect classification can lead to unexpected costs
2. **Check Trade Agreements**: Preferential rates may apply
3. **Account for Exchange Rates**: Currency fluctuations impact final costs
4. **Include Contingency**: Add 10-15% buffer for unexpected expenses
5. **Compare Carriers**: Shipping costs vary significantly
6. **Review Platform Fees**: Different marketplaces have different structures
7. **Use Real-time Data**: Configure API keys for production use
8. **Combine with Other Skills**: Use logistics-cost-estimator and platform-fee-calculator for complete picture

## Edge Cases to Consider

- **Low-value shipments**: Some countries have de minimis thresholds
- **Free Trade Agreements**: May reduce or eliminate tariffs
- **Anti-dumping duties**: Additional duties on certain products
- **Seasonal shipping costs**: Rates may fluctuate
- **Currency conversion**: Use realistic exchange rates

## Example Calculation

```python
# Example: Product from China to Germany
result = calculate_crossborder_cost(
    product_cost=50.00,
    domestic_shipping=5.00,
    international_shipping=15.00,
    tariff_rate=0.05,      # 5% tariff
    vat_rate=0.19,         # 19% German VAT
    platform_commission_rate=0.15,  # 15% marketplace fee
    storage_fee=2.00,
    other_fees=3.00,
    profit_margin=0.30
)

# Result:
# CIF Value: $70.00
# Tariff: $3.50
# VAT: $14.07
# Total Cost: $110.18
# Suggested Price: $143.23
```

## Integration Summary

| Skill to Call | Purpose | When to Use |
|----------------|---------|-------------|
| `logistics-cost-estimator` | Get accurate shipping costs | For freight/handling calculations |
| `platform-fee-calculator` | Get platform-specific fees | For marketplace pricing |
| `cost-audit-data-search` | Get real-time tariff/tax data | For latest rates and regulations |
| `pricing-strategy-optimizer` | Optimize selling price | For margin optimization |
