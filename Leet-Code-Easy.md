# [1050. Actors and Directors Who Cooperated At Least Three Times](https://leetcode.com/problems/actors-and-directors-who-cooperated-at-least-three-times/description/)
```
Table: ActorDirector
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| actor_id    | int     |
| director_id | int     |
| timestamp   | int     |
+-------------+---------+
timestamp is the primary key (column with unique values) for this table.
 
Write a solution to find all the pairs (actor_id, director_id) where the actor has cooperated with the director at least three times.
Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
ActorDirector table:
+-------------+-------------+-------------+
| actor_id    | director_id | timestamp   |
+-------------+-------------+-------------+
| 1           | 1           | 0           |
| 1           | 1           | 1           |
| 1           | 1           | 2           |
| 1           | 2           | 3           |
| 1           | 2           | 4           |
| 2           | 1           | 5           |
| 2           | 1           | 6           |
+-------------+-------------+-------------+
Output: 
+-------------+-------------+
| actor_id    | director_id |
+-------------+-------------+
| 1           | 1           |
+-------------+-------------+
Explanation: The only pair is (1, 1) where they cooperated exactly 3 times.
```
```sql
SELECT ACTOR_ID, DIRECTOR_ID FROM ACTORDIRECTOR GROUP BY ACTOR_ID, DIRECTOR_ID HAVING COUNT(*) >= 3
```

# [1068. Product Sales Analysis I](https://leetcode.com/problems/product-sales-analysis-i/)
```
Table: Sales

+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |
+-------------+-------+
(sale_id, year) is the primary key (combination of columns with unique values) of this table.
product_id is a foreign key (reference column) to Product table.
Each row of this table shows a sale on the product product_id in a certain year.
Note that the price is per unit.
 
Table: Product

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
+--------------+---------+
product_id is the primary key (column with unique values) of this table.
Each row of this table indicates the product name of each product.

Write a solution to report the product_name, year, and price for each sale_id in the Sales table.
Return the resulting table in any order.
The result format is in the following example.

Example 1:

Input: 
Sales table:
+---------+------------+------+----------+-------+
| sale_id | product_id | year | quantity | price |
+---------+------------+------+----------+-------+ 
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |
+---------+------------+------+----------+-------+
Product table:
+------------+--------------+
| product_id | product_name |
+------------+--------------+
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |
+------------+--------------+
Output: 
+--------------+-------+-------+
| product_name | year  | price |
+--------------+-------+-------+
| Nokia        | 2008  | 5000  |
| Nokia        | 2009  | 5000  |
| Apple        | 2011  | 9000  |
+--------------+-------+-------+
Explanation: 
From sale_id = 1, we can conclude that Nokia was sold for 5000 in the year 2008.
From sale_id = 2, we can conclude that Nokia was sold for 5000 in the year 2009.
From sale_id = 7, we can conclude that Apple was sold for 9000 in the year 2011.
```
```sql
SELECT
    P.PRODUCT_NAME,
    S.YEAR,
    S.PRICE
FROM
    SALES AS S
INNER JOIN
    PRODUCT AS P
ON
    S.PRODUCT_ID = P.PRODUCT_ID;
```

# [1069. Product Sales Analysis II](https://leetcode.com/problems/product-sales-analysis-ii/)
```
Table: Sales
+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |
+-------------+-------+
sale_id is the primary key of this table.
product_id is a foreign key to Product table.
Note that the price is per unit.

Table: Product
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
+--------------+---------+
product_id is the primary key of this table.

Write an SQL query that reports the total quantity sold for every product id.
The query result format is in the following example:

Sales table:
+---------+------------+------+----------+-------+
| sale_id | product_id | year | quantity | price |
+---------+------------+------+----------+-------+
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |
+---------+------------+------+----------+-------+
Product table:
+------------+--------------+
| product_id | product_name |
+------------+--------------+
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |
+------------+--------------+
Result table:
+--------------+----------------+
| product_id   | total_quantity |
+--------------+----------------+
| 100          | 22             |
| 200          | 15             |
+--------------+----------------+
```
```sql
SELECT
    PRODUCT_ID,
    SUM(QUANTITY) AS TOTAL_QUANTITY
FROM
    SALES
GROUP BY
    PRODUCT_ID;
```

# [1075. Project Employees I](https://leetcode.com/problems/project-employees-i/)
```
Table: Project
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| project_id  | int     |
| employee_id | int     |
+-------------+---------+
(project_id, employee_id) is the primary key of this table.
employee_id is a foreign key to Employee table.
Each row of this table indicates that the employee with employee_id is working on the project with project_id.
 
Table: Employee
+------------------+---------+
| Column Name      | Type    |
+------------------+---------+
| employee_id      | int     |
| name             | varchar |
| experience_years | int     |
+------------------+---------+
employee_id is the primary key of this table. It's guaranteed that experience_years is not NULL.
Each row of this table contains information about one employee.
 
Write an SQL query that reports the average experience years of all the employees for each project, rounded to 2 digits.
Return the result table in any order.
The query result format is in the following example.

Example 1:

Input: 
Project table:
+-------------+-------------+
| project_id  | employee_id |
+-------------+-------------+
| 1           | 1           |
| 1           | 2           |
| 1           | 3           |
| 2           | 1           |
| 2           | 4           |
+-------------+-------------+
Employee table:
+-------------+--------+------------------+
| employee_id | name   | experience_years |
+-------------+--------+------------------+
| 1           | Khaled | 3                |
| 2           | Ali    | 2                |
| 3           | John   | 1                |
| 4           | Doe    | 2                |
+-------------+--------+------------------+
Output: 
+-------------+---------------+
| project_id  | average_years |
+-------------+---------------+
| 1           | 2.00          |
| 2           | 2.50          |
+-------------+---------------+
Explanation: The average experience years for the first project is (3 + 2 + 1) / 3 = 2.00 and for the second project is (3 + 2) / 2 = 2.50
```
```sql
SELECT
    P.PROJECT_ID,
    ROUND(AVG(E.EXPERIENCE_YEARS),2) AS AVERAGE_YEARS
FROM
    PROJECT AS P
INNER JOIN
    EMPLOYEE AS E
ON
    P.EMPLOYEE_ID = E.EMPLOYEE_ID
GROUP BY
    P.PROJECT_ID;
```

# [1076. Project Employees II](https://leetcode.com/problems/project-employees-ii/)
```
Table: Project
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| project_id  | int     |
| employee_id | int     |
+-------------+---------+
(project_id, employee_id) is the primary key of this table.
employee_id is a foreign key to Employee table.

Table: Employee
+------------------+---------+
| Column Name      | Type    |
+------------------+---------+
| employee_id      | int     |
| name             | varchar |
| experience_years | int     |
+------------------+---------+
employee_id is the primary key of this table.
 
Write an SQL query that reports all the projects that have the most employees.

The query result format is in the following example:
Project table:
+-------------+-------------+
| project_id  | employee_id |
+-------------+-------------+
| 1           | 1           |
| 1           | 2           |
| 1           | 3           |
| 2           | 1           |
| 2           | 4           |
+-------------+-------------+
Employee table:
+-------------+--------+------------------+
| employee_id | name   | experience_years |
+-------------+--------+------------------+
| 1           | Khaled | 3                |
| 2           | Ali    | 2                |
| 3           | John   | 1                |
| 4           | Doe    | 2                |
+-------------+--------+------------------+
Result table:
+-------------+
| project_id  |
+-------------+
| 1           |
+-------------+
The first project has 3 employees while the second one has 2.
```
```sql
SELECT
    PROJECT_ID
FROM
    PROJECT
GROUP BY
    PROJECT_ID
HAVING
    COUNT(EMPLOYEE_ID) = (SELECT MAX(EMPLOYEE_COUNT) FROM (SELECT COUNT(EMPLOYEE_ID) AS EMPLOYEE_COUNT FROM PROJECT GROUP BY PROJECT_ID) AS COUNTS);
```

# [1082. Sales Analysis I](https://leetcode.com/problems/sales-analysis-i/)
```
Table: Product
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
| unit_price   | int     |
+--------------+---------+
product_id is the primary key (column with unique values) of this table.
Each row of this table indicates the name and the price of each product.

Table: Sales
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| seller_id   | int     |
| product_id  | int     |
| buyer_id    | int     |
| sale_date   | date    |
| quantity    | int     |
| price       | int     |
+-------------+---------+
This table can have repeated rows.
product_id is a foreign key (reference column) to the Product table.
Each row of this table contains some information about one sale.

Write a solution that reports the best seller by total sales price, If there is a tie, report them all.
Return the result table in any order.

The result format is in the following example.
Example 1:

Input: 
Product table:
+------------+--------------+------------+
| product_id | product_name | unit_price |
+------------+--------------+------------+
| 1          | S8           | 1000       |
| 2          | G4           | 800        |
| 3          | iPhone       | 1400       |
+------------+--------------+------------+
Sales table:
+-----------+------------+----------+------------+----------+-------+
| seller_id | product_id | buyer_id | sale_date  | quantity | price |
+-----------+------------+----------+------------+----------+-------+
| 1         | 1          | 1        | 2019-01-21 | 2        | 2000  |
| 1         | 2          | 2        | 2019-02-17 | 1        | 800   |
| 2         | 2          | 3        | 2019-06-02 | 1        | 800   |
| 3         | 3          | 4        | 2019-05-13 | 2        | 2800  |
+-----------+------------+----------+------------+----------+-------+
Output: 
+-------------+
| seller_id   |
+-------------+
| 1           |
| 3           |
+-------------+
Explanation: Both sellers with id 1 and 3 sold products with the most total price of 2800.
```
```sql
SELECT SELLER_ID
FROM SALES
GROUP BY SELLER_ID
HAVING
    SUM(PRICE) >= ALL (
        SELECT SUM(PRICE)
        FROM SALES
        GROUP BY SELLER_ID
    );
-----------------------
WITH
  SELLERTOPRICE AS (
    SELECT SELLER_ID, SUM(PRICE) AS PRICE
    FROM SALES
    GROUP BY 1
  )
SELECT SELLER_ID
FROM SELLERTOPRICE
WHERE PRICE = (
    SELECT MAX(PRICE)
    FROM SELLERTOPRICE
  );
```

# [1083. Sales Analysis II](https://leetcode.com/problems/sales-analysis-ii/)
```
Table: Product
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
| unit_price   | int     |
+--------------+---------+
product_id is the primary key of this table.
Table: Sales
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| seller_id   | int     |
| product_id  | int     |
| buyer_id    | int     |
| sale_date   | date    |
| quantity    | int     |
| price       | int     |
+------ ------+---------+
This table has no primary key, it can have repeated rows.
product_id is a foreign key to Product table.

Write an SQL query that reports the buyers who have bought S8 but not iPhone. Note that S8 and iPhone are products present in the Product table.

The query result format is in the following example:
Product table:
+------------+--------------+------------+
| product_id | product_name | unit_price |
+------------+--------------+------------+
| 1          | S8           | 1000       |
| 2          | G4           | 800        |
| 3          | iPhone       | 1400       |
+------------+--------------+------------+
Sales table:
+-----------+------------+----------+------------+----------+-------+
| seller_id | product_id | buyer_id | sale_date  | quantity | price |
+-----------+------------+----------+------------+----------+-------+
| 1         | 1          | 1        | 2019-01-21 | 2        | 2000  |
| 1         | 2          | 2        | 2019-02-17 | 1        | 800   |
| 2         | 1          | 3        | 2019-06-02 | 1        | 800   |
| 3         | 3          | 3        | 2019-05-13 | 2        | 2800  |
+-----------+------------+----------+------------+----------+-------+
Result table:
+-------------+
| buyer_id    |
+-------------+
| 1           |
+-------------+
The buyer with id 1 bought an S8 but didn't buy an iPhone. The buyer with id 3 bought both.
```
```sql
SELECT S.BUYER_ID
FROM SALES AS S
INNER JOIN PRODUCT AS P
    ON S.PRODUCT_ID = P.PRODUCT_ID
GROUP BY S.BUYER_ID
HAVING 
    SUM(CASE WHEN P.PRODUCT_NAME = 'S8' THEN 1 ELSE 0 END) > 0
    AND SUM(CASE WHEN P.PRODUCT_NAME = 'iPhone' THEN 1 ELSE 0 END) = 0;
```

# [1084. Sales Analysis III](https://leetcode.com/problems/sales-analysis-iii/)
```
Table: Product
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
| unit_price   | int     |
+--------------+---------+
product_id is the primary key of this table.

Table: Sales
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| seller_id   | int     |
| product_id  | int     |
| buyer_id    | int     |
| sale_date   | date    |
| quantity    | int     |
| price       | int     |
+------ ------+---------+
This table has no primary key, it can have repeated rows.
product_id is a foreign key to Product table.
Write an SQL query that reports the products that were only sold in spring 2019. That is, between 2019-01-01 and 2019-03-31 inclusive.
The query result format is in the following example:

Product table:
+------------+--------------+------------+
| product_id | product_name | unit_price |
+------------+--------------+------------+
| 1          | S8           | 1000       |
| 2          | G4           | 800        |
| 3          | iPhone       | 1400       |
+------------+--------------+------------+
Sales table:
+-----------+------------+----------+------------+----------+-------+
| seller_id | product_id | buyer_id | sale_date  | quantity | price |
+-----------+------------+----------+------------+----------+-------+
| 1         | 1          | 1        | 2019-01-21 | 2        | 2000  |
| 1         | 2          | 2        | 2019-02-17 | 1        | 800   |
| 2         | 2          | 3        | 2019-06-02 | 1        | 800   |
| 3         | 3          | 4        | 2019-05-13 | 2        | 2800  |
+-----------+------------+----------+------------+----------+-------+
Result table:
+-------------+--------------+
| product_id  | product_name |
+-------------+--------------+
| 1           | S8           |
+-------------+--------------+
The product with id 1 was only sold in spring 2019 while the other two were sold after.
```
```sql
SELECT
    P.PRODUCT_ID,
    P.PRODUCT_NAME
FROM
    PRODUCT P
JOIN
    SALES S ON P.PRODUCT_ID = S.PRODUCT_ID
GROUP BY
    P.PRODUCT_ID, P.PRODUCT_NAME
HAVING
    MIN(S.SALE_DATE) >= '2019-01-01' AND MAX(S.SALE_DATE) <= '2019-03-31';
```

# [1113. Reported Posts](https://leetcode.com/problems/reported-posts/)
```
Table: Actions
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| post_id       | int     |
| action_date   | date    |
| action        | enum    |
| extra         | varchar |
+---------------+---------+
There is no primary key for this table, it may have duplicate rows.
The action column is an ENUM type of ('view', 'like', 'reaction', 'comment', 'report', 'share').
The extra column has optional information about the action such as a reason for report or a type of reaction. 

Write an SQL query that reports the number of posts reported yesterday for each report reason. Assume today is 2019-07-05.
The query result format is in the following example:

Actions table:
+---------+---------+-------------+--------+--------+
| user_id | post_id | action_date | action | extra  |
+---------+---------+-------------+--------+--------+
| 1       | 1       | 2019-07-01  | view   | null   |
| 1       | 1       | 2019-07-01  | like   | null   |
| 1       | 1       | 2019-07-01  | share  | null   |
| 2       | 4       | 2019-07-04  | view   | null   |
| 2       | 4       | 2019-07-04  | report | spam   |
| 3       | 4       | 2019-07-04  | view   | null   |
| 3       | 4       | 2019-07-04  | report | spam   |
| 4       | 3       | 2019-07-02  | view   | null   |
| 4       | 3       | 2019-07-02  | report | spam   |
| 5       | 2       | 2019-07-04  | view   | null   |
| 5       | 2       | 2019-07-04  | report | racism |
| 5       | 5       | 2019-07-04  | view   | null   |
| 5       | 5       | 2019-07-04  | report | racism |
+---------+---------+-------------+--------+--------+
Result table:
+---------------+--------------+
| report_reason | report_count |
+---------------+--------------+
| spam          | 1            |
| racism        | 2            |
+---------------+--------------+
Note that we only care about report reasons with non zero number of reports.
```
```sql
SELECT
  EXTRA AS REPORT_REASON,
  COUNT(DISTINCT POST_ID) AS REPORT_COUNT
FROM ACTIONS
WHERE
  ACTION = 'report'
  AND DATEDIFF('2019-07-05', ACTION_DATE) = 1
GROUP BY 1;
```

