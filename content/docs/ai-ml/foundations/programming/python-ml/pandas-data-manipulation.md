---
title: "Pandas Mastery 2025: Complete Data Manipulation Guide for Data Science"
description: "Master Pandas for data science: DataFrames, data cleaning, aggregation, merging, time series analysis with practical examples and best practices."
date: 2025-01-26
lastmod: 2025-01-26
draft: false
weight: 2
categories: ["AI & ML", "Programming"]
tags: ["pandas tutorial", "data manipulation python", "pandas dataframe", "data cleaning", "python data analysis"]
contributors: ["Quantitative Research Team"]
toc: true
math: true
---

Pandas is the Swiss Army knife of data manipulation in Python. Whether you're cleaning messy datasets, performing complex aggregations, or preparing features for machine learning, Pandas provides intuitive and powerful tools that form the foundation of modern data science workflows.

## Why Pandas Is Essential for Data Science

In the real world, data doesn't come clean and ready for analysis. You'll spend 60-80% of your time on data preparation—and Pandas makes this process bearable, even enjoyable.

> "Pandas transformed how we work with data in Python. It's the reason Python became the dominant language in data science." — Wes McKinney, Pandas Creator

### Real-World Impact in Indian Tech

- **Zomato:** Analyzing millions of food delivery orders
- **BYJU'S:** Processing student learning patterns
- **PhonePe:** Transaction analysis and fraud detection
- **Swiggy:** Demand forecasting and inventory optimization

## Section 1: DataFrame Fundamentals

### What Is a DataFrame and How Does It Work?

A DataFrame is a 2-dimensional labeled data structure—think of it as a spreadsheet or SQL table with superpowers.

```python
import pandas as pd
import numpy as np

# =============================================================================
# CREATING DATAFRAMES
# =============================================================================

# From dictionary
data = {
    'name': ['Alice', 'Bob', 'Charlie', 'Diana', 'Eve'],
    'age': [25, 30, 35, 28, 32],
    'city': ['Mumbai', 'Delhi', 'Bangalore', 'Chennai', 'Hyderabad'],
    'salary': [50000, 60000, 75000, 55000, 70000]
}
df = pd.DataFrame(data)
print("=== DataFrame from Dictionary ===")
print(df)

# From list of dictionaries
records = [
    {'product': 'Laptop', 'price': 50000, 'quantity': 10},
    {'product': 'Phone', 'price': 20000, 'quantity': 25},
    {'product': 'Tablet', 'price': 30000, 'quantity': 15}
]
df_products = pd.DataFrame(records)
print("\n=== DataFrame from Records ===")
print(df_products)

# From NumPy array
arr = np.random.randn(5, 3)
df_numpy = pd.DataFrame(arr, columns=['A', 'B', 'C'])
print("\n=== DataFrame from NumPy ===")
print(df_numpy.round(3))

# From CSV/Excel (common in practice)
# df = pd.read_csv('data.csv')
# df = pd.read_excel('data.xlsx')
# df = pd.read_json('data.json')
# df = pd.read_sql('SELECT * FROM table', connection)
```

### How Do You Explore a DataFrame?

```python
# =============================================================================
# DATA EXPLORATION
# =============================================================================

# Create sample dataset
np.random.seed(42)
n = 1000

df = pd.DataFrame({
    'id': range(1, n + 1),
    'name': np.random.choice(['Alice', 'Bob', 'Charlie', 'Diana', 'Eve'], n),
    'age': np.random.randint(18, 65, n),
    'department': np.random.choice(['Engineering', 'Sales', 'Marketing', 'HR', 'Finance'], n),
    'salary': np.random.normal(60000, 15000, n).round(2),
    'hire_date': pd.date_range('2015-01-01', periods=n, freq='D'),
    'performance_score': np.random.uniform(1, 5, n).round(2)
})

# Basic info
print("=== DataFrame Info ===")
print(f"Shape: {df.shape}")
print(f"Columns: {df.columns.tolist()}")
print(f"Data types:\n{df.dtypes}")

# First/last rows
print("\n=== First 3 Rows ===")
print(df.head(3))

print("\n=== Last 3 Rows ===")
print(df.tail(3))

# Statistical summary
print("\n=== Numerical Summary ===")
print(df.describe())

# Categorical summary
print("\n=== Categorical Summary ===")
print(df.describe(include='object'))

# Missing value check
print("\n=== Missing Values ===")
print(df.isnull().sum())

# Memory usage
print(f"\n=== Memory Usage ===")
print(df.memory_usage(deep=True))
```

