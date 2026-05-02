---
name: "crossborder-cost-calculator"
description: "计算境外电商商品的综合成本，包括采购价、运费、关税、平台费用等。Invoke when user needs to calculate cross-border e-commerce costs or price products for overseas sales."
---

# 境外电商成本计算器

## 功能介绍

本技能用于计算境外电商商品的综合成本，帮助卖家确定合理的售价和利润空间。

## 成本构成要素

1. **采购成本**：商品的进货价格
2. **国内运费**：从供应商到国内仓库的运费
3. **国际物流费**：从国内仓库到海外目的地的运费
4. **关税与税费**：目的国的进口关税、增值税等
5. **平台费用**：电商平台佣金、手续费等
6. **仓储费**：海外仓储费用（如适用）
7. **其他杂费**：包装费、保险费等

## 计算公式

```
总成本 = 采购成本 + 国内运费 + 国际物流费 + 关税 + 平台费用 + 仓储费 + 杂费
建议售价 = 总成本 × (1 + 利润率)
```

## 使用步骤

1. 收集所有成本相关数据
2. 调用成本计算函数
3. 根据计算结果制定定价策略

## 代码示例

```python
def calculate_crossborder_cost(
    product_cost: float,
    domestic_shipping: float,
    international_shipping: float,
    tariff_rate: float,
    platform_commission_rate: float,
    storage_fee: float = 0,
    other_fees: float = 0,
    profit_margin: float = 0.3
) -> dict:
    """
    计算境外电商综合成本
    
    Args:
        product_cost: 商品采购成本
        domestic_shipping: 国内运费
        international_shipping: 国际运费
        tariff_rate: 关税率（如0.1表示10%）
        platform_commission_rate: 平台佣金率
        storage_fee: 仓储费
        other_fees: 其他杂费
        profit_margin: 期望利润率
    
    Returns:
        包含各项成本和建议售价的字典
    """
    # 计算关税（基于CIF价：成本+保险+运费）
    cif_value = product_cost + domestic_shipping + international_shipping
    tariff = cif_value * tariff_rate
    
    # 计算总成本
    total_cost = (
        product_cost +
        domestic_shipping +
        international_shipping +
        tariff +
        storage_fee +
        other_fees
    )
    
    # 计算平台佣金（基于售价预估）
    # 先预估售价，再计算佣金
    estimated_price = total_cost * (1 + profit_margin)
    platform_commission = estimated_price * platform_commission_rate
    
    # 重新计算包含佣金的总成本
    final_total_cost = total_cost + platform_commission
    
    # 最终建议售价
    suggested_price = final_total_cost * (1 + profit_margin)
    
    return {
        "product_cost": product_cost,
        "domestic_shipping": domestic_shipping,
        "international_shipping": international_shipping,
        "tariff": tariff,
        "platform_commission": platform_commission,
        "storage_fee": storage_fee,
        "other_fees": other_fees,
        "total_cost": final_total_cost,
        "suggested_price": suggested_price,
        "profit_margin": profit_margin
    }
```

## 常见关税率参考

- 美国：一般商品0-25%
- 欧盟：0-17% VAT + 关税
- 英国：0-20% VAT + 关税
- 日本：0-10%消费税 + 关税

## 注意事项

1. 汇率波动会影响成本计算
2. 不同国家的税费政策差异较大
3. 物流时效与成本成正比
4. 建议预留10-15%的风险缓冲
