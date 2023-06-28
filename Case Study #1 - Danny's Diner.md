# Case Study Danny's Diner: Thought Process 

### Question 1: "What is the total amount each customer spent at the restaurant?"

**Thought Process:**
1. Understanding the goal: The question aims to determine the total amount spent by each customer at the restaurant. This indicates the need to calculate the sum of the prices for all the items purchased by each customer.
2. Identifying the relevant tables: To calculate the total amount spent, I need to consider the sales data, which likely contains information about the customer ID and the product ID associated with each sale. Additionally, I need access to the menu table, which contains the price information for each item.
3. Utilizing aggregate functions: The mention of the "total amount" indicates that I need to calculate the sum of the prices. This implies the use of an aggregate function like SUM() to add up the prices for each customer.
4. Grouping the data: Since I am calculating the total amount for each customer, I need to group the data by the customer ID. Any time an aggregate function is used in the SELECT statement, I need to include a corresponding GROUP BY clause to define the grouping criteria.
5. Joining the tables: In order to access the price information for each product, I need to join the sales table with the menu table. By linking the product IDs in both tables, I can retrieve the relevant price information.
6. Ordering the results: Finally, since the question asks for the total amount spent by each customer, I can order the results by the customer ID to display the information in a systematic manner.

By following this thought process, I can construct the SQL query that calculates the total amount each customer spent at the restaurant, grouping the results by customer ID and ordering them accordingly.

```sql
SELECT s.customer_id, SUM(m.price) AS total_amount_spent
FROM sales s
LEFT JOIN menu m ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id;
```

This query will provide the desired result for the total amount spent by each customer, ordered by customer ID.

### Question 2: "How many days has each customer visited the restaurant?"

**Thought Process:**
1. Understanding the goal: The question aims to determine the number of days each customer has visited the restaurant. This suggests that we need to count the unique occurrences of order dates for each customer.
2. Identifying the relevant table: Since we are interested in tracking the customer visits, we can focus on the sales data, which likely contains information about the customer ID and the order dates.
3. Utilizing aggregate functions: To count the number of days visited, we can use the COUNT() function. This function counts the occurrences of a particular column, in this case, the order dates.
4. Grouping the data: Since we want to calculate the count for each customer, we need to group the data by the customer ID. The GROUP BY clause allows us to group the data accordingly.
5. Ordering the results: Finally, to display the information in a systematic manner, we can order the results by the customer ID.

By following this thought process, we can construct the SQL query that calculates the number of days each customer has visited the restaurant, grouping the results by customer ID and ordering them accordingly.

```sql
SELECT customer_id, COUNT(DISTINCT order_date) AS num_days_visited
FROM sales
GROUP BY customer_id
ORDER BY customer_id;
```

This query will provide the desired result for the number of days each customer has visited the restaurant, ordered by customer ID.

### Question 3: "What was the first item from the menu purchased by each customer?"

**Thought Process:**
1. Understanding the goal: The question aims to identify the first item purchased from the menu by each customer. This suggests that we need to determine the earliest order date for each customer and retrieve the corresponding menu item.
2. Identifying the relevant tables: To answer this question, we need to consider the sales data, which likely contains information about the customer ID, the product ID, and the order dates. Additionally, we require the menu table to access the product names.
3. Ranking the data: To identify the first item purchased by each customer, we can use the DENSE_RANK() function. By partitioning the data by the customer ID and ordering it by the order date, we assign a rank to each unique combination of customer and order date.
4. Selecting the relevant data: Since we are interested in the first item purchased by each customer, we need to filter the results based on the rank. In this case, we want to retrieve the rows where the rank is equal to 1.
5. Displaying the results: To present the information, we select all columns from the derived table (aliased as 'e') that contains the distinct customer ID, product name, order date, and rank.

By following this thought process, we can construct the SQL query that retrieves the first item from the menu purchased by each customer. The query uses a subquery to calculate the ranks and then filters the results to display the rows with a rank of 1.

```sql
SELECT e.*
FROM
    (SELECT DISTINCT s.customer_id, m.product_name, s.order_date,
        DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS rnk
    FROM sales s
    LEFT JOIN menu m ON s.product_id = m.product_id) e
WHERE rnk = 1;
```

