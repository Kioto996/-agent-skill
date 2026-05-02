---
name: platform-fee-calculator
description: Calculates fees for major e-commerce platforms (Amazon, eBay, Shopee, etc.) including commissions, fulfillment fees, and promotional costs. Invoke when calculating platform costs, comparing marketplaces, or planning pricing strategy.
---

# Platform Fee Calculator

## When to Use
Use this skill when:
- Calculating fees for different e-commerce platforms
- Comparing profitability across marketplaces
- Planning pricing strategy with fee considerations
- Estimating Amazon FBA or eBay fees
- Budgeting for Shopee or Lazada operations

## Supported Platforms

| Platform | Commission | Fulfillment | Notes |
|----------|------------|-------------|-------|
| **Amazon** | 8-15% | $3-10/item | Category-dependent |
| **eBay** | 10-15% | $3-15/package | + payment processing |
| **Shopee** | 5-8% | $1-5/package | Regional variation |
| **Lazada** | 5-8% | $1-5/package | SEA focused |
| **Walmart** | 6-15% | $3-10/item | Invite only |
| **Target** | 15% | $3-8/item | Limited access |
| **Temu** | 5-12% | Included | Newer platform |

## Fee Components by Platform

### Amazon Fee Structure

| Fee Type | Rate | Notes |
|----------|------|-------|
| **Referral Fee** | 6-15% | Category dependent |
| **Closing Fee** | $1.80 | Media items |
| **FBA Pick & Pack** | $3.22-6.50 | By size/weight |
| **FBA Weight Handle** | $0.55-1.00+ | By weight tier |
| **Monthly Storage** | $0.78-2.40/cu.ft | Oct-Dec higher |
| **Long-term Storage** | $6.90/cu.ft | 365+ days |
| **Returns Processing** | $2.00-6.50 | By category |
| **Advertising (PPC)** | Variable | Typically 10-30% of sales |

### eBay Fee Structure

| Fee Type | Rate | Notes |
|----------|------|-------|
| **Final Value Fee** | 10-15% | Category dependent |
| **Payment Processing** | 2.9% + $0.30 | PayPal/eBay Payments |
| **Listing Fee** | $0.35 | After 50 free/month |
| **International** | 1.5% extra | Cross-border sales |
| **Promoted Listings** | Variable | 1-10% of sale |

## Usage Workflow

### Phase 1 — Select Platform and Category
- [ ] Choose target marketplace
- [ ] Identify product category
- [ ] Determine product size/weight
- [ ] Plan fulfillment method (FBM vs FBA)

### Phase 2 — Calculate Platform Fees

