---
name: cost-audit-data-search
description: Real-time data search, update, and analysis for cost audit specialists. Retrieves authoritative data including tax laws, tariff policies, industry benchmarks, and market price fluctuations. Invoke when cost audit specialists need to search, update, and analyze cost-related data in real-time.
---

# Cost Audit Data Search System

## When to Use
Use this skill when:
- Researching tariff rates for cross-border transactions
- Verifying tax compliance across jurisdictions
- Analyzing cost impact factors for imported goods
- Detecting compliance risks in international trade
- Comparing industry cost benchmarks
- Monitoring market price fluctuations

## Authoritative Data Sources

| Data Type | Sources | Update Frequency |
|-----------|---------|------------------|
| **Tariff Data** | WTO, National Customs Websites | Real-time |
| **Tax Rates** | OECD, National Tax Agencies | Daily |
| **Laws & Regulations** | Government Official Channels | Real-time |
| **Exchange Rates** | Central Banks, Reuters, Bloomberg | Real-time |
| **Commodity Prices** | LME, SHFE, NYMEX | Real-time |
| **Logistics Costs** | IATA, DHL, UPS | Daily |

## Data Source Fallback Mechanism

When external APIs are unavailable, this skill uses built-in fallback data. This ensures the skill remains functional without external dependencies.

### Fallback Strategy

```python
class CostAuditDataClient:
    """Client with automatic fallback support"""

    def __init__(
        self,
        api_key: str = None,
        use_fallback: bool = True,
        fallback_source: str = "built-in"
    ):
        """
        Initialize client with fallback support

        Args:
            api_key: API key for premium data (optional)
            use_fallback: Use fallback when API unavailable (default True)
            fallback_source: 'built-in', 'custom', or 'manual'
        """
        self.api_key = api_key
        self.use_fallback = use_fallback
        self.fallback_source = fallback_source
        self._fallback_data = self._load_fallback_data()

    def search_tariff(self, country_code: str, hs_code: str) -> dict:
        """
        Search tariff rate with automatic fallback

        Args:
            country_code: ISO 3166-1 alpha-2 country code
            hs_code: Harmonized System code (6-10 digits)

        Returns:
            Tariff information (from API or fallback)
        """
        try:
            # Try API first
            if self.api_key:
                response = self._call_api("/tariff", country_code, hs_code)
                return self._format_tariff_response(response, source="api")
        except APIError:
            pass

        # Fallback to built-in data
        if self.use_fallback:
            return self._get_fallback_tariff(country_code, hs_code)

        raise DataSourceError("No API key provided and fallback disabled")

    def _get_fallback_tariff(self, country_code: str, hs_code: str) -> dict:
        """Get tariff from fallback data"""
        # Built-in fallback tariff data by country
        fallback_tariffs = {
            'US': {'standard_rate': 0.05, 'notes': 'General rate - verify HS code'},
            'DE': {'standard_rate': 0.04, 'notes': 'EU common external tariff'},
            'GB': {'standard_rate': 0.05, 'notes': 'UK global tariff'},
            'JP': {'standard_rate': 0.03, 'notes': 'Japan tariff rate'},
            'AU': {'standard_rate': 0.05, 'notes': 'Australian tariff'},
            'CA': {'standard_rate': 0.05, 'notes': 'MFN rate'},
            'CN': {'standard_rate': 0.06, 'notes': 'China tariff'},
            'FR': {'standard_rate': 0.04, 'notes': 'EU common tariff'},
            'IT': {'standard_rate': 0.04, 'notes': 'EU common tariff'},
            'ES': {'standard_rate': 0.04, 'notes': 'EU common tariff'},
            'MX': {'standard_rate': 0.07, 'notes': 'Mexico tariff'},
            'BR': {'standard_rate': 0.08, 'notes': 'Brazil tariff'}
        }

        tariff = fallback_tariffs.get(country_code, {'standard_rate': 0.05, 'notes': 'MFN rate'})

        return {
            'country': country_code,
            'hs_code': hs_code,
            'standard_rate': tariff['standard_rate'],
            'preferential_rate': None,
            'effective_date': '2024-01-01',
            'notes': tariff['notes'],
            'data_source': 'fallback',
            'warning': 'Verify with official source for accuracy'
        }
```

## Custom Data Source Integration

### Using Your Own Data Sources

