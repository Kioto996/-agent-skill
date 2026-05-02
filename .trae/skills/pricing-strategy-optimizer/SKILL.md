---
name: pricing-strategy-optimizer
description: Optimizes pricing strategy for cross-border e-commerce by analyzing costs, competitor prices, market conditions, and profit margins. Invoke when setting product prices, planning promotions, or optimizing profitability.
---

# Pricing Strategy Optimizer

## When to Use
Use this skill when:
- Setting initial product prices for new markets
- Adjusting prices based on competition
- Planning promotional pricing
- Optimizing profit margins
- Analyzing pricing viability
- Conducting competitor price analysis

## Pricing Strategy Components

| Strategy | Description | Use Case |
|----------|-------------|----------|
| **Cost-Plus** | Add fixed margin to cost | Baseline pricing |
| **Competitive** | Match or beat competitors | Market penetration |
| **Value-Based** | Price based on perceived value | Premium products |
| **Penetration** | Low initial price for market share | New market entry |
| **Premium** | High price for brand positioning | Luxury/quality goods |
| **Dynamic** | Real-time price adjustment | High competition |

## Usage Workflow

### Phase 1 — Gather Market Data
- [ ] Total product cost (from crossborder-cost-calculator)
- [ ] Logistics costs (from logistics-cost-estimator)
- [ ] Platform fees (from platform-fee-calculator)
- [ ] Competitor prices
- [ ] Target margin requirements
- [ ] Market demand indicators

### Phase 2 — Calculate Base Pricing

```python
class PricingStrategyOptimizer:
    """Optimize pricing for cross-border e-commerce"""

    def __init__(self):
        self.strategies = {}

    def cost_plus_pricing(
        self,
        total_cost: float,
        target_margin: float = 0.30
    ) -> dict:
        """
        Calculate price using cost-plus method

        Args:
            total_cost: Total landed cost
            target_margin: Target profit margin (0.30 = 30%)

        Returns:
            Price breakdown
        """
        selling_price = total_cost / (1 - target_margin)
        profit = selling_price - total_cost
        profit_margin = profit / selling_price

        return {
            'strategy': 'cost_plus',
            'total_cost': total_cost,
            'selling_price': round(selling_price, 2),
            'profit': round(profit, 2),
            'profit_margin': round(profit_margin * 100, 1),
            'markup': round((profit / total_cost) * 100, 1)
        }

    def competitive_pricing(
        self,
        total_cost: float,
        competitor_price: float,
        target_margin: float = 0.15
    ) -> dict:
        """
        Analyze competitive pricing scenario

        Args:
            total_cost: Total landed cost
            competitor_price: Main competitor's price
            target_margin: Minimum acceptable margin

        Returns:
            Analysis and recommendations
        """
        min_price = total_cost / (1 - target_margin)
        is_viable = competitor_price >= min_price
        max_recommended = competitor_price * 1.10  # 10% above

        if is_viable:
            recommended_price = min(competitor_price, max_recommended)
            actual_margin = (recommended_price - total_cost) / recommended_price
            return {
                'strategy': 'competitive',
                'status': 'viable',
                'total_cost': total_cost,
                'competitor_price': competitor_price,
                'recommended_price': round(recommended_price, 2),
                'actual_margin': round(actual_margin * 100, 1),
                'notes': 'Price at or below competitor recommended'
            }
        else:
            return {
                'strategy': 'competitive',
                'status': 'not_viable',
                'total_cost': total_cost,
                'competitor_price': competitor_price,
                'minimum_viable_price': round(min_price, 2),
                'margin_required': target_margin * 100,
                'gap': round(min_price - competitor_price, 2),
                'notes': 'Cannot compete profitably at this price'
            }

    def penetration_pricing(
        self,
        total_cost: float,
        competitor_price: float,
        target_margin: float = 0.10,
        penetration_period_months: int = 6
    ) -> dict:
        """
        Calculate penetration pricing strategy

        Args:
            total_cost: Total landed cost
            competitor_price: Competitor's current price
            target_margin: Reduced margin during penetration
            penetration_period_months: Duration of penetration strategy

        Returns:
            Penetration pricing analysis
        """
        penetration_price = competitor_price * 0.85  # 15% below
        normal_price = competitor_price

        penetration_margin = (penetration_price - total_cost) / penetration_price
        normal_margin = (normal_price - total_cost) / normal_price

        # Total profit during penetration
        estimated_monthly_volume = 100  # Assumed
        penetration_profit = (penetration_price - total_cost) * estimated_monthly_volume * penetration_period_months
        normal_profit = (normal_price - total_cost) * estimated_monthly_volume * 12

        return {
            'strategy': 'penetration',
            'penetration_price': round(penetration_price, 2),
            'penetration_margin': round(penetration_margin * 100, 1),
            'normal_price': round(normal_price, 2),
            'normal_margin': round(normal_margin * 100, 1),
            'total_cost': total_cost,
            'profit_during_penetration': round(penetration_profit, 2),
            'profit_after_penetration': round(normal_profit, 2),
            'breakeven_months': round(
                (normal_price - penetration_price) * estimated_monthly_volume /
                ((normal_margin - penetration_margin) * normal_price * estimated_monthly_volume),
                1
            ) if penetration_margin > 0 else 'N/A',
            'recommendation': 'Viable if willing to invest in market share'
        }
```

