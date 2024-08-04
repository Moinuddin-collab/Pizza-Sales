# Project Overview:

Analyzing Pizza Sales Data using MySQL

## Objective:

To analyze and generate insights from pizza sales data, focusing on order trends, pizza popularity, and sales performance using SQL.

### Tables :
1.Orders
2.orders_details
3.Pizza
4.Pizza_types

#### Tools

I leveraged MySQL to explore and apply various concepts, including Joins, CTEs, Subqueries, and more.

1.**Retrieve the total number of orders placed.**


SELECT 
    COUNT(order_id) AS total_orders
FROM
    orders;
    
2.**.Calculate the total revenue generated from pizza sales.**
   
SELECT 
    ROUND(SUM(orders_details.quantity * pizzas.price),
            2) AS total
FROM
    orders_details
        JOIN
    pizzas ON pizzas.pizza_id = orders_details.pizza_id;

     -- Identify the highest-priced pizza --   
     SELECT 
    pizza_types.name, pizzas.price
FROM
    pizzas
        JOIN
    pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;
    
3.Identify the most common pizza size ordered.
  
SELECT 
    pizzas.size,
    COUNT(orders_details.order_details_id) AS order_count
FROM
    pizzas
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC
LIMIT 1;

4.List the top 5 most ordered pizza types 
-- along with their quantities.

SELECT 
    pizza_types.name, SUM(orders_details.quantity) AS quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY quantity DESC
LIMIT 5;


5.Join the necessary tables to find the total quantity of each pizza category ordered.

SELECT 
    pizza_types.category,
    SUM(orders_details.quantity) AS Total_quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
        JOIN
    orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY Total_quantity DESC;

6.Determine the distribution of orders by hour of the day.

SELECT 
    HOUR(order_time), COUNT(order_id) AS order_count
FROM
    orders
GROUP BY HOUR(order_time);


6.find the category-wise distribution of pizzas.

SELECT 
    category, COUNT(name)
FROM
    pizza_types
GROUP BY category;
    
-- Group the orders by date and calculate the average number of pizzas ordered per day.

SELECT 
    ROUND(AVG(quantity), 0) AS avg_pizzas_ordered_per_day
FROM
    (SELECT 
        orders.order_date, SUM(orders_details.quantity) AS quantity
    FROM
        orders
    JOIN orders_details ON orders.order_id = orders_details.order_id
    GROUP BY orders.order_date) AS order_quantity;


7.Determine the top 3 most ordered pizza based on revenue.
SELECT 
    pizza_types.name,
    ROUND(SUM(orders_details.quantity * pizzas.price),
            2) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;




 8.Determine the top 3 most ordered pizza types based on revenue for each pizza category
 WITH RevenueCTE AS (
    SELECT 
        pizza_types.category,
        pizza_types.name,
        SUM(orders_details.quantity * pizzas.price) AS revenue
    FROM 
        pizza_types
    JOIN 
        pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    JOIN 
        orders_details ON orders_details.pizza_id = pizzas.pizza_id
    GROUP BY 
        pizza_types.category, 
        pizza_types.name
),
RankedCTE AS (
    SELECT 
        category,
        name,
        revenue,
        RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS rn
    FROM 
        RevenueCTE
)
SELECT 
    category, name, revenue
FROM
    RankedCTE
WHERE
    rn <= 3;
    
Total Number of Orders:

The project tracks the total number of orders placed, providing a count of all order IDs from the orders table. This metric gives a high-level overview of the volume of business activity.

Total Revenue from Pizza Sales:

The project calculates the total revenue by multiplying the quantity of each pizza ordered by its price and summing these values. This helps understand the overall financial performance of the pizza sales.

Highest-Priced Pizza:

By identifying the pizza with the highest price, the project highlights the premium offerings and could inform pricing strategies or marketing focus on high-value items.

Most Common Pizza Size:

The analysis of the most frequently ordered pizza size provides insights into customer preferences, potentially guiding inventory and marketing strategies to align with popular choices.

Top 5 Most Ordered Pizza Types:

This query lists the top 5 pizza types based on the quantity ordered, showing which types are most popular among customers. This can assist in menu planning and promotional efforts.

Total Quantity Ordered per Pizza Category:

Summarizing the total quantity of pizzas ordered by category helps understand which categories (e.g., vegetarian, meat) are performing best and can guide stocking and promotional strategies.

Order Distribution by Hour:

Analyzing orders by hour of the day reveals peak times for pizza orders, which can optimize staffing and operational efficiency during busy periods.

Category-Wise Distribution of Pizzas:

Understanding the number of pizzas within each category helps in assessing the variety and popularity of different pizza types, aiding in inventory and marketing decisions.

Average Number of Pizzas Ordered Per Day:

By calculating the average number of pizzas ordered per day, the project provides insights into daily sales patterns, helping with inventory management and operational planning.

Top 3 Most Ordered Pizzas by Revenue:

Identifying the top 3 pizzas based on revenue helps pinpoint high-revenue items, which can be leveraged for targeted promotions or menu highlights.

Top 3 Most Ordered Pizza Types by Revenue for Each Category:

This detailed analysis ranks the top 3 pizza types by revenue within each category, offering granular insights into performance and helping tailor strategies to different segments of the menu.

Conclusion
This analysis helps in understanding sales performance, customer preferences, and product profitability.These insights collectively provide a comprehensive view of sales performance, customer preferences, and operational efficiency, enabling data-driven decisions to enhance business outcomes.