```python
def configure_custom_data_source(
    source_type: str,
    endpoint: str,
    api_key: str = None,
    data_format: str = "json"
):
    """
    Configure custom data source

    Args:
        source_type: 'tariff', 'tax', 'exchange', 'commodity'
        endpoint: API endpoint URL
        api_key: API authentication key (optional)
        data_format: Response format ('json', 'xml', 'csv')

    Example:
        configure_custom_data_source(
            source_type='tariff',
            endpoint='https://api.yourcustomsource.com/v1/tariff',
            api_key='your_api_key'
        )
    """
    pass

def import_manual_data(
    data_type: str,
    data: dict,
    effective_date: str = None
):
    """
    Import manual data entries

    Args:
        data_type: Type of data (tariff/tax/exchange)
        data: Dictionary of country_code -> rate/value
        effective_date: When this data becomes effective

    Example:
        import_manual_data(
            data_type='tariff',
            data={
                'US': {'rate': 0.05, 'notes': 'Electronics'},
                'DE': {'rate': 0.04, 'notes': 'EU rate'}
            },
            effective_date='2024-06-01'
        )
    """
    pass
```

## Recommended API Providers

### Tariff & Trade Data

| Provider | API Type | Coverage | Cost |
|----------|----------|----------|------|
| **WTO** | REST | Global tariff database | Free tier |
| **Tradint** | REST | Multi-country tariffs | Paid |
| **CPCS** | REST | Trade intelligence | Paid |
| **Flexport** | REST | Freight + tariff | Subscription |

### Tax & Compliance

| Provider | API Type | Coverage | Cost |
|----------|----------|----------|------|
| **TaxJar** | REST | US sales tax | Usage-based |
| **Avalara** | REST | Global tax | Subscription |
| **Vertex** | REST | Enterprise tax | Enterprise |

### Exchange Rates

| Provider | API Type | Coverage | Cost |
|----------|----------|----------|------|
| ** exchangerate-api.com** | REST | 160+ currencies | Free tier |
| **Open Exchange Rates** | REST | Global | Free/Paid |
| **Fixer.io** | REST | 170+ currencies | Free tier |

### Freight & Logistics

| Provider | API Type | Coverage | Cost |
|----------|----------|----------|------|
| **Freightos** | REST | Global shipping | Subscription |
| **XPO** | REST | Freight quotes | Usage-based |
| **DAT** | REST | Truck freight | Subscription |

## Search Workflow

### Phase 1 — Define Search Criteria
- [ ] Select data category (tariff/tax/law/benchmark/market)
- [ ] Specify country/countries of interest
- [ ] Provide HS code for product classification
- [ ] Set date range for historical analysis
- [ ] Configure additional filters as needed

### Phase 2 — Execute Search with Fallback

```python
class CostAuditDataClient:
    """Client for accessing cost audit data services with fallback"""

    def __init__(self, api_key: str = None, use_fallback: bool = True):
        self.api_key = api_key
        self.use_fallback = use_fallback
        self.base_url = "https://api.costauditdata.com/v1"

    def search_tariff(self, country_code: str, hs_code: str) -> dict:
        """
        Search tariff rate for specific product

        Args:
            country_code: ISO 3166-1 alpha-2 country code
            hs_code: Harmonized System code (6-10 digits)

        Returns:
            Tariff information including standard and preferential rates
        """
        try:
            response = self._request(f"/tariff/{country_code}/{hs_code}")
            return {
                'country': country_code,
                'hs_code': hs_code,
                'standard_rate': response.get('standard_rate'),
                'preferential_rate': response.get('preferential_rate'),
                'effective_date': response.get('effective_date'),
                'notes': response.get('notes'),
                'fta_agreements': response.get('fta_agreements', []),
                'data_source': 'api'
            }
        except (APIError, ConnectionError):
            if self.use_fallback:
                return self._get_fallback_tariff(country_code, hs_code)
            raise

    def compare_tariffs(self, countries: list, hs_code: str) -> dict:
        """
        Compare tariff rates across multiple countries

        Args:
            countries: List of country codes
            hs_code: Harmonized System code

        Returns:
            Comparative tariff data across countries
        """
        results = {}
        for country in countries:
            results[country] = self.search_tariff(country, hs_code)
        return results
```

### Phase 3 — Analyze Results