This query will provide the desired result, showing the first item purchased from the menu by each customer, along with the order date.

### Question 4: "What is the most purchased item on the menu and how many times was it purchased by all customers?"

**Thought Process:**
Part one: Finding the most purchased item
1. Understanding the goal: The first part of the question aims to identify the most purchased item on the menu. This suggests that we need to determine the item that has the highest number of purchases.
2. Identifying the relevant tables: To answer this question, we need to consider the sales data, which likely contains information about the product ID associated with each purchase. Additionally, we require the menu table to access the product names.
3. Counting the occurrences: By using the COUNT() function and grouping the data by the product name, we can calculate the number of times each item has been purchased.
4. Grouping and limiting the results: To find the most purchased item, we group the results by the product name and limit the output to only one row using the LIMIT clause.

By following this thought process, we can construct the SQL query that retrieves the most purchased item from the menu.

```sql
SELECT m.product_name, COUNT(m.product_name) AS most_purchased
FROM sales s
LEFT JOIN menu m ON s.product_id = m.product_id
GROUP BY m.product_name
ORDER BY most_purchased DESC
LIMIT 1;
```

Part two: Counting the purchases of the most purchased item
1. Understanding the goal: The second part of the question aims to determine how many times the most purchased item was bought by all customers. We focus on the specific item identified in the first part.
2. Identifying the relevant tables: We still need to consider the sales data and the menu table to access the

 necessary information.
3. Filtering the data: To count the purchases of the most purchased item, we add a WHERE clause to specify the product name that we obtained in the first part.
4. Counting the occurrences and grouping the results: We use the COUNT() function again to calculate the number of times the item was purchased, and we group the results by the customer ID and product name to get a count per customer.
5. Ordering the results: Finally, we order the results by the customer ID to display the information in a systematic manner.

By following this thought process, we can construct the SQL query that counts the purchases of the most purchased item by all customers.

```sql
SELECT s.customer_id, m.product_name, COUNT(m.product_name) AS times_purchased
FROM sales s
LEFT JOIN menu m ON s.product_id = m.product_id
WHERE m.product_name = 'ramen' -- Replace 'ramen' with the most purchased item obtained in the first part
GROUP BY s.customer_id, m.product_name
ORDER BY s.customer_id;
```

This query will provide the desired result, showing the customer ID, the most purchased item (specified in the WHERE clause), and the number of times that item was purchased by each customer, ordered by the customer ID.

**Question 5: "Which item was the most popular for each customer?"**

**Thought Process:**
1. Understanding the goal: The question aims to identify the most popular item for each customer, indicating that we need to determine the item with the highest number of purchases for each customer.
2. Identifying the relevant tables: To answer this question, we need to consider the sales data, which likely contains information about the customer ID and the product ID associated with each purchase. Additionally, we require the menu table to access the product names.
3. Counting the occurrences: By using the COUNT() function and grouping the data by the customer ID and product name, we can calculate the number of times each item has been purchased by each customer.
4. Ranking the data: To identify the most popular item for each customer, we can use the RANK() function. By partitioning the data by the customer ID and ordering it by the count of purchases in descending order, we assign a rank to each unique combination of customer and product.
5. Selecting the relevant data: Since we are interested in the most popular item for each customer, we need to filter the results based on the rank. In this case, we want to retrieve the rows where the rank is equal to 1.
6. Displaying the results: To present the information, we select all columns from the derived table (aliased as 'e') that contains the distinct customer ID, product name, the count of purchases, and the rank.

By following this thought process, we can construct the SQL query that retrieves the most popular item for each customer. The query uses a subquery to calculate the counts and ranks, and then filters the results to display the rows with a rank of 1.

```sql
SELECT e.*
FROM
    (SELECT DISTINCT s.customer_id, m.product_name, COUNT(m.product_name) AS purchased,
        RANK() OVER (PARTITION BY s.customer_id ORDER BY COUNT(m.product_name) DESC) AS rnk
    FROM sales s
    LEFT JOIN menu m ON s.product_id = m.product_id
    GROUP BY s.customer_id, m.product_name
    ORDER BY s.customer_id) e
WHERE rnk = 1;
```

This query will provide the desired result, showing the most popular item for each customer along with the count of purchases. The results will be ordered by the customer ID.

