# INTRODUCTION

This project focuses on analyzing a pizza sales dataset to uncover key business insights using SQL and visualisation. The dataset consists of four CSV files: orders, order_details, pizzas, and pizza_types, each containing crucial information about sales transactions, pizza types, pricing, and order metadata.

The main goals of the analysis are to:

Understand overall sales performance.

Identify top-selling pizzas and categories.

Analyze order trends by time and day.

Determine revenue distribution across different pizza types and sizes.

To achieve these goals, I used MySQL for data querying, Excel/Google Sheets for initial exploration, and Python libraries such as Matplotlib and Seaborn to visualize the results. Git and GitHub were used for version control and documentation throughout the project.






## Background
![image](https://github.com/user-attachments/assets/d537ccd0-2857-4b94-b090-2acbe5b98a5a)


# The questions I wanted to answer through my SQL queries were:
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






# The Analysis

### 1. Total Number of Orders Placed
This query counts how many unique orders were placed.
```sql
SELECT COUNT(DISTINCT order_id) AS total_orders
FROM orders;
```

![image](https://github.com/user-attachments/assets/645ddae7-96cb-46a8-8f68-dd50485dfd6e)


&nbsp;

 A total of 21,350 orders were placed, reflecting steady customer demand across the dataset.


&nbsp;


 ### 2. Total Revenue Generated from Pizza Sales
Calculates the total revenue by multiplying quantity and price for each pizza.
```sql
SELECT ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_revenue
FROM order_details
JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id;
```

![image](https://github.com/user-attachments/assets/48fa62b1-d6a0-4750-bdfc-4e6b3084d9bd)

&nbsp;

he business earned approximately $817,860 in total revenue from pizza sales.

&nbsp;

### 3. Highest Priced Pizza
Identifies the most expensive pizza based on price.
```sql
SELECT pizza_id, price
FROM pizzas
ORDER BY price DESC
LIMIT 1;
```

![image](https://github.com/user-attachments/assets/7214f8b1-ad00-4ee4-8e92-d29c19472ccc)

&nbsp;

 The Greek Pizza (XXL) is the most expensive item on the menu at $35.95.
 
 &nbsp;

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
![image](https://github.com/user-attachments/assets/7d674213-0962-4262-be58-4676632b0f72)

&nbsp;

 The most popular size among customers is Large, dominating overall pizza sales.
 
 &nbsp;



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
![image](https://github.com/user-attachments/assets/7e69c226-2b33-439d-b557-3ef8dd8b764a)

&nbsp;


 The five most frequently ordered pizzas include Pepperoni, BBQ Chicken, Margherita, Hawaiian, and Veggie Lovers,each sold over 800 units, showcasing their consistent appeal to the customer base.

&nbsp;

### 6. Total Quantity per Pizza Category
Shows how many pizzas were ordered in each category.
```sql
SELECT pt.category, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category;
```

![image](https://github.com/user-attachments/assets/d2783da6-8d4c-4353-8829-6679a5459ff1)

&nbsp;

The Classic category led in sales volume, followed by Specialty and Veggie categories.

&nbsp;

### 7. Order Distribution by Hour
Helps understand customer ordering behavior over the day.
```sql
SELECT HOUR(time) AS order_hour, COUNT(*) AS total_orders
FROM orders
GROUP BY order_hour
ORDER BY order_hour;
```

![image](https://github.com/user-attachments/assets/7e6dd247-974d-4fec-aafb-1c14832e6680)

&nbsp;


Peak order times are around 12 PM (lunch) and 7 PM (dinner), reflecting a typical consumer behavior, with traffic highest during major mealtimes.
&nbsp;


### 8. Category-wise Pizza Distribution
Shows how pizzas are distributed across categories.
```sql
SELECT pt.category, COUNT(DISTINCT p.pizza_id) AS total_pizzas
FROM pizza_types pt
JOIN pizzas p ON pt.pizza_type_id = p.pizza_type_id
GROUP BY pt.category;
```

![image](https://github.com/user-attachments/assets/4d559802-1622-4abb-8590-dc2f67cc8b1e)

&nbsp;


 Out of all pizzas, there are 10 Classic, 8 Specialty, and 7 Veggie types available.This shows a balanced menu with a slight edge toward classic offerings.

&nbsp;


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
![image](https://github.com/user-attachments/assets/bfbaf83e-9353-4ed9-9005-ed86a3615d67)

&nbsp;


An average of 138 pizzas are sold per day, based on total quantity grouped by order date.

&nbsp;


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
![image](https://github.com/user-attachments/assets/929c395d-1b61-42f9-818a-8ce4ae99d2bd)

&nbsp;

The highest revenue-generating pizzas are Classic Pepperoni, BBQ Chicken, and Supreme Pizza, with individual revenues above $14,000.
&nbsp;

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

![image](https://github.com/user-attachments/assets/fa400dda-059e-4b14-bd3f-e76e98b30651)

&nbsp;

epperoni leads with ~23% of total revenue, followed by BBQ Chicken (~20.5%), Supreme (~19.7%), Veggie Lovers (~19%), and Cheese (~18.3%), collectively accounting for over 100% due to rounding in stacked contributions.
&nbsp;


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


![image](https://github.com/user-attachments/assets/f305b88b-7fa0-4244-bd88-a16cb889aad2)

&nbsp;

Revenue shows a steady upward trend, surpassing $800,000 by the dataset’s end.This growth curve demonstrates consistent sales and business scalability.

&nbsp;

### 13. Top 3 Revenue-Generating Pizzas by Category
Ranks the top 3 pizzas by revenue within each category.
```sql
SELECT category, name, revenue, rn
FROM (
    SELECT 
        category, 
        name, 
        revenue,
        RANK() OVER(PARTITION BY category ORDER BY revenue DESC) AS rn
    FROM (
        SELECT 
            pizza_types.category, 
            pizza_types.name,
            SUM(order_details.quantity * pizzas.price) AS revenue
        FROM pizza_types 
        JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
        GROUP BY pizza_types.category, pizza_types.name
    ) AS a
) AS ranked_pizzas
WHERE rn <= 3;
```

![image](https://github.com/user-attachments/assets/bd35c0c5-cbfc-4398-8870-cb6c35066e6b)

&nbsp;

Each category has standout performers:

 Classic: Pepperoni, Cheese, Margherita

 Veggie: Veggie Lovers, Mushroom, Spinach

 Specialty: BBQ Chicken, Supreme, Hawaiian
Each generated over $13,000, highlighting their popularity and profitability.

&nbsp;
&nbsp;
&nbsp;

_All visualizations in this project were generated with the help of ChatGPT using Python libraries like Matplotlib and Seaborn. This ensured consistent styling, accurate insights, and efficient analysis presentation._