### What Are the Essential DataFrame Operations?

```python
# =============================================================================
# ESSENTIAL OPERATIONS
# =============================================================================

# Column selection
print("=== Column Selection ===")
print(df['name'].head())                    # Single column (Series)
print(df[['name', 'salary']].head())        # Multiple columns (DataFrame)

# Row selection
print("\n=== Row Selection ===")
print(df.loc[0])                            # By label
print(df.iloc[0])                           # By position
print(df.loc[0:3, ['name', 'salary']])      # Range with columns

# Conditional selection
print("\n=== Conditional Selection ===")
high_salary = df[df['salary'] > 70000]
print(f"Employees with salary > 70000: {len(high_salary)}")

# Multiple conditions
engineers_high_perf = df[(df['department'] == 'Engineering') & 
                          (df['performance_score'] > 4)]
print(f"High-performing engineers: {len(engineers_high_perf)}")

# Using query (SQL-like syntax)
result = df.query("salary > 70000 and age < 40")
print(f"Young high earners: {len(result)}")

# Adding columns
df['bonus'] = df['salary'] * 0.1
df['total_compensation'] = df['salary'] + df['bonus']
df['experience_years'] = (pd.Timestamp.now() - df['hire_date']).dt.days / 365

# Conditional column assignment
df['salary_tier'] = pd.cut(df['salary'], 
                           bins=[0, 50000, 70000, float('inf')],
                           labels=['Low', 'Medium', 'High'])

print("\n=== New Columns ===")
print(df[['name', 'salary', 'bonus', 'total_compensation', 'salary_tier']].head())
```

## Section 2: Data Cleaning and Preprocessing

### How Do You Handle Missing Data?

```python
# =============================================================================
# MISSING DATA HANDLING
# =============================================================================

# Create dataset with missing values
df_missing = pd.DataFrame({
    'name': ['Alice', 'Bob', np.nan, 'Diana', 'Eve'],
    'age': [25, np.nan, 35, 28, np.nan],
    'salary': [50000, 60000, np.nan, 55000, 70000],
    'department': ['HR', 'Engineering', 'Sales', np.nan, 'Marketing']
})

print("=== Original Data with Missing Values ===")
print(df_missing)
print(f"\nMissing count:\n{df_missing.isnull().sum()}")

# Check for missing values
print(f"\nTotal missing: {df_missing.isnull().sum().sum()}")
print(f"Percentage missing: {(df_missing.isnull().sum() / len(df_missing) * 100).round(2)}%")

# Drop missing values
df_dropped_any = df_missing.dropna()          # Drop rows with ANY missing
df_dropped_all = df_missing.dropna(how='all') # Drop rows with ALL missing
df_dropped_col = df_missing.dropna(subset=['name', 'salary'])  # Drop if missing in specific cols

print(f"\nAfter dropna(): {len(df_dropped_any)} rows")

# Fill missing values
df_filled = df_missing.copy()

# Fill with constant
df_filled['department'] = df_filled['department'].fillna('Unknown')

# Fill with mean/median
df_filled['age'] = df_filled['age'].fillna(df_filled['age'].mean())
df_filled['salary'] = df_filled['salary'].fillna(df_filled['salary'].median())

# Forward/backward fill (good for time series)
df_filled['name'] = df_filled['name'].fillna(method='ffill')

print("\n=== After Filling Missing Values ===")
print(df_filled)

# Interpolation (for numerical data)
df_interp = pd.DataFrame({
    'time': range(10),
    'value': [1, np.nan, np.nan, 4, 5, np.nan, 7, 8, np.nan, 10]
})
df_interp['value_interp'] = df_interp['value'].interpolate()
print("\n=== Interpolation ===")
print(df_interp)
```

### How Do You Handle Duplicates?