**Question 6: "Which item was purchased first by the customer after they became a member?"**

**Thought Process:**
1. Understanding the goal: The question aims to determine the first item purchased by each customer after they became a member. This suggests that we need to identify the earliest order date for each customer after their join date and retrieve the corresponding menu item.
2. Identifying the relevant tables: To answer this question, we need to consider the sales data, members data, and the menu table. The sales data contains information about the customer ID, product ID, and order dates. The members data provides the customer's join date. The menu table contains the product names.
3. Joining the tables: To connect the necessary information, we need to join the sales table with the members table based on the customer ID. Additionally, we need to join the menu table based on the product ID to retrieve the product names.
4. Ranking the data: To identify the first item purchased by each customer after they became a member, we can use the RANK() function. By partitioning the data by the customer ID and ordering it by the order date, we assign a rank to each unique combination of customer and order date.
5. Filtering the data: Since we are interested in the first item purchased after the customer's join date, we can add a WHERE clause to specify that the order date should be greater than or equal to the join date obtained from the members table.
6. Selecting the relevant data: We can select all columns from the derived table (aliased as 'e') that contains the customer ID, order date, product name, and the rank.
7. Displaying the results: To present the information, we can order the results by the customer ID.

By following this thought process, we can construct the SQL query that retrieves the first item purchased by each customer after they became a member. The query uses multiple joins to connect the relevant tables and calculates the ranks based on the order date. The results are filtered to show only the rows with a rank of 1.

```sql
SELECT e.*
FROM
    (SELECT m.customer_id, s.order_date, mu.product_name,
        RANK() OVER (PARTITION BY m.customer_id ORDER BY s.order_date) AS rnk
    FROM sales s
    LEFT JOIN members m ON s.customer_id = m.customer_id
    LEFT JOIN menu mu ON s.product_id = mu.product_id
    WHERE s.order_date >= m.join_date) e
WHERE rnk = 1;
```

This query will provide the desired result, showing the first item purchased by each customer after they became a member, along with the order date. The results will be ordered by the customer ID.

**Question 7: "Which item was purchased just before the customer became a member?"**

**Thought Process:**
1. Understanding the goal: The question aims to determine the item purchased by each customer just before they became a member. This suggests that we need to identify the latest order date for each customer before their join date and retrieve the corresponding menu item.
2. Identifying the relevant tables: To answer this question, we need to consider the sales data, members data, and the menu table. The sales data contains information about the customer ID, product ID, and order dates. The members data provides the customer's join date. The menu table contains the product names.
3. Joining the tables: To connect the necessary information, we need to join the sales table with the members table based on the customer ID. Additionally, we need to join the menu table based on the product ID to retrieve the product names.
4. Ranking the data: To identify the item purchased just before each customer became a member, we can use the RANK() function. By partitioning the data by the customer ID and ordering it by the order date in descending order, we assign a rank to each unique combination of customer and order date.
5. Filtering the data: Since we are interested in the item purchased just before the customer's join date, we can add a WHERE clause to specify that the order date should be less than the join date obtained from the members table.
6. Selecting the relevant data: We can select all columns from the derived table (aliased as 'e') that contains the customer ID, order date, product name, and the rank.
7. Displaying the results: To present the information, we can filter the results to show only the rows with a rank of 1.

By following this thought process, we can construct the SQL query that retrieves the item purchased just before each customer became a member. The query uses multiple joins to connect the relevant tables and calculates the ranks based on the order date in descending order. The results are filtered to show only the rows with a rank of 1.

```sql
SELECT e.*
FROM
    (SELECT m.customer_id, s.order_date, mu.product_name,
        RANK() OVER (PARTITION BY m.customer_id ORDER BY s.order_date DESC) AS rnk
    FROM sales s
    LEFT JOIN members m ON s.customer_id = m.customer_id
    LEFT JOIN menu mu ON s.product_id = mu.product_id
    WHERE s.order_date < m.join_date) e
WHERE rnk = 1;
```

This query will provide the desired result, showing the item purchased just before each customer became a member, along with the order date.

**Question 8: "What is the total items and amount spent for each member before they became a member?"**

