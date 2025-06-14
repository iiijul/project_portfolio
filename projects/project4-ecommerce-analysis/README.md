## Project 4: E-commerce Sales Analysis
### 🛒 E-commerce Sales Analysis Dashboard

## 📋 Project Overview
Comprehensive sales analysis dashboard untuk platform e-commerce, memberikan insights mendalam tentang customer behavior, product performance, dan revenue optimization opportunities.

## 🎯 Business Problem
E-commerce platforms menghadapi tantangan dalam:
- Understanding customer purchase patterns
- Optimizing product mix dan inventory
- Reducing cart abandonment rate (currently 70%)
- Improving customer lifetime value (CLV)
- Personalizing marketing campaigns

## 📊 Dataset
| Dataset | Description | Size | Period |
|---------|-------------|------|---------|
| Transactions | Order details & payment | 2.5M rows | 2022-2024 |
| Customers | User profiles & demographics | 500K rows | 2022-2024 |
| Products | SKU details & categories | 50K rows | Current |
| Sessions | Website behavior data | 10M rows | 2022-2024 |
| Marketing | Campaign performance | 100K rows | 2022-2024 |

## 🛠️ Technical Implementation

### Data Pipeline Architecture
```python
import pandas as pd
import numpy as np
from datetime import datetime, timedelta

class EcommerceDataPipeline:
    def __init__(self, connection_string):
        self.engine = create_engine(connection_string)
        
    def extract_transaction_data(self, start_date, end_date):
        """Extract and transform transaction data"""
        query = """
        SELECT 
            t.order_id,
            t.customer_id,
            t.order_date,
            t.total_amount,
            t.discount_amount,
            t.shipping_cost,
            t.payment_method,
            c.customer_segment,
            c.acquisition_channel,
            c.location_city,
            DATEDIFF(day, c.first_purchase_date, t.order_date) as customer_age_days,
            p.product_category,
            p.product_subcategory,
            p.brand,
            od.quantity,
            od.unit_price
        FROM transactions t
        JOIN customers c ON t.customer_id = c.customer_id
        JOIN order_details od ON t.order_id = od.order_id
        JOIN products p ON od.product_id = p.product_id
        WHERE t.order_date BETWEEN %s AND %s
        """
        return pd.read_sql(query, self.engine, params=[start_date, end_date])
```
---

## Customer Segmentation
```
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler

def create_rfm_segments(transaction_df):
    """Create RFM (Recency, Frequency, Monetary) segments"""
    
    # Calculate RFM metrics
    rfm = transaction_df.groupby('customer_id').agg({
        'order_date': lambda x: (datetime.now() - x.max()).days,  # Recency
        'order_id': 'count',  # Frequency
        'total_amount': 'sum'  # Monetary
    })
    rfm.columns = ['Recency', 'Frequency', 'Monetary']
    
    # Create RFM scores
    rfm['R_Score'] = pd.qcut(rfm['Recency'], 5, labels=[5,4,3,2,1])
    rfm['F_Score'] = pd.qcut(rfm['Frequency'].rank(method='first'), 5, labels=[1,2,3,4,5])
    rfm['M_Score'] = pd.qcut(rfm['Monetary'], 5, labels=[1,2,3,4,5])
    
    # Combine scores
    rfm['RFM_Score'] = rfm['R_Score'].astype(str) + rfm['F_Score'].astype(str) + rfm['M_Score'].astype(str)
    
    # Define segments
    segment_map = {
        '555': 'Champions',
        '554': 'Loyal Customers',
        '553': 'Loyal Customers',
        '544': 'Loyal Customers',
        '545': 'Loyal Customers',
        '454': 'Potential Loyalists',
        '455': 'Potential Loyalists',
        '445': 'Potential Loyalists',
        '354': 'New Customers',
        '355': 'New Customers',
        '344': 'New Customers',
        '345': 'New Customers',
        '444': 'Promising',
        '435': 'Promising',
        '434': 'Promising',
        '443': 'Promising',
        '334': 'Need Attention',
        '335': 'Need Attention',
        '333': 'About to Sleep',
        '332': 'About to Sleep',
        '322': 'At Risk',
        '323': 'At Risk',
        '222': 'Hibernating',
        '223': 'Hibernating',
        '111': 'Lost'
    }
    
    rfm['Segment'] = rfm['RFM_Score'].map(segment_map)
    return rfm
```
---

## Sales Forecasting
```
from prophet import Prophet

def forecast_sales(sales_data, periods=30):
    """Forecast sales using Facebook Prophet"""
    
    # Prepare data for Prophet
    df_prophet = sales_data[['order_date', 'total_amount']].copy()
    df_prophet.columns = ['ds', 'y']
    df_prophet = df_prophet.groupby('ds').sum().reset_index()
    
    # Initialize and fit model
    model = Prophet(
        daily_seasonality=True,
        weekly_seasonality=True,
        yearly_seasonality=True,
        seasonality_mode='multiplicative'
    )
    
    # Add custom seasonalities
    model.add_seasonality(name='monthly', period=30.5, fourier_order=5)
    model.fit(df_prophet)
    
    # Make predictions
    future = model.make_future_dataframe(periods=periods)
    forecast = model.predict(future)
    
    return forecast[['ds', 'yhat', 'yhat_lower', 'yhat_upper']]
```
---

## 📈 Dashboard Components
### 1. Executive Summary
- Revenue Metrics: Daily/Monthly/Yearly revenue with YoY growth
- Customer Metrics: Active users, new vs returning, churn rate
- Product Performance: Best sellers, inventory turnover
- Conversion Funnel: Session → Cart → Purchase conversion rates

### 2. Customer Analytics
- RFM Segmentation: Interactive segment distribution
- CLV Analysis: Lifetime value by segment and channel
- Cohort Analysis: Retention curves by acquisition month
- Geographic Distribution: Sales heatmap by region

### 3. Product Analytics
- Category Performance: Revenue and margin by category
- Cross-sell Analysis: Product association rules
- Price Elasticity: Demand sensitivity analysis
- Inventory Optimization: Stock-out and overstock alerts

### 4. Marketing Analytics
Channel Performance: ROI by marketing