# [1141. User Activity for the Past 30 Days I](https://leetcode.com/problems/user-activity-for-the-past-30-days-i/)
```
Table: Activity
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| session_id    | int     |
| activity_date | date    |
| activity_type | enum    |
+---------------+---------+
This table may have duplicate rows.
The activity_type column is an ENUM (category) of type ('open_session', 'end_session', 'scroll_down', 'send_message').
The table shows the user activities for a social media website. 
Note that each session belongs to exactly one user.

Write a solution to find the daily active user count for a period of 30 days ending 2019-07-27 inclusively. A user was active on someday if they made at least one activity on that day.
Return the result table in any order.
The result format is in the following example.
Note: Any activity from ('open_session', 'end_session', 'scroll_down', 'send_message') will be considered valid activity for a user to be considered active on a day.
Example 1:

Input: 
Activity table:
+---------+------------+---------------+---------------+
| user_id | session_id | activity_date | activity_type |
+---------+------------+---------------+---------------+
| 1       | 1          | 2019-07-20    | open_session  |
| 1       | 1          | 2019-07-20    | scroll_down   |
| 1       | 1          | 2019-07-20    | end_session   |
| 2       | 4          | 2019-07-20    | open_session  |
| 2       | 4          | 2019-07-21    | send_message  |
| 2       | 4          | 2019-07-21    | end_session   |
| 3       | 2          | 2019-07-21    | open_session  |
| 3       | 2          | 2019-07-21    | send_message  |
| 3       | 2          | 2019-07-21    | end_session   |
| 4       | 3          | 2019-06-25    | open_session  |
| 4       | 3          | 2019-06-25    | end_session   |
+---------+------------+---------------+---------------+
Output: 
+------------+--------------+ 
| day        | active_users |
+------------+--------------+ 
| 2019-07-20 | 2            |
| 2019-07-21 | 2            |
+------------+--------------+ 
Explanation: Note that we do not care about days with zero active users.
```
```sql
SELECT
    ACTIVITY_DATE AS DAY,
    COUNT(DISTINCT USER_ID) AS ACTIVE_USERS
FROM
    ACTIVITY
WHERE
    ACTIVITY_DATE BETWEEN DATEADD(DAY, -29, '2019-07-27') AND '2019-07-27'
GROUP BY
    ACTIVITY_DATE
ORDER BY
    DAY;
```

# [1142. User Activity for the Past 30 Days II](https://leetcode.com/problems/user-activity-for-the-past-30-days-ii/)
```
Table: Activity
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| session_id    | int     |
| activity_date | date    |
| activity_type | enum    |
+---------------+---------+
There is no primary key for this table, it may have duplicate rows.
The activity_type column is an ENUM of type ('open_session', 'end_session', 'scroll_down', 'send_message').
The table shows the user activities for a social media website.
Note that each session belongs to exactly one user.
 
Write an SQL query to find the average number of sessions per user for a period of 30 days ending 2019-07-27 inclusively, rounded to 2 decimal places.
The sessions we want to count for a user are those with at least one activity in that time period.
The query result format is in the following example:

Activity table:
+---------+------------+---------------+---------------+
| user_id | session_id | activity_date | activity_type |
+---------+------------+---------------+---------------+
| 1       | 1          | 2019-07-20    | open_session  |
| 1       | 1          | 2019-07-20    | scroll_down   |
| 1       | 1          | 2019-07-20    | end_session   |
| 2       | 4          | 2019-07-20    | open_session  |
| 2       | 4          | 2019-07-21    | send_message  |
| 2       | 4          | 2019-07-21    | end_session   |
| 3       | 2          | 2019-07-21    | open_session  |
| 3       | 2          | 2019-07-21    | send_message  |
| 3       | 2          | 2019-07-21    | end_session   |
| 3       | 5          | 2019-07-21    | open_session  |
| 3       | 5          | 2019-07-21    | scroll_down   |
| 3       | 5          | 2019-07-21    | end_session   |
| 4       | 3          | 2019-06-25    | open_session  |
| 4       | 3          | 2019-06-25    | end_session   |
+---------+------------+---------------+---------------+
Result table:
+---------------------------+
| average_sessions_per_user |
+---------------------------+
| 1.33                      |
+---------------------------+
User 1 and 2 each had 1 session in the past 30 days while user 3 had 2 sessions so the average is (1 + 1 + 2) / 3 = 1.33.
```
```sql
SELECT
  IFNULL(
    ROUND(
      COUNT(DISTINCT SESSION_ID) / COUNT(DISTINCT USER_ID),
      2
    ),
    0.00
  ) AS AVERAGE_SESSIONS_PER_USER
FROM ACTIVITY
WHERE ACTIVITY_DATE BETWEEN '2019-06-28' AND  '2019-07-27';
```

# [1148. Article Views I](https://leetcode.com/problems/article-views-i/)
```
Table: Views
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| article_id    | int     |
| author_id     | int     |
| viewer_id     | int     |
| view_date     | date    |
+---------------+---------+
There is no primary key (column with unique values) for this table, the table may have duplicate rows.
Each row of this table indicates that some viewer viewed an article (written by some author) on some date. 
Note that equal author_id and viewer_id indicate the same person.

Write a solution to find all the authors that viewed at least one of their own articles.
Return the result table sorted by id in ascending order.
The result format is in the following example.

Example 1:

Input: 
Views table:
+------------+-----------+-----------+------------+
| article_id | author_id | viewer_id | view_date  |
+------------+-----------+-----------+------------+
| 1          | 3         | 5         | 2019-08-01 |
| 1          | 3         | 6         | 2019-08-02 |
| 2          | 7         | 7         | 2019-08-01 |
| 2          | 7         | 6         | 2019-08-02 |
| 4          | 7         | 1         | 2019-07-22 |
| 3          | 4         | 4         | 2019-07-21 |
| 3          | 4         | 4         | 2019-07-21 |
+------------+-----------+-----------+------------+
Output: 
+------+
| id   |
+------+
| 4    |
| 7    |
+------+
```
```sql
SELECT DISTINCT AUTHOR_ID AS ID
FROM VIEWS
WHERE AUTHOR_ID = VIEWER_ID
ORDER BY ID ASC;
```

# [1173. Immediate Food Delivery I](https://leetcode.com/problems/immediate-food-delivery-i/)
```
Table: Delivery
+-----------------------------+---------+
| Column Name                 | Type    |
+-----------------------------+---------+
| delivery_id                 | int     |
| customer_id                 | int     |
| order_date                  | date    |
| customer_pref_delivery_date | date    |
+-----------------------------+---------+
delivery_id is the primary key of this table.
The table holds information about food delivery to customers that make orders at some date and specify a preferred delivery date (on the same order date or after it).
If the preferred delivery date of the customer is the same as the order date then the order is called immediate otherwise it's called scheduled.

Write an SQL query to find the percentage of immediate orders in the table, rounded to 2 decimal places.
The query result format is in the following example:

Delivery table:
+-------------+-------------+------------+-----------------------------+
| delivery_id | customer_id | order_date | customer_pref_delivery_date |
+-------------+-------------+------------+-----------------------------+
| 1           | 1           | 2019-08-01 | 2019-08-02                  |
| 2           | 5           | 2019-08-02 | 2019-08-02                  |
| 3           | 1           | 2019-08-11 | 2019-08-11                  |
| 4           | 3           | 2019-08-24 | 2019-08-26                  |
| 5           | 4           | 2019-08-21 | 2019-08-22                  |
| 6           | 2           | 2019-08-11 | 2019-08-13                  |
+-------------+-------------+------------+-----------------------------+
Result table:
+----------------------+
| immediate_percentage |
+----------------------+
| 33.33                |
+----------------------+
The orders with delivery id 2 and 3 are immediate while the others are scheduled.
```
```sql
SELECT 
  CAST(
    SUM(
      CASE WHEN ORDER_DATE = CUSTOMER_PREF_DELIVERY_DATE THEN 1 ELSE 0 END
    ) * 100.0 AS DECIMAL(5, 2)
  ) / COUNT(*) AS IMMEDIATE_PERCENTAGE 
FROM 
  DELIVERY;
```

# [1179. Reformat Department Table](https://leetcode.com/problems/reformat-department-table/)
```
Table: Department
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| revenue     | int     |
| month       | varchar |
+-------------+---------+
In SQL,(id, month) is the primary key of this table.
The table has information about the revenue of each department per month.
The month has values in ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"].
 
Reformat the table such that there is a department id column and a revenue column for each month.
Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
Department table:
+------+---------+-------+
| id   | revenue | month |
+------+---------+-------+
| 1    | 8000    | Jan   |
| 2    | 9000    | Jan   |
| 3    | 10000   | Feb   |
| 1    | 7000    | Feb   |
| 1    | 6000    | Mar   |
+------+---------+-------+
Output: 
+------+-------------+-------------+-------------+-----+-------------+
| id   | Jan_Revenue | Feb_Revenue | Mar_Revenue | ... | Dec_Revenue |
+------+-------------+-------------+-------------+-----+-------------+
| 1    | 8000        | 7000        | 6000        | ... | null        |
| 2    | 9000        | null        | null        | ... | null        |
| 3    | null        | 10000       | null        | ... | null        |
+------+-------------+-------------+-------------+-----+-------------+
Explanation: The revenue from Apr to Dec is null.
Note that the result table has 13 columns (1 for the department id + 12 for the months).
```
```sql
SELECT
    ID,
    SUM(CASE WHEN MONTH = 'Jan' THEN REVENUE ELSE NULL END) AS JAN_REVENUE,
    SUM(CASE WHEN MONTH = 'Feb' THEN REVENUE ELSE NULL END) AS FEB_REVENUE,
    SUM(CASE WHEN MONTH = 'Mar' THEN REVENUE ELSE NULL END) AS MAR_REVENUE,
    SUM(CASE WHEN MONTH = 'Apr' THEN REVENUE ELSE NULL END) AS APR_REVENUE,
    SUM(CASE WHEN MONTH = 'May' THEN REVENUE ELSE NULL END) AS MAY_REVENUE,
    SUM(CASE WHEN MONTH = 'Jun' THEN REVENUE ELSE NULL END) AS JUN_REVENUE,
    SUM(CASE WHEN MONTH = 'Jul' THEN REVENUE ELSE NULL END) AS JUL_REVENUE,
    SUM(CASE WHEN MONTH = 'Aug' THEN REVENUE ELSE NULL END) AS AUG_REVENUE,
    SUM(CASE WHEN MONTH = 'Sep' THEN REVENUE ELSE NULL END) AS SEP_REVENUE,
    SUM(CASE WHEN MONTH = 'Oct' THEN REVENUE ELSE NULL END) AS OCT_REVENUE,
    SUM(CASE WHEN MONTH = 'Nov' THEN REVENUE ELSE NULL END) AS NOV_REVENUE,
    SUM(CASE WHEN MONTH = 'Dec' THEN REVENUE ELSE NULL END) AS DEC_REVENUE
FROM DEPARTMENT
GROUP BY ID
--------------------------------
SELECT
    ID,
    [Jan] AS Jan_Revenue,
    [Feb] AS Feb_Revenue,
    [Mar] AS Mar_Revenue,
    [Apr] AS Apr_Revenue,
    [May] AS May_Revenue,
    [Jun] AS Jun_Revenue,
    [Jul] AS Jul_Revenue,
    [Aug] AS Aug_Revenue,
    [Sep] AS Sep_Revenue,
    [Oct] AS Oct_Revenue,
    [Nov] AS Nov_Revenue,
    [Dec] AS Dec_Revenue
FROM (
    SELECT ID, REVENUE, MONTH FROM DEPARTMENT
) S
PIVOT (
    SUM(REVENUE)
    FOR MONTH IN (
        [Jan],[Feb],[Mar],[Apr],[May],[Jun],
        [Jul],[Aug],[Sep],[Oct],[Nov],[Dec] )
) P;
```

# [1211. Queries Quality and Percentage](https://leetcode.com/problems/queries-quality-and-percentage/)
```
Table: Queries
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| query_name  | varchar |
| result      | varchar |
| position    | int     |
| rating      | int     |
+-------------+---------+
This table may have duplicate rows.
This table contains information collected from some queries on a database.
The position column has a value from 1 to 500.
The rating column has a value from 1 to 5. Query with rating less than 3 is a poor query.
 
We define query quality as:
- The average of the ratio between query rating and its position.
We also define poor query percentage as:
- The percentage of all queries with rating less than 3.

Write a solution to find each query_name, the quality and poor_query_percentage.
Both quality and poor_query_percentage should be rounded to 2 decimal places.
Return the result table in any order.

The result format is in the following example.
Example 1:

Input: 
Queries table:
+------------+-------------------+----------+--------+
| query_name | result            | position | rating |
+------------+-------------------+----------+--------+
| Dog        | Golden Retriever  | 1        | 5      |
| Dog        | German Shepherd   | 2        | 5      |
| Dog        | Mule              | 200      | 1      |
| Cat        | Shirazi           | 5        | 2      |
| Cat        | Siamese           | 3        | 3      |
| Cat        | Sphynx            | 7        | 4      |
+------------+-------------------+----------+--------+
Output: 
+------------+---------+-----------------------+
| query_name | quality | poor_query_percentage |
+------------+---------+-----------------------+
| Dog        | 2.50    | 33.33                 |
| Cat        | 0.66    | 33.33                 |
+------------+---------+-----------------------+
Explanation: 
Dog queries quality is ((5 / 1) + (5 / 2) + (1 / 200)) / 3 = 2.50
Dog queries poor_ query_percentage is (1 / 3) * 100 = 33.33

Cat queries quality equals ((2 / 5) + (3 / 3) + (4 / 7)) / 3 = 0.66
Cat queries poor_ query_percentage is (1 / 3) * 100 = 33.33
```
```sql
SELECT QUERY_NAME,
       ROUND(SUM(RATING*1.0/POSITION)*1.0/COUNT(*),2) AS QUALITY,
       ROUND(COUNT(CASE WHEN RATING<3 THEN 1 ELSE NULL END)*100.00/COUNT(*), 2) AS POOR_QUERY_PERCENTAGE 
FROM QUERIES
GROUP BY QUERY_NAME
```

# [1241. Number of Comments per Post](https://leetcode.com/problems/number-of-comments-per-post/)
```
Table: Submissions
+---------------+----------+
| Column Name   | Type     |
+---------------+----------+
| sub_id        | int      |
| parent_id     | int      |
+---------------+----------+
This table may have duplicate rows.
Each row can be a post or comment on the post.
parent_id is null for posts.
parent_id for comments is sub_id for another post in the table.
 
Write a solution to find the number of comments per post. The result table should contain post_id and its corresponding number_of_comments.
The Submissions table may contain duplicate comments. You should count the number of unique comments per post.
The Submissions table may contain duplicate posts. You should treat them as one post.
The result table should be ordered by post_id in ascending order.

The result format is in the following example.
Example 1:

Input: 
Submissions table:
+---------+------------+
| sub_id  | parent_id  |
+---------+------------+
| 1       | Null       |
| 2       | Null       |
| 1       | Null       |
| 12      | Null       |
| 3       | 1          |
| 5       | 2          |
| 3       | 1          |
| 4       | 1          |
| 9       | 1          |
| 10      | 2          |
| 6       | 7          |
+---------+------------+
Output: 
+---------+--------------------+
| post_id | number_of_comments |
+---------+--------------------+
| 1       | 3                  |
| 2       | 2                  |
| 12      | 0                  |
+---------+--------------------+
Explanation: 
The post with id 1 has three comments in the table with id 3, 4, and 9. The comment with id 3 is repeated in the table, we counted it only once.
The post with id 2 has two comments in the table with id 5 and 10.
The post with id 12 has no comments in the table.
The comment with id 6 is a comment on a deleted post with id 7 so we ignored it.
```
```sql
WITH POSTS AS (
  SELECT 
    DISTINCT SUB_ID AS POST_ID 
  FROM 
    SUBMISSIONS 
  WHERE 
    PARENT_ID IS NULL
) 
SELECT 
  POSTS.POST_ID, 
  COUNT(DISTINCT COMMENTS.SUB_ID) AS NUMBER_OF_COMMENTS 
FROM 
  POSTS 
  LEFT JOIN SUBMISSIONS AS COMMENTS ON (
    POSTS.POST_ID = COMMENTS.PARENT_ID
  ) 
GROUP BY 
  1;
```

# [1251. Average Selling Price](https://leetcode.com/problems/average-selling-price/)
```
Table: Prices
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| start_date    | date    |
| end_date      | date    |
| price         | int     |
+---------------+---------+
(product_id, start_date, end_date) is the primary key (combination of columns with unique values) for this table.
Each row of this table indicates the price of the product_id in the period from start_date to end_date.
For each product_id there will be no two overlapping periods. That means there will be no two intersecting periods for the same product_id.

Table: UnitsSold
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| purchase_date | date    |
| units         | int     |
+---------------+---------+
This table may contain duplicate rows.
Each row of this table indicates the date, units, and product_id of each product sold. 
 
Write a solution to find the average selling price for each product. average_price should be rounded to 2 decimal places. If a product does not have any sold units, its average selling price is assumed to be 0.
Return the result table in any order.

The result format is in the following example.
Example 1:

Input: 
Prices table:
+------------+------------+------------+--------+
| product_id | start_date | end_date   | price  |
+------------+------------+------------+--------+
| 1          | 2019-02-17 | 2019-02-28 | 5      |
| 1          | 2019-03-01 | 2019-03-22 | 20     |
| 2          | 2019-02-01 | 2019-02-20 | 15     |
| 2          | 2019-02-21 | 2019-03-31 | 30     |
+------------+------------+------------+--------+
UnitsSold table:
+------------+---------------+-------+
| product_id | purchase_date | units |
+------------+---------------+-------+
| 1          | 2019-02-25    | 100   |
| 1          | 2019-03-01    | 15    |
| 2          | 2019-02-10    | 200   |
| 2          | 2019-03-22    | 30    |
+------------+---------------+-------+
Output: 
+------------+---------------+
| product_id | average_price |
+------------+---------------+
| 1          | 6.96          |
| 2          | 16.96         |
+------------+---------------+
Explanation: 
Average selling price = Total Price of Product / Number of products sold.
Average selling price for product 1 = ((100 * 5) + (15 * 20)) / 115 = 6.96
Average selling price for product 2 = ((200 * 15) + (30 * 30)) / 230 = 16.96
```
```sql
SELECT 
  P.PRODUCT_ID, 
  ISNULL(
    ROUND(SUM(P.PRICE * U.UNITS * 1.0) / SUM(U.UNITS),2),0
  ) AS AVERAGE_PRICE 
FROM 
  PRICES P 
  LEFT JOIN UNITSSOLD U ON P.PRODUCT_ID = U.PRODUCT_ID 
  AND U.PURCHASE_DATE BETWEEN P.START_DATE 
  AND P.END_DATE 
GROUP BY 
  P.PRODUCT_ID;
```