```python
# =============================================================================
# DUPLICATE HANDLING
# =============================================================================

df_dupes = pd.DataFrame({
    'order_id': [1, 2, 2, 3, 4, 4, 4],
    'product': ['A', 'B', 'B', 'C', 'D', 'D', 'D'],
    'quantity': [10, 20, 20, 15, 25, 25, 30]  # Note: last row has different quantity
})

print("=== Data with Duplicates ===")
print(df_dupes)

# Find duplicates
print(f"\nDuplicate rows:\n{df_dupes[df_dupes.duplicated()]}")
print(f"Duplicate order_ids:\n{df_dupes[df_dupes.duplicated(subset=['order_id'])]}")

# Count duplicates
print(f"\nTotal duplicate rows: {df_dupes.duplicated().sum()}")

# Remove duplicates
df_unique = df_dupes.drop_duplicates()
df_unique_order = df_dupes.drop_duplicates(subset=['order_id'], keep='first')
df_unique_last = df_dupes.drop_duplicates(subset=['order_id'], keep='last')

print(f"\n=== After Removing Duplicates ===")
print(f"All columns unique:\n{df_unique}")
print(f"\nFirst occurrence by order_id:\n{df_unique_order}")
```

### How Do You Transform Data Types?

```python
# =============================================================================
# DATA TYPE CONVERSION
# =============================================================================

# Sample data with type issues
df_types = pd.DataFrame({
    'date_string': ['2024-01-01', '2024-01-02', '2024-01-03'],
    'price_string': ['$100.50', '$200.75', '$150.25'],
    'quantity': ['10', '20', '15'],
    'is_sale': ['True', 'False', 'True'],
    'category_num': [1, 2, 3]
})

print("=== Original Types ===")
print(df_types.dtypes)

# Convert to datetime
df_types['date'] = pd.to_datetime(df_types['date_string'])

# Clean and convert price
df_types['price'] = df_types['price_string'].str.replace('$', '').astype(float)

# Convert to numeric
df_types['quantity'] = pd.to_numeric(df_types['quantity'])

# Convert to boolean
df_types['is_sale_bool'] = df_types['is_sale'].map({'True': True, 'False': False})

# Convert to categorical (memory efficient for repeated strings)
df_types['category'] = pd.Categorical(df_types['category_num'].map({1: 'A', 2: 'B', 3: 'C'}))

print("\n=== Converted Types ===")
print(df_types.dtypes)
print(f"\nMemory savings with categorical: significant for large datasets!")
```

## Section 3: Aggregation and GroupBy

### How Does GroupBy Work?

GroupBy is the backbone of data aggregation—it implements the split-apply-combine pattern.

```python
# =============================================================================
# GROUPBY FUNDAMENTALS
# =============================================================================

# Create sales dataset
np.random.seed(42)
n = 1000

sales = pd.DataFrame({
    'date': pd.date_range('2024-01-01', periods=n, freq='D'),
    'region': np.random.choice(['North', 'South', 'East', 'West'], n),
    'product': np.random.choice(['Laptop', 'Phone', 'Tablet', 'Accessories'], n),
    'salesperson': np.random.choice(['Alice', 'Bob', 'Charlie', 'Diana'], n),
    'quantity': np.random.randint(1, 50, n),
    'unit_price': np.random.uniform(100, 1000, n).round(2)
})
sales['revenue'] = sales['quantity'] * sales['unit_price']

print("=== Sales Data Sample ===")
print(sales.head())

# Basic groupby
print("\n=== Revenue by Region ===")
print(sales.groupby('region')['revenue'].sum().round(2))

# Multiple aggregations
print("\n=== Multiple Aggregations by Region ===")
print(sales.groupby('region')['revenue'].agg(['sum', 'mean', 'count']).round(2))

# Named aggregations (clean syntax)
print("\n=== Named Aggregations by Region ===")
result = sales.groupby('region').agg(
    total_revenue=('revenue', 'sum'),
    avg_revenue=('revenue', 'mean'),
    total_quantity=('quantity', 'sum'),
    num_transactions=('revenue', 'count')
).round(2)
print(result)

# Multiple groupby columns
print("\n=== Revenue by Region and Product ===")
multi_group = sales.groupby(['region', 'product'])['revenue'].sum().round(2)
print(multi_group)

# Unstack for pivot-like view
print("\n=== Unstacked View ===")
print(multi_group.unstack(fill_value=0).round(2))
```