**Thought Process:**
1. Understanding the goal: The question aims to calculate the total number of items purchased and the total amount spent by each member before they became a member. This indicates the need to aggregate the data based on the customer ID and calculate the count of products and the sum of prices.
2. Identifying the relevant tables: To answer this question, we need to consider the sales data, members data, and the menu table. The sales data contains information about the customer ID, product ID, order dates, and prices. The members data provides the customer's join date. The menu table contains the price information for each item.
3. Joining the tables: To access the necessary information, we need to join the sales table with the members table based on the customer ID. Additionally, we need to join the menu table based on the product ID to retrieve the prices.
4. Filtering the data: Since we are interested in the items and amounts spent before the customer became a member, we can add a WHERE clause to specify that the order date should be less than the join date obtained from the members table.
5. Utilizing aggregate functions: To calculate the total number of items, we can use the COUNT() function on the product ID column. Similarly, to calculate the total amount spent, we can use the SUM() function on the price column.
6. Grouping the data: Since we are calculating aggregates, we need to group the data by the customer ID using the GROUP BY clause.
7. Selecting the relevant data: We can select the customer ID, count of products as "total_items," and the sum of prices as "amount_spent" in the SELECT statement.
8. Displaying the results: The results are grouped by the customer ID, so the total items and amount spent will be shown for each member.

By following this thought process, we can construct the SQL query that calculates the total items and amount spent for each member before they became a member. The query joins the necessary tables, filters the data based on the join date, and aggregates the data using the COUNT() and SUM() functions.

```sql
SELECT m.customer_id, COUNT(s.product_id) AS total_items, SUM(mu.price) AS amount_spent
FROM sales s
LEFT JOIN members m ON s.customer_id = m.customer_id
LEFT JOIN menu mu ON s.product_id = mu.product_id
WHERE s.order_date < m.join_date
GROUP BY m.customer_id;
```

This query will provide the desired result, showing the customer ID, the total number of items purchased, and the total amount spent by each member before they became a member.

**Question 9: "If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?"**

**Thought Process:**
1. Understanding the goal: The question aims to calculate the total points earned by each customer based on their purchases. Each $1 spent earns 10 points, but sushi has a 2x points multiplier, meaning it earns 20 points per $1 spent.
2. Identifying the relevant tables: To answer this question, we need the sales data, which contains information about the customer ID, product ID, and prices. We also require the menu table to access the product names.
3. Calculating points: We can calculate the points earned for each product by multiplying the price by the appropriate multiplier. For sushi, we use the 2x multiplier (price * 20), and for other products, we use the regular multiplier (price * 10).
4. Utilizing a Common Table Expression (CTE): To simplify the query and reuse the calculated points, we can use a CTE. The CTE calculates the points for each product, grouped by the customer ID, order date, product name, and price.
5. Summing the points: In the final query, we select the customer ID and sum the points to calculate the total points earned by each customer.
6. Grouping the data: Since we want the total points per customer, we group the results by the customer ID using the GROUP BY clause.
7. Displaying the results: The results show the customer ID and the total points earned by each customer.

By following this thought process, we can construct the SQL query that calculates the total points earned by each customer. The query uses a CTE to calculate the points for each product, considering the different multipliers, and then sums the points for each customer.

```sql
WITH CTE AS (
    SELECT s.customer_id, mu.product_name, 
        CASE WHEN mu.product_name = 'sushi' THEN mu.price * 2 
             ELSE mu.price * 10 
        END AS points
    FROM sales s
    LEFT JOIN menu mu ON s.product_id = mu.product_id
    GROUP BY s.customer_id, s.order_date, mu.product_name, mu.price
)
SELECT customer_id, SUM(points) AS total_points
FROM CTE
GROUP BY customer_id;
```

This query will provide the desired result, showing the customer ID and the total points earned by each customer based on their purchases.

**Question 10: "In the first week after a customer joins the program (including their join date), they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?"**

