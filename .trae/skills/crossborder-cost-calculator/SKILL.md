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
    profit_margin: float = 0.3
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
        }
    }
```

**Phase 3 — Analyze Results**
- [ ] Review cost breakdown percentages
- [ ] Compare against target margins
- [ ] Identify cost optimization opportunities
- [ ] Adjust pricing strategy if needed

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

## Edge Cases to Consider

- **Low-value shipments**: Some countries have de minimis thresholds
- **Free Trade Agreements**: May reduce or eliminate tariffs
- **Anti-dumping duties**: Additional duties on certain products
- **Seasonal shipping costs**: Rates may fluctuate
- **Currency conversion**: Use realistic exchange rates
