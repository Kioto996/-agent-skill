---
name: logistics-cost-estimator
description: Estimates cross-border logistics costs including shipping, customs clearance, last-mile delivery, and warehousing. Invoke when calculating shipping costs, comparing carriers, or optimizing logistics expenses.
---

# Logistics Cost Estimator

## When to Use
Use this skill when:
- Calculating shipping costs for international packages
- Comparing freight forwarders and carriers
- Estimating FBA or overseas warehouse costs
- Planning inventory and storage strategy
- Optimizing shipping routes and methods

## Logistics Cost Components

| Component | Description | Typical Range |
|-----------|-------------|---------------|
| **Origin Handling** | Pickup, processing at origin warehouse | $0.5-5.0 |
| **Air Freight** | From origin country to destination | $3-15/kg |
| **Sea Freight** | From origin country to destination | $0.5-3/kg |
| **Rail Freight** | China-Europe rail (where applicable) | $1-5/kg |
| **Customs Clearance** | Import/export duties and taxes | $50-500 |
| **Last Mile Delivery** | Final delivery to customer | $2-15 |
| **FBA Fees** | Amazon fulfillment fees | $3-10/item |
| **Storage Fees** | Monthly warehousing costs | $0.5-2.5/cubic ft |

## Shipping Method Comparison

| Method | Speed | Cost | Best For |
|--------|-------|------|----------|
| **Express (DHL/FedEx)** | 3-5 days | High | Urgent, small packages |
| **Air Freight** | 7-15 days | Medium-High | Regular inventory |
| **Sea Freight (FCL)** | 25-35 days | Lowest | Large volume, non-urgent |
| **Sea Freight (LCL)** | 25-40 days | Low | Consolidated shipments |
| **China-Europe Rail** | 15-25 days | Medium | Europe-bound goods |
| **Road Freight** | 20-30 days | Medium | Neighboring countries |

## Usage Workflow

### Phase 1 — Gather Shipping Details
- [ ] Package weight and dimensions
- [ ] Origin and destination countries/cities
- [ ] Shipping method preference
- [ ] Special requirements (fragile, hazmat, etc.)
- [ ] Volume and frequency estimates

### Phase 2 — Calculate Costs