# [1280. Students and Examinations](https://leetcode.com/problems/students-and-examinations/)
```
Table: Students
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| student_id    | int     |
| student_name  | varchar |
+---------------+---------+
student_id is the primary key (column with unique values) for this table.
Each row of this table contains the ID and the name of one student in the school.
 
Table: Subjects
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| subject_name | varchar |
+--------------+---------+
subject_name is the primary key (column with unique values) for this table.
Each row of this table contains the name of one subject in the school.

Table: Examinations
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| student_id   | int     |
| subject_name | varchar |
+--------------+---------+
There is no primary key (column with unique values) for this table. It may contain duplicates.
Each student from the Students table takes every course from the Subjects table.
Each row of this table indicates that a student with ID student_id attended the exam of subject_name.
 

Write a solution to find the number of times each student attended each exam.
Return the result table ordered by student_id and subject_name.
The result format is in the following example.

Example 1:

Input: 
Students table:
+------------+--------------+
| student_id | student_name |
+------------+--------------+
| 1          | Alice        |
| 2          | Bob          |
| 13         | John         |
| 6          | Alex         |
+------------+--------------+
Subjects table:
+--------------+
| subject_name |
+--------------+
| Math         |
| Physics      |
| Programming  |
+--------------+
Examinations table:
+------------+--------------+
| student_id | subject_name |
+------------+--------------+
| 1          | Math         |
| 1          | Physics      |
| 1          | Programming  |
| 2          | Programming  |
| 1          | Physics      |
| 1          | Math         |
| 13         | Math         |
| 13         | Programming  |
| 13         | Physics      |
| 2          | Math         |
| 1          | Math         |
+------------+--------------+
Output: 
+------------+--------------+--------------+----------------+
| student_id | student_name | subject_name | attended_exams |
+------------+--------------+--------------+----------------+
| 1          | Alice        | Math         | 3              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Programming  | 1              |
| 2          | Bob          | Math         | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Programming  | 1              |
| 6          | Alex         | Math         | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Programming  | 0              |
| 13         | John         | Math         | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Programming  | 1              |
+------------+--------------+--------------+----------------+
Explanation: 
The result table should contain all students and all subjects.
Alice attended the Math exam 3 times, the Physics exam 2 times, and the Programming exam 1 time.
Bob attended the Math exam 1 time, the Programming exam 1 time, and did not attend the Physics exam.
Alex did not attend any exams.
John attended the Math exam 1 time, the Physics exam 1 time, and the Programming exam 1 time.
```
```sql
SELECT 
  STUDENTS.STUDENT_ID, 
  STUDENTS.STUDENT_NAME, 
  SUBJECTS.SUBJECT_NAME, 
  COUNT(EXAMINATIONS.STUDENT_ID) AS ATTENDED_EXAMS 
FROM 
  STUDENTS CROSS 
  JOIN SUBJECTS 
  LEFT JOIN EXAMINATIONS ON (
    STUDENTS.STUDENT_ID = EXAMINATIONS.STUDENT_ID 
    AND SUBJECTS.SUBJECT_NAME = EXAMINATIONS.SUBJECT_NAME
  ) 
GROUP BY 
  STUDENTS.STUDENT_ID, STUDENTS.STUDENT_NAME, SUBJECTS.SUBJECT_NAME 
ORDER BY 
  1, 2, 3
```

# [1294. Weather Type in Each Country](https://leetcode.com/problems/weather-type-in-each-country/)
```
Table: Countries
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| country_id    | int     |
| country_name  | varchar |
+---------------+---------+
country_id is the primary key (column with unique values) for this table.
Each row of this table contains the ID and the name of one country.

Table: Weather
+---------------+------+
| Column Name   | Type |
+---------------+------+
| country_id    | int  |
| weather_state | int  |
| day           | date |
+---------------+------+
(country_id, day) is the primary key (combination of columns with unique values) for this table.
Each row of this table indicates the weather state in a country for one day.

Write a solution to find the type of weather in each country for November 2019.
The type of weather is:
- Cold if the average weather_state is less than or equal 15,
- Hot if the average weather_state is greater than or equal to 25, and
- Warm otherwise.
Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
Countries table:
+------------+--------------+
| country_id | country_name |
+------------+--------------+
| 2          | USA          |
| 3          | Australia    |
| 7          | Peru         |
| 5          | China        |
| 8          | Morocco      |
| 9          | Spain        |
+------------+--------------+
Weather table:
+------------+---------------+------------+
| country_id | weather_state | day        |
+------------+---------------+------------+
| 2          | 15            | 2019-11-01 |
| 2          | 12            | 2019-10-28 |
| 2          | 12            | 2019-10-27 |
| 3          | -2            | 2019-11-10 |
| 3          | 0             | 2019-11-11 |
| 3          | 3             | 2019-11-12 |
| 5          | 16            | 2019-11-07 |
| 5          | 18            | 2019-11-09 |
| 5          | 21            | 2019-11-23 |
| 7          | 25            | 2019-11-28 |
| 7          | 22            | 2019-12-01 |
| 7          | 20            | 2019-12-02 |
| 8          | 25            | 2019-11-05 |
| 8          | 27            | 2019-11-15 |
| 8          | 31            | 2019-11-25 |
| 9          | 7             | 2019-10-23 |
| 9          | 3             | 2019-12-23 |
+------------+---------------+------------+
Output: 
+--------------+--------------+
| country_name | weather_type |
+--------------+--------------+
| USA          | Cold         |
| Australia    | Cold         |
| Peru         | Hot          |
| Morocco      | Hot          |
| China        | Warm         |
+--------------+--------------+
Explanation: 
Average weather_state in USA in November is (15) / 1 = 15 so weather type is Cold.
Average weather_state in Austraila in November is (-2 + 0 + 3) / 3 = 0.333 so weather type is Cold.
Average weather_state in Peru in November is (25) / 1 = 25 so the weather type is Hot.
Average weather_state in China in November is (16 + 18 + 21) / 3 = 18.333 so weather type is Warm.
Average weather_state in Morocco in November is (25 + 27 + 31) / 3 = 27.667 so weather type is Hot.
We know nothing about the average weather_state in Spain in November so we do not include it in the result
```
```sql
SELECT
  COUNTRY_NAME,
  (
    CASE
      WHEN AVG(WEATHER.WEATHER_STATE * 1.0) <= 15.0 THEN 'COLD'
      WHEN AVG(WEATHER.WEATHER_STATE * 1.0) >= 25.0 THEN 'HOT'
      ELSE 'WARM'
    END
  ) AS WEATHER_TYPE
FROM COUNTRIES
INNER JOIN WEATHER
  USING (COUNTRY_ID)
WHERE DAY BETWEEN '2019-11-01' AND '2019-11-30'
GROUP BY 1;
```

# [1303. Find the Team Size](https://leetcode.com/problems/find-the-team-size/)
```
Table: Employee
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| employee_id   | int     |
| team_id       | int     |
+---------------+---------+
employee_id is the primary key for this table.
Each row of this table contains the ID of each employee and their respective team.

Write an SQL query to find the team size of each of the employees.
Return result table in any order.

The query result format is in the following example:
Employee Table:
+-------------+------------+
| employee_id | team_id    |
+-------------+------------+
|     1       |     8      |
|     2       |     8      |
|     3       |     8      |
|     4       |     7      |
|     5       |     9      |
|     6       |     9      |
+-------------+------------+
Result table:
+-------------+------------+
| employee_id | team_size  |
+-------------+------------+
|     1       |     3      |
|     2       |     3      |
|     3       |     3      |
|     4       |     1      |
|     5       |     2      |
|     6       |     2      |
+-------------+------------+
Employees with Id 1,2,3 are part of a team with team_id = 8.
Employees with Id 4 is part of a team with team_id = 7.
Employees with Id 5,6 are part of a team with team_id = 9.
```
```sql
SELECT
  EMPLOYEE_ID,
  COUNT(*) OVER(PARTITION BY TEAM_ID) AS TEAM_SIZE
FROM EMPLOYEE;
```

# [1322. Ads Performance](https://leetcode.com/problems/ads-performance/)
```
Table: Ads
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| ad_id         | int     |
| user_id       | int     |
| action        | enum    |
+---------------+---------+
(ad_id, user_id) is the primary key for this table.
Each row of this table contains the ID of an Ad, the ID of a user and the action taken by this user regarding this Ad.
The action column is an ENUM type of ('Clicked', 'Viewed', 'Ignored').
 
A company is running Ads and wants to calculate the performance of each Ad.

Performance of the Ad is measured using Click-Through Rate (CTR) where:
![img.png](img.png)
Write an SQL query to find the ctr of each Ad.
Round ctr to 2 decimal points. Order the result table by ctr in descending order and by ad_id in ascending order in case of a tie.
The query result format is in the following example:

Ads table:
+-------+---------+---------+
| ad_id | user_id | action  |
+-------+---------+---------+
| 1     | 1       | Clicked |
| 2     | 2       | Clicked |
| 3     | 3       | Viewed  |
| 5     | 5       | Ignored |
| 1     | 7       | Ignored |
| 2     | 7       | Viewed  |
| 3     | 5       | Clicked |
| 1     | 4       | Viewed  |
| 2     | 11      | Viewed  |
| 1     | 2       | Clicked |
+-------+---------+---------+
Result table:
+-------+-------+
| ad_id | ctr   |
+-------+-------+
| 1     | 66.67 |
| 3     | 50.00 |
| 2     | 33.33 |
| 5     | 0.00  |
+-------+-------+
for ad_id = 1, ctr = (2/(2+1)) * 100 = 66.67
for ad_id = 2, ctr = (1/(1+2)) * 100 = 33.33
for ad_id = 3, ctr = (1/(1+1)) * 100 = 50.00
for ad_id = 5, ctr = 0.00, Note that ad_id = 5 has no clicks or views.
Note that we don't care about Ignored Ads.
Result table is ordered by the ctr. in case of a tie we order them by ad_id
```
```sql
SELECT
    AD_ID,
    ROUND(IFNULL(SUM(ACTION = 'Clicked') / SUM(ACTION IN ('Clicked', 'Viewed')) * 100, 0), 2) AS CTR
FROM ADS
GROUP BY 1
ORDER BY 2 DESC, 1;
```

# [1327. List the Products Ordered in a Period](https://leetcode.com/problems/list-the-products-ordered-in-a-period/)
```sql
SELECT
  P.PRODUCT_NAME,
  SUM(O.UNIT) AS UNIT
FROM PRODUCTS P
INNER JOIN ORDERS O
ON P.PRODUCT_ID = O.PRODUCT_ID
WHERE FORMAT(O.ORDER_DATE, 'yyyy-MM') = '2020-02'
GROUP BY P.PRODUCT_NAME
HAVING SUM(O.UNIT) >= 100;
```

# [1350. Students With Invalid Departments](https://leetcode.com/problems/students-with-invalid-departments/)
```
Table: Departments
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| name          | varchar |
+---------------+---------+
id is the primary key of this table.
The table has information about the id of each department of a university.
 
Table: Students
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| name          | varchar |
| department_id | int     |
+---------------+---------+
id is the primary key of this table.
The table has information about the id of each student at a university and the id of the department he/she studies at.

Write an SQL query to find the id and the name of all students who are enrolled in departments that no longer exists.
Return the result table in any order.

The query result format is in the following example:
Departments table:
+------+--------------------------+
| id   | name                     |
+------+--------------------------+
| 1    | Electrical Engineering   |
| 7    | Computer Engineering     |
| 13   | Bussiness Administration |
+------+--------------------------+
Students table:
+------+----------+---------------+
| id   | name     | department_id |
+------+----------+---------------+
| 23   | Alice    | 1             |
| 1    | Bob      | 7             |
| 5    | Jennifer | 13            |
| 2    | John     | 14            |
| 4    | Jasmine  | 77            |
| 3    | Steve    | 74            |
| 6    | Luis     | 1             |
| 8    | Jonathan | 7             |
| 7    | Daiana   | 33            |
| 11   | Madelynn | 1             |
+------+----------+---------------+
Result table:
+------+----------+
| id   | name     |
+------+----------+
| 2    | John     |
| 7    | Daiana   |
| 4    | Jasmine  |
| 3    | Steve    |
+------+----------+
John, Daiana, Steve and Jasmine are enrolled in departments 14, 33, 74 and 77 respectively. department 14, 33, 74 and 77 doesn't exist in the Departments table.
```
```sql
SELECT
  STUDENTS.ID,
  STUDENTS.NAME
FROM STUDENTS
LEFT JOIN DEPARTMENTS
  ON STUDENTS.DEPARTMENT_ID = DEPARTMENTS.ID
WHERE DEPARTMENTS.ID IS NULL;
```

# [1378. Replace Employee ID With The Unique Identifier](https://leetcode.com/problems/replace-employee-id-with-the-unique-identifier/)
```
Table: Employees
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| name          | varchar |
+---------------+---------+
id is the primary key (column with unique values) for this table.
Each row of this table contains the id and the name of an employee in a company.
 
Table: EmployeeUNI
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| unique_id     | int     |
+---------------+---------+
(id, unique_id) is the primary key (combination of columns with unique values) for this table.
Each row of this table contains the id and the corresponding unique id of an employee in the company.
Write a solution to show the unique ID of each user, If a user does not have a unique ID replace just show null.
Return the result table in any order.

The result format is in the following example.
Example 1:

Input: 
Employees table:
+----+----------+
| id | name     |
+----+----------+
| 1  | Alice    |
| 7  | Bob      |
| 11 | Meir     |
| 90 | Winston  |
| 3  | Jonathan |
+----+----------+
EmployeeUNI table:
+----+-----------+
| id | unique_id |
+----+-----------+
| 3  | 1         |
| 11 | 2         |
| 90 | 3         |
+----+-----------+
Output: 
+-----------+----------+
| unique_id | name     |
+-----------+----------+
| null      | Alice    |
| null      | Bob      |
| 2         | Meir     |
| 3         | Winston  |
| 1         | Jonathan |
+-----------+----------+
Explanation: 
Alice and Bob do not have a unique ID, We will show null instead.
The unique ID of Meir is 2.
The unique ID of Winston is 3.
The unique ID of Jonathan is 1.
```
```sql
SELECT
  EU.UNIQUE_ID,
  E.NAME
FROM EMPLOYEES E
LEFT JOIN EMPLOYEEUNI EU
ON E.ID = EU.ID;
```

# [1407. Top Travellers](https://leetcode.com/problems/top-travellers/)
```sql
SELECT 
  NAME, TRAVELLED_DISTANCE 
FROM 
  (
    SELECT 
      U.ID, U.NAME, ISNULL(SUM(R.DISTANCE), 0) AS TRAVELLED_DISTANCE 
    FROM 
      USERS U 
      LEFT JOIN RIDES R ON (U.ID = R.USER_ID) 
    GROUP BY 
      U.ID, U.NAME
  ) D 
ORDER BY 2 DESC, 1;
```

# [1421. NPV Queries](https://leetcode.com/problems/npv-queries/)
```sql
SELECT
  Q.ID,
  Q.YEAR,
  ISNULL(N.NPV, 0) AS NPV
FROM QUERIES Q
LEFT JOIN NPV N
ON Q.ID = N.ID AND Q.YEAR = N.YEAR;
```

# [1435. Create a Session Bar Chart](https://leetcode.com/problems/create-a-session-bar-chart/)
```sql
SELECT 
  '[0-5>' AS BIN, 
  COUNT(1) AS TOTAL 
FROM SESSIONS WHERE DURATION < 300 
UNION 
SELECT 
  '[5-10>' AS BIN, 
  COUNT(1) AS TOTAL 
FROM SESSIONS WHERE 300 <= DURATION AND DURATION < 600 
UNION 
SELECT 
  '[10-15>' AS BIN, 
  COUNT(1) AS TOTAL 
FROM SESSIONS WHERE 600 <= DURATION AND DURATION < 900 
UNION 
SELECT 
  '15 OR MORE' AS BIN, 
  COUNT(1) AS TOTAL 
FROM SESSIONS WHERE 900 <= DURATION;
```

# [1484. Group Sold Products By The Date](https://leetcode.com/problems/group-sold-products-by-the-date/)
```sql
WITH T AS (
    SELECT DISTINCT * FROM ACTIVITIES
    )
SELECT 
     SELL_DATE
    ,COUNT(1) AS NUM_SOLD
    ,STRING_AGG(PRODUCT,',') WITHIN GROUP (ORDER BY PRODUCT) AS PRODUCTS
FROM T
GROUP BY SELL_DATE
ORDER BY SELL_DATE
```

# [1495. Friendly Movies Streamed Last Month](https://leetcode.com/problems/friendly-movies-streamed-last-month/)
```sql
SELECT DISTINCT CONTENT.TITLE
FROM CONTENT
INNER JOIN TVPROGRAM  USING (CONTENT_ID)
WHERE
    CONTENT.KIDS_CONTENT = 'Y'
    AND CONTENT.CONTENT_TYPE = 'Movies'
    AND DATE_FORMAT(TVPROGRAM.PROGRAM_DATE, '%Y-%M') = '2020-06';
```