**Thought Process:**
1. Understanding the goal: The question aims to calculate the total points earned by customers A and B during the first week after they join the program, including their join date. During this period, all items earn 2x points, not just sushi.
2. Identifying the relevant tables: To answer this question, we need the sales data, which contains information about the customer ID, product ID, order dates, and prices. We also require the members table to access the join dates of customers A and B.
3. Calculating points: During the first week after joining, all items earn 2x points. We can calculate the points earned for each product by multiplying the price by the appropriate multiplier (2x or 10x).
4. Utilizing a Common Table Expression (CTE): To simplify the query and reuse the calculated points, we can use a CTE. The CTE calculates the points for each product, considering the join date and the first week after joining, grouped by the customer ID, product name, order date, and price.
5. Filtering the data: Since we only want to calculate the points for the month of January, we add a WHERE clause to filter the sales data based on the order date.
6. Summing the points: In the final query, we select the customer ID and sum the points to calculate the total points earned by each customer.
7. Grouping the data: Since we want the total points for customer A and B separately, we can add a WHERE clause to filter the results for those specific customers.
8. Displaying the results: The results show the customer ID and the total points earned by each customer.

By following this thought process, we can construct the SQL query that calculates the total points earned by customer A and B during the first week after joining the program, including their join date, and only for the month of January.

```sql
WITH CTE AS (
    SELECT m.customer_id, mu.product_name, s.order_date,
        CASE 
            WHEN mu.product_name = 'sushi' OR (s.order_date BETWEEN m.join_date AND m.new_date) THEN mu.price * 20
            ELSE mu.price * 10 
        END AS points
    FROM sales s
    LEFT JOIN menu mu ON s.product_id = mu.product_id
    RIGHT JOIN (
        SELECT customer_id, join_date, join_date + INTERVAL '7 days' AS new_date
        FROM members
        WHERE customer_id IN ('A', 'B')
    ) m ON s.customer_id = m.customer_id
    WHERE EXTRACT(MONTH FROM s.order_date) = 1
    GROUP BY m.customer_id, s.order_date, new_date, join_date, mu.product_name, mu.price
) 
SELECT customer_id, SUM(points)
FROM CTE
GROUP BY customer_id;
```

This query will provide the desired result, showing the customer ID and the total points earned by customer A and B at the end of January during the first week after they join the program, including their join date.

**BONUS QUESTIONS: Recreate the Table and Ranking of Customer Products**

**Part 1: Recreate the Table**

To recreate the table based on the given information, we can use a SELECT statement with appropriate JOINs and conditions. The query will retrieve the customer ID, order date, product name, price, and a flag indicating whether the customer is a member ('Y') or not ('N').

```sql
SELECT s.customer_id, s.order_date, mu.product_name, mu.price,
    CASE 
        WHEN s.order_date >= m.join_date THEN 'Y'
        ELSE 'N'
    END AS member
FROM sales s
LEFT JOIN menu mu ON s.product_id = mu.product_id
LEFT JOIN members m ON s.customer_id = m.customer_id
ORDER BY s.customer_id, s.order_date, mu.product_name;
```

This query retrieves the necessary columns from the sales, menu, and members tables. It uses LEFT JOIN to include all records from the sales table, even if there is no corresponding match in the menu or members table. The CASE statement determines whether the customer is a member based on the order date and join date.

**Part 2: Ranking of Customer Products with Null Ranking Values for Non-Members**

To provide ranking values for customer products but exclude ranking for non-member purchases, we can utilize a CTE. The CTE will first retrieve the necessary columns, and then the final query will calculate the ranking using the RANK() function. For non-member purchases, the ranking value will be null.

```sql
WITH CTE AS (
    SELECT s.customer_id, s.order_date, mu.product_name, mu.price,
        CASE 
            WHEN s.order_date >= m.join_date THEN 'Y'
            ELSE 'N'
        END AS member
    FROM sales s
    LEFT JOIN menu mu ON s.product_id = mu.product_id
    LEFT JOIN members m ON s.customer_id = m.customer_id
    ORDER BY s.customer_id, s.order_date, mu.product_name
)
SELECT *,
    CASE 
        WHEN member = 'N' THEN null
        ELSE RANK() OVER (PARTITION BY customer_id, member ORDER BY order_date)
    END AS ranking
FROM CTE;
```

In this query, the CTE (named CTE) retrieves the same columns as in Part 1. The final SELECT statement uses the CTE and applies the RANK() function to calculate the ranking for each customer and their products. For non-members (member = 'N'), the ranking value is set to null.

These queries will recreate the table based on the given information and provide the desired ranking values, including null for non-member purchases.
