# Data Analytics Assignment - User Behavior, Cooking Preferences, and Order Trends

## Overview
This project involves analyzing datasets related to user behavior, cooking preferences, and order trends for a food delivery platform. The task is to clean, merge, and analyze the data to uncover insights into customer preferences, popular dishes, and demographic factors that influence user behavior.

## Datasets Used
The project uses the following datasets:
1. **UserDetails**: Contains information about the users including their ID, name, age, location, registration date, and favorite meal.
2. **CookingSessions**: Includes details about each cooking session such as session ID, user ID, dish name, meal type, session duration, and session rating.
3. **OrderDetails**: Records of orders placed by users, including order ID, user ID, meal type, dish name, order status, amount, and time of day.

## Data Processing Steps
### 1. **Cleaning Data**
   - Removed canceled orders from the **OrderDetails** dataset to focus only on completed orders.

### 2. **Merging Datasets**
   - The datasets **UserDetails**, **CookingSessions**, and **OrderDetails** were merged on relevant columns such as `User ID` and `Session ID`.

### 3. **Insights and Analysis**
   - **Popular Dishes**: Identified the top 5 most ordered dishes and analyzed their average ratings.
   - **Average Order Amount by Location**: Analyzed the average spending per location.
   - **Meal Preferences**: Analyzed the frequency of meal types (Breakfast, Lunch, Dinner) ordered by each user.
   - **User Activity**: Calculated the total number of orders placed by each user.
   - **Rating Trends by Dish**: Analyzed the average ratings for dishes and identified top-rated dishes.
   - **Order Time Analysis**: Examined the number of orders and average ratings based on the time of day.

## Code Implementation

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Reading data from Excel file
data = pd.read_excel(r"C:\Users\Admin\Downloads\Assignment.xlsx", sheet_name=None)

# Extracting individual datasets
UserDetails = data['UserDetails.csv']
CookingSessions = data['CookingSessions.csv']
OrderDetails = data['OrderDetails.csv']

# Removing canceled orders
df_clean_orders = OrderDetails[OrderDetails['Order Status'] != 'Canceled']

# Merging datasets
df_merged = pd.merge(df_clean_orders, CookingSessions,  on=['Session ID', 'User ID'], how='inner')
df_final = pd.merge(UserDetails, df_merged, on='User ID', how='inner')

# Popular dishes by number of orders and ratings
popular_dishes = df_final.groupby('Dish Name_x').agg(
    Total_Orders=('Order ID', 'count'),
    Average_Rating=('Rating', 'mean')
).reset_index().sort_values(by='Total_Orders', ascending=False)

# Visualizing Top 5 Popular Dishes
plt.figure(figsize=(10, 6))
sns.barplot(data=popular_dishes.head(5), x='Dish Name_x', y='Total_Orders', palette='coolwarm')
plt.title('Top 5 Popular Dishes by Number of Orders')
plt.xlabel('Dish Name')
plt.ylabel('Total Orders')
plt.show()

# Average order amount by location
location_order_amount = df_final.groupby('Location').agg(
    Average_Amount=('Amount (USD)', 'mean')
).reset_index().sort_values(by='Average_Amount', ascending=False)

# Visualizing Average Order Amount by Location
plt.figure(figsize=(12, 6))
sns.barplot(data=location_order_amount, x='Location', y='Average_Amount', palette='viridis')
plt.title('Average Order Amount by Location')
plt.xticks(rotation=45)
plt.show()

# Meal preferences per user
user_meal_type = df_final.groupby(['User ID', 'Meal Type_x']).size().reset_index(name='Order Count')

# Total orders per user
Total_orders_per_user = df_final.groupby('User ID').size().reset_index(name='Total Orders')

# Rating trends by dish type
rating_trends_by_dish = df_final.groupby('Dish Name_x').agg(
    Average_Rating=('Rating', 'mean'),
    Rating_Count=('Rating', 'count')
).reset_index().sort_values(by='Average_Rating', ascending=False)

# Visualizing Top 5 Dishes by Average Rating
plt.figure(figsize=(12, 6))
sns.barplot(data=rating_trends_by_dish.head(5), x='Dish Name_x', y='Average_Rating', palette='coolwarm')
plt.title('Top 5 Dishes by Average Rating')
plt.xlabel('Dish Name')
plt.ylabel('Average Rating')
plt.show()

# Order time analysis (by time of day)
order_time_analysis = df_final.groupby('Time of Day').agg(
    Total_Orders=('Order ID', 'count'),
    Average_Rating=('Rating', 'mean')
).reset_index()