```python
def analyze_cost_impact(
    base_cost: float,
    country_code: str,
    hs_code: str,
    exchange_rate: float = 1.0,
    use_fallback: bool = True
) -> dict:
    """
    Analyze cost impact factors for imported goods

    Args:
        base_cost: Product cost in source currency
        country_code: Destination country code
        hs_code: Product HS code
        exchange_rate: Currency exchange rate (default 1.0)
        use_fallback: Use fallback data if API unavailable

    Returns:
        Cost impact analysis report
    """
    client = CostAuditDataClient(use_fallback=use_fallback)
    tariff_data = client.search_tariff(country_code, hs_code)
    tax_data = client.get_tax_rates(country_code)
    logistics_data = client.get_logistics_cost(country_code)

    # Convert to destination currency
    base_cost_local = base_cost * exchange_rate

    # CIF value (Cost + Insurance + Freight)
    cif_value = base_cost_local + logistics_data.get('freight_cost', 0)

    # Calculate duties and taxes
    tariff = cif_value * tariff_data.get('standard_rate', 0)
    vat = (cif_value + tariff) * tax_data.get('vat_rate', 0)

    # Total landed cost
    total_cost = cif_value + tariff + vat

    return {
        'base_cost': round(base_cost_local, 2),
        'freight_cost': round(logistics_data.get('freight_cost', 0), 2),
        'cif_value': round(cif_value, 2),
        'tariff_rate': tariff_data.get('standard_rate'),
        'tariff_amount': round(tariff, 2),
        'vat_rate': tax_data.get('vat_rate'),
        'vat_amount': round(vat, 2),
        'total_landed_cost': round(total_cost, 2),
        'cost_breakdown': {
            'product': round(base_cost_local / total_cost * 100, 1),
            'freight': round(logistics_data.get('freight_cost', 0) / total_cost * 100, 1),
            'duties': round(tariff / total_cost * 100, 1),
            'taxes': round(vat / total_cost * 100, 1)
        },
        'data_source': tariff_data.get('data_source', 'unknown')
    }
```

### Phase 4 — Risk Detection

```python
def detect_compliance_risk(
    country_code: str,
    hs_code: str,
    origin_country: str = None,
    use_fallback: bool = True
) -> dict:
    """
    Detect compliance risks for international trade

    Args:
        country_code: Destination country code
        hs_code: Product HS code
        origin_country: Country of origin (optional)
        use_fallback: Use fallback data if API unavailable

    Returns:
        Risk assessment report with recommendations
    """
    risk_factors = []

    # Check anti-dumping measures
    ad_measures = check_anti_dumping(country_code, hs_code, use_fallback)
    if ad_measures:
        risk_factors.append({
            'type': 'anti_dumping',
            'level': 'high',
            'description': f"Anti-dumping measures apply: {ad_measures}"
        })

    # Check export controls
    export_control = check_export_control(origin_country, country_code, hs_code, use_fallback)
    if export_control:
        risk_factors.append({
            'type': 'export_control',
            'level': 'high',
            'description': f"Subject to export controls: {export_control}"
        })

    # Check sanctions lists
    sanctions = check_sanctions(country_code, use_fallback)
    if sanctions:
        risk_factors.append({
            'type': 'sanctions',
            'level': 'critical',
            'description': "Destination country under sanctions"
        })

    # Check recent regulatory changes
    recent_changes = check_regulation_changes(country_code, hs_code, use_fallback)
    if recent_changes:
        risk_factors.append({
            'type': 'regulation_change',
            'level': 'medium',
            'description': f"Recent regulatory changes: {recent_changes}"
        })

    # Determine overall risk level
    overall_risk = 'low'
    if any(r['level'] in ['high', 'critical'] for r in risk_factors):
        overall_risk = 'high'
    elif any(r['level'] == 'medium' for r in risk_factors):
        overall_risk = 'medium'

    return {
        'overall_risk': overall_risk,
        'risk_factors': risk_factors,
        'recommendations': generate_recommendations(risk_factors),
        'assessment_date': datetime.now().isoformat()
    }
```

### Phase 5 — Generate Report
- [ ] Summarize findings
- [ ] Document cost breakdown
- [ ] Highlight risk areas
- [ ] Provide actionable recommendations
- [ ] Archive for audit trail

## Skill Calling Examples

This skill can call other skills for comprehensive analysis:

