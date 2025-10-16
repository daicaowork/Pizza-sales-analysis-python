# 🍕 Pizza Sales Analysis Project

# Import libraries
```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
import warnings
import plotly.express as px
```

# Load the dataset
```python
df = pd.read_excel("C:/Users/Admin/Documents/SQL PROJECT/Pizza/Pizza Sales Dataset.xlsx", sheet_name="pizza_sales")
```
# Explore data
```python
df.head()
df.shape
df.columns
df.info()
df.dtypes
df.describe()
```

# 1. Business Overview

```python
total_revenue = df['total_price'].sum()
total_pizzas_sold = df['quantity'].sum()
total_orders = df['order_id'].nunique()

avg_order_value = total_revenue / total_orders
avg_pizzas_per_order = total_pizzas_sold / total_orders

print(f"Total Revenue: ${total_revenue:.2f}")
print(f"Total Pizzas Sold: {total_pizzas_sold}")
print(f"Total Orders: {total_orders}")
print(f"Average Order Value: ${avg_order_value:.2f}")
print(f"Average Pizzas per Order: {avg_pizzas_per_order:.2f}")
```

# 2. Most Common Ingredients
```python
ingredient = (
    df['pizza_ingredients']
    .str.split(',')
    .explode()
    .str.strip()
    .value_counts()
    .reset_index()
    .rename(columns={'index': 'Ingredients', 'pizza_ingredients': 'count'})
)
print(ingredient.head(10))
```

# 3. Orders by Day of the Week
```python
df['order_date'] = pd.to_datetime(df['order_date'], dayfirst=True)
df['day_name'] = df['order_date'].dt.day_name()
weekday_order = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
df['day_name'] = pd.Categorical(df['day_name'], categories=weekday_order, ordered=True)
orders_by_day = df.groupby('day_name', observed=False)['order_id'].nunique()

plt.figure(figsize=(8,5))
orders_by_day.plot(kind='bar', color='blue', edgecolor='black')
plt.title("Total Orders by Day of the Week")
plt.xlabel("Day of the Week")
plt.ylabel("Number of Orders")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

# 4. Revenue by Day of the Week
```python
orders_by_day_revenue = df.groupby('day_name', observed=False)['total_price'].sum()
plt.figure(figsize=(8,5))
orders_by_day_revenue.plot(kind='bar', color='navy', edgecolor='black')
plt.title("Total Revenue by Day of the Week")
plt.xlabel("Day of the Week")
plt.ylabel("Total Revenue")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

# 5. Orders by Hour of the Day
```python
df['order_time'] = pd.to_datetime(df['order_time'], format='%H:%M:%S')
df['order_hour'] = df['order_time'].dt.hour
orders_by_hour = df.groupby('order_hour', observed=False)['order_id'].nunique()
plt.figure(figsize=(10,5))
orders_by_hour.plot(kind='bar', color='maroon', edgecolor='black')
plt.title("Total Orders by Hour of the Day")
plt.xlabel("Hour of the Day (24-hour format)")
plt.ylabel("Number of Orders")
plt.tight_layout()
plt.show()
```

# 6. Monthly Order Trends
```python
df['month_name'] = df['order_date'].dt.month_name()
month_order = ['January','February','March','April','May','June','July','August','September','October','November','December']
df['month_name'] = pd.Categorical(df['month_name'], categories=month_order, ordered=True)
orders_by_month = df.groupby('month_name', observed=False)['order_id'].nunique()
plt.figure(figsize=(10,5))
plt.plot(orders_by_month.index, orders_by_month.values, marker='o', color='green', linewidth=2)
plt.title("Monthly Trend – Total Orders")
plt.xlabel("Month")
plt.ylabel("Number of Orders")
plt.grid(True, linestyle='--', alpha=0.5)
plt.tight_layout()
plt.show()
```

# 7. Sales Percentage by Pizza Category
```python
category_sales = df.groupby('pizza_category')['total_price'].sum()
category_pct = category_sales / category_sales.sum() * 100
plt.figure(figsize=(7,7))
colors = plt.get_cmap('tab20').colors
plt.pie(category_pct, labels=category_pct.index, autopct='%1.1f%%', startangle=90,
        colors=colors, wedgeprops={'edgecolor': 'black', 'width': 0.4})
plt.title("Percentage of Sales by Pizza Category")
plt.show()
```

# 8. Heatmap – Category vs Size
```python
sales_pivot = df.pivot_table(index='pizza_category', columns='pizza_size',
                             values='total_price', aggfunc='sum', fill_value=0)
sales_pct = sales_pivot / sales_pivot.sum().sum() * 100
plt.figure(figsize=(10,6))
sns.heatmap(sales_pct, annot=True, fmt=".1f", cmap="YlOrRd", linewidths=0.5)
plt.title("% of Sales by Pizza Category and Size")
plt.xlabel("Pizza Size")
plt.ylabel("Pizza Category")
plt.show()
```

# 9. Total Pizzas Sold by Category
```python
pizzas_by_category = df.groupby('pizza_category')['quantity'].sum()
plt.figure(figsize=(8,5))
pizzas_by_category.plot(kind='bar', color=plt.get_cmap('tab20').colors, edgecolor='black')
plt.title("Total Pizzas Sold by Pizza Category")
plt.xlabel("Pizza Category")
plt.ylabel("Total Pizzas Sold")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

# 10. Top 5 Best-Selling Pizzas
```python
pizzas_by_name = df.groupby('pizza_name')['quantity'].sum()
top5 = pizzas_by_name.sort_values(ascending=False).head(5)
plt.figure(figsize=(8,5))
top5.plot(kind='bar', color='grey', edgecolor='black')
plt.title("Top 5 Best-Selling Pizzas")
plt.xlabel("Pizza Name")
plt.ylabel("Total Pizzas Sold")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```