### Phase 3 — Profit Optimization

```python
def optimize_profit_margin(
        total_cost: float,
        market_price_range: tuple,
        demand elasticity: float = -1.0
    ) -> dict:
        """
        Find optimal price for maximum profit

        Args:
            total_cost: Total landed cost
            market_price_range: (min_price, max_price) tuple
            demand elasticity: Price sensitivity (-1.0 = unit elastic)

        Returns:
            Profit optimization analysis
        """
        min_price, max_price = market_price_range

        # Calculate profit at different price points
        price_points = []
        for price in [min_price, (min_price + max_price) / 2, max_price]:
            volume_multiplier = (price / max_price) ** abs(demand_elasticity)
            profit = (price - total_cost) * volume_multiplier
            margin = (price - total_cost) / price
            price_points.append({
                'price': round(price, 2),
                'volume_multiplier': round(volume_multiplier, 2),
                'profit': round(profit, 2),
                'margin': round(margin * 100, 1)
            })

        # Find optimal price (simplified)
        optimal_price = (max_price + total_cost) / 2
        optimal_profit = (optimal_price - total_cost) * 1.0

        return {
            'total_cost': total_cost,
            'market_range': market_price_range,
            'price_analysis': price_points,
            'optimal_price': round(optimal_price, 2),
            'optimal_profit': round(optimal_profit, 2),
            'optimal_margin': round((optimal_price - total_cost) / optimal_price * 100, 1),
            'notes': 'Higher prices reduce volume but increase per-unit margin'
        }

    def calculate_breakeven(
        self,
        total_cost: float,
        selling_price: float,
        fixed_costs: float = 0
    ) -> dict:
        """
        Calculate breakeven analysis

        Args:
            total_cost: Variable cost per unit
            selling_price: Selling price per unit
            fixed_costs: Monthly fixed costs

        Returns:
            Breakeven analysis
        """
        if selling_price <= total_cost:
            return {
                'status': 'loss',
                'loss_per_unit': round(total_cost - selling_price, 2),
                'notes': 'Price below cost - immediate loss on each sale'
            }

        contribution_margin = selling_price - total_cost
        breakeven_units = fixed_costs / contribution_margin if fixed_costs > 0 else 1
        breakeven_revenue = breakeven_units * selling_price

        return {
            'status': 'viable',
            'selling_price': selling_price,
            'variable_cost': total_cost,
            'contribution_margin': round(contribution_margin, 2),
            'breakeven_units': round(breakeven_units, 0) if fixed_costs > 0 else 1,
            'breakeven_revenue': round(breakeven_revenue, 2) if fixed_costs > 0 else 'N/A',
            'fixed_costs': fixed_costs
        }
```

### Phase 4 — Promotional Pricing

```python
def promotional_pricing(
        base_price: float,
        total_cost: float,
        promotion_type: str,
        discount_pct: float = 0
    ) -> dict:
        """
        Analyze promotional pricing scenarios

        Args:
            base_price: Current regular price
            total_cost: Total cost per unit
            promotion_type: flash_sale/clearance/seasonal/competitor_match
            discount_pct: Discount percentage

        Returns:
            Promotional pricing analysis
        """
        discount_amount = base_price * (discount_pct / 100)
        promo_price = base_price - discount_amount
        promo_margin = (promo_price - total_cost) / promo_price

        results = {
            'base_price': base_price,
            'promotion_type': promotion_type,
            'discount_pct': discount_pct,
            'promo_price': round(promo_price, 2),
            'total_cost': total_cost,
            'margin_at_promo': round(promo_margin * 100, 1),
            'loss_at_promo': promo_price < total_cost
        }

        # Add recommendations based on promotion type
        if promo_margin < 0:
            results['recommendation'] = 'DO NOT proceed - selling at loss'
        elif promo_margin < 0.10:
            results['recommendation'] = 'Minimum margin - only for inventory clearance'
        elif promo_margin < 0.20:
            results['recommendation'] = 'Acceptable for customer acquisition'
        else:
            results['recommendation'] = 'Healthy margin for promotion'

        return results
```

### Phase 5 — Multi-Platform Strategy