# [1511. Customer Order Frequency](https://leetcode.com/problems/customer-order-frequency/)
```sql
SELECT 
  C.CUSTOMER_ID, 
  C.NAME 
FROM 
  ORDERS AS O 
  JOIN PRODUCT AS P ON O.PRODUCT_ID = P.PRODUCT_ID 
  JOIN CUSTOMERS AS C ON O.CUSTOMER_ID = C.CUSTOMER_ID 
WHERE 
  YEAR(O.ORDER_DATE) = 2020 
GROUP BY 
  C.CUSTOMER_ID, 
  C.NAME 
HAVING 
  SUM(
    CASE WHEN MONTH(O.ORDER_DATE) = 6 THEN O.QUANTITY * P.PRICE ELSE 0 END
  ) >= 100 
  AND SUM(
    CASE WHEN MONTH(O.ORDER_DATE) = 7 THEN O.QUANTITY * P.PRICE ELSE 0 END
  ) >= 100;

```

# [1517. Find Users With Valid E-Mails](https://leetcode.com/problems/find-users-with-valid-e-mails/)
```sql
SELECT 
    *
FROM
    USERS
WHERE
    -- last 13 digits should be '@leetcode.com'
    RIGHT(MAIL,13) = '@leetcode.com' COLLATE Latin1_General_CS_AS
    -- before the '@leetcode.com', there should not be any digit which are not a-z , A-Z , 0-9 , - , . , _
    AND LEFT(MAIL, LEN(MAIL) - 13) NOT LIKE '%[^a-zA-Z0-9_.-]%'
    -- 1st digit should be any digit of a-z or A-Z
    AND LEFT(MAIL,1) LIKE '[a-zA-Z]%';
```

# [1527. Patients With a Condition](https://leetcode.com/problems/patients-with-a-condition/)
```
Table: Patients
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| patient_id   | int     |
| patient_name | varchar |
| conditions   | varchar |
+--------------+---------+
patient_id is the primary key (column with unique values) for this table.
'conditions' contains 0 or more code separated by spaces. 
This table contains information of the patients in the hospital.
 
Write a solution to find the patient_id, patient_name, and conditions of the patients who have Type I Diabetes. Type I Diabetes always starts with DIAB1 prefix.
Return the result table in any order.
The result format is in the following example.
Example 1:

Input: 
Patients table:
+------------+--------------+--------------+
| patient_id | patient_name | conditions   |
+------------+--------------+--------------+
| 1          | Daniel       | YFEV COUGH   |
| 2          | Alice        |              |
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 |
| 5          | Alain        | DIAB201      |
+------------+--------------+--------------+
Output: 
+------------+--------------+--------------+
| patient_id | patient_name | conditions   |
+------------+--------------+--------------+
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 | 
+------------+--------------+--------------+
Explanation: Bob and George both have a condition that starts with DIAB1.
```
```sql
SELECT *
FROM PATIENTS
WHERE
    CONDITIONS LIKE 'DIAB1%' OR
    CONDITIONS LIKE '% DIAB1%';
```

# [1543. Fix Product Name Format](https://leetcode.com/problems/fix-product-name-format/)
```
Table: Sales
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| sale_id      | int     |
| product_name | varchar |
| sale_date    | date    |
+--------------+---------+
sale_id is the column with unique values for this table.
Each row of this table contains the product name and the date it was sold.
Since table Sales was filled manually in the year 2000, product_name may contain leading and/or trailing white spaces, also they are case-insensitive.

Write a solution to report
- product_name in lowercase without leading or trailing white spaces.
- sale_date in the format ('YYYY-MM').
- total the number of times the product was sold in this month.
Return the result table ordered by product_name in ascending order. In case of a tie, order it by sale_date in ascending order.
The result format is in the following example.
Example 1:

Input: 
Sales table:
+---------+--------------+------------+
| sale_id | product_name | sale_date  |
+---------+--------------+------------+
| 1       | LCPHONE      | 2000-01-16 |
| 2       | LCPhone      | 2000-01-17 |
| 3       | LcPhOnE      | 2000-02-18 |
| 4       | LCKeyCHAiN   | 2000-02-19 |
| 5       | LCKeyChain   | 2000-02-28 |
| 6       | Matryoshka   | 2000-03-31 |
+---------+--------------+------------+
Output: 
+--------------+-----------+-------+
| product_name | sale_date | total |
+--------------+-----------+-------+
| lckeychain   | 2000-02   | 2     |
| lcphone      | 2000-01   | 2     |
| lcphone      | 2000-02   | 1     |
| matryoshka   | 2000-03   | 1     |
+--------------+-----------+-------+
Explanation: 
In January, 2 LcPhones were sold. Please note that the product names are not case sensitive and may contain spaces.
In February, 2 LCKeychains and 1 LCPhone were sold.
In March, one matryoshka was sold.
```
```sql
WITH
    T AS (
        SELECT
            LOWER(TRIM(PRODUCT_NAME)) AS PRODUCT_NAME,
            DATE_FORMAT(SALE_DATE, '%Y-%M') AS SALE_DATE
        FROM SALES
    )
SELECT PRODUCT_NAME, SALE_DATE, COUNT(1) AS TOTAL
FROM T
GROUP BY 1, 2
ORDER BY 1, 2;
```

# [1565. Unique Orders and Customers Per Month](https://leetcode.com/problems/unique-orders-and-customers-per-month/)
```sql
SELECT
    FORMAT(ORDER_DATE, 'yyyy-MM') AS MONTH,
    COUNT(DISTINCT ORDER_ID) AS ORDER_COUNT,
    COUNT(DISTINCT CUSTOMER_ID) AS CUSTOMER_COUNT
FROM
    ORDERS
WHERE
    INVOICE > 20
GROUP BY
    FORMAT(ORDER_DATE, 'yyyy-MM')
ORDER BY
    MONTH;
```

# [1571. Warehouse Manager](https://leetcode.com/problems/warehouse-manager/)
```sql
SELECT
    W.NAME AS WAREHOUSE_NAME,
    SUM(W.UNITS * P.WIDTH * P.LENGTH * P.HEIGHT) AS VOLUME
FROM
    WAREHOUSE W
JOIN
    PRODUCTS P ON W.PRODUCT_ID = P.PRODUCT_ID
GROUP BY
    W.NAME;
```

# [1581. Customer Who Visited but Did Not Make Any Transactions](https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/)
```sql
SELECT
    V.CUSTOMER_ID,
    COUNT(V.VISIT_ID) AS COUNT_NO_TRANS
FROM
    VISITS V
LEFT JOIN
    TRANSACTIONS T ON V.VISIT_ID = T.VISIT_ID
WHERE
    T.VISIT_ID IS NULL
GROUP BY
    V.CUSTOMER_ID;
```

# [1587. Bank Account Summary II](https://leetcode.com/problems/bank-account-summary-ii/)
```sql
SELECT
    U.NAME,
    SUM(T.AMOUNT) AS BALANCE
FROM
    USERS U
JOIN
    TRANSACTIONS T ON U.ACCOUNT = T.ACCOUNT
GROUP BY
    U.NAME
HAVING
    SUM(T.AMOUNT) > 10000;
```

# [1607. Sellers With No Sales](https://leetcode.com/problems/sellers-with-no-sales/)
```sql
SELECT
    S.SELLER_NAME
FROM
    SELLER S
LEFT JOIN
    ORDERS O ON S.SELLER_ID = O.SELLER_ID AND YEAR(O.SALE_DATE) = 2020
WHERE
    O.ORDER_ID IS NULL;
```

# [1623. All Valid Triplets That Can Represent a Country](https://leetcode.com/problems/all-valid-triplets-that-can-represent-a-country/)
```
Table: SchoolA
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| student_id    | int     |
| student_name  | varchar |
+---------------+---------+
student_id is the primary key for this table.
Each row of this table contains the name and the id of a student in school A.
All student_name are distinct.
 
Table: SchoolB
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| student_id    | int     |
| student_name  | varchar |
+---------------+---------+
student_id is the primary key for this table.
Each row of this table contains the name and the id of a student in school B.
All student_name are distinct.
 
Table: SchoolC
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| student_id    | int     |
| student_name  | varchar |
+---------------+---------+
student_id is the primary key for this table.
Each row of this table contains the name and the id of a student in school C.
All student_name are distinct.

There is a country with three schools, where each student is enrolled in exactly one school. The country is joining a competition and wants to select one student from each school to represent the country such that:
member_A is selected from SchoolA,
member_B is selected from SchoolB,
member_C is selected from SchoolC, and
The selected students' names and IDs are pairwise distinct (i.e. no two students share the same name, and no two students share the same ID).

Write an SQL query to find all the possible triplets representing the country under the given constraints.
Return the result table in any order.
The query result format is in the following example.

SchoolA table:
+------------+--------------+
| student_id | student_name |
+------------+--------------+
| 1          | Alice        |
| 2          | Bob          |
+------------+--------------+
SchoolB table:
+------------+--------------+
| student_id | student_name |
+------------+--------------+
| 3          | Tom          |
+------------+--------------+
SchoolC table:
+------------+--------------+
| student_id | student_name |
+------------+--------------+
| 3          | Tom          |
| 2          | Jerry        |
| 10         | Alice        |
+------------+--------------+
Result table:
+----------+----------+----------+
| member_A | member_B | member_C |
+----------+----------+----------+
| Alice    | Tom      | Jerry    |
| Bob      | Tom      | Alice    |
+----------+----------+----------+
Let us see all the possible triplets.
- (Alice, Tom, Tom) --> Rejected because member_B and member_C have the same name and the same ID.
- (Alice, Tom, Jerry) --> Valid triplet.
- (Alice, Tom, Alice) --> Rejected because member_A and member_C have the same name.
- (Bob, Tom, Tom) --> Rejected because member_B and member_C have the same name and the same ID.
- (Bob, Tom, Jerry) --> Rejected because member_A and member_C have the same ID.
- (Bob, Tom, Alice) --> Valid triplet.
```
```sql
SELECT
    SA.STUDENT_NAME AS MEMBER_A,
    SB.STUDENT_NAME AS MEMBER_B,
    SC.STUDENT_NAME AS MEMBER_C
FROM
    SCHOOLA SA
CROSS JOIN
    SCHOOLB SB
CROSS JOIN
    SCHOOLC SC
WHERE
    SA.STUDENT_NAME != SB.STUDENT_NAME
    AND SA.STUDENT_NAME != SC.STUDENT_NAME
    AND SB.STUDENT_NAME != SC.STUDENT_NAME
    AND SA.STUDENT_ID != SB.STUDENT_ID
    AND SA.STUDENT_ID != SC.STUDENT_ID
    AND SB.STUDENT_ID != SC.STUDENT_ID;
--------------------------------
SELECT
    S1.STUDENT_ID AS STUDENT_ID1,
    S2.STUDENT_ID AS STUDENT_ID2,
    S3.STUDENT_ID AS STUDENT_ID3
FROM
    SCHOOLA S1
JOIN
    SCHOOLB S2 ON S1.STUDENT_NAME != S2.STUDENT_NAME AND S1.STUDENT_ID != S2.STUDENT_ID
JOIN
    SCHOOLC S3 ON S2.STUDENT_NAME != S3.STUDENT_NAME AND S1.STUDENT_NAME != S3.STUDENT_NAME AND S1.STUDENT_ID != S3.STUDENT_ID AND S2.STUDENT_ID != S3.STUDENT_ID
ORDER BY
    STUDENT_ID1, STUDENT_ID2, STUDENT_ID3;
```

# [1633. Percentage of Users Attended a Contest](https://leetcode.com/problems/percentage-of-users-attended-a-contest/)
```sql
SELECT
    CONTEST_ID,
    ROUND(COUNT(USER_ID) * 100.0 / (SELECT COUNT(*) FROM USERS), 2) AS PERCENTAGE
FROM
    REGISTER
GROUP BY
    CONTEST_ID
ORDER BY
    PERCENTAGE DESC, CONTEST_ID;
```

# [1661. Average Time of Process per Machine](https://leetcode.com/problems/average-time-of-process-per-machine/)
```sql
SELECT
    A1.MACHINE_ID,
    ROUND(AVG(CAST(A2.TIMESTAMP AS DECIMAL(10, 3)) - CAST(A1.TIMESTAMP AS DECIMAL(10, 3))), 3) AS PROCESSING_TIME
FROM
    ACTIVITY A1
JOIN
    ACTIVITY A2 ON A1.MACHINE_ID = A2.MACHINE_ID
    AND A1.PROCESS_ID = A2.PROCESS_ID
    AND A1.ACTIVITY_TYPE = 'START'
    AND A2.ACTIVITY_TYPE = 'END'
GROUP BY
    A1.MACHINE_ID;
```

# [1667. Fix Names in a Table](https://leetcode.com/problems/fix-names-in-a-table/)
```sql
SELECT
    USER_ID,
    UPPER(LEFT(NAME, 1)) + LOWER(SUBSTRING(NAME, 2, LEN(NAME))) AS NAME
FROM
    USERS
ORDER BY
    USER_ID;
```

# [1677. Products Worth Over Invoices](https://leetcode.com/problems/products-worth-over-invoices/)
```sql
SELECT
  PRODUCT.NAME,
  ISNULL(SUM(I.REST), 0) AS REST,
  ISNULL(SUM(I.PAID), 0) AS PAID,
  ISNULL(SUM(I.CANCELED), 0) AS CANCELED,
  ISNULL(SUM(I.REFUNDED), 0) AS REFUNDED
FROM PRODUCT P
LEFT JOIN INVOICE I
ON I.PRODUCT_ID = P.PRODUCT_ID
GROUP BY 1
ORDER BY 1;
```

# [1683. Invalid Tweets](https://leetcode.com/problems/invalid-tweets/)
```sql
SELECT
    TWEET_ID
FROM
    TWEETS
WHERE
    LEN(CONTENT) > 15;
```

# [1693. Daily Leads and Partners](https://leetcode.com/problems/daily-leads-and-partners/)
```
Table: DailySales
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| date_id     | date    |
| make_name   | varchar |
| lead_id     | int     |
| partner_id  | int     |
+-------------+---------+
There is no primary key (column with unique values) for this table. It may contain duplicates.
This table contains the date and the name of the product sold and the IDs of the lead and partner it was sold to.
The name consists of only lowercase English letters.
 
For each date_id and make_name, find the number of distinct lead_id's and distinct partner_id's.
Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
DailySales table:
+-----------+-----------+---------+------------+
| date_id   | make_name | lead_id | partner_id |
+-----------+-----------+---------+------------+
| 2020-12-8 | toyota    | 0       | 1          |
| 2020-12-8 | toyota    | 1       | 0          |
| 2020-12-8 | toyota    | 1       | 2          |
| 2020-12-7 | toyota    | 0       | 2          |
| 2020-12-7 | toyota    | 0       | 1          |
| 2020-12-8 | honda     | 1       | 2          |
| 2020-12-8 | honda     | 2       | 1          |
| 2020-12-7 | honda     | 0       | 1          |
| 2020-12-7 | honda     | 1       | 2          |
| 2020-12-7 | honda     | 2       | 1          |
+-----------+-----------+---------+------------+
Output: 
+-----------+-----------+--------------+-----------------+
| date_id   | make_name | unique_leads | unique_partners |
+-----------+-----------+--------------+-----------------+
| 2020-12-8 | toyota    | 2            | 3               |
| 2020-12-7 | toyota    | 1            | 2               |
| 2020-12-8 | honda     | 2            | 2               |
| 2020-12-7 | honda     | 3            | 2               |
+-----------+-----------+--------------+-----------------+
Explanation: 
For 2020-12-8, toyota gets leads = [0, 1] and partners = [0, 1, 2] while honda gets leads = [1, 2] and partners = [1, 2].
For 2020-12-7, toyota gets leads = [0] and partners = [1, 2] while honda gets leads = [0, 1, 2] and partners = [1, 2].
```
```sql
SELECT
    DATE_ID,
    MAKE_NAME,
    COUNT(DISTINCT LEAD_ID) AS UNIQUE_LEADS,
    COUNT(DISTINCT PARTNER_ID) AS UNIQUE_PARTNERS
FROM
    DAILYSALES
GROUP BY
    DATE_ID,
    MAKE_NAME;
```

# [1729. Find Followers Count](https://leetcode.com/problems/find-followers-count/)
```sql
SELECT
    USER_ID,
    COUNT(FOLLOWER_ID) AS FOLLOWERS_COUNT
FROM
    FOLLOWERS
GROUP BY
    USER_ID
ORDER BY
    USER_ID;
```

