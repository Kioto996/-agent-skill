---
name: skill-marketplace-guide
description: Guide for discovering, installing, and managing skills from skill marketplaces and GitHub repositories. Invoke when you need to find new skills, install from GitHub, or manage your skill collection.
---

# Skill Marketplace Guide

## When to Use
Use this skill when:
- Searching for new skills to enhance your capabilities
- Installing skills from GitHub repositories
- Managing your skill collection
- Getting recommendations for new skills
- Understanding how to contribute skills to the community

## Overview

Skills are modular capabilities that extend the agent's functionality. They can be:
- **Installed from marketplaces**: Browse and install pre-built skills
- **Added from GitHub**: Import skills from any GitHub repository
- **Created locally**: Build custom skills for your needs

## Finding New Skills

### 1. GitHub Search
Search for skills in GitHub repositories:

```
site:github.com SKILL.md cross-border OR e-commerce
site:github.com .trae/skills/
site:github.com agent-skill
```

### 2. Popular Skill Categories

| Category | Use Case | Example Skills |
|----------|----------|----------------|
| **E-commerce** | Cross-border cost, logistics, pricing | cost calculators, platform analyzers |
| **Development** | Code review, debugging, architecture | TDD, code diagnosis, setup guides |
| **Productivity** | Meeting notes, email, documentation | meeting summarizer, doc generator |
| **Research** | Data analysis, reporting, insights | market research, competitor analysis |
| **Automation** | Workflow, integrations, APIs | CRM sync, inventory management |

### 3. Community Skill Lists
- Trae Official Skill Marketplace
- GitHub Topics: `agent-skill`, `trae-skill`
- Awesome Lists for AI agents

## Installing Skills from GitHub

### Option 1: Clone & Use
```bash
# Clone a skill repository
git clone https://github.com/username/skill-name.git temp-skill

# Copy to your skills directory
cp -r temp-skill/.trae/skills/* ~/.trae/skills/

# Clean up
rm -rf temp-skill
```

### Option 2: Direct Import
If you have a GitHub URL, I can install it directly:

```
Install the skill from: https://github.com/username/repo
```

### Option 3: Manual Installation
1. Find the skill repository on GitHub
2. Locate the SKILL.md file
3. Copy the file to your `.trae/skills/[skill-name]/` directory

## Skill Structure

A valid skill requires:

```
.trae/skills/[skill-name]/
└── SKILL.md    # Required file with skill definition
```

### SKILL.md Format

```markdown
---
name: "skill-name"
description: "What the skill does. When to invoke it."
---

# Skill Title

## When to Use
- Scenario 1
- Scenario 2

## Usage
[Detailed instructions and examples]
```

## Recommended Skills for E-commerce

### Core Skills (Already Installed)

| Skill | Purpose |
|-------|---------|
| `crossborder-cost-calculator` | Calculate landed costs for imports |
| `cost-audit-data-search` | Search tariffs, taxes, regulations |
| `logistics-cost-estimator` | Estimate shipping and fulfillment costs |
| `platform-fee-calculator` | Calculate marketplace fees |
| `pricing-strategy-optimizer` | Optimize pricing strategy |

### Recommended Additions

#### 1. Inventory Management
```markdown
# inventory-management
Purpose: Track and optimize inventory levels
Features:
- Stock level monitoring
- Reorder point calculation
- Lead time analysis
```

#### 2. Competitor Price Tracker
```markdown
# competitor-price-tracker
Purpose: Monitor competitor pricing
Features:
- Price change alerts
- Price history tracking
- Margin impact analysis
```

#### 3. Tax Compliance Helper
```markdown
# tax-compliance-helper
Purpose: Ensure tax compliance across jurisdictions
Features:
- VAT/GST calculation
- Tax filing reminders
- exemption tracking
```

#### 4. Supplier Analysis
```markdown
# supplier-analysis
Purpose: Evaluate and compare suppliers
Features:
- Cost comparison
- Quality scoring
- Lead time analysis
```

#### 5. Currency Risk Manager
```markdown
# currency-risk-manager
Purpose: Manage foreign exchange risks
Features:
- Exchange rate alerts
- Hedging recommendations
- Currency conversion
```

## Skill Management

### Listing Installed Skills
```python
import os
skills_dir = ".trae/skills/"
installed_skills = [d for d in os.listdir(skills_dir) if os.path.isdir(os.path.join(skills_dir, d))]
```

### Updating a Skill
```bash
# Pull latest from GitHub
cd .trae/skills/[skill-name]
git pull origin main
```

### Removing a Skill
```bash
# Delete the skill directory
rm -rf .trae/skills/[skill-name]
```

## Contributing Skills

### Steps to Contribute
1. Create a GitHub repository
2. Follow the SKILL.md format
3. Add clear documentation
4. Include code examples
5. Test the skill
6. Submit to skill marketplace

### Skill Quality Checklist
- [ ] Clear name and description
- [ ] "When to Use" section
- [ ] Usage examples
- [ ] Error handling
- [ ] API fallback support
- [ ] Integration examples with other skills

## Skill Dependencies

Skills can reference each other for complex workflows:

```python
def full_ecommerce_analysis(product_cost, destination):
    # Call cost-audit-data-search for tariffs
    tariffs = search_tariff(destination)

    # Call logistics-cost-estimator for shipping
    shipping = estimate_logistics(destination)

    # Call platform-fee-calculator for fees
    fees = calculate_platform_fees(destination)

    # Combine results
    return {
        'tariffs': tariffs,
        'shipping': shipping,
        'fees': fees,
        'total_cost': calculate_total(tariffs, shipping, fees)
    }
```

## Troubleshooting

### Skill Not Loading
- Check SKILL.md exists in the correct directory
- Verify YAML frontmatter is valid
- Ensure name field is unique

### Skill Not Found
- Check the skill name matches exactly
- Verify skills directory path
- Restart the agent

### API Errors
- Use fallback data when APIs unavailable
- Set `use_fallback=True` in function calls
- Provide custom API keys for production

## Getting Help

### Finding Skill Documentation
1. Check the skill's SKILL.md file
2. Look for README.md in the repository
3. Search for examples in the codebase

### Reporting Issues
- Create an issue on the skill's GitHub repository
- Include error messages and steps to reproduce
- Specify which skill is affected

## Quick Reference

### Common GitHub Skill Repositories
| Repository | Skills Included |
|------------|----------------|
| mattpocock/skills | Engineering, productivity |
| trae-ai/skills | Official Trae skills |
| [username]/-agent-skill | Custom agent skills |

### Search Queries
```
# Find e-commerce skills
site:github.com "cross-border" SKILL.md

# Find cost calculation skills
site:github.com "cost calculator" SKILL.md

# Find Trae-compatible skills
site:github.com ".trae/skills" SKILL.md
```

### Installation Commands
```bash
# Clone all skills from a repository
git clone https://github.com/username/skills-repo.git
cp -r skills-repo/.trae/skills/* .trae/skills/

# Update all skills
cd .trae/skills && git pull --all
```

## Skill Recommendations

Based on your current setup, here are suggested additions:

### High Priority
1. **Currency Risk Manager** - Manage exchange rate fluctuations
2. **Competitor Price Tracker** - Monitor market pricing

### Medium Priority
3. **Tax Compliance Helper** - Automate tax calculations
4. **Supplier Database** - Manage supplier relationships

### Low Priority (Future)
5. **AI Demand Forecaster** - Predict market trends
6. **Multi-channel Lister** - List on multiple platforms

Would you like me to:
1. Search GitHub for specific skills?
2. Create any of the recommended skills above?
3. Help you contribute your skills to the community?