### What Are Advanced GroupBy Operations?

```python
# =============================================================================
# ADVANCED GROUPBY
# =============================================================================

# Transform - apply function and broadcast back to original shape
sales['revenue_pct_of_region'] = (
    sales['revenue'] / 
    sales.groupby('region')['revenue'].transform('sum') * 100
).round(2)

print("=== Transform Example (% of Region Revenue) ===")
print(sales[['region', 'revenue', 'revenue_pct_of_region']].head(10))

# Filter - keep groups that meet condition
large_regions = sales.groupby('region').filter(lambda x: x['revenue'].sum() > 3_000_000)
print(f"\n=== Regions with > 3M revenue: {large_regions['region'].nunique()} regions ===")

# Apply - custom functions
def top_products(group, n=2):
    """Get top n products by revenue for each group."""
    return group.nlargest(n, 'revenue')[['product', 'revenue']]

print("\n=== Top 2 Products per Region ===")
top_by_region = sales.groupby('region').apply(top_products, n=2)
print(top_by_region)

# Window functions with groupby
sales['cumulative_revenue'] = sales.groupby('region')['revenue'].cumsum()
sales['rolling_avg'] = sales.groupby('region')['revenue'].transform(
    lambda x: x.rolling(7, min_periods=1).mean()
).round(2)

print("\n=== Window Functions by Region ===")
print(sales[['date', 'region', 'revenue', 'cumulative_revenue', 'rolling_avg']].head(15))

# Rank within groups
sales['rank_in_region'] = sales.groupby('region')['revenue'].rank(ascending=False)

# Percent rank
sales['percentile_in_region'] = sales.groupby('region')['revenue'].rank(pct=True)

print("\n=== Ranking within Groups ===")
print(sales[['region', 'revenue', 'rank_in_region', 'percentile_in_region']].head(10))
```

## Section 4: Merging and Joining

### How Do You Combine DataFrames?

```python
# =============================================================================
# MERGING AND JOINING
# =============================================================================

# Create sample dataframes
customers = pd.DataFrame({
    'customer_id': [1, 2, 3, 4, 5],
    'name': ['Alice', 'Bob', 'Charlie', 'Diana', 'Eve'],
    'city': ['Mumbai', 'Delhi', 'Bangalore', 'Chennai', 'Hyderabad']
})

orders = pd.DataFrame({
    'order_id': [101, 102, 103, 104, 105, 106],
    'customer_id': [1, 2, 2, 3, 5, 6],  # Note: 6 doesn't exist in customers
    'amount': [500, 750, 300, 450, 900, 200]
})

products = pd.DataFrame({
    'order_id': [101, 102, 103, 104],
    'product': ['Laptop', 'Phone', 'Tablet', 'Accessories']
})

print("=== Customers ===")
print(customers)
print("\n=== Orders ===")
print(orders)

# Inner join (only matching rows)
inner = pd.merge(customers, orders, on='customer_id', how='inner')
print("\n=== Inner Join ===")
print(inner)

# Left join (all from left, matching from right)
left = pd.merge(customers, orders, on='customer_id', how='left')
print("\n=== Left Join ===")
print(left)

# Right join (all from right, matching from left)
right = pd.merge(customers, orders, on='customer_id', how='right')
print("\n=== Right Join ===")
print(right)

# Outer join (all rows from both)
outer = pd.merge(customers, orders, on='customer_id', how='outer')
print("\n=== Outer Join ===")
print(outer)

# Multiple joins (chain)
full_data = (
    orders
    .merge(customers, on='customer_id', how='left')
    .merge(products, on='order_id', how='left')
)
print("\n=== Chained Joins ===")
print(full_data)

# Different column names
df1 = pd.DataFrame({'id_left': [1, 2, 3], 'value1': ['a', 'b', 'c']})
df2 = pd.DataFrame({'id_right': [1, 2, 4], 'value2': ['x', 'y', 'z']})
merged = pd.merge(df1, df2, left_on='id_left', right_on='id_right', how='outer')
print("\n=== Merge with Different Column Names ===")
print(merged)
```