# [1731. The Number of Employees Which Report to Each Employee](https://leetcode.com/problems/the-number-of-employees-which-report-to-each-employee/)
```
Table: Employees
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| employee_id | int      |
| name        | varchar  |
| reports_to  | int      |
| age         | int      |
+-------------+----------+
employee_id is the column with unique values for this table.
This table contains information about the employees and the id of the manager they report to. Some employees do not report to anyone (reports_to is null). 
 
For this problem, we will consider a manager an employee who has at least 1 other employee reporting to them.
Write a solution to report the ids and the names of all managers, the number of employees who report directly to them, and the average age of the reports rounded to the nearest integer.
Return the result table ordered by employee_id.
The result format is in the following example.

Example 1:

Input: 
Employees table:
+-------------+---------+------------+-----+
| employee_id | name    | reports_to | age |
+-------------+---------+------------+-----+
| 9           | Hercy   | null       | 43  |
| 6           | Alice   | 9          | 41  |
| 4           | Bob     | 9          | 36  |
| 2           | Winston | null       | 37  |
+-------------+---------+------------+-----+
Output: 
+-------------+-------+---------------+-------------+
| employee_id | name  | reports_count | average_age |
+-------------+-------+---------------+-------------+
| 9           | Hercy | 2             | 39          |
+-------------+-------+---------------+-------------+
Explanation: Hercy has 2 people report directly to him, Alice and Bob. Their average age is (41+36)/2 = 38.5, which is 39 after rounding it to the nearest integer.
Example 2:

Input: 
Employees table:
+-------------+---------+------------+-----+ 
| employee_id | name    | reports_to | age |
|-------------|---------|------------|-----|
| 1           | Michael | null       | 45  |
| 2           | Alice   | 1          | 38  |
| 3           | Bob     | 1          | 42  |
| 4           | Charlie | 2          | 34  |
| 5           | David   | 2          | 40  |
| 6           | Eve     | 3          | 37  |
| 7           | Frank   | null       | 50  |
| 8           | Grace   | null       | 48  |
+-------------+---------+------------+-----+ 
Output: 
+-------------+---------+---------------+-------------+
| employee_id | name    | reports_count | average_age |
| ----------- | ------- | ------------- | ----------- |
| 1           | Michael | 2             | 40          |
| 2           | Alice   | 2             | 37          |
| 3           | Bob     | 1             | 37          |
+-------------+---------+---------------+-------------+
```
```sql
SELECT
    M.EMPLOYEE_ID,
    M.NAME,
    COUNT(R.EMPLOYEE_ID) AS REPORTS_COUNT,
    ROUND(AVG(CAST(R.AGE AS DECIMAL)), 0) AS AVERAGE_AGE
FROM
    EMPLOYEES M -- M FOR MANAGER
JOIN
    EMPLOYEES R ON M.EMPLOYEE_ID = R.REPORTS_TO -- R FOR REPORT
GROUP BY
    M.EMPLOYEE_ID,
    M.NAME
ORDER BY
    M.EMPLOYEE_ID;
```

# [1741. Find Total Time Spent by Each Employee](https://leetcode.com/problems/find-total-time-spent-by-each-employee/)
```
Table: Employees
+-------------+------+
| Column Name | Type |
+-------------+------+
| emp_id      | int  |
| event_day   | date |
| in_time     | int  |
| out_time    | int  |
+-------------+------+
(emp_id, event_day, in_time) is the primary key (combinations of columns with unique values) of this table.
The table shows the employees' entries and exits in an office.
event_day is the day at which this event happened, in_time is the minute at which the employee entered the office, and out_time is the minute at which they left the office.
in_time and out_time are between 1 and 1440.
It is guaranteed that no two events on the same day intersect in time, and in_time < out_time.

Write a solution to calculate the total time in minutes spent by each employee on each day at the office. Note that within one day, an employee can enter and leave more than once. The time spent in the office for a single entry is out_time - in_time.

Return the result table in any order.
The result format is in the following example.
Example 1:

Input: 
Employees table:
+--------+------------+---------+----------+
| emp_id | event_day  | in_time | out_time |
+--------+------------+---------+----------+
| 1      | 2020-11-28 | 4       | 32       |
| 1      | 2020-11-28 | 55      | 200      |
| 1      | 2020-12-03 | 1       | 42       |
| 2      | 2020-11-28 | 3       | 33       |
| 2      | 2020-12-09 | 47      | 74       |
+--------+------------+---------+----------+
Output: 
+------------+--------+------------+
| day        | emp_id | total_time |
+------------+--------+------------+
| 2020-11-28 | 1      | 173        |
| 2020-11-28 | 2      | 30         |
| 2020-12-03 | 1      | 41         |
| 2020-12-09 | 2      | 27         |
+------------+--------+------------+
Explanation: 
Employee 1 has three events: two on day 2020-11-28 with a total of (32 - 4) + (200 - 55) = 173, and one on day 2020-12-03 with a total of (42 - 1) = 41.
Employee 2 has two events: one on day 2020-11-28 with a total of (33 - 3) = 30, and one on day 2020-12-09 with a total of (74 - 47) = 27.
```
```sql
SELECT
    EVENT_DAY AS DAY,
    EMP_ID,
    SUM(OUT_TIME - IN_TIME) AS TOTAL_TIME
FROM
    EMPLOYEES
GROUP BY
    EVENT_DAY,
    EMP_ID;
```

# [175. Combine Two Tables](https://leetcode.com/problems/combine-two-tables/)
```sql
SELECT
    P.FIRSTNAME,
    P.LASTNAME,
    A.CITY,
    A.STATE
FROM
    PERSON P
LEFT JOIN
    ADDRESS A ON P.PERSONID = A.PERSONID;
```

# [1757. Recyclable and Low Fat Products](https://leetcode.com/problems/recyclable-and-low-fat-products/)
```sql
SELECT PRODUCT_ID
FROM PRODUCTS
WHERE LOW_FATS = 'Y' AND RECYCLABLE = 'Y';
```

# [1777. Product's Price for Each Store](https://leetcode.com/problems/products-price-for-each-store/)
```sql
SELECT
    PRODUCT_ID,
    MAX(CASE WHEN STORE = 'store1' THEN PRICE END) AS STORE1,
    MAX(CASE WHEN STORE = 'store2' THEN PRICE END) AS STORE2,
    MAX(CASE WHEN STORE = 'store3' THEN PRICE END) AS STORE3
FROM
    PRODUCTS
GROUP BY
    PRODUCT_ID;
```

# [1789. Primary Department for Each Employee](https://leetcode.com/problems/primary-department-for-each-employee/)
```sql
SELECT EMPLOYEE_ID, DEPARTMENT_ID
FROM EMPLOYEE
WHERE PRIMARY_FLAG = 'Y'
UNION
SELECT EMPLOYEE_ID, MIN(DEPARTMENT_ID) AS DEPARTMENT_ID 
FROM EMPLOYEE
GROUP BY EMPLOYEE_ID
HAVING COUNT(EMPLOYEE_ID) = 1;
-------------------------
SELECT E.EMPLOYEE_ID, 
COALESCE(
MAX(CASE WHEN E.PRIMARY_FLAG = 'Y' THEN E.DEPARTMENT_ID ELSE NULL END),
MIN(CASE WHEN E.PRIMARY_FLAG = 'N' THEN E.DEPARTMENT_ID ELSE NULL END)
) 
DEPARTMENT_ID 
FROM EMPLOYEE E
GROUP BY E.EMPLOYEE_ID
HAVING 
COUNT(CASE WHEN E.PRIMARY_FLAG = 'Y' THEN E.DEPARTMENT_ID ELSE NULL END) = 1 
OR COUNT(CASE WHEN E.PRIMARY_FLAG = 'N' THEN E.DEPARTMENT_ID ELSE NULL END) = 1
```

# [1795. Rearrange Products Table](https://leetcode.com/problems/rearrange-products-table/)
```sql
SELECT PRODUCT_ID, 'store1' AS STORE, STORE1 AS PRICE
FROM PRODUCTS
WHERE STORE1 IS NOT NULL
UNION ALL
SELECT PRODUCT_ID, 'store2' AS STORE, STORE2 AS PRICE
FROM PRODUCTS
WHERE STORE2 IS NOT NULL
UNION ALL
SELECT PRODUCT_ID, 'store3' AS STORE, STORE3 AS PRICE
FROM PRODUCTS
WHERE STORE3 IS NOT NULL;
```

# [1809. Ad-Free Sessions](https://leetcode.com/problems/ad-free-sessions/)
```sql
SELECT DISTINCT
    P.SESSION_ID
FROM
    PLAYBACK P
LEFT JOIN
    ADS A ON P.CUSTOMER_ID = A.CUSTOMER_ID
    AND A.TIMESTAMP BETWEEN P.START_TIME AND P.END_TIME
WHERE
    A.AD_ID IS NULL;
```

# [181. Employees Earning More Than Their Managers](https://leetcode.com/problems/employees-earning-more-than-their-managers/)
```sql
SELECT
    E1.NAME AS EMPLOYEE
FROM
    EMPLOYEE E1
JOIN
    EMPLOYEE E2 ON E1.MANAGERID = E2.ID
WHERE
    E1.SALARY > E2.SALARY;
```

# [182. Duplicate Emails](https://leetcode.com/problems/duplicate-emails/)
```sql
SELECT
    EMAIL
FROM
    PERSON
GROUP BY
    EMAIL
HAVING
    COUNT(EMAIL) > 1;
```

# [1821. Find Customers With Positive Revenue this Year](https://leetcode.com/problems/find-customers-with-positive-revenue-this-year/)
```sql
SELECT
    CUSTOMER_ID
FROM
    CUSTOMERS
WHERE
    YEAR = 2021 AND REVENUE > 0;
```

# [183. Customers Who Never Order](https://leetcode.com/problems/customers-who-never-order/)
```sql
SELECT
    C.NAME AS CUSTOMERS
FROM
    CUSTOMERS C
LEFT JOIN
    ORDERS O ON C.ID = O.CUSTOMERID
WHERE
    O.ID IS NULL;
```

# [1853. Convert Date Format](https://leetcode.com/problems/convert-date-format/)
```sql
SELECT
    FORMAT(DAY, 'dddd, MMMM d, yyyy') AS DAY
FROM
    DAYS;
```

# [1873. Calculate Special Bonus](https://leetcode.com/problems/calculate-special-bonus/)
```sql
SELECT 
    EMPLOYEE_ID, 
    CASE 
        WHEN NAME LIKE 'M%' OR EMPLOYEE_ID % 2 = 0 THEN 0 
        ELSE SALARY 
    END AS BONUS
FROM EMPLOYEES
ORDER BY EMPLOYEE_ID;
```

# [1890. The Latest Login in 2020](https://leetcode.com/problems/the-latest-login-in-2020/)
```sql
SELECT USER_ID, MAX(TIME_STAMP) LAST_STAMP
FROM LOGINS
WHERE YEAR(TIME_STAMP)=2020
GROUP BY USER_ID
```

# [1939. Users That Actively Request Confirmation Messages](https://leetcode.com/problems/users-that-actively-request-confirmation-messages/)
```sql
SELECT DISTINCT C1.USER_ID
    FROM CONFIRMATIONS C1
    INNER JOIN CONFIRMATIONS C2
    WHERE C1.USER_ID = C2.USER_ID
    AND C1.TIME_STAMP < C2.TIME_STAMP
    AND TIMESTAMPDIFF(SECOND, C1.TIME_STAMP, C2.TIME_STAMP) <= 86400;
---------------
WITH
  USERTOTIMESTAMPDIFF AS (
    SELECT USER_ID,
      TIMESTAMPDIFF(
        SECOND,
        TIME_STAMP,
        LEAD(TIME_STAMP) OVER(
          PARTITION BY USER_ID
          ORDER BY TIME_STAMP
        )
      ) AS TIMESTAMP_DIFF
    FROM CONFIRMATIONS
  )
SELECT DISTINCT USER_ID
FROM USERTOTIMESTAMPDIFF
WHERE TIMESTAMP_DIFF <= 24 * 60 * 60;
```

# [196. Delete Duplicate Emails](https://leetcode.com/problems/delete-duplicate-emails/)
```
Table: Person
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| email       | varchar |
+-------------+---------+
id is the primary key (column with unique values) for this table.
Each row of this table contains an email. The emails will not contain uppercase letters.

Write a solution to delete all duplicate emails, keeping only one unique email with the smallest id.
For SQL users, please note that you are supposed to write a DELETE statement and not a SELECT one.
For Pandas users, please note that you are supposed to modify Person in place.

After running your script, the answer shown is the Person table. The driver will first compile and run your piece of code and then show the Person table. The final order of the Person table does not matter.
The result format is in the following example.
Example 1:

Input: 
Person table:
+----+------------------+
| id | email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
Output: 
+----+------------------+
| id | email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
Explanation: john@example.com is repeated two times. We keep the row with the smallest Id = 1.
```
```sql
DELETE P1 
FROM PERSON P1, PERSON P2
WHERE P1.EMAIL = P2.EMAIL 
AND P1.ID>P2.ID;
--------------
DELETE FROM PERSON 
WHERE ID NOT IN (
  SELECT MIN(ID)
  FROM PERSON 
  GROUP BY EMAIL 
);
```

# [1965. Employees With Missing Information](https://leetcode.com/problems/employees-with-missing-information/)
```
Table: Employees
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| name        | varchar |
+-------------+---------+
employee_id is the column with unique values for this table.
Each row of this table indicates the name of the employee whose ID is employee_id.

Table: Salaries
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| salary      | int     |
+-------------+---------+
employee_id is the column with unique values for this table.
Each row of this table indicates the salary of the employee whose ID is employee_id.

Write a solution to report the IDs of all the employees with missing information. The information of an employee is missing if:
- The employee's name is missing, or
- The employee's salary is missing.
Return the result table ordered by employee_id in ascending order.
The result format is in the following example.
Example 1:

Input: 
Employees table:
+-------------+----------+
| employee_id | name     |
+-------------+----------+
| 2           | Crew     |
| 4           | Haven    |
| 5           | Kristian |
+-------------+----------+
Salaries table:
+-------------+--------+
| employee_id | salary |
+-------------+--------+
| 5           | 76071  |
| 1           | 22517  |
| 4           | 63539  |
+-------------+--------+
Output: 
+-------------+
| employee_id |
+-------------+
| 1           |
| 2           |
+-------------+
Explanation: 
Employees 1, 2, 4, and 5 are working at this company.
The name of employee 1 is missing.
The salary of employee 2 is missing.
```
```sql
SELECT EMPLOYEE_ID 
FROM EMPLOYEES 
WHERE EMPLOYEE_ID NOT IN(SELECT EMPLOYEE_ID FROM SALARIES) 
UNION ALL 
SELECT EMPLOYEE_ID
FROM SALARIES 
WHERE EMPLOYEE_ID NOT IN (SELECT EMPLOYEE_ID FROM EMPLOYEES) 
ORDER BY EMPLOYEE_ID ASC
-----------------------------------------------
SELECT ISNULL(E.EMPLOYEE_ID,S.EMPLOYEE_ID) AS EMPLOYEE_ID
FROM EMPLOYEES E FULL JOIN SALARIES S
ON E.EMPLOYEE_ID = S.EMPLOYEE_ID 
WHERE S.EMPLOYEE_ID IS NULL OR E.EMPLOYEE_ID IS NULL 
ORDER BY  EMPLOYEE_ID ASC 
```

# [197. Rising Temperature](https://leetcode.com/problems/rising-temperature/)
```
Table: Weather
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| recordDate    | date    |
| temperature   | int     |
+---------------+---------+
id is the column with unique values for this table.
There are no different rows with the same recordDate.
This table contains information about the temperature on a certain day.
 
Write a solution to find all dates' id with higher temperatures compared to its previous dates (yesterday).
Return the result table in any order.
The result format is in the following example.
Example 1:
Input: 
Weather table:
+----+------------+-------------+
| id | recordDate | temperature |
+----+------------+-------------+
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |
+----+------------+-------------+
Output: 
+----+
| id |
+----+
| 2  |
| 4  |
+----+
Explanation: 
In 2015-01-02, the temperature was higher than the previous day (10 -> 25).
In 2015-01-04, the temperature was higher than the previous day (20 -> 30).
```
```sql
WITH CTE AS (
  SELECT 
    *, 
    LAG(TEMPERATURE) OVER (ORDER BY RECORDDATE ASC) AS PREV_TEMP, 
    LAG(RECORDDATE) OVER (ORDER BY RECORDDATE ASC) AS PREV_DATE 
  FROM 
    WEATHER
) 
SELECT 
  ID 
FROM 
  CTE 
WHERE 
  TEMPERATURE > PREV_TEMP 
  AND DATEDIFF(DAY, PREV_DATE, RECORDDATE)= 1
```

# [1978. Employees Whose Manager Left the Company](https://leetcode.com/problems/employees-whose-manager-left-the-company/)
```
Table: Employees
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| employee_id | int      |
| name        | varchar  |
| manager_id  | int      |
| salary      | int      |
+-------------+----------+
In SQL, employee_id is the primary key for this table.
This table contains information about the employees, their salary, and the ID of their manager. Some employees do not have a manager (manager_id is null). 
 
Find the IDs of the employees whose salary is strictly less than $30000 and whose manager left the company.
When a manager leaves the company, their information is deleted from the Employees table, but the reports still have their manager_id set to the manager that left.

Return the result table ordered by employee_id.
The result format is in the following example.
Example 1:

Input:  
Employees table:
+-------------+-----------+------------+--------+
| employee_id | name      | manager_id | salary |
+-------------+-----------+------------+--------+
| 3           | Mila      | 9          | 60301  |
| 12          | Antonella | null       | 31000  |
| 13          | Emery     | null       | 67084  |
| 1           | Kalel     | 11         | 21241  |
| 9           | Mikaela   | null       | 50937  |
| 11          | Joziah    | 6          | 28485  |
+-------------+-----------+------------+--------+
Output: 
+-------------+
| employee_id |
+-------------+
| 11          |
+-------------+

Explanation: 
The employees with a salary less than $30000 are 1 (Kalel) and 11 (Joziah).
Kalel's manager is employee 11, who is still in the company (Joziah).
Joziah's manager is employee 6, who left the company because there is no row for employee 6 as it was deleted.
```
```sql
SELECT E.EMPLOYEE_ID
FROM EMPLOYEES E
LEFT JOIN EMPLOYEES M
ON M.EMPLOYEE_ID = E.MANAGER_ID
WHERE E.SALARY < 30000 AND M.EMPLOYEE_ID IS NULL AND E.MANAGER_ID IS NOT NULL
ORDER BY EMPLOYEE_ID
```

