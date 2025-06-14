# Project 2: Dashboard Analisis Tren Pasar Kopi Indonesia
☕ Dashboard Analisis Tren Pasar Kopi Indonesia

## 📋 Project Overview
Comprehensive market analysis dashboard untuk industri kopi Indonesia, menyediakan insights untuk UMKM kopi dalam pengambilan keputusan bisnis berbasis data.

## 🎯 Business Problem
UMKM kopi Indonesia menghadapi tantangan:
- Volatilitas harga yang tinggi
- Kurangnya akses terhadap market intelligence
- Kesulitan dalam inventory planning
- Ketidakpastian ROI untuk investasi coffee shop

## 📊 Dataset
| Dataset | Source | Update Frequency | Format |
|---------|--------|------------------|--------|
| Harga Kopi Global | ICO (International Coffee Organization) | Daily | API/CSV |
| Data Ekspor-Impor | BPS & Kemendag | Monthly | Excel |
| Produksi Regional | Ditjenbun Kementan | Quarterly | PDF/Excel |
| Weather Data | BMKG | Daily | API |
| Coffee Shop Reviews | Google Maps API | Real-time | JSON |

## 🛠️ Technical Implementation

### Data Pipeline
```python
# Example: Automated data collection
import requests
import pandas as pd
from datetime import datetime

class CoffeeDataCollector:
    def __init__(self):
        self.api_key = os.getenv('COFFEE_API_KEY')
        
    def fetch_daily_prices(self):
        """Fetch daily coffee prices from ICO API"""
        url = f"https://api.ico.org/prices/daily"
        response = requests.get(url, headers={'API-Key': self.api_key})
        return pd.DataFrame(response.json())
    
    def calculate_price_volatility(self, df, window=30):
        """Calculate rolling volatility"""
        df['returns'] = df['price'].pct_change()
        df['volatility'] = df['returns'].rolling(window).std() * np.sqrt(252)
        return df
```
## Key Features Implementation
### 1. Price Prediction Model
```
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import train_test_split

# Simple price prediction
def predict_coffee_price(historical_data):
    # Feature engineering
    historical_data['MA_7'] = historical_data['price'].rolling(7).mean()
    historical_data['MA_30'] = historical_data['price'].rolling(30).mean()
    
    # Model training
    features = ['MA_7', 'MA_30', 'volume', 'weather_index']
    X = historical_data[features]
    y = historical_data['price_next_day']
    
    model = RandomForestRegressor(n_estimators=100)
    model.fit(X_train, y_train)
    
    return model
```
### 2. ROI Calculator
```
def coffee_shop_roi_calculator(initial_investment, monthly_costs, avg_cup_price, daily_cups_sold):
    """Calculate coffee shop ROI and break-even point"""
    monthly_revenue = avg_cup_price * daily_cups_sold * 30
    monthly_profit = monthly_revenue - monthly_costs
    
    roi_metrics = {
        'monthly_profit': monthly_profit,
        'break_even_months': initial_investment / monthly_profit,
        'annual_roi': (monthly_profit * 12 / initial_investment) * 100
    }
    
    return roi_metrics
```
##📈 Dashboard Components
### 1. Market Overview
- Real-time Prices: Arabica & Robusta spot prices
- Price Trends: 30-day, 90-day, 1-year charts
 Volatility Index: Risk indicator for traders

### 2. Production Analysis
Regional Heatmap: Production volume by province
Seasonal Patterns: Monthly production trends
Weather Impact: Correlation with rainfall data

### 3. Business Intelligence
Demand Forecast: ML-based prediction
Competition Analysis: Coffee shop density maps
ROI Calculator: Interactive tool for investors

---
## 🎯 Key Insights & Recommendations
### For Coffee Farmers
- Optimal Harvest Time: Data shows price peaks in Q2 & Q4
- Regional Advantages: Aceh & North Sumatra show highest price premiums

### For Coffee Shop Owners
- Location Analysis: High-traffic areas with <3 competitors show best ROI
- Menu Optimization: Specialty drinks show 40% higher margins