### How Do You Concatenate DataFrames?

```python
# =============================================================================
# CONCATENATION
# =============================================================================

# Vertical concatenation (stack rows)
df1 = pd.DataFrame({'A': [1, 2], 'B': [3, 4]})
df2 = pd.DataFrame({'A': [5, 6], 'B': [7, 8]})
df3 = pd.DataFrame({'A': [9, 10], 'B': [11, 12]})

stacked = pd.concat([df1, df2, df3], ignore_index=True)
print("=== Vertical Concatenation ===")
print(stacked)

# Horizontal concatenation (add columns)
df_left = pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]})
df_right = pd.DataFrame({'C': [7, 8, 9], 'D': [10, 11, 12]})

side_by_side = pd.concat([df_left, df_right], axis=1)
print("\n=== Horizontal Concatenation ===")
print(side_by_side)

# Concatenation with mismatched columns
df_a = pd.DataFrame({'A': [1, 2], 'B': [3, 4]})
df_b = pd.DataFrame({'A': [5, 6], 'C': [7, 8]})

concat_outer = pd.concat([df_a, df_b], ignore_index=True)  # Fills with NaN
concat_inner = pd.concat([df_a, df_b], join='inner', ignore_index=True)  # Only common columns

print("\n=== Concat with Mismatched Columns (outer) ===")
print(concat_outer)
print("\n=== Concat with Mismatched Columns (inner) ===")
print(concat_inner)
```

## Section 5: Pivot Tables and Reshaping

### How Do You Create Pivot Tables?

```python
# =============================================================================
# PIVOT TABLES
# =============================================================================

# Create sales data
sales_data = pd.DataFrame({
    'date': pd.date_range('2024-01-01', periods=100, freq='D').repeat(4),
    'region': ['North', 'South', 'East', 'West'] * 100,
    'product': np.tile(np.repeat(['Laptop', 'Phone'], 2), 100),
    'revenue': np.random.uniform(1000, 5000, 400).round(2)
})

print("=== Sales Data Sample ===")
print(sales_data.head(10))

# Basic pivot table
pivot_basic = pd.pivot_table(
    sales_data,
    values='revenue',
    index='region',
    columns='product',
    aggfunc='sum'
).round(2)

print("\n=== Basic Pivot Table ===")
print(pivot_basic)

# Multiple aggregation functions
pivot_multi = pd.pivot_table(
    sales_data,
    values='revenue',
    index='region',
    columns='product',
    aggfunc=['sum', 'mean', 'count']
).round(2)

print("\n=== Pivot with Multiple Aggregations ===")
print(pivot_multi)

# Multiple indexes and values
pivot_complex = pd.pivot_table(
    sales_data,
    values='revenue',
    index=['region'],
    columns=['product'],
    aggfunc='sum',
    margins=True,  # Add totals
    margins_name='Total'
).round(2)

print("\n=== Pivot with Margins ===")
print(pivot_complex)
```

### How Do You Reshape Data with Melt and Stack?

```python
# =============================================================================
# RESHAPING: WIDE TO LONG AND VICE VERSA
# =============================================================================

# Wide format data
df_wide = pd.DataFrame({
    'student': ['Alice', 'Bob', 'Charlie'],
    'math': [85, 92, 78],
    'science': [90, 88, 95],
    'english': [88, 85, 92]
})

print("=== Wide Format ===")
print(df_wide)

# Melt: Wide to Long
df_long = pd.melt(
    df_wide,
    id_vars=['student'],
    value_vars=['math', 'science', 'english'],
    var_name='subject',
    value_name='score'
)

print("\n=== Long Format (after melt) ===")
print(df_long)

# Pivot: Long to Wide
df_back_to_wide = df_long.pivot(
    index='student',
    columns='subject',
    values='score'
)

print("\n=== Back to Wide (after pivot) ===")
print(df_back_to_wide)

# Stack and Unstack
df = pd.DataFrame({
    'A': [1, 2, 3],
    'B': [4, 5, 6]
}, index=['x', 'y', 'z'])

print("\n=== Original ===")
print(df)

stacked = df.stack()
print("\n=== Stacked ===")
print(stacked)

unstacked = stacked.unstack()
print("\n=== Unstacked ===")
print(unstacked)
```