# [2026. Low-Quality Problems](https://leetcode.com/problems/low-quality-problems/)
```sql
SELECT PROBLEM_ID
FROM PROBLEMS
WHERE LIKES / (LIKES + DISLIKES) < 0.6
ORDER BY PROBLEM_ID;
```

# [2072. The Winner University](https://leetcode.com/problems/the-winner-university/)
```sql
WITH NYU_CTE AS (
    SELECT COUNT(*) AS CNT FROM NEWYORK WHERE SCORE >= 90
), CU_CTE AS (
    SELECT COUNT(*) AS CNT FROM CALIFORNIA WHERE SCORE >= 90
)
SELECT
    (CASE
     WHEN N.CNT > C.CNT THEN 'NEW YORK UNIVERSITY'
     WHEN N.CNT < C.CNT THEN 'CALIFORNIA UNIVERSITY'
     ELSE 'NO WINNER'
     END) AS WINNER
FROM NYU_CTE N, CU_CTE C;
```

# [2082. The Number of Rich Customers](https://leetcode.com/problems/the-number-of-rich-customers/)
```sql
SELECT
    COUNT(DISTINCT(CUSTOMER_ID)) AS RICH_COUNT
FROM
    STORE
WHERE
    AMOUNT > 500;
```

# [2205. The Number of Users That Are Eligible for Discount](https://leetcode.com/problems/the-number-of-users-that-are-eligible-for-discount/)
```sql
SELECT 
  COUNT(DISTINCT USER_ID) AS USER_CNT 
FROM 
  PURCHASES 
WHERE 
  TIME_STAMP BETWEEN STARTDATE 
  AND ENDDATE 
  AND AMOUNT >= MINAMOUNT
```

# [2230. The Users That Are Eligible for Discount](https://leetcode.com/problems/the-users-that-are-eligible-for-discount/)
```sql
CREATE PROCEDURE getUserIDs(startDate DATE, endDate DATE, minAmount INT)
BEGIN
  SELECT DISTINCT user_id
  FROM Purchases
  WHERE
    time_stamp BETWEEN startDate AND endDate
    AND amount >= minAmount
  ORDER BY 1;
END
```

# [2329. Product Sales Analysis V](https://leetcode.com/problems/product-sales-analysis-v/)
```sql
SELECT
    S.USER_ID,
    SUM(S.QUANTITY * P.PRICE) AS SPENDING
FROM
    SALES AS S
JOIN
    PRODUCT AS P ON S.PRODUCT_ID = P.PRODUCT_ID
GROUP BY
    S.USER_ID
ORDER BY
    SPENDING DESC, S.USER_ID;
```

# [2339. All the Matches of the League](https://leetcode.com/problems/all-the-matches-of-the-league/)
```sql
SELECT
    T1.TEAM_NAME AS HOME_TEAM,
    T2.TEAM_NAME AS AWAY_TEAM
FROM
    TEAMS AS T1
CROSS JOIN
    TEAMS AS T2
WHERE
    T1.TEAM_NAME != T2.TEAM_NAME;
```

# [2356. Number of Unique Subjects Taught by Each Teacher](https://leetcode.com/problems/number-of-unique-subjects-taught-by-each-teacher/)
```sql
SELECT
    TEACHER_ID,
    COUNT(DISTINCT SUBJECT_ID) AS CNT
FROM
    TEACHER
GROUP BY
    TEACHER_ID;
```

# [2377. Sort the Olympic Table](https://leetcode.com/problems/sort-the-olympic-table/)
```sql
SELECT
  COUNTRY,
  GOLD_MEDALS,
  SILVER_MEDALS,
  BRONZE_MEDALS
FROM OLYMPIC
ORDER BY
  GOLD_MEDALS DESC,
  SILVER_MEDALS DESC,
  BRONZE_MEDALS DESC,
  COUNTRY;
```

# [2480. Form a Chemical Bond](https://leetcode.com/problems/form-a-chemical-bond/)
```sql
SELECT
    A.SYMBOL AS METAL,
    B.SYMBOL AS NONMETAL
FROM
    ELEMENTS AS A
CROSS JOIN
    ELEMENTS AS B
WHERE
    A.TYPE = 'Metal' AND B.TYPE = 'Nonmetal';
```

# [2504. Concatenate the Name and the Profession](https://leetcode.com/problems/concatenate-the-name-and-the-profession/)
```sql
SELECT
    PERSON_ID,
    CONCAT(NAME, '(', SUBSTRING(PROFESSION, 1, 1), ')') AS NAME
FROM
    PERSON
ORDER BY
    PERSON_ID DESC;
```

# [2668. Find Latest Salaries](https://leetcode.com/problems/find-latest-salaries/)
```sql
SELECT
    EMP_ID,
    FIRSTNAME,
    LASTNAME,
    MAX(SALARY) AS SALARY,
    DEPARTMENT_ID
FROM
    SALARY
GROUP BY
    EMP_ID,
    FIRSTNAME,
    LASTNAME,
    DEPARTMENT_ID
ORDER BY
    EMP_ID;
```

# [2669. Count Artist Occurrences On Spotify Ranking List](https://leetcode.com/problems/count-artist-occurrences-on-spotify-ranking-list/)
```
Table: Spotify
+-------------+---------+ 
| Column Name | Type    | 
+-------------+---------+ 
| id          | int     | 
| track_name  | varchar |
| artist      | varchar |
+-------------+---------+
id is the primary Key for this table.
Each row contains an id, track_name, and artist.
Write an SQL query to find how many times each artist appeared on the spotify ranking list.
Return the result table having the artist's name along with the corresponding number of occurrences ordered by occurrence count in descending order. If the occurrences are equal, then it’s ordered by the artist’s name in ascending order.
The query result format is in the following example​​​​​​.
Example 1:

Input:
Spotify table: 
+---------+--------------------+------------+ 
| id      | track_name         | artist     |  
+---------+--------------------+------------+
| 303651  | Heart Won't Forget | Sia        |
| 1046089 | Shape of you       | Ed Sheeran |
| 33445   | I'm the one        | DJ Khalid  |
| 811266  | Young Dumb & Broke | DJ Khalid  | 
| 505727  | Happier            | Ed Sheeran |
+---------+--------------------+------------+ 
Output:
+------------+-------------+
| artist     | occurrences | 
+------------+-------------+
| DJ Khalid  | 2           |
| Ed Sheeran | 2           |
| Sia        | 1           | 
+------------+-------------+ 

Explanation: The count of occurrences is listed in descending order under the column name "occurrences". If the number of occurrences is the same, the artist's names are sorted in ascending order.
```
```sql
SELECT
    ARTIST,
    COUNT(1) AS OCCURRENCES
FROM SPOTIFY
GROUP BY ARTIST
ORDER BY OCCURRENCES DESC, ARTIST;
```

# [2687. Bikes Last Time Used](https://leetcode.com/problems/bikes-last-time-used/)
```sql
SELECT
    BIKE_NUMBER,
    MAX(END_TIME) AS END_TIME
FROM BIKES
GROUP BY BIKE_NUMBER
ORDER BY END_TIME DESC;
```

# [2837. Total Traveled Distance](https://leetcode.com/problems/total-traveled-distance/)
```sql
SELECT
  U.USER_ID,
  U.NAME,
  SUM(ISNULL(R.DISTANCE, 0)) AS TOTAL_TRAVELED_DISTANCE
FROM
  USERS U
  LEFT JOIN RIDES R ON U.USER_ID = R.USER_ID
GROUP BY
  U.USER_ID,
  U.NAME
ORDER BY
  U.USER_ID;
```

# [2853. Highest Salaries Difference](https://leetcode.com/problems/highest-salaries-difference/)
```
Table: Salaries
+-------------+---------+ 
| Column Name | Type    | 
+-------------+---------+ 
| emp_name    | varchar | 
| department  | varchar | 
| salary      | int     |
+-------------+---------+
(emp_name, department) is the primary key for this table.
Each row of this table contains emp_name, department and salary. There will be at least one entry for the engineering and marketing departments.

Write an SQL query to calculate the difference between the highest salaries in the marketing and engineering department. Output the absolute difference in salaries.
Return the result table.

The query result format is in the following example.
Example 1:

Input: 
Salaries table:
+----------+-------------+--------+
| emp_name | department  | salary |
+----------+-------------+--------+
| Kathy    | Engineering | 50000  |
| Roy      | Marketing   | 30000  |
| Charles  | Engineering | 45000  |
| Jack     | Engineering | 85000  | 
| Benjamin | Marketing   | 34000  |
| Anthony  | Marketing   | 42000  |
| Edward   | Engineering | 102000 |
| Terry    | Engineering | 44000  |
| Evelyn   | Marketing   | 53000  |
| Arthur   | Engineering | 32000  |
+----------+-------------+--------+
Output: 
+-------------------+
| salary_difference | 
+-------------------+
| 49000             | 
+-------------------+
Explanation: 
- The Engineering and Marketing departments have the highest salaries of 102,000 and 53,000, respectively. Resulting in an absolute difference of 49,000.
Solutions
```
```sql
SELECT
  MAX(CASE WHEN DEPARTMENT = 'Engineering' THEN SALARY ELSE 0 END) - MAX(CASE WHEN DEPARTMENT = 'Marketing' THEN SALARY ELSE 0 END) AS SALARY_DIFFERENCE
FROM
  SALARIES;
```

# [2985. Calculate Compressed Mean](https://leetcode.com/problems/calculate-compressed-mean/)
```sql
SELECT
    ROUND(
        SUM(ITEM_COUNT * ORDER_OCCURRENCES) / SUM(ORDER_OCCURRENCES),
        2
    ) AS AVERAGE_ITEMS_PER_ORDER
FROM ORDERS;
```

# [2987. Find Expensive Cities](https://leetcode.com/problems/find-expensive-cities/)
```sql
SELECT CITY
FROM LISTINGS
GROUP BY CITY
HAVING AVG(PRICE) > (SELECT AVG(PRICE) FROM LISTINGS)
ORDER BY 1
```

# [2990. Loan Types](https://leetcode.com/problems/loan-types/)
```sql
SELECT
  USER_ID
FROM
  LOANS
GROUP BY
  USER_ID
HAVING
  SUM(CASE WHEN LOAN_TYPE = 'REFINANCE' THEN 1 ELSE 0 END) > 0
  AND SUM(CASE WHEN LOAN_TYPE = 'MORTGAGE' THEN 1 ELSE 0 END) > 0
ORDER BY
  USER_ID;
```

# [3051. Find Candidates for Data Scientist Position](https://leetcode.com/problems/find-candidates-for-data-scientist-position/)
```sql
SELECT CANDIDATE_ID
FROM CANDIDATES
WHERE SKILL IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY 1
HAVING COUNT(1) = 3
ORDER BY 1;
```

# [3053. Classifying Triangles by Lengths](https://leetcode.com/problems/classifying-triangles-by-lengths/)
```sql
SELECT
  CASE
    WHEN A + B <= C OR A + C <= B OR B + C <= A THEN 'Not a Triangle'
    WHEN A = B AND B = C THEN 'Equilateral'
    WHEN A = B OR B = C OR A = C THEN 'Isosceles'
    ELSE 'Scalene'
  END AS TRIANGLE_TYPE
FROM
  TRIANGLES;
```

# [3059. Find All Unique Email Domains](https://leetcode.com/problems/find-all-unique-email-domains/)
```sql
SELECT
  SUBSTRING(EMAIL, CHARINDEX('@', EMAIL) + 1, LEN(EMAIL)) AS EMAIL_DOMAIN,
  COUNT(*) AS COUNT
FROM
  EMAILS
WHERE
  EMAIL LIKE '%.com'
GROUP BY
  SUBSTRING(EMAIL, CHARINDEX('@', EMAIL) + 1, LEN(EMAIL))
ORDER BY
  EMAIL_DOMAIN;
```

# [3150. Invalid Tweets II](https://leetcode.com/problems/invalid-tweets-ii/)
```sql
SELECT
  TWEET_ID
FROM
  TWEETS
WHERE
  LEN(CONTENT) > 140
  OR (LEN(CONTENT) - LEN(REPLACE(CONTENT, '@', ''))) > 3
  OR (LEN(CONTENT) - LEN(REPLACE(CONTENT, '#', ''))) > 3
ORDER BY
  TWEET_ID;
```

# [3172. Second Day Verification](https://leetcode.com/problems/second-day-verification/)
```
Table: emails
+-------------+----------+
| Column Name | Type     | 
+-------------+----------+
| email_id    | int      |
| user_id     | int      |
| signup_date | datetime |
+-------------+----------+
(email_id, user_id) is the primary key (combination of columns with unique values) for this table.
Each row of this table contains the email ID, user ID, and signup date.

Table: texts
+---------------+----------+
| Column Name   | Type     | 
+---------------+----------+
| text_id       | int      |
| email_id      | int      |
| signup_action | enum     |
| action_date   | datetime |
+---------------+----------+
(text_id, email_id) is the primary key (combination of columns with unique values) for this table. 
signup_action is an enum type of ('Verified', 'Not Verified'). 
Each row of this table contains the text ID, email ID, signup action, and action date.

Write a Solution to find the user IDs of those who verified their sign-up on the second day.
Return the result table ordered by user_id in ascending order.
The result format is in the following example.

Example:

Input:

emails table:
+----------+---------+---------------------+
| email_id | user_id | signup_date         |
+----------+---------+---------------------+
| 125      | 7771    | 2022-06-14 09:30:00 |
| 433      | 1052    | 2022-07-09 08:15:00 |
| 234      | 7005    | 2022-08-20 10:00:00 |
+----------+---------+---------------------+
texts table:
+---------+----------+--------------+---------------------+
| text_id | email_id | signup_action| action_date         |
+---------+----------+--------------+---------------------+
| 1       | 125      | Verified     | 2022-06-15 08:30:00 |
| 2       | 433      | Not Verified | 2022-07-10 10:45:00 |
| 4       | 234      | Verified     | 2022-08-21 09:30:00 |
+---------+----------+--------------+---------------------+
    
Output:
+---------+
| user_id |
+---------+
| 7005    |
| 7771    |
+---------+
Explanation:

User with user_id 7005 and email_id 234 signed up on 2022-08-20 10:00:00 and verified on second day of the signup.
User with user_id 7771 and email_id 125 signed up on 2022-06-14 09:30:00 and verified on second day of the signup.
```
```sql
SELECT
  E.USER_ID
FROM
  EMAILS E
  INNER JOIN TEXTS T ON E.EMAIL_ID = T.EMAIL_ID
WHERE
  T.SIGNUP_ACTION = 'Verified'
  AND DATEDIFF(DAY, E.SIGNUP_DATE, T.ACTION_DATE) = 1
ORDER BY
  E.USER_ID;
```

# [3198. Find Cities in Each State](https://leetcode.com/problems/find-cities-in-each-state/)
```sql
SELECT
    STATE,
    STRING_AGG(CITY, ', ') WITHIN GROUP (ORDER BY CITY) AS CITIES
FROM CITIES
GROUP BY STATE
ORDER BY STATE;
```

# [3246. Premier League Table Ranking](https://leetcode.com/problems/premier-league-table-ranking/)
```sql
SELECT
    TEAM_ID,
    TEAM_NAME,
    WINS * 3 + DRAWS AS POINTS,
    RANK() OVER (ORDER BY (WINS * 3 + DRAWS) DESC) AS POSITION
FROM TEAMSTATS
ORDER BY POINTS DESC, TEAM_NAME;
```

# [3358. Books with NULL Ratings](https://leetcode.com/problems/books-with-null-ratings/)
```
Table: books

| Column Name    | Type    |
| book_id        | int     |
| title          | varchar |
| author         | varchar |
| published_year | int     |
| rating         | decimal |

book_id is the unique key for this table.
Each row of this table contains information about a book including its unique ID, title, author, publication year, and rating.
rating can be NULL, indicating that the book hasn't been rated yet.
Write a solution to find all  books that have not been rated yet (i.e., have a NULL rating).

Return the result table ordered by book_id in ascending order.
The result format is in the following example.

Example:

Input:

books table:

| book_id | title                  | author           | published_year | rating |
| 1       | The Great Gatsby       | F. Scott         | 1925           | 4.5    |
| 2       | To Kill a Mockingbird  | Harper Lee       | 1960           | NULL   |
| 3       | Pride and Prejudice    | Jane Austen      | 1813           | 4.8    |
| 4       | The Catcher in the Rye | J.D. Salinger    | 1951           | NULL   |
| 5       | Animal Farm            | George Orwell    | 1945           | 4.2    |
| 6       | Lord of the Flies      | William Golding  | 1954           | NULL   |

Output:

| book_id | title                  | author           | published_year |
| 2       | To Kill a Mockingbird  | Harper Lee       | 1960           |
| 4       | The Catcher in the Rye | J.D. Salinger    | 1951           |
| 6       | Lord of the Flies      | William Golding  | 1954           |

Explanation:

The books with book_id 2, 4, and 6 have NULL ratings.
These books are included in the result table.
The other books (book_id 1, 3, and 5) have ratings and are not included.
Bookshelves
The result is ordered by book_id in ascending order
```
```sql
SELECT BOOK_ID, TITLE, AUTHOR, PUBLISHED_YEAR
FROM BOOKS
WHERE RATING IS NULL
ORDER BY 1;
```

