import pandas as pd
import numpy as np
import seaborn as sns
np.random.seed(42)
import matplotlib.pyplot as plt
df = pd.read_csv("/content/ecommerce_dataset.csv")
df.head()
df.info()
df.isnull().sum()
df.describe()
#Data Cleaning ,Handle Missing Values
df['Customer_Name'].fillna("Unknown", inplace=True)
df['Email'].fillna("Not Available", inplace=True)
df['City'].fillna("Unknown", inplace=True)

df['Price'] = pd.to_numeric(df['Price'], errors='coerce')
df['Quantity'] = pd.to_numeric(df['Quantity'], errors='coerce')

df['Price'].fillna(df['Price'].median(), inplace=True)
df['Quantity'].fillna(df['Quantity'].median(), inplace=True)
#Remove Duplicates
df = df.drop_duplicates()
df
#Fix Data Types
df['Order_Date'] = pd.to_datetime(df['Order_Date'])
df
#Handle Outliers
df = df[df['Price'] > 0]
df = df[df['Quantity'] < 50]
df
#Analysis
df['Revenue'] = df['Price'] * df['Quantity']

top_products = df.groupby('Product')['Quantity'].sum().sort_values(ascending=False)
category_revenue = df.groupby('Category')['Revenue'].sum()
sales_trend = df.groupby('Order_Date')['Revenue'].sum()
df
plt.figure(figsize=(20, 5))
#Bar plot-Total Sales by Product
plt.subplot(1, 3, 1)
sns.barplot(
    x='Product',
    y='Revenue',
    data=df,
    estimator=sum,
    palette='viridis'
)

plt.title('Total Sales by Product')
plt.ylabel('Total Revenue')
plt.xlabel('Product')
plt.xticks(rotation=45)
#Bar Plot – Sales by City
plt.subplot(1, 3, 2)
sns.barplot(
    x='City',
    y='Revenue',
    data=df,
    estimator=sum,
    palette='coolwarm'
)

plt.title('Sales by City')
plt.ylabel('Total Revenue')
plt.xlabel('City')
plt.xticks(rotation=45)
#Bar Plot – Category Revenue
plt.subplot(1, 3, 3)
sns.barplot(
    x='Category',
    y='Revenue',
    data=df,
    estimator=sum,
    palette='Set2'
)

plt.title('Revenue by Category')
plt.ylabel('Total Revenue')
plt.xlabel('Category')
#Bonus: Line Plot with Styling

sns.lineplot(
    x='Order_Date',
    y='Revenue',
    data=df,
    estimator=sum
)

plt.title('Revenue Trend Over Time')
plt.xlabel('Date')
plt.ylabel('Revenue')
plt.xticks(rotation=45)

plt.show()
#Line Chart
sales_trend.plot()
plt.title("Sales Trend")
plt.show()
#Pie Chart
category_revenue.plot(kind='pie', autopct='%1.1f%%')
plt.legend()
plt.show()
#Heatmap
sns.heatmap(df.corr(numeric_only=True), annot=True)
plt.show()