```python
class PlatformFeeCalculator:
    """Calculate e-commerce platform fees"""

    def __init__(self, custom_rates: dict = None):
        """
        Initialize calculator

        Args:
            custom_rates: Override default rates with custom data
        """
        self.custom_rates = custom_rates or {}

    def calculate_amazon_fba(
        self,
        product_price: float,
        category: str,
        size: str,
        weight: float,
        storage_months: int = 1
    ) -> dict:
        """
        Calculate total Amazon FBA fees

        Args:
            product_price: Your selling price
            category: Product category
            size: standard/oversize
            weight: Weight in lbs
            storage_months: Expected storage duration

        Returns:
            Fee breakdown
        """
        # Referral fees by category
        referral_rates = {
            'electronics': 0.08,
            'home': 0.12,
            'clothing': 0.17,
            'beauty': 0.08,
            'toys': 0.12,
            'sports': 0.12,
            'books': 0.15,
            'other': 0.15
        }
        referral_rate = referral_rates.get(category.lower(), 0.15)

        # FBA fulfillment fees by size tier
        fulfillment_fees = {
            'standard_1lb': 4.76,
            'standard_2lb': 5.21,
            'standard_3lb': 5.69,
            'oversize_5lb': 9.78,
            'oversize_10lb': 16.50,
            'oversize_70lb': 100.00
        }

        # Determine size tier
        if size == 'standard':
            if weight <= 1:
                fulfillment_key = 'standard_1lb'
            elif weight <= 2:
                fulfillment_key = 'standard_2lb'
            else:
                fulfillment_key = 'standard_3lb'
        else:
            if weight <= 5:
                fulfillment_key = 'oversize_5lb'
            elif weight <= 10:
                fulfillment_key = 'oversize_10lb'
            else:
                fulfillment_key = 'oversize_70lb'

        fulfillment_fee = fulfillment_fees.get(fulfillment_key, 5.21)

        # Calculate storage fee (cubic feet estimate)
        cubic_feet = 0.1  # Assume average
        storage_rate = 0.78 if storage_months < 12 else 2.40
        storage_fee = cubic_feet * storage_rate

        # Referral fee
        referral_fee = product_price * referral_rate

        # Calculate total fees
        total_fees = referral_fee + fulfillment_fee + storage_fee

        return {
            'platform': 'Amazon FBA',
            'selling_price': product_price,
            'fees': {
                'referral_fee': round(referral_fee, 2),
                'fulfillment_fee': round(fulfillment_fee, 2),
                'storage_fee': round(storage_fee, 2),
                'total_fees': round(total_fees, 2)
            },
            'net_proceeds': round(product_price - total_fees, 2),
            'fee_percentage': round((total_fees / product_price) * 100, 1),
            'breakdown': {
                'referral_pct': referral_rate * 100,
                'fulfillment_pct': round((fulfillment_fee / product_price) * 100, 1),
                'storage_pct': round((storage_fee / product_price) * 100, 1)
            }
        }

    def calculate_ebay(
        self,
        product_price: float,
        category: str,
        weight: float,
        is_international: bool = False
    ) -> dict:
        """
        Calculate eBay fees

        Args:
            product_price: Selling price
            category: Product category
            weight: Package weight in lbs
            is_international: Cross-border sale

        Returns:
            Fee breakdown
        """
        # Final value fees by category
        fv_rates = {
            'electronics': 0.12,
            'home': 0.12,
            'clothing': 0.13,
            'collectibles': 0.15,
            'other': 0.13
        }
        fv_rate = fv_rates.get(category.lower(), 0.13)

        # International fee
        intl_fee = 0.015 if is_international else 0

        # Payment processing
        payment_rate = 0.029
        payment_fixed = 0.30

        # Calculate fees
        fv_fee = product_price * (fv_rate + intl_fee)
        payment_fee = product_price * payment_rate + payment_fixed
        total_fees = fv_fee + payment_fee

        return {
            'platform': 'eBay',
            'selling_price': product_price,
            'fees': {
                'final_value_fee': round(fv_fee, 2),
                'payment_processing': round(payment_fee, 2),
                'total_fees': round(total_fees, 2)
            },
            'net_proceeds': round(product_price - total_fees, 2),
            'fee_percentage': round((total_fees / product_price) * 100, 1)
        }

    def calculate_shopee(
        self,
        product_price: float,
        category: str,
        is_flash_sale: bool = False,
        use_coin: bool = False
    ) -> dict:
        """
        Calculate Shopee fees (Commission + Fulfillment)

        Args:
            product_price: Selling price
            category: Product category
            is_flash_sale: Flash sale promotion
            use_coin: Using Shopee coins program

        Returns:
            Fee breakdown
        """
        # Commission by category
        commission_rates = {
            'electronics': 0.06,
            'fashion': 0.08,
            'home': 0.07,
            'beauty': 0.08,
            'sports': 0.07,
            'other': 0.08
        }
        commission_rate = commission_rates.get(category.lower(), 0.08)

        # Transaction fee
        transaction_rate = 0.02

        # Flash sale commission boost
        flash_sale_boost = 0.02 if is_flash_sale else 0

        # Coin cashback (from seller)
        coin_rate = 0.02 if use_coin else 0

        # Calculate fees
        commission = product_price * (commission_rate + flash_sale_boost)
        transaction = product_price * transaction_rate
        coins = product_price * coin_rate
        total_fees = commission + transaction + coins

        return {
            'platform': 'Shopee',
            'selling_price': product_price,
            'fees': {
                'commission': round(commission, 2),
                'transaction_fee': round(transaction, 2),
                'coin_cashback': round(coins, 2),
                'total_fees': round(total_fees, 2)
            },
            'net_proceeds': round(product_price - total_fees, 2),
            'fee_percentage': round((total_fees / product_price) * 100, 1),
            'promotions': {
                'flash_sale': is_flash_sale,
                'coin_program': use_coin
            }
        }
```

### Phase 3 — Compare Platforms