```python
class LogisticsCostEstimator:
    """Estimate logistics costs for cross-border shipping"""
    
    def __init__(self, api_key: str = None, use_mock: bool = True):
        """
        Initialize estimator

        Args:
            api_key: API key for logistics provider (optional)
            use_mock: Use mock data if no API key (for testing)
        """
        self.api_key = api_key
        self.use_mock = use_mock or not api_key

    def estimate_shipping(
        self,
        weight: float,
        dimensions: dict,
        origin_country: str,
        destination_country: str,
        shipping_method: str = "air"
    ) -> dict:
        """
        Estimate shipping cost

        Args:
            weight: Package weight in kg
            dimensions: Dict with length, width, height in cm
            origin_country: ISO country code
            destination_country: ISO country code
            shipping_method: air/sea/rail/express

        Returns:
            Shipping cost breakdown
        """
        if self.use_mock:
            return self._mock_estimate(weight, dimensions, origin_country, destination_country, shipping_method)

        # Real API call would go here
        return self._call_logistics_api(weight, dimensions, origin_country, destination_country, shipping_method)

    def _mock_estimate(
        self,
        weight: float,
        dimensions: dict,
        origin_country: str,
        destination_country: str,
        shipping_method: str
    ) -> dict:
        """Generate realistic mock estimate"""
        volumetric_weight = (dimensions['length'] * dimensions['width'] * dimensions['height']) / 6000
        chargeable_weight = max(weight, volumetric_weight)

        rates = {
            'express': {'per_kg': 12.0, 'base': 50},
            'air': {'per_kg': 6.0, 'base': 30},
            'sea': {'per_kg': 1.5, 'base': 150},
            'rail': {'per_kg': 3.0, 'base': 200}
        }

        rate = rates.get(shipping_method, rates['air'])
        freight_cost = chargeable_weight * rate['per_kg'] + rate['base']

        # Add fuel surcharge (15-25%)
        fuel_surcharge = freight_cost * 0.18

        # Add security surcharge
        security_surcharge = freight_cost * 0.05

        # Destination charges
        dest_charges = self._estimate_destination_charges(destination_country)

        return {
            'weight': weight,
            'volumetric_weight': round(volumetric_weight, 2),
            'chargeable_weight': round(chargeable_weight, 2),
            'dimensions': dimensions,
            'shipping_method': shipping_method,
            'cost_breakdown': {
                'freight': round(freight_cost, 2),
                'fuel_surcharge': round(fuel_surcharge, 2),
                'security_surcharge': round(security_surcharge, 2),
                'destination_charges': dest_charges
            },
            'total_cost': round(freight_cost + fuel_surcharge + security_surcharge + dest_charges, 2),
            'currency': 'USD',
            'estimated_days': self._get_transit_days(shipping_method, origin_country, destination_country),
            'data_source': 'mock_data'
        }

    def _estimate_destination_charges(self, country_code: str) -> float:
        """Estimate destination country charges"""
        charges = {
            'US': 45,  # Customs clearance + last mile
            'DE': 55,  # EU clearance + delivery
            'GB': 50,
            'JP': 60,
            'AU': 65,
            'CA': 55,
            'FR': 55,
            'IT': 55,
            'ES': 50,
            'MX': 70,
            'BR': 80
        }
        return charges.get(country_code, 50)

    def _get_transit_days(self, method: str, origin: str, dest: str) -> dict:
        """Get estimated transit time range"""
        transit_times = {
            'express': {'min': 3, 'max': 5},
            'air': {'min': 7, 'max': 12},
            'sea': {'min': 25, 'max': 35},
            'rail': {'min': 15, 'max': 22}
        }
        return transit_times.get(method, {'min': 10, 'max': 20})
```

### Phase 3 — Compare Options

```python
def compare_shipping_options(
    weight: float,
    dimensions: dict,
    origin_country: str,
    destination_country: str
) -> dict:
    """
    Compare all shipping methods

    Args:
        weight: Package weight in kg
        dimensions: Package dimensions
        origin_country: Origin country code
        destination_country: Destination country code

    Returns:
        Comparison of all shipping methods
    """
    estimator = LogisticsCostEstimator()
    methods = ['express', 'air', 'sea', 'rail']
    results = {}

    for method in methods:
        estimate = estimator.estimate_shipping(
            weight, dimensions, origin_country, destination_country, method
        )
        results[method] = {
            'total_cost': estimate['total_cost'],
            'transit_days': estimate['estimated_days'],
            'cost_per_day': round(estimate['total_cost'] / estimate['estimated_days']['min'], 2)
        }

    return results
```

### Phase 4 — FBA Cost Estimation