## Section 6: Time Series with Pandas

### How Do You Work with Time Series Data?

```python
# =============================================================================
# TIME SERIES FUNDAMENTALS
# =============================================================================

# Create time series data
dates = pd.date_range('2024-01-01', periods=365, freq='D')
ts_data = pd.DataFrame({
    'date': dates,
    'sales': np.random.poisson(100, 365) + np.sin(np.arange(365) * 2 * np.pi / 365) * 20,
    'temperature': 25 + 10 * np.sin(np.arange(365) * 2 * np.pi / 365) + np.random.randn(365) * 3
})

# Set date as index
ts_data.set_index('date', inplace=True)

print("=== Time Series Data ===")
print(ts_data.head(10))

# Date components
print("\n=== Date Components ===")
print(f"First date: {ts_data.index[0]}")
print(f"Last date: {ts_data.index[-1]}")
print(f"Date range: {ts_data.index.max() - ts_data.index.min()}")

# Accessing by date
print("\n=== Slicing by Date ===")
print(ts_data['2024-01'].head())  # All of January
print(ts_data['2024-01-15':'2024-01-20'])  # Date range

# Resampling (aggregating to different frequency)
print("\n=== Monthly Aggregation ===")
monthly = ts_data.resample('M').agg({
    'sales': 'sum',
    'temperature': 'mean'
}).round(2)
print(monthly.head())

# Weekly with multiple aggregations
print("\n=== Weekly Statistics ===")
weekly = ts_data.resample('W').agg({
    'sales': ['sum', 'mean', 'std'],
    'temperature': ['mean', 'min', 'max']
}).round(2)
print(weekly.head())

# Rolling windows
ts_data['sales_7d_avg'] = ts_data['sales'].rolling(7).mean()
ts_data['sales_30d_avg'] = ts_data['sales'].rolling(30).mean()

print("\n=== Rolling Averages ===")
print(ts_data[['sales', 'sales_7d_avg', 'sales_30d_avg']].tail(10).round(2))

# Expanding window (cumulative)
ts_data['sales_cumsum'] = ts_data['sales'].expanding().sum()
ts_data['sales_cum_avg'] = ts_data['sales'].expanding().mean()

print("\n=== Cumulative Statistics ===")
print(ts_data[['sales', 'sales_cumsum', 'sales_cum_avg']].tail(10).round(2))
```

### How Do You Handle Date/Time Operations?

```python
# =============================================================================
# DATE/TIME OPERATIONS
# =============================================================================

# Create datetime column
df_dates = pd.DataFrame({
    'timestamp': pd.date_range('2024-01-01 08:00:00', periods=100, freq='H')
})

# Extract components
df_dates['year'] = df_dates['timestamp'].dt.year
df_dates['month'] = df_dates['timestamp'].dt.month
df_dates['day'] = df_dates['timestamp'].dt.day
df_dates['hour'] = df_dates['timestamp'].dt.hour
df_dates['day_of_week'] = df_dates['timestamp'].dt.day_name()
df_dates['is_weekend'] = df_dates['timestamp'].dt.dayofweek >= 5

print("=== DateTime Components ===")
print(df_dates.head(10))

# Date arithmetic
df_dates['days_from_start'] = (df_dates['timestamp'] - df_dates['timestamp'].min()).dt.days
df_dates['next_day'] = df_dates['timestamp'] + pd.Timedelta(days=1)

print("\n=== Date Arithmetic ===")
print(df_dates[['timestamp', 'days_from_start', 'next_day']].head())

# Business days
business_dates = pd.bdate_range('2024-01-01', periods=20)
print(f"\n=== Business Days ===")
print(f"First 10 business days: {business_dates[:10].tolist()}")

# Timezone handling
df_tz = pd.DataFrame({
    'local_time': pd.date_range('2024-01-01', periods=5, freq='D')
})
df_tz['utc'] = df_tz['local_time'].dt.tz_localize('UTC')
df_tz['ist'] = df_tz['utc'].dt.tz_convert('Asia/Kolkata')

print("\n=== Timezone Conversion ===")
print(df_tz)

# Lag and lead (shift)
ts_df = pd.DataFrame({
    'date': pd.date_range('2024-01-01', periods=10, freq='D'),
    'value': range(10)
})

ts_df['lag_1'] = ts_df['value'].shift(1)    # Previous value
ts_df['lead_1'] = ts_df['value'].shift(-1)  # Next value
ts_df['diff'] = ts_df['value'].diff()        # Difference from previous
ts_df['pct_change'] = ts_df['value'].pct_change() * 100

print("\n=== Lag, Lead, and Differences ===")
print(ts_df)
```