# [3415. Find Products with Three Consecutive Digits](https://leetcode.com/problems/find-products-with-three-consecutive-digits/)
```
Table: Products
| product_id  | int     |
| name        | varchar |

Explanation:

Product 1: ABC123XYZ contains the digits 123.
Product 5: 789Product contains the digits 789.
Product 6: Item003Description contains 003, which is exactly three digits.

Note:

Results are ordered by product_id in ascending order.
Only products with exactly three consecutive digits in their names are included in the result.
```
```sql
SELECT PRODUCT_ID, NAME
FROM PRODUCTS
WHERE
    (NAME LIKE '%[0-9][0-9][0-9]%'  -- CONTAINS AT LEAST THREE CONSECUTIVE DIGITS
    AND NAME NOT LIKE '%[0-9][0-9][0-9][0-9]%') -- BUT NOT FOUR OR MORE CONSECUTIVE DIGITS
    OR NAME LIKE '[0-9][0-9][0-9]%' -- STARTS WITH EXACTLY THREE DIGITS
    OR NAME LIKE '%[0-9][0-9][0-9]' -- ENDS WITH EXACTLY THREE DIGITS
ORDER BY PRODUCT_ID;
```

# [3436. Find Valid Emails](https://leetcode.com/problems/find-valid-emails/)
```
Table: Users
+-----------------+---------+
| Column Name     | Type    |
+-----------------+---------+
| user_id         | int     |
| email           | varchar |
+-----------------+---------+
(user_id) is the unique key for this table.
Each row contains a user's unique ID and email address.
Write a solution to find all the valid email addresses. A valid email address meets the following criteria:

It contains exactly one @ symbol.
It ends with .com.
The part before the @ symbol contains only alphanumeric characters and underscores.
The part after the @ symbol and before .com contains a domain name that contains only letters.
Return the result table ordered by user_id in ascending order.

Example:

Input:
Users table:
+---------+---------------------+
| user_id | email               |
+---------+---------------------+
| 1       | alice@example.com   |
| 2       | bob_at_example.com  |
| 3       | charlie@example.net |
| 4       | david@domain.com    |
| 5       | eve@invalid         |
+---------+---------------------+
Output:
+---------+-------------------+
| user_id | email             |
+---------+-------------------+
| 1       | alice@example.com |
| 4       | david@domain.com  |
+---------+-------------------+

Explanation:

alice@example.com is valid because it contains one @, alice is alphanumeric, and example.com starts with a letter and ends with .com.
bob_at_example.com is invalid because it contains an underscore instead of an @.
charlie@example.net is invalid because the domain does not end with .com.
david@domain.com is valid because it meets all criteria.
eve@invalid is invalid because the domain does not end with .com.
Result table is ordered by user_id in ascending order.
```
```sql
SELECT * FROM USERS
WHERE UPPER(EMAIL) LIKE '%@%.COM' AND UPPER(EMAIL) NOT LIKE '%[^0-9A-Z_]%@%.COM' AND UPPER(EMAIL) NOT LIKE '%@%[^A-Z]%.COM'
ORDER BY USER_ID
---
SELECT *
FROM USERS
WHERE UPPER(EMAIL) LIKE '%@%.COM'
  AND PATINDEX('%[^0-9A-Z_]%@%.COM', UPPER(EMAIL)) = 0
  AND PATINDEX('%@%[^A-Z]%.COM%', UPPER(EMAIL)) = 0
ORDER BY USER_ID;
```

# [3465. Find Products with Valid Serial Numbers](https://leetcode.com/problems/find-products-with-valid-serial-numbers/)
```
Table: products
+--------------+------------+
| Column Name  | Type       |
+--------------+------------+
| product_id   | int        |
| product_name | varchar    |
| description  | varchar    |
+--------------+------------+
(product_id) is the unique key for this table.
Each row in the table represents a product with its unique ID, name, and description.
Write a solution to find all products whose description contains a valid serial number pattern. A valid serial number follows these rules:

It starts with the letters SN (case-sensitive).
Followed by exactly 4 digits.
It must have a hyphen (-) followed by exactly 4 digits.
The serial number must be within the description (it may not necessarily start at the beginning).
Return the result table ordered by product_id in ascending order.

The result format is in the following example.

Example:

Input:

products table:

+------------+--------------+------------------------------------------------------+
| product_id | product_name | description                                          |
+------------+--------------+------------------------------------------------------+
| 1          | Widget A     | This is a sample product with SN1234-5678            |
| 2          | Widget B     | A product with serial SN9876-1234 in the description |
| 3          | Widget C     | Product SN1234-56789 is available now                |
| 4          | Widget D     | No serial number here                                |
| 5          | Widget E     | Check out SN4321-8765 in this description            |
+------------+--------------+------------------------------------------------------+
    
Output:

+------------+--------------+------------------------------------------------------+
| product_id | product_name | description                                          |
+------------+--------------+------------------------------------------------------+
| 1          | Widget A     | This is a sample product with SN1234-5678            |
| 2          | Widget B     | A product with serial SN9876-1234 in the description |
| 5          | Widget E     | Check out SN4321-8765 in this description            |
+------------+--------------+------------------------------------------------------+
    
Explanation:

Product 1: Valid serial number SN1234-5678
Product 2: Valid serial number SN9876-1234
Product 3: Invalid serial number SN1234-56789 (contains 5 digits after the hyphen)
Product 4: No serial number in the description
Product 5: Valid serial number SN4321-8765
The result table is ordered by product_id in ascending order.
```
```sql
SELECT *
FROM PRODUCTS
WHERE
    -- Condition 1: Matches serial numbers at the beginning of the string, followed by a space.
    -- Example: 'SN1234-5678 Some other text'
    DESCRIPTION LIKE 'SN[0-9][0-9][0-9][0-9]-[0-9][0-9][0-9][0-9] %' COLLATE Latin1_General_BIN
    OR 
    -- Condition 2: Matches serial numbers that are in the middle of the string, with a space on either side.
    -- Example: 'Some other text SN1234-5678 Some other text'
    DESCRIPTION LIKE '% SN[0-9][0-9][0-9][0-9]-[0-9][0-9][0-9][0-9] %' COLLATE Latin1_General_BIN
    OR 
    -- Condition 3: Matches serial numbers at the very end of the string, preceded by a space.
    -- Example: 'Some other text SN1234-5678'
    DESCRIPTION LIKE '% SN[0-9][0-9][0-9][0-9]-[0-9][0-9][0-9][0-9]' COLLATE Latin1_General_BIN
ORDER BY 1 ASC;
```

# [3570. Find Books with No Available Copies](https://leetcode.com/problems/find-books-with-no-available-copies/)
```
Table: library_books
+------------------+---------+
| Column Name      | Type    |
+------------------+---------+
| book_id          | int     |
| title            | varchar |
| author           | varchar |
| genre            | varchar |
| publication_year | int     |
| total_copies     | int     |
+------------------+---------+
book_id is the unique identifier for this table.
Each row contains information about a book in the library, including the total number of copies owned by the library.
Table: borrowing_records

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| record_id     | int     |
| book_id       | int     |
| borrower_name | varchar |
| borrow_date   | date    |
| return_date   | date    |
+---------------+---------+
record_id is the unique identifier for this table.
Each row represents a borrowing transaction and return_date is NULL if the book is currently borrowed and hasn't been returned yet.
Write a solution to find all books that are currently borrowed (not returned) and have zero copies available in the library.

A book is considered currently borrowed if there exists a borrowing record with a NULL return_date
Return the result table ordered by current borrowers in descending order, then by book title in ascending order.

The result format is in the following example.

Example:

Input:

library_books table:
+---------+------------------------+------------------+----------+------------------+--------------+
| book_id | title                  | author           | genre    | publication_year | total_copies |
+---------+------------------------+------------------+----------+------------------+--------------+
| 1       | The Great Gatsby       | F. Scott         | Fiction  | 1925             | 3            |
| 2       | To Kill a Mockingbird  | Harper Lee       | Fiction  | 1960             | 3            |
| 3       | 1984                   | George Orwell    | Dystopian| 1949             | 1            |
| 4       | Pride and Prejudice    | Jane Austen      | Romance  | 1813             | 2            |
| 5       | The Catcher in the Rye | J.D. Salinger    | Fiction  | 1951             | 1            |
| 6       | Brave New World        | Aldous Huxley    | Dystopian| 1932             | 4            |
+---------+------------------------+------------------+----------+------------------+--------------+
borrowing_records table:
+-----------+---------+---------------+-------------+-------------+
| record_id | book_id | borrower_name | borrow_date | return_date |
+-----------+---------+---------------+-------------+-------------+
| 1         | 1       | Alice Smith   | 2024-01-15  | NULL        |
| 2         | 1       | Bob Johnson   | 2024-01-20  | NULL        |
| 3         | 2       | Carol White   | 2024-01-10  | 2024-01-25  |
| 4         | 3       | David Brown   | 2024-02-01  | NULL        |
| 5         | 4       | Emma Wilson   | 2024-01-05  | NULL        |
| 6         | 5       | Frank Davis   | 2024-01-18  | 2024-02-10  |
| 7         | 1       | Grace Miller  | 2024-02-05  | NULL        |
| 8         | 6       | Henry Taylor  | 2024-01-12  | NULL        |
| 9         | 2       | Ivan Clark    | 2024-02-12  | NULL        |
| 10        | 2       | Jane Adams    | 2024-02-15  | NULL        |
+-----------+---------+---------------+-------------+-------------+
Output:
+---------+------------------+---------------+-----------+------------------+-------------------+
| book_id | title            | author        | genre     | publication_year | current_borrowers |
+---------+------------------+---------------+-----------+------------------+-------------------+
| 1       | The Great Gatsby | F. Scott      | Fiction   | 1925             | 3                 | 
| 3       | 1984             | George Orwell | Dystopian | 1949             | 1                 |
+---------+------------------+---------------+-----------+------------------+-------------------+
Explanation:

The Great Gatsby (book_id = 1):
- Total copies: 3
- Currently borrowed by Alice Smith, Bob Johnson, and Grace Miller (3 borrowers)
- Available copies: 3 - 3 = 0
- Included because available_copies = 0
1984 (book_id = 3):
- Total copies: 1
- Currently borrowed by David Brown (1 borrower)
- Available copies: 1 - 1 = 0
- Included because available_copies = 0
Books not included:
- To Kill a Mockingbird (book_id = 2): Total copies = 3, current borrowers = 2, available = 1
- Pride and Prejudice (book_id = 4): Total copies = 2, current borrowers = 1, available = 1
- The Catcher in the Rye (book_id = 5): Total copies = 1, current borrowers = 0, available = 1
- Brave New World (book_id = 6): Total copies = 4, current borrowers = 1, available = 3
Result ordering:
- The Great Gatsby appears first with 3 current borrowers
- 1984 appears second with 1 current borrower

Output table is ordered by current_borrowers in descending order, then by book_title in ascending order.
```
```sql
WITH BORROWED_BOOK AS (
  SELECT 
    BOOK_ID, COUNT(*) AS CURRENT_BORROWERS 
  FROM 
    BORROWING_RECORDS 
  WHERE 
    RETURN_DATE IS NULL 
  GROUP BY 
    BOOK_ID
) 
SELECT 
  LB.BOOK_ID, TITLE, AUTHOR, GENRE, PUBLICATION_YEAR, CURRENT_BORROWERS 
FROM 
  LIBRARY_BOOKS LB 
  INNER JOIN BORROWED_BOOK BR ON LB.BOOK_ID = BR.BOOK_ID 
  AND (
    BR.CURRENT_BORROWERS - LB.TOTAL_COPIES
  ) = 0 
ORDER BY 
  6 DESC, 2 ASC
```

# [3642. Find Books with Polarized Opinions](https://leetcode.com/problems/find-books-with-polarized-opinions/)
```
Table: books
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| book_id     | int     |
| title       | varchar |
| author      | varchar |
| genre       | varchar |
| pages       | int     |
+-------------+---------+
book_id is the unique ID for this table.
Each row contains information about a book including its genre and page count.
Table: reading_sessions

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| session_id     | int     |
| book_id        | int     |
| reader_name    | varchar |
| pages_read     | int     |
| session_rating | int     |
+----------------+---------+
session_id is the unique ID for this table.
Each row represents a reading session where someone read a portion of a book. session_rating is on a scale of 1-5.
Write a solution to find books that have polarized opinions - books that receive both very high ratings and very low ratings from different readers.

- A book has polarized opinions if it has at least one rating ≥ 4 and at least one rating ≤ 2
- Only consider books that have at least 5 reading sessions
- Calculate the rating spread as (highest_rating - lowest_rating)
- Calculate the polarization score as the number of extreme ratings (ratings ≤ 2 or ≥ 4) divided by total sessions
- Only include books where polarization score ≥ 0.6 (at least 60% extreme ratings)
- Return the result table ordered by polarization score in descending order, then by title in descending order.

The result format is in the following example.

Example:

Input:

books table:
+---------+------------------------+---------------+----------+-------+
| book_id | title                  | author        | genre    | pages |
+---------+------------------------+---------------+----------+-------+
| 1       | The Great Gatsby       | F. Scott      | Fiction  | 180   |
| 2       | To Kill a Mockingbird  | Harper Lee    | Fiction  | 281   |
| 3       | 1984                   | George Orwell | Dystopian| 328   |
| 4       | Pride and Prejudice    | Jane Austen   | Romance  | 432   |
| 5       | The Catcher in the Rye | J.D. Salinger | Fiction  | 277   |
+---------+------------------------+---------------+----------+-------+
reading_sessions table:
+------------+---------+-------------+------------+----------------+
| session_id | book_id | reader_name | pages_read | session_rating |
+------------+---------+-------------+------------+----------------+
| 1          | 1       | Alice       | 50         | 5              |
| 2          | 1       | Bob         | 60         | 1              |
| 3          | 1       | Carol       | 40         | 4              |
| 4          | 1       | David       | 30         | 2              |
| 5          | 1       | Emma        | 45         | 5              |
| 6          | 2       | Frank       | 80         | 4              |
| 7          | 2       | Grace       | 70         | 4              |
| 8          | 2       | Henry       | 90         | 5              |
| 9          | 2       | Ivy         | 60         | 4              |
| 10         | 2       | Jack        | 75         | 4              |
| 11         | 3       | Kate        | 100        | 2              |
| 12         | 3       | Liam        | 120        | 1              |
| 13         | 3       | Mia         | 80         | 2              |
| 14         | 3       | Noah        | 90         | 1              |
| 15         | 3       | Olivia      | 110        | 4              |
| 16         | 3       | Paul        | 95         | 5              |
| 17         | 4       | Quinn       | 150        | 3              |
| 18         | 4       | Ruby        | 140        | 3              |
| 19         | 5       | Sam         | 80         | 1              |
| 20         | 5       | Tara        | 70         | 2              |
+------------+---------+-------------+------------+----------------+
Output:
+---------+------------------+---------------+-----------+-------+---------------+--------------------+
| book_id | title            | author        | genre     | pages | rating_spread | polarization_score |
+---------+------------------+---------------+-----------+-------+---------------+--------------------+
| 1       | The Great Gatsby | F. Scott      | Fiction   | 180   | 4             | 1.00               |
| 3       | 1984             | George Orwell | Dystopian | 328   | 4             | 1.00               |
+---------+------------------+---------------+-----------+-------+---------------+--------------------+
Explanation:

- The Great Gatsby (book_id = 1):
-     Has 5 reading sessions (meets minimum requirement)
-     Ratings: 5, 1, 4, 2, 5
-     Has ratings ≥ 4: 5, 4, 5 (3 sessions)
-     Has ratings ≤ 2: 1, 2 (2 sessions)
-     Rating spread: 5 - 1 = 4
-     Extreme ratings (≤2 or ≥4): All 5 sessions (5, 1, 4, 2, 5)
-     Polarization score: 5/5 = 1.00 (≥ 0.6, qualifies)
- 1984 (book_id = 3):
-    Has 6 reading sessions (meets minimum requirement)
-    Ratings: 2, 1, 2, 1, 4, 5
-    Has ratings ≥ 4: 4, 5 (2 sessions)
-    Has ratings ≤ 2: 2, 1, 2, 1 (4 sessions)
-    Rating spread: 5 - 1 = 4
-    Extreme ratings (≤2 or ≥4): All 6 sessions (2, 1, 2, 1, 4, 5)
-    Polarization score: 6/6 = 1.00 (≥ 0.6, qualifies)
Books not included:
- To Kill a Mockingbird (book_id = 2): All ratings are 4-5, no low ratings (≤2)
- Pride and Prejudice (book_id = 4): Only 2 sessions (< 5 minimum)
- The Catcher in the Rye (book_id = 5): Only 2 sessions (< 5 minimum)

The result table is ordered by polarization score in descending order, then by book title in descending order.
```
```sql
WITH POLAR_RATING AS (
  SELECT 
    BOOK_ID, 
    MAX(SESSION_RATING) - MIN(SESSION_RATING) AS RATING_SPREAD, 
    SUM(
      CASE WHEN SESSION_RATING >= 4 
      OR SESSION_RATING <= 2 THEN 1 ELSE 0 END
    ) * 1.0 / COUNT(SESSION_ID) AS POLARIZATION_SCORE 
  FROM 
    READING_SESSIONS 
  GROUP BY 
    BOOK_ID 
  HAVING 
    SUM(
      CASE WHEN SESSION_RATING >= 4 THEN 1 ELSE 0 END
    ) >= 1 
    AND SUM(
      CASE WHEN SESSION_RATING <= 2 THEN 1 ELSE 0 END
    ) >= 1 
    AND COUNT(SESSION_ID) >= 5
) 
SELECT 
  B.BOOK_ID, 
  TITLE, 
  AUTHOR, 
  GENRE, 
  PAGES, 
  RATING_SPREAD, 
  POLARIZATION_SCORE 
FROM 
  BOOKS B 
  INNER JOIN POLAR_RATING PR ON B.BOOK_ID = PR.BOOK_ID 
  AND PR.POLARIZATION_SCORE >= 0.6 
ORDER BY 
  7 DESC, 
  2 DESC
```

