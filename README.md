# INTRODUCTION

This project focuses on analyzing a pizza sales dataset to uncover key business insights using SQL and data visualization tools. The dataset consists of four CSV files: orders, order_details, pizzas, and pizza_types, each containing crucial information about sales transactions, pizza types, pricing, and order metadata.

The main goals of the analysis are to:

Understand overall sales performance.

Identify top-selling pizzas and categories.

Analyze order trends by time and day.

Determine revenue distribution across different pizza types and sizes.

To achieve these goals, I used MySQL for data querying, Excel/Google Sheets for initial exploration, and Python libraries such as Matplotlib and Seaborn to visualize the results. Git and GitHub were used for version control and documentation throughout the project.


## Background
![image](https://github.com/user-attachments/assets/d537ccd0-2857-4b94-b090-2acbe5b98a5a)


## The questions I wanted to answer through my SQL queries were:
How many total orders were placed?
What is the total revenue generated from pizza sales?
Which pizza has the highest price?
What is the most common pizza size ordered?
What are the top 5 most ordered pizza types by quantity?
What is the total quantity of pizzas ordered by category?
How are orders distributed by hour of the day?
How many pizzas exist in each category?
What is the average number of pizzas ordered per day?
What are the top 3 pizza types by total revenue?
What percentage of total revenue does each pizza type contribute?
How does revenue accumulate over time (cumulative revenue by date)?
What are the top 3 pizza types by revenue within each category?


# Tools I Used
MySQL – For storing and querying data using SQL.

SQL – To perform data analysis with joins, aggregations, filters, and subqueries.

Git – For version control and tracking project changes.

GitHub – To host and share code, queries, visualizations, and documentation.

Excel / Google Sheets – For initial data cleaning, exploration, and CSV inspection.

Matplotlib – To create visualizations such as line plots and bar charts.

Seaborn – For statistical and aesthetically enhanced visualizations.




# The Analysis

### 1. Total Number of Orders Placed
This query counts how many unique orders were placed.
```sql
SELECT COUNT(DISTINCT order_id) AS total_orders
FROM orders;
``` 
 ### 2. Total Revenue Generated from Pizza Sales
Calculates the total revenue by multiplying quantity and price for each pizza.
```sql
SELECT ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_revenue
FROM order_details
JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id;
```

### 3. Highest Priced Pizza
Identifies the most expensive pizza based on price.
```sql
SELECT pizza_id, price
FROM pizzas
ORDER BY price DESC
LIMIT 1;
```

### 4. Most Common Pizza Size Ordered
Finds out which pizza size is ordered the most.
```sql
SELECT size, COUNT(*) AS total_orders
FROM pizzas
JOIN order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY size
ORDER BY total_orders DESC
LIMIT 1;
```

### 5. Top 5 Most Ordered Pizza Types
Displays the top 5 pizza types based on total quantity ordered.
```sql
SELECT pt.name, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY total_quantity DESC
LIMIT 5;
```

### 6. Total Quantity per Pizza Category
Shows how many pizzas were ordered in each category.
```sql
SELECT pt.category, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category;
```


### 7. Order Distribution by Hour
Helps understand customer ordering behavior over the day.
```sql
SELECT HOUR(time) AS order_hour, COUNT(*) AS total_orders
FROM orders
GROUP BY order_hour
ORDER BY order_hour;
```

### 8. Category-wise Pizza Distribution
Shows how pizzas are distributed across categories.
```sql
SELECT pt.category, COUNT(DISTINCT p.pizza_id) AS total_pizzas
FROM pizza_types pt
JOIN pizzas p ON pt.pizza_type_id = p.pizza_type_id
GROUP BY pt.category;
```


### 9. Average Pizzas Ordered Per Day
Calculates the average number of pizzas ordered daily.
```sql
SELECT date, ROUND(AVG(pizzas_per_order), 2) AS avg_pizzas
FROM (
    SELECT date, COUNT(*) AS pizzas_per_order
    FROM orders o
    JOIN order_details od ON o.order_id = od.order_id
    GROUP BY o.order_id, date
) AS daily_orders
GROUP BY date;
```

### 10. Top 3 Most Ordered Pizza Types by Revenue
Ranks pizza types based on revenue.
```sql
SELECT pt.name, ROUND(SUM(od.quantity * p.price), 2) AS revenue
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY revenue DESC
LIMIT 3;
```

### 11. Percentage Revenue Contribution per Pizza Type
Calculates each pizza type’s contribution to the total revenue.
```sql
WITH revenue_cte AS (
  SELECT pt.name, SUM(od.quantity * p.price) AS revenue
  FROM order_details od
  JOIN pizzas p ON od.pizza_id = p.pizza_id
  JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
  GROUP BY pt.name
)
SELECT name,
       ROUND(revenue, 2) AS revenue,
       ROUND(100 * revenue / SUM(revenue) OVER (), 2) AS revenue_percentage
FROM revenue_cte
ORDER BY revenue_percentage DESC;
```

### 12. Cumulative Revenue Over Time
Tracks total revenue growth day by day.
```sql
SELECT date,
       ROUND(SUM(od.quantity * p.price), 2) AS daily_revenue,
       ROUND(SUM(SUM(od.quantity * p.price)) OVER (ORDER BY o.date), 2) AS cumulative_revenue
FROM orders o
JOIN order_details od ON o.order_id = od.order_id
JOIN pizzas p ON od.pizza_id = p.pizza_id
GROUP BY date
ORDER BY date;
```

### 13. Top 3 Revenue-Generating Pizzas by Category
Ranks the top 3 pizzas by revenue within each category.
```sql
SELECT category, name, revenue
FROM (
  SELECT pt.category, pt.name,
         ROUND(SUM(od.quantity * p.price), 2) AS revenue,
         RANK() OVER (PARTITION BY pt.category ORDER BY SUM(od.quantity * p.price) DESC) AS rank
  FROM order_details od
  JOIN pizzas p ON od.pizza_id = p.pizza_id
  JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
  GROUP BY pt.category, pt.name
) ranked
WHERE rank <= 3;
```




























