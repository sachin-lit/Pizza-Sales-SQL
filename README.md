# 🍕 Pizza Sales SQL Case Study

This project is a SQL case study built on a pizza sales dataset.
It explores orders, revenue, pizza types, and customer ordering trends through structured queries.
A total of 13 SQL queries were written, ranging from basic to advanced.
The analysis includes revenue trends, category-wise contribution, top-selling items, and percentage breakdowns.
It demonstrates SQL skills in data analysis, aggregation, joins, subqueries, CTEs, and window functions.

---

## 📁 Dataset Overview

The dataset consists of 4 related tables (`pizzahut` database):

- **orders** → `order_id`, `date`, `time` — stores the date and time each order was placed
- **order_details** → `order_details_id` (PK), `order_id`, `pizza_id`, `quantity` — stores which pizzas (and how many) were part of each order
- **pizzas** → `pizza_id` (PK), `pizza_type_id`, `size`, `price` — stores price and size variant of each pizza
- **pizza_types** → `pizza_type_id` (PK), `name`, `category`, `ingredients` — stores the pizza's name, category (Classic/Chicken/Veggie/Supreme), and ingredients

**Relationships:**
- `orders.order_id` → `order_details.order_id`
- `order_details.pizza_id` → `pizzas.pizza_id`
- `pizzas.pizza_type_id` → `pizza_types.pizza_type_id`

---

## ✅ Queries 

### Q1 Retrive the total number of orders placed.
```SQL
select count(order_id) as Total_orders from orders; 
```

### Q2 Calculated total revenue generated from pizza sales;
```SQL
SELECT 
    ROUND(SUM(order_details.quantity * pizzas.price),
            2) AS total_sales
FROM
    order_details
        JOIN
    pizzas ON order_details.pizza_id = pizzas.pizza_id;
```

  ### Q3 Calculated total revenue generated from pizza sales;
```SQL
SELECT 
    ROUND(SUM(order_details.quantity * pizzas.price),
            2) AS total_sales
FROM
    order_details
        JOIN
    pizzas ON order_details.pizza_id = pizzas.pizza_id;
```

  ### Q4 Identity the most common pizza size ordered.
  ```SQL
SELECT 
    pizzas.size,
    COUNT(order_details.order_details_id) AS order_count
FROM
    pizzas
        JOIN
    order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC;
```

### Q5 list the top 5 most ordered pizza type along with their quantities.
```SQL
SELECT 
    pizza_types.name, SUM(order_details.quantity)
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY SUM(order_details.quantity) DESC
LIMIT 5;
```

  ### Q6 join the necessary tables to find the total quantity of each pizza category ordered.
  ```SQL
SELECT 
    pizza_types.category, SUM(order_details.quantity)
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY SUM(order_details.quantity) DESC;
```

### Q7 Determine the  distribution of orders by hours of the day.
```SQL
SELECT 
    HOUR(order_time) AS hour, COUNT(order_id) AS order_count
FROM
    orders;
```

  ### Q8 Group the orders by date and calculate the average number of pizzas ordered per day;
  ```SQL
SELECT 
    AVG(quantity) as avg_pizza_ordered_per_day
FROM
    (SELECT 
        orders.order_date, SUM(order_details.quantity) AS quantity
    FROM
        orders
    JOIN order_details ON orders.order_id = order_details.order_id
    GROUP BY orders.order_date) AS order_quantity;
```

  ### Q9 Determine the top 3 most ordered pizza type based on revenue.
  ```SQL
SELECT 
    pizza_types.name,
    SUM(order_details.quantity * pizzas.price) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```

### Q10 Calculate the percentage contribution of each pizza type to total revenue.
```SQL
SELECT 
    pizza_types.category,
    (SUM(order_details.quantity * pizzas.price) / (SELECT 
            ROUND(SUM(order_details.quantity * pizzas.price),
                        2) AS total_sales
        FROM
            order_details
                JOIN
            pizzas ON order_details.pizza_id = pizzas.pizza_id)) * 100 AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue DESC;
```

### Q11 Analyze the cumulative revenue generated over time.
```SQL
select order_date, 
sum(revenue) over(order by order_date) as cum_revenue from  
(select orders.order_date,
sum(order_details.quantity*pizzas.price) as revenue from order_details 
join pizzas on order_details.pizza_id=pizzas.pizza_id 
join orders on orders.order_id=order_details.order_id 
group by orders.order_date) as sales;
```

### Q12 Determine the  top 3 most ordered pizza type based on revenue for each pizza category.
``` SQL
select category,name,revenue from
(select category,name,revenue,
rank() over(partition by category order by revenue desc) as rn
from
(select pizza_types.category,pizza_types.name,
sum(order_details.quantity*pizzas.price) as revenue from pizza_types 
join pizzas on pizza_types.pizza_type_id=pizzas.pizza_type_id 
join order_details on order_details.pizza_id=pizzas.pizza_id
group by pizza_types.category,pizza_types.name) as a ) t
where t.rn<=3;
```


## 🛠️ Tools Used
- MySQL Workbench
- SQL (Joins, Subqueries, CTEs, Window Functions, Aggregate Functions)

---

## 🔗 How to Use
1. Clone this repository
2. Import the dataset CSV files into your MySQL database
3. Run the queries from `Pizza_SQL_Queries.sql` in MySQL Workbench or phpMyAdmin