# [511. Game Play Analysis I](https://leetcode.com/problems/game-play-analysis-i/)
```sql
SELECT PLAYER_ID , MIN(EVENT_DATE) AS FIRST_LOGIN FROM ACTIVITY GROUP BY PLAYER_ID
```

# [512. Game Play Analysis II](https://leetcode.com/problems/game-play-analysis-ii/)
```sql
SELECT
    PLAYER_ID,
    DEVICE_ID
FROM ACTIVITY
WHERE
    (PLAYER_ID, EVENT_DATE) IN (
        SELECT
            PLAYER_ID,
            MIN(EVENT_DATE) AS EVENT_DATE
        FROM ACTIVITY
        GROUP BY 1
    );
--------
WITH
    T AS (
        SELECT
            *,
            RANK() OVER (
                PARTITION BY PLAYER_ID
                ORDER BY EVENT_DATE
            ) AS RK
        FROM ACTIVITY
    )
SELECT PLAYER_ID, DEVICE_ID
FROM T
WHERE RK = 1;
```

# [577. Employee Bonus](https://leetcode.com/problems/employee-bonus/)
```sql
SELECT NAME, BONUS FROM EMPLOYEE E LEFT JOIN BONUS B ON E.EMPID = B.EMPID WHERE B.BONUS < 1000 OR B.BONUS IS NULL
```

# [584. Find Customer Referee](https://leetcode.com/problems/find-customer-referee/)
```sql
SELECT NAME FROM CUSTOMER WHERE REFEREE_ID <> '2' OR REFEREE_ID IS NULL
```

# [586. Customer Placing the Largest Number of Orders](https://leetcode.com/problems/customer-placing-the-largest-number-of-orders/)
```
Table: Orders
+-----------------+----------+
| Column Name     | Type     |
+-----------------+----------+
| order_number    | int      |
| customer_number | int      |
+-----------------+----------+
order_number is the primary key (column with unique values) for this table.
This table contains information about the order ID and the customer ID.
 
Write a solution to find the customer_number for the customer who has placed the largest number of orders.
The test cases are generated so that exactly one customer will have placed more orders than any other customer.
The result format is in the following example.

Example 1:

Input: 
Orders table:
+--------------+-----------------+
| order_number | customer_number |
+--------------+-----------------+
| 1            | 1               |
| 2            | 2               |
| 3            | 3               |
| 4            | 3               |
+--------------+-----------------+
Output: 
+-----------------+
| customer_number |
+-----------------+
| 3               |
+-----------------+
Explanation: 
The customer with number 3 has two orders, which is greater than either customer 1 or 2 because each of them only has one order. 
So the result is customer_number 3.
```
```sql
SELECT TOP 1 CUSTOMER_NUMBER
FROM ORDERS
GROUP BY CUSTOMER_NUMBER
ORDER BY COUNT(*) DESC;
```

# [595. Big Countries](https://leetcode.com/problems/big-countries/)
```
Table: World
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| name        | varchar |
| continent   | varchar |
| area        | int     |
| population  | int     |
| gdp         | bigint  |
+-------------+---------+
name is the primary key (column with unique values) for this table.
Each row of this table gives information about the name of a country, the continent to which it belongs, its area, the population, and its GDP value.
 

A country is big if:
it has an area of at least three million (i.e., 3000000 km2), or
it has a population of at least twenty-five million (i.e., 25000000).
Write a solution to find the name, population, and area of the big countries.
Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
World table:
+-------------+-----------+---------+------------+--------------+
| name        | continent | area    | population | gdp          |
+-------------+-----------+---------+------------+--------------+
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |
+-------------+-----------+---------+------------+--------------+
Output: 
+-------------+------------+---------+
| name        | population | area    |
+-------------+------------+---------+
| Afghanistan | 25500100   | 652230  |
| Algeria     | 37100000   | 2381741 |
+-------------+------------+---------+
```
```sql
SELECT NAME,POPULATION,AREA FROM WORLD WHERE POPULATION>=25000000 OR AREA>=3000000 
```

# [596. Classes With at Least 5 Students](https://leetcode.com/problems/classes-with-at-least-5-students/)
```
Table: Courses
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| student     | varchar |
| class       | varchar |
+-------------+---------+
(student, class) is the primary key (combination of columns with unique values) for this table.
Each row of this table indicates the name of a student and the class in which they are enrolled.
 
Write a solution to find all the classes that have at least five students.
Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
Courses table:
+---------+----------+
| student | class    |
+---------+----------+
| A       | Math     |
| B       | English  |
| C       | Math     |
| D       | Biology  |
| E       | Math     |
| F       | Computer |
| G       | Math     |
| H       | Math     |
| I       | Math     |
+---------+----------+
Output: 
+---------+
| class   |
+---------+
| Math    |
+---------+
Explanation: 
- Math has 6 students, so we include it.
- English has 1 student, so we do not include it.
- Biology has 1 student, so we do not include it.
- Computer has 1 student, so we do not include it.
```
```sql
SELECT CLASS
FROM COURSES
GROUP BY CLASS
HAVING COUNT(STUDENT) >= 5;
```

# [597. Friend Requests I: Overall Acceptance Rate](https://leetcode.com/problems/friend-requests-i-overall-acceptance-rate/)
```
In social network like Facebook or Twitter, people send friend requests and accept others’ requests as well. Now given two tables as below:

Table: friend_request
| sender_id | send_to_id |request_date|
|-----------|------------|------------|
| 1         | 2          | 2016_06-01 |
| 1         | 3          | 2016_06-01 |
| 1         | 4          | 2016_06-01 |
| 2         | 3          | 2016_06-02 |
| 3         | 4          | 2016-06-09 |

Table: request_accepted
| requester_id | accepter_id |accept_date |
|--------------|-------------|------------|
| 1            | 2           | 2016_06-03 |
| 1            | 3           | 2016-06-08 |
| 2            | 3           | 2016-06-08 |
| 3            | 4           | 2016-06-09 |
| 3            | 4           | 2016-06-10 |
 
Write a query to find the overall acceptance rate of requests rounded to 2 decimals, which is the number of acceptance divide the number of requests.
For the sample data above, your query should return the following result.

|accept_rate|
|-----------|
|       0.80|
 
Note:
The accepted requests are not necessarily from the table friend_request. In this case, you just need to simply count the total accepted requests (no matter whether they are in the original requests), and divide it by the number of requests to get the acceptance rate.
It is possible that a sender sends multiple requests to the same receiver, and a request could be accepted more than once. In this case, the ‘duplicated’ requests or acceptances are only counted once.
If there is no requests at all, you should return 0.00 as the accept_rate.
Explanation: There are 4 unique accepted requests, and there are 5 requests in total. So the rate is 0.80.
```
```sql
SELECT
    ROUND(
        ISNULL(
            (SELECT COUNT(*) FROM (SELECT DISTINCT ACCEPTER_ID, REQUESTER_ID FROM REQUESTACCEPTED) AS T1)
            * 1.0 / NULLIF((SELECT COUNT(*) FROM (SELECT DISTINCT SEND_TO_ID, SENDER_ID FROM FRIENDREQUEST) AS T2), 0),
            0
        ),
        2
    ) AS ACCEPT_RATE;
```

# [603. Consecutive Available Seats](https://leetcode.com/problems/consecutive-available-seats/)
```
Several friends at a cinema ticket office would like to reserve consecutive available seats.
Can you help to query all the consecutive available seats order by the seat_id using the following cinema table?
| seat_id | free |
|---------|------|
| 1       | 1    |
| 2       | 0    |
| 3       | 1    |
| 4       | 1    |
| 5       | 1    |
 
Your query should return the following result for the sample case above.

| seat_id |
|---------|
| 3       |
| 4       |
| 5       |

Note:
The seat_id is an auto increment int, and free is bool ('1' means free, and '0' means occupied.).
Consecutive available seats are more than 2(inclusive) seats consecutively available.
```
```sql
WITH CINEMANEIGHBORS AS (
  SELECT
    *,
    LAG(FREE) OVER(ORDER BY SEAT_ID) AS PREV_FREE,
    LEAD(FREE) OVER(ORDER BY SEAT_ID) AS NEXT_FREE
  FROM CINEMA
)
SELECT SEAT_ID
FROM CINEMANEIGHBORS
WHERE FREE = 1
  AND (PREV_FREE = 1 OR NEXT_FREE = 1)
ORDER BY 1;
-----------------
WITH FREESEATSWITHGAP AS (
  SELECT
    SEAT_ID,
    SEAT_ID - ROW_NUMBER() OVER (ORDER BY SEAT_ID) AS GAP_KEY
  FROM CINEMA
  WHERE FREE = 1
)
, FREESEATCOUNTS AS (
  SELECT
    SEAT_ID,
    COUNT(*) OVER (PARTITION BY GAP_KEY) AS GROUP_COUNT
  FROM FREESEATSWITHGAP
)
SELECT
  SEAT_ID
FROM FREESEATCOUNTS
WHERE GROUP_COUNT >= 2
ORDER BY SEAT_ID;
```

# [607. Sales Person](https://leetcode.com/problems/sales-person/)
```
Table: SalesPerson
+-----------------+---------+
| Column Name     | Type    |
+-----------------+---------+
| sales_id        | int     |
| name            | varchar |
| salary          | int     |
| commission_rate | int     |
| hire_date       | date    |
+-----------------+---------+
sales_id is the primary key (column with unique values) for this table.
Each row of this table indicates the name and the ID of a salesperson alongside their salary, commission rate, and hire date.
 
Table: Company
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| com_id      | int     |
| name        | varchar |
| city        | varchar |
+-------------+---------+
com_id is the primary key (column with unique values) for this table.
Each row of this table indicates the name and the ID of a company and the city in which the company is located.
 
Table: Orders
+-------------+------+
| Column Name | Type |
+-------------+------+
| order_id    | int  |
| order_date  | date |
| com_id      | int  |
| sales_id    | int  |
| amount      | int  |
+-------------+------+
order_id is the primary key (column with unique values) for this table.
com_id is a foreign key (reference column) to com_id from the Company table.
sales_id is a foreign key (reference column) to sales_id from the SalesPerson table.
Each row of this table contains information about one order. This includes the ID of the company, the ID of the salesperson, the date of the order, and the amount paid.
 
Write a solution to find the names of all the salespersons who did not have any orders related to the company with the name "RED".
Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
SalesPerson table:
+----------+------+--------+-----------------+------------+
| sales_id | name | salary | commission_rate | hire_date  |
+----------+------+--------+-----------------+------------+
| 1        | John | 100000 | 6               | 4/1/2006   |
| 2        | Amy  | 12000  | 5               | 5/1/2010   |
| 3        | Mark | 65000  | 12              | 12/25/2008 |
| 4        | Pam  | 25000  | 25              | 1/1/2005   |
| 5        | Alex | 5000   | 10              | 2/3/2007   |
+----------+------+--------+-----------------+------------+
Company table:
+--------+--------+----------+
| com_id | name   | city     |
+--------+--------+----------+
| 1      | RED    | Boston   |
| 2      | ORANGE | New York |
| 3      | YELLOW | Boston   |
| 4      | GREEN  | Austin   |
+--------+--------+----------+
Orders table:
+----------+------------+--------+----------+--------+
| order_id | order_date | com_id | sales_id | amount |
+----------+------------+--------+----------+--------+
| 1        | 1/1/2014   | 3      | 4        | 10000  |
| 2        | 2/1/2014   | 4      | 5        | 5000   |
| 3        | 3/1/2014   | 1      | 1        | 50000  |
| 4        | 4/1/2014   | 1      | 4        | 25000  |
+----------+------------+--------+----------+--------+
Output: 
+------+
| name |
+------+
| Amy  |
| Mark |
| Alex |
+------+
Explanation: 
According to orders 3 and 4 in the Orders table, it is easy to tell that only salesperson John and Pam have sales to company RED, so we report all the other names in the table salesperson.
```
```sql
SELECT S.NAME
FROM ORDERS O
INNER JOIN COMPANY C
  ON (O.COM_ID = C.COM_ID AND C.NAME = 'RED')
RIGHT JOIN SALESPERSON S
  ON S.SALES_ID = O.SALES_ID
WHERE O.SALES_ID IS NULL;
```

# [610. Triangle Judgement](https://leetcode.com/problems/triangle-judgement/)
```
Table: Triangle
+-------------+------+
| Column Name | Type |
+-------------+------+
| x           | int  |
| y           | int  |
| z           | int  |
+-------------+------+
In SQL, (x, y, z) is the primary key column for this table.
Each row of this table contains the lengths of three line segments.
 
Report for every three line segments whether they can form a triangle.
Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
Triangle table:
+----+----+----+
| x  | y  | z  |
+----+----+----+
| 13 | 15 | 30 |
| 10 | 20 | 15 |
+----+----+----+
Output: 
+----+----+----+----------+
| x  | y  | z  | triangle |
+----+----+----+----------+
| 13 | 15 | 30 | No       |
| 10 | 20 | 15 | Yes      |
+----+----+----+----------+
```
```sql
SELECT
    *,
    CASE
        WHEN X + Y > Z AND X + Z > Y AND Y + Z > X THEN 'Yes'
        ELSE 'No'
    END AS TRIANGLE
FROM
    TRIANGLE;
```

# [613. Shortest Distance in a Line](https://leetcode.com/problems/shortest-distance-in-a-line/)
```
Table point holds the x coordinate of some points on x-axis in a plane, which are all integers.
Write a query to find the shortest distance between two points in these points.

| x   |
|-----|
| -1  |
| 0   |
| 2   |
 
The shortest distance is '1' obviously, which is from point '-1' to '0'. So the output is as below:

| shortest|
|---------|
| 1       |

Note: Every point is unique, which means there is no duplicates in table point.
```
```sql
SELECT MIN(P2.X - P1.X) AS SHORTEST
FROM
    POINT AS P1
    JOIN POINT AS P2 ON P1.X < P2.X
```

# [619. Biggest Single Number](https://leetcode.com/problems/biggest-single-number/)
```
Table: MyNumbers
+-------------+------+
| Column Name | Type |
+-------------+------+
| num         | int  |
+-------------+------+
This table may contain duplicates (In other words, there is no primary key for this table in SQL).
Each row of this table contains an integer.

A single number is a number that appeared only once in the MyNumbers table.
Find the largest single number. If there is no single number, report null.
The result format is in the following example.

Example 1:

Input: 
MyNumbers table:
+-----+
| num |
+-----+
| 8   |
| 8   |
| 3   |
| 3   |
| 1   |
| 4   |
| 5   |
| 6   |
+-----+
Output: 
+-----+
| num |
+-----+
| 6   |
+-----+
Explanation: The single numbers are 1, 4, 5, and 6.
Since 6 is the largest single number, we return it.
Example 2:

Input: 
MyNumbers table:
+-----+
| num |
+-----+
| 8   |
| 8   |
| 7   |
| 7   |
| 3   |
| 3   |
| 3   |
+-----+
Output: 
+------+
| num  |
+------+
| null |
+------+
Explanation: There are no single numbers in the input table so we return null.
```
```sql
WITH CTE AS (
  SELECT 
    NUM,COUNT(NUM) COUNTED 
  FROM 
    MYNUMBERS 
  GROUP BY 
    NUM 
  HAVING 
    COUNT(NUM) = 1
) 
SELECT 
  ISNULL(MAX(NUM), NULL) NUM 
FROM 
  CTE
```

# [620. Not Boring Movies](https://leetcode.com/problems/not-boring-movies/)
```
Table: Cinema

+----------------+----------+
| Column Name    | Type     |
+----------------+----------+
| id             | int      |
| movie          | varchar  |
| description    | varchar  |
| rating         | float    |
+----------------+----------+
id is the primary key (column with unique values) for this table.
Each row contains information about the name of a movie, its genre, and its rating.
rating is a 2 decimal places float in the range [0, 10]
 
Write a solution to report the movies with an odd-numbered ID and a description that is not "boring".
Return the result table ordered by rating in descending order.
The result format is in the following example.

Example 1:

Input: 
Cinema table:
+----+------------+-------------+--------+
| id | movie      | description | rating |
+----+------------+-------------+--------+
| 1  | War        | great 3D    | 8.9    |
| 2  | Science    | fiction     | 8.5    |
| 3  | irish      | boring      | 6.2    |
| 4  | Ice song   | Fantacy     | 8.6    |
| 5  | House card | Interesting | 9.1    |
+----+------------+-------------+--------+
Output: 
+----+------------+-------------+--------+
| id | movie      | description | rating |
+----+------------+-------------+--------+
| 5  | House card | Interesting | 9.1    |
| 1  | War        | great 3D    | 8.9    |
+----+------------+-------------+--------+
Explanation: 
We have three movies with odd-numbered IDs: 1, 3, and 5. The movie with ID = 3 is boring so we do not include it in the answer.
```
```sql
SELECT ID, MOVIE, DESCRIPTION, RATING FROM CINEMA WHERE DESCRIPTION <> 'boring' AND ID % 2 = 1 ORDER BY RATING DESC

```














