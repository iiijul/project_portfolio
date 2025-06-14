# 💹 Visualisasi Perbandingan Kinerja Reksadana Indonesia

## 📋 Project Overview
Interactive visualization tool untuk membantu investor retail memahami dan membandingkan performa reksadana di Indonesia dengan metrics yang mudah dipahami.

## 🎯 Business Problem
Investor retail Indonesia menghadapi kesulitan:
- Membandingkan 2000+ produk reksadana yang tersedia
- Memahami risk-adjusted returns
- Menentukan alokasi portfolio yang optimal
- Tracking performa investasi vs benchmark

## 📊 Dataset
| Dataset | Source | Period | Update |
|---------|--------|--------|---------|
| NAB Reksadana | OJK/Bareksa API | 2019-2024 | Daily |
| IHSG Historical | IDX | 2019-2024 | Daily |
| SBI Rate | Bank Indonesia | 2019-2024 | Monthly |
| Fund Profiles | OJK | Current | Quarterly |
| Expense Ratio | Fund Factsheets | Current | Annual |

## 🛠️ Technical Implementation

### Data Collection
```sql
-- Query untuk aggregate performa reksadana
WITH daily_returns AS (
    SELECT 
        fund_id,
        fund_name,
        fund_type,
        date,
        nav,
        LAG(nav) OVER (PARTITION BY fund_id ORDER BY date) as prev_nav,
        (nav - LAG(nav) OVER (PARTITION BY fund_id ORDER BY date)) / 
         LAG(nav) OVER (PARTITION BY fund_id ORDER BY date) * 100 as daily_return
    FROM mutual_funds_nav
    WHERE date >= '2019-01-01'
),
performance_metrics AS (
    SELECT
        fund_id,
        fund_name,
        fund_type,
        AVG(daily_return) * 252 as annual_return,
        STDDEV(daily_return) * SQRT(252) as annual_volatility,
        (AVG(daily_return) * 252 - 2.5) / (STDDEV(daily_return) * SQRT(252)) as sharpe_ratio
    FROM daily_returns
    WHERE daily_return IS NOT NULL
    GROUP BY fund_id, fund_name, fund_type
)
SELECT * FROM performance_metrics
ORDER BY sharpe_ratio DESC;
```
---
## Risk Metrics Calculation

```
import pandas as pd
import numpy as np
from scipy import stats

class MutualFundAnalyzer:
    def __init__(self, risk_free_rate=0.025):
        self.risk_free_rate = risk_free_rate
        
    def calculate_risk_metrics(self, returns_df):
        """Calculate comprehensive risk metrics"""
        metrics = {}
        
        # Basic metrics
        metrics['annual_return'] = returns_df.mean() * 252
        metrics['annual_volatility'] = returns_df.std() * np.sqrt(252)
        metrics['sharpe_ratio'] = (metrics['annual_return'] - self.risk_free_rate) / metrics['annual_volatility']
        
        # Advanced metrics
        metrics['max_drawdown'] = self.calculate_max_drawdown(returns_df)
        metrics['var_95'] = np.percentile(returns_df, 5)
        metrics['sortino_ratio'] = self.calculate_sortino_ratio(returns_df)
        
        return metrics
    
    def calculate_max_drawdown(self, returns):
        """Calculate maximum drawdown"""
        cum_returns = (1 + returns).cumprod()
        running_max = cum_returns.expanding().max()
        drawdown = (cum_returns - running_max) / running_max
        return drawdown.min()
```
---
## 📈 Key Features
### 1. Performance Dashboard
- Returns Comparison: YTD, 1Y, 3Y, 5Y returns
- Risk-Adjusted Metrics: Sharpe, Sortino, Calmar ratios
- Peer Comparison: Ranking within category
- Benchmark Tracking: vs IHSG, LQ45, bonds

### 2. Portfolio Builder
- Asset Allocation Tool: Drag-and-drop portfolio construction
- Optimization Engine: Efficient frontier visualization
- Rebalancing Alerts: Automated notifications
- What-If Analysis: Scenario testing
---

## 🎯 Key Insights
1. Fund Selection Tips
2. Consistency Beats Performance: Funds with stable returns outperform in long term
3. Expense Matters: Every 1% in expense ratio reduces returns by 15% over 10 years
4. Diversification: Optimal portfolio has 5-7 funds across categories
