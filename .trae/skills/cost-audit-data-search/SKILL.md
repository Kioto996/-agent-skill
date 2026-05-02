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

## Search Workflow

### Phase 1 — Define Search Criteria
- [ ] Select data category (tariff/tax/law/benchmark/market)
- [ ] Specify country/countries of interest
- [ ] Provide HS code for product classification
- [ ] Set date range for historical analysis
- [ ] Configure additional filters as needed

### Phase 2 — Execute Search

```python
class CostAuditDataClient:
    """Client for accessing cost audit data services"""
    
    def __init__(self, api_key: str):
        self.api_key = api_key
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
        response = self._request(f"/tariff/{country_code}/{hs_code}")
        return {
            'country': country_code,
            'hs_code': hs_code,
            'standard_rate': response.get('standard_rate'),
            'preferential_rate': response.get('preferential_rate'),
            'effective_date': response.get('effective_date'),
            'notes': response.get('notes'),
            'fta_agreements': response.get('fta_agreements', [])
        }
    
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
    exchange_rate: float = 1.0
) -> dict:
    """
    Analyze cost impact factors for imported goods
    
    Args:
        base_cost: Product cost in source currency
        country_code: Destination country code
        hs_code: Product HS code
        exchange_rate: Currency exchange rate (default 1.0)
    
    Returns:
        Cost impact analysis report
    """
    client = CostAuditDataClient(api_key="your_api_key")
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
        }
    }
```

### Phase 4 — Risk Detection

```python
def detect_compliance_risk(
    country_code: str,
    hs_code: str,
    origin_country: str = None
) -> dict:
    """
    Detect compliance risks for international trade
    
    Args:
        country_code: Destination country code
        hs_code: Product HS code
        origin_country: Country of origin (optional)
    
    Returns:
        Risk assessment report with recommendations
    """
    risk_factors = []
    
    # Check anti-dumping measures
    ad_measures = check_anti_dumping(country_code, hs_code)
    if ad_measures:
        risk_factors.append({
            'type': 'anti_dumping',
            'level': 'high',
            'description': f"Anti-dumping measures apply: {ad_measures}"
        })
    
    # Check export controls
    export_control = check_export_control(origin_country, country_code, hs_code)
    if export_control:
        risk_factors.append({
            'type': 'export_control',
            'level': 'high',
            'description': f"Subject to export controls: {export_control}"
        })
    
    # Check sanctions lists
    sanctions = check_sanctions(country_code)
    if sanctions:
        risk_factors.append({
            'type': 'sanctions',
            'level': 'critical',
            'description': "Destination country under sanctions"
        })
    
    # Check recent regulatory changes
    recent_changes = check_regulation_changes(country_code, hs_code)
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

## Key Features

### Real-Time Data Updates
```python
def setup_auto_update(
    data_type: str,
    frequency: str = 'daily',
    notify_email: str = None
) -> bool:
    """
    Configure automatic data updates
    
    Args:
        data_type: Type of data to monitor (tariff/tax/law/exchange/commodity)
        frequency: Update frequency (hourly/daily/weekly)
        notify_email: Email for notifications (optional)
    
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
    
    schedule_update(data_type, frequency, notify_email)
    return True
```

### Version Management
```python
def get_data_version(data_type: str) -> dict:
    """
    Get data version information
    
    Args:
        data_type: Type of data
    
    Returns:
        Version metadata
    """
    return {
        'data_type': data_type,
        'version': get_current_version(data_type),
        'last_updated': get_last_update_time(data_type),
        'status': get_update_status(data_type),
        'source': get_data_source(data_type)
    }
```

## Performance Standards

| Metric | Standard |
|--------|----------|
| Query Response Time | < 2 seconds |
| Data Update Latency | < 15 minutes |
| Data Accuracy | ≥ 99.5% |
| System Availability | ≥ 99.9% |
| Concurrent Requests | ≥ 1000 QPS |

## Security & Compliance

- **Encryption**: TLS 1.3 for data transmission
- **Authentication**: OAuth 2.0 with API keys
- **Data Privacy**: GDPR and CCPA compliant
- **Audit Trail**: Complete logging of all operations

## Usage Examples

```python
# Initialize client
client = CostAuditDataClient(api_key="your_api_key")

# Search US tariff for electronic goods
us_tariff = client.search_tariff(country_code="US", hs_code="85258000")

# Compare tariffs across multiple countries
comparison = client.compare_tariffs(
    countries=["US", "DE", "JP", "GB"],
    hs_code="85258000"
)

# Analyze cost impact for Germany
cost_analysis = analyze_cost_impact(
    base_cost=100.0,
    country_code="DE",
    hs_code="85258000",
    exchange_rate=0.92
)

# Detect compliance risks for US export
risk_report = detect_compliance_risk(
    country_code="US",
    hs_code="85258000",
    origin_country="CN"
)

# Configure automatic updates
client.setup_auto_update(
    data_type="tariff",
    frequency="daily",
    notify_email="auditor@company.com"
)
```

## Future Enhancements

- AI-powered cost trend prediction
- Automated compliance report generation
- Multi-language support
- Mobile application
- Custom alert rules
- Integration with ERP systems