## Section 7: String Operations

### How Do You Work with Text Data?

```python
# =============================================================================
# STRING OPERATIONS
# =============================================================================

# Create text data
df_text = pd.DataFrame({
    'name': ['  John Smith  ', 'jane DOE', 'Bob Johnson', 'ALICE williams'],
    'email': ['john@example.com', 'jane@COMPANY.com', 'bob@test.org', 'alice@sample.net'],
    'phone': ['123-456-7890', '(234) 567-8901', '345.678.9012', '456 789 0123'],
    'address': ['123 Main St, Mumbai', '456 Oak Ave, Delhi', '789 Pine Rd, Chennai', '012 Elm Blvd, Bangalore']
})

print("=== Original Text Data ===")
print(df_text)

# Basic string methods (using .str accessor)
df_text['name_clean'] = df_text['name'].str.strip().str.title()
df_text['email_lower'] = df_text['email'].str.lower()

print("\n=== Cleaned Names and Emails ===")
print(df_text[['name', 'name_clean', 'email', 'email_lower']])

# String extraction
df_text['email_domain'] = df_text['email'].str.extract(r'@(\w+\.\w+)')
df_text['phone_digits'] = df_text['phone'].str.replace(r'\D', '', regex=True)

print("\n=== Extracted Data ===")
print(df_text[['email', 'email_domain', 'phone', 'phone_digits']])

# Split strings
df_text['city'] = df_text['address'].str.split(',').str[-1].str.strip()
df_text[['first_name', 'last_name']] = df_text['name_clean'].str.split(' ', n=1, expand=True)

print("\n=== Split Data ===")
print(df_text[['name_clean', 'first_name', 'last_name', 'city']])

# String contains and matching
df_text['is_example_email'] = df_text['email'].str.contains('example')
df_text['starts_with_j'] = df_text['name_clean'].str.startswith('J')

print("\n=== String Matching ===")
print(df_text[['email', 'is_example_email', 'name_clean', 'starts_with_j']])

# Replace patterns
df_text['phone_formatted'] = df_text['phone_digits'].str.replace(
    r'(\d{3})(\d{3})(\d{4})', r'(\1) \2-\3', regex=True
)

print("\n=== Formatted Phone ===")
print(df_text[['phone', 'phone_formatted']])
```

## Section 8: Performance Optimization

### How Do You Optimize Pandas Performance?