```python
def multi_platform_strategy(
        total_cost: float,
        platform_margins: dict,
        market_positions: dict
    ) -> dict:
        """
        Develop pricing strategy for multiple platforms

        Args:
            total_cost: Total landed cost
            platform_margins: Dict of platform -> target margin
            market_positions: Dict of platform -> market position (premium/standard/budget)

        Returns:
            Multi-platform pricing strategy
        """
        strategy = {}

        for platform, target_margin in platform_margins.items():
            position = market_positions.get(platform, 'standard')
            min_price = total_cost / (1 - target_margin)

            # Adjust based on market position
            if position == 'premium':
                base_price = min_price * 1.15  # 15% premium
            elif position == 'budget':
                base_price = min_price * 0.95  # 5% discount
            else:
                base_price = min_price

            strategy[platform] = {
                'target_margin': target_margin * 100,
                'minimum_price': round(min_price, 2),
                'recommended_price': round(base_price, 2),
                'position': position,
                'profit_per_unit': round(base_price - total_cost, 2)
            }

        # Find best platform by profit
        best_platform = max(strategy.items(), key=lambda x: x[1]['profit_per_unit'])

        return {
            'total_cost': total_cost,
            'platform_strategy': strategy,
            'best_platform': best_platform[0],
            'highest_profit': best_platform[1]['profit_per_unit'],
            'notes': 'Consider different positioning on each platform'
        }
```

## Data Source Fallback

When external data is unavailable, this skill uses:

### Manual Data Input Options

```python
def set_competitor_prices(
        marketplace: str,
        prices: dict
    ):
    """
    Manually set competitor prices for analysis

    Args:
        marketplace: Platform name
        prices: Dict of product_asin -> price

    Example:
        set_competitor_prices('amazon', {
            'B08N5WRWNW': 29.99,
            'B07XGY3GZQ': 34.99
        })
    """
    pass

def set_market_data(
        country: str,
        data: dict
    ):
    """
    Set market conditions manually

    Args:
        country: Country code
        data: Dict with demand_indicators, avg_price, etc.

    Example:
        set_market_data('US', {
            'avg_category_price': 35.00,
            'demand_level': 'high',
            'competition_intensity': 'medium'
        })
    """
    pass

def set_custom_strategy(
        strategy_name: str,
        parameters: dict
    ):
    """
    Define custom pricing strategy

    Args:
        strategy_name: Name of strategy
        parameters: Strategy parameters

    Example:
        set_custom_strategy('my_premium', {
            'min_margin': 0.25,
            'max_discount': 0.15,
            'flash_sale_threshold': 0.20
        })
    """
    pass
```

## Integration with Other Skills

This skill works best when combined with:

- `crossborder-cost-calculator` — Total landed cost input
- `logistics-cost-estimator` — Logistics cost data
- `platform-fee-calculator` — Platform fee calculations
- `cost-audit-data-search` — Competitor price research

### Example Integration

```python
# Full pricing workflow
# 1. Get total costs
from crossborder_cost_calculator import calculate_crossborder_cost
costs = calculate_crossborder_cost(
    product_cost=50,
    domestic_shipping=5,
    international_shipping=15,
    tariff_rate=0.05,
    vat_rate=0.19,
    platform_commission_rate=0.15
)

# 2. Get platform fees
from platform_fee_calculator import PlatformFeeCalculator
calc = PlatformFeeCalculator()
fees = calc.calculate_amazon_fba(49.99, 'electronics', 'standard', 1.2)

# 3. Get competitor prices (manual or from cost-audit-data-search)
competitor_price = 59.99

# 4. Optimize pricing
from pricing_strategy_optimizer import PricingStrategyOptimizer
optimizer = PricingStrategyOptimizer()
result = optimizer.cost_plus_pricing(
    total_cost=costs['total_cost'] + fees['fees']['total_fees'],
    target_margin=0.30
)
```

## Best Practices

1. **Always know your costs**: Base price on true total cost
2. **Monitor competitors**: Adjust prices based on market changes
3. **Consider all costs**: Include returns, refunds, customer service
4. **Plan for promotions**: Budget for promotional pricing impact
5. **Test different strategies**: A/B test when possible
6. **Account for seasonality**: Prices may need adjustment seasonally

## Example Usage

```python
# Basic cost-plus pricing
optimizer = PricingStrategyOptimizer()
result = optimizer.cost_plus_pricing(
    total_cost=85.00,
    target_margin=0.30
)
# Returns: selling_price=121.43, margin=30%

# Competitive analysis
result = optimizer.competitive_pricing(
    total_cost=75.00,
    competitor_price=99.99,
    target_margin=0.20
)

# Profit optimization
result = optimize_profit_margin(
    total_cost=50.00,
    market_price_range=(59.99, 129.99),
    demand_elasticity=-0.8
)

# Breakeven analysis
result = optimizer.calculate_breakeven(
    total_cost=45.00,
    selling_price=59.99,
    fixed_costs=500.00
)
# Returns: breakeven_units=33

# Multi-platform strategy
result = multi_platform_strategy(
    total_cost=50.00,
    platform_margins={'Amazon': 0.25, 'eBay': 0.20, 'Shopee': 0.18},
    market_positions={'Amazon': 'premium', 'eBay': 'standard', 'Shopee': 'budget'}
)
```