```python
def estimate_fba_costs(
    item_size: str,
    weight: float,
    storage_months: int = 3,
    monthly_volume: int = 100
) -> dict:
    """
    Estimate Amazon FBA costs

    Args:
        item_size: standard/oversize (standard < 18x14x8 inches, < 20 lbs)
        weight: Item weight in lbs
        storage_months: Expected storage duration
        monthly_volume: Monthly units sold

    Returns:
        FBA cost breakdown
    """
    fulfillment_fees = {
        'standard': {
            '1-2 oz': 3.22,
            '3-8 oz': 3.40,
            '9-15 oz': 3.58,
            '1-2 lb': 4.76,
            '2-3 lb': 5.21
        },
        'oversize': {
            '1-2 lb': 6.10,
            '2-3 lb': 6.50,
            '3-4 lb': 7.00,
            '4-5 lb': 7.60
        }
    }

    storage_rates = {
        'standard': {'jan-sep': 0.78, 'oct-dec': 2.40},  # per cubic ft
        'oversize': {'jan-sep': 0.56, 'oct-dec': 1.40}
    }

    # Find applicable fee tier
    fee_tier = '1-2 oz'
    if weight > 3:
        fee_tier = '1-2 lb'
    elif weight > 2:
        fee_tier = '9-15 oz'
    elif weight > 0.5:
        fee_tier = '3-8 oz'

    size_category = 'standard' if item_size == 'standard' else 'oversize'
    fulfillment_fee = fulfillment_fees.get(size_category, {}).get(fee_tier, 4.00)

    # Calculate storage costs (estimate 1 cubic ft per 10 units)
    cubic_feet_per_unit = 0.1
    total_storage = cubic_feet_per_unit * monthly_volume * storage_months
    storage_fee = total_storage * storage_rates[size_category]['jan-sep']

    # Referral fee (typically 8-15% depending on category, using 15% as estimate)
    referral_fee_estimate = 2.00 * 0.15  # Assuming average item value

    return {
        'fulfillment_fee': fulfillment_fee,
        'storage_fee_monthly': round(storage_fee / storage_months, 2),
        'storage_fee_total': round(storage_fee, 2),
        'referral_fee_estimate': referral_fee_estimate,
        'total_per_unit': round(fulfillment_fee + (storage_fee / monthly_volume / storage_months) + referral_fee_estimate, 2),
        'total_monthly': round((fulfillment_fee + referral_fee_estimate) * monthly_volume + storage_fee, 2)
    }
```

## Data Source Fallback

When API is unavailable, this skill uses built-in mock data. For production use, integrate with:

| Provider | API Type | Coverage |
|----------|----------|----------|
| **DHL** | REST API | Global express |
| **FedEx** | REST API | Global freight |
| **UPS** | REST API | Global shipping |
| **China Post** | XML API | China origin |
| **Flexport** | REST API | Sea/air freight |
| **Freightos** | REST API | Multi-carrier comparison |

### Using Custom Data Sources

```python
def set_custom_rates(
    carrier: str,
    rates: dict,
    origin_country: str = None
):
    """
    Set custom rates for a carrier or route

    Args:
        carrier: Carrier name
        rates: Dict with rate tiers, e.g., {
            'per_kg': 5.50,
            'base': 25,
            'fuel_surcharge_pct': 0.18
        }
        origin_country: Optional origin country filter

    Example:
        set_custom_rates('my_carrier', {
            'per_kg': 4.50,
            'base': 30,
            'fuel_surcharge_pct': 0.15
        })
    """
    # Store in skill's internal cache
    pass
```

## Best Practices

1. **Use volumetric weight**: Airlines charge by volumetric weight (L×W×H/5000 or /6000)
2. **Consolidate shipments**: LCL is cheaper than individual packages
3. **Plan for peak season**: Rates increase 30-50% during Q4
4. **Consider total cost**: Cheapest shipping isn't always best (consider transit time, reliability)
5. **Build in buffer**: Add 10-15% for unexpected charges
6. **Monitor carrier performance**: Track delivery times and damage rates

## Integration with Other Skills

This skill integrates with:

- `crossborder-cost-calculator` — For total landed cost calculation
- `cost-audit-data-search` — For real-time rate updates
- `pricing-strategy-optimizer` — For margin optimization

## Example Usage

```python
# Quick shipping estimate
estimator = LogisticsCostEstimator()
result = estimator.estimate_shipping(
    weight=5.5,
    dimensions={'length': 30, 'width': 20, 'height': 15},
    origin_country='CN',
    destination_country='US',
    shipping_method='air'
)

# Compare all methods
comparison = compare_shipping_options(
    weight=10,
    dimensions={'length': 40, 'width': 30, 'height': 20},
    origin_country='CN',
    destination_country='DE'
)

# FBA cost estimation
fba_costs = estimate_fba_costs(
    item_size='standard',
    weight=1.5,
    storage_months=2,
    monthly_volume=200
)
```