```python
# =============================================================================
# PERFORMANCE OPTIMIZATION
# =============================================================================

import time

# Create large dataset
np.random.seed(42)
n = 100_000

df_large = pd.DataFrame({
    'id': range(n),
    'category': np.random.choice(['A', 'B', 'C', 'D'], n),
    'value1': np.random.randn(n),
    'value2': np.random.randn(n),
    'date': pd.date_range('2020-01-01', periods=n, freq='T')
})

print(f"Dataset size: {len(df_large):,} rows")

# 1. Use categorical for repeated strings
print("\n=== Memory Optimization with Categorical ===")
print(f"Before: {df_large['category'].memory_usage(deep=True):,} bytes")
df_large['category'] = df_large['category'].astype('category')
print(f"After:  {df_large['category'].memory_usage(deep=True):,} bytes")

# 2. Vectorized operations vs apply
def calculate_slow(row):
    return row['value1'] ** 2 + row['value2'] ** 2

def benchmark_operations():
    print("\n=== Vectorization vs Apply ===")
    
    # Slow: apply
    start = time.time()
    result1 = df_large.apply(calculate_slow, axis=1)
    apply_time = time.time() - start
    
    # Fast: vectorized
    start = time.time()
    result2 = df_large['value1'] ** 2 + df_large['value2'] ** 2
    vectorized_time = time.time() - start
    
    print(f"Apply time: {apply_time:.3f} seconds")
    print(f"Vectorized time: {vectorized_time:.3f} seconds")
    print(f"Speedup: {apply_time/vectorized_time:.1f}x")

benchmark_operations()

# 3. Use query for complex filtering
print("\n=== Query vs Boolean Indexing ===")

start = time.time()
result1 = df_large[(df_large['value1'] > 0) & (df_large['value2'] < 0)]
bool_time = time.time() - start

start = time.time()
result2 = df_large.query('value1 > 0 and value2 < 0')
query_time = time.time() - start

print(f"Boolean indexing: {bool_time*1000:.2f} ms")
print(f"Query: {query_time*1000:.2f} ms")

# 4. Efficient iteration (when you must iterate)
print("\n=== Iteration Methods ===")

# Never do this (very slow)
# for index, row in df_large.iterrows():
#     pass

# Better: itertuples
start = time.time()
for row in df_large.head(10000).itertuples():
    _ = row.value1 + row.value2
itertuples_time = time.time() - start

# Even better: vectorize!
start = time.time()
result = df_large.head(10000)['value1'] + df_large.head(10000)['value2']
vectorized_time = time.time() - start

print(f"itertuples (10k rows): {itertuples_time*1000:.2f} ms")
print(f"Vectorized (10k rows): {vectorized_time*1000:.2f} ms")

# 5. Chunked reading for large files
print("\n=== Chunked Processing (simulated) ===")
chunk_size = 10000
n_chunks = len(df_large) // chunk_size

total = 0
for i in range(n_chunks):
    chunk = df_large.iloc[i*chunk_size:(i+1)*chunk_size]
    total += chunk['value1'].sum()

print(f"Processed {n_chunks} chunks of {chunk_size:,} rows each")
```

## Frequently Asked Questions

### When should I use Pandas vs SQL?

Use Pandas when:
- Data fits in memory
- Exploratory data analysis
- Complex transformations
- Integration with Python ML libraries

Use SQL when:
- Data is in a database
- Very large datasets
- Need to join across many tables
- Sharing queries with non-Python users

### How do I handle large datasets that don't fit in memory?

1. **Chunked reading:** `pd.read_csv(..., chunksize=10000)`
2. **Use Dask:** Drop-in replacement for larger-than-memory data
3. **Use appropriate dtypes:** Categorical for strings, smaller numeric types
4. **Process and aggregate in chunks**
5. **Use databases for very large data**

### What's the difference between loc and iloc?

- `loc`: Label-based indexing (uses index labels)
- `iloc`: Integer-based indexing (uses positions)

```python
df.loc['row_label', 'column_name']  # By label
df.iloc[0, 1]  # By position (first row, second column)
```

### How do I avoid SettingWithCopyWarning?

Use `.loc` for assignment:
```python
# Warning: df[df['a'] > 0]['b'] = 1
# Correct: 
df.loc[df['a'] > 0, 'b'] = 1
```

## Key Takeaways

1. **DataFrames are powerful** - Master selection, filtering, and manipulation

2. **GroupBy is essential** - Learn split-apply-combine for aggregation

3. **Clean data systematically** - Handle missing values, duplicates, and types

4. **Merge with intention** - Understand join types and when to use each

5. **Vectorize operations** - Avoid loops; use Pandas/NumPy operations

6. **Optimize memory** - Use appropriate dtypes and categorical data

## Next Steps in Your Learning Journey

Now that you've mastered Pandas:

1. **Learn data visualization** with Matplotlib and Seaborn
2. **Explore scikit-learn** for machine learning
3. **Study Dask** for larger-than-memory data
4. **Practice with real datasets** from Kaggle

---

*Last updated: January 2025. This article covers Pandas best practices for data science applications.*