```python
def full_cost_analysis_with_pricing(
    product_cost: float,
    hs_code: str,
    origin_country: str,
    destination_country: str,
    platform: str,
    target_margin: float = 0.30
) -> dict:
    """
    Perform full cost analysis with pricing optimization

    Calls:
        - cost-audit-data-search (for tariff/tax rates)
        - logistics-cost-estimator (for shipping costs)
        - platform-fee-calculator (for platform fees)
        - pricing-strategy-optimizer (for price optimization)

    Args:
        product_cost: Product purchase cost
        hs_code: Product HS code
        origin_country: Origin country code
        destination_country: Destination country code
        platform: Target platform (amazon/ebay/shopee)
        target_margin: Target profit margin

    Returns:
        Comprehensive analysis from multiple skills
    """
    # Get tariff and tax data
    tariff_data = search_tariff(destination_country, hs_code)

    # Get logistics costs
    logistics = estimate_shipping(
        origin_country=origin_country,
        destination_country=destination_country,
        shipping_method='air'
    )

    # Get platform fees
    platform_fees = calculate_platform_fees(
        platform=platform,
        category=get_category_from_hs(hs_code)
    )

    # Calculate total cost
    total_cost = calculate_total_cost(
        product_cost=product_cost,
        logistics_cost=logistics['total'],
        tariff_rate=tariff_data['standard_rate'],
        platform_fees=platform_fees
    )

    # Optimize pricing
    pricing = optimize_price(
        total_cost=total_cost,
        target_margin=target_margin,
        competitor_prices=get_competitor_prices(destination_country, hs_code)
    )

    return {
        'product_cost': product_cost,
        'logistics': logistics,
        'tariff': tariff_data,
        'platform_fees': platform_fees,
        'total_cost': total_cost,
        'recommended_price': pricing['optimal_price'],
        'margin_analysis': pricing
    }
```

## Real-Time Data Updates

```python
def setup_auto_update(
    data_type: str,
    frequency: str = 'daily',
    notify_email: str = None,
    api_key: str = None
) -> bool:
    """
    Configure automatic data updates

    Args:
        data_type: Type of data to monitor (tariff/tax/law/exchange/commodity)
        frequency: Update frequency (hourly/daily/weekly)
        notify_email: Email for notifications (optional)
        api_key: API key for real-time data (optional)

    Returns:
        True if configuration successful
    """
    valid_frequencies = {
        'tariff': {'hourly', 'daily'},
        'tax': {'daily', 'weekly'},
        'law': {'daily', 'weekly'},
        'exchange': {'hourly'},
        'commodity': {'hourly', 'daily'}
    }

    if frequency not in valid_frequencies.get(data_type, []):
        raise ValueError(f"Invalid frequency '{frequency}' for {data_type}")

    schedule_update(data_type, frequency, notify_email, api_key)
    return True
```

## Performance Standards

| Metric | Standard |
|--------|----------|
| Query Response Time | < 2 seconds |
| Data Update Latency | < 15 minutes |
| Data Accuracy | ≥ 99.5% (API) / ≥ 90% (fallback) |
| System Availability | ≥ 99.9% |
| Concurrent Requests | ≥ 1000 QPS |

## Security & Compliance

- **Encryption**: TLS 1.3 for data transmission
- **Authentication**: OAuth 2.0 with API keys
- **Data Privacy**: GDPR and CCPA compliant
- **Audit Trail**: Complete logging of all operations

## Usage Examples

```python
# Initialize with fallback (default)
client = CostAuditDataClient()  # Uses built-in fallback

# Initialize with API key
client = CostAuditDataClient(api_key="your_api_key")

# Search tariff (uses fallback if API fails)
tariff = client.search_tariff(country_code="US", hs_code="85258000")

# Compare tariffs across multiple countries
comparison = client.compare_tariffs(
    countries=["US", "DE", "JP", "GB"],
    hs_code="85258000"
)

# Analyze cost impact with fallback
cost_analysis = analyze_cost_impact(
    base_cost=100.0,
    country_code="DE",
    hs_code="85258000",
    exchange_rate=0.92,
    use_fallback=True  # Default
)

# Detect compliance risks
risk_report = detect_compliance_risk(
    country_code="US",
    hs_code="85258000",
    origin_country="CN",
    use_fallback=True
)
```

## Future Enhancements

- AI-powered cost trend prediction
- Automated compliance report generation
- Multi-language support
- Mobile application
- Custom alert rules
- Integration with ERP systems