```python
def compare_platforms(
    product_price: float,
    category: str,
    size: str = 'standard',
    weight: float = 1.0,
    is_international: bool = False
) -> dict:
    """
    Compare fees across multiple platforms

    Args:
        product_price: Selling price
        category: Product category
        size: standard/oversize
        weight: Weight in lbs
        is_international: Cross-border consideration

    Returns:
        Platform comparison
    """
    calc = PlatformFeeCalculator()

    amazon = calc.calculate_amazon_fba(product_price, category, size, weight)
    ebay = calc.calculate_ebay(product_price, category, weight, is_international)
    shopee = calc.calculate_shopee(product_price, category)

    return {
        'selling_price': product_price,
        'platforms': {
            'Amazon FBA': amazon,
            'eBay': ebay,
            'Shopee': shopee
        },
        'recommendation': min(
            [('Amazon FBA', amazon['net_proceeds']),
             ('eBay', ebay['net_proceeds']),
             ('Shopee', shopee['net_proceeds'])],
            key=lambda x: x[1]
        )[0]
    }
```

## Promotional Costs

```python
def calculate_promotional_costs(
    platform: str,
    base_price: float,
    promotion_type: str,
    discount_pct: float = 0
) -> dict:
    """
    Calculate costs of promotional activities

    Args:
        platform: Platform name
        base_price: Regular selling price
        promotion_type: flash_sale/deal/coupon/ads
        discount_pct: Discount percentage (0-100)

    Returns:
        Promotional cost breakdown
    """
    costs = {}

    if promotion_type == 'flash_sale':
        # Flash sale fees (Amazon)
        flash_fee = base_price * 0.005  # $0.005 per unit
        reduced_price = base_price * (1 - discount_pct/100)
        costs = {
            'flash_sale_fee': flash_fee,
            'discount_impact': base_price - reduced_price,
            'total_cost': flash_fee + (base_price - reduced_price)
        }

    elif promotion_type == 'ads':
        # PPC advertising costs
        ad_spend = base_price * 0.15  # Assume 15% ACOS
        costs = {
            'estimated_ad_spend': ad_spend,
            'notes': 'Based on typical 15-30% ACOS'
        }

    elif promotion_type == 'coupon':
        # Coupon costs
        coupon_value = base_price * (discount_pct/100)
        costs = {
            'coupon_cost': coupon_value,
            'net_price_after_coupon': base_price - coupon_value
        }

    return {
        'platform': platform,
        'promotion_type': promotion_type,
        'base_price': base_price,
        'promotional_costs': costs
    }
```

## Data Source Fallback

When API is unavailable, this skill uses built-in fee schedules. For accurate data:

### Recommended Data Sources

| Platform | Official Source | Update Frequency |
|----------|-----------------|------------------|
| Amazon | [Seller Central](https://sellercentral.amazon.com) | Monthly |
| eBay | [Seller Center](https://www.ebay.com/sellerresources) | Quarterly |
| Shopee | [Seller Hub](https://shopee.com/buyer/login) | Regional |
| Lazada | [Seller Center](https://sellercenter.lazada.com) | Regional |

### Using Custom Fee Data

```python
def set_custom_platform_fees(
    platform: str,
    fees: dict,
    effective_date: str = None
):
    """
    Set custom fee structure for a platform

    Args:
        platform: Platform name (amazon/ebay/shopee)
        fees: Fee structure dict, e.g., {
            'commission': 0.10,
            'fulfillment': 4.50,
            'storage': 0.75
        }
        effective_date: When these fees take effect

    Example:
        set_custom_platform_fees('amazon', {
            'commission': 0.08,
            'fulfillment_standard': 3.99,
            'storage': 0.85
        })
    """
    pass  # Store in skill's internal cache
```

## Best Practices

1. **Account for all fees**: Include referral, fulfillment, storage, returns
2. **Consider hidden costs**: Return rates, customer service, packaging
3. **Factor in promotions**: Flash sales and ads reduce margins
4. **Compare total cost**: Cheapest platform isn't always best
5. **Plan for volume**: Fees often have tiered structures
6. **Monitor fee changes**: Platforms update rates quarterly

## Integration with Other Skills

This skill integrates with:

- `crossborder-cost-calculator` — Total cost calculation
- `logistics-cost-estimator` — Combined logistics + platform costs
- `cost-audit-data-search` — Real-time fee updates
- `pricing-strategy-optimizer` — Margin optimization

## Example Usage

```python
# Calculate Amazon FBA fees
calc = PlatformFeeCalculator()
result = calc.calculate_amazon_fba(
    product_price=49.99,
    category='electronics',
    size='standard',
    weight=1.2,
    storage_months=2
)

# Compare all platforms
comparison = compare_platforms(
    product_price=39.99,
    category='home',
    size='standard',
    weight=2.0
)

# Calculate promotional costs
promo = calculate_promotional_costs(
    platform='amazon',
    base_price=29.99,
    promotion_type='flash_sale',
    discount_pct=20
)
```
