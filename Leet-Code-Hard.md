# [1097. Game Play Analysis V](https://leetcode.com/problems/game-play-analysis-v/)
```
Table: Activity
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
(player_id, event_date) is the primary key of this table.
This table shows the activity of players of some game.
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on some day using some device.

We define the install date of a player to be the first login day of that player.Games
We also define day 1 retention of some date X to be the number of players whose install date is X and they logged back in on the day right after X, divided by the number of players whose install date is X, rounded to 2 decimal places
Write an SQL query that reports for each install date, the number of players that installed the  game on that day and the day 1 retention.

The query result format is in the following example:
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-03-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-01 | 0            |
| 3         | 4         | 2016-07-03 | 5            |
+-----------+-----------+------------+--------------+
Result table:
+------------+----------+----------------+
| install_dt | installs | Day1_retention |
+------------+----------+----------------+
| 2016-03-01 | 2        | 0.50           |
| 2017-06-25 | 1        | 0.00           |
+------------+----------+----------------+
Player 1 and 3 installed the game on 2016-03-01 but only player 1 logged back in on 2016-03-02 so the day 1 retention of 2016-03-01 is 1 / 2 = 0.50
Player 2 installed the game on 2017-06-25 but didn't log back in on 2017-06-26 so the day 1 retention of 2017-06-25 is 0 / 1 = 0.00
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS PLAYER_ACTIVITY;
GO

CREATE TABLE PLAYER_ACTIVITY (
    PLAYER_ID INT,
    DEVICE_ID INT,
    EVENT_DATE DATE,
    GAMES_PLAYED INT
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO PLAYER_ACTIVITY (PLAYER_ID, DEVICE_ID, EVENT_DATE, GAMES_PLAYED)
VALUES
(1, 2, '2016-03-01', 5),
(1, 2, '2016-03-02', 6),
(2, 3, '2017-06-25', 1),
(3, 1, '2016-03-01', 0),
(3, 4, '2016-07-03', 5);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM PLAYER_ACTIVITY;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
-- Calculate Day 1 retention by install date
WITH INSTALL_DATA AS (
    SELECT 
        PLAYER_ID,
        EVENT_DATE AS INSTALL_DT, -- first login date
        LEAD(EVENT_DATE) OVER (PARTITION BY PLAYER_ID ORDER BY EVENT_DATE) AS NEXT_LOGIN, -- next login date
        ROW_NUMBER() OVER (PARTITION BY PLAYER_ID ORDER BY EVENT_DATE) AS RNK -- rank to identify first login
    FROM PLAYER_ACTIVITY
)
SELECT 
    INSTALL_DT,                          -- install date
    COUNT(*) AS INSTALLS,                -- number of installs on that date
    ROUND(
        SUM(CASE WHEN DATEDIFF(DAY, INSTALL_DT, NEXT_LOGIN) = 1 THEN 1 ELSE 0 END) 
        * 1.0 / COUNT(*), 2              -- fraction of players returning next day
    ) AS DAY1_RETENTION
FROM INSTALL_DATA
WHERE RNK = 1                            -- only consider first login per player
GROUP BY INSTALL_DT;                     -- group results by install date
```

# [1127. User Purchase Platform](https://leetcode.com/problems/user-purchase-platform/)
```
Table: Spending
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| spend_date  | date    |
| platform    | enum    |
| amount      | int     |
+-------------+---------+
The table logs the spendings history of users that make purchases from an online shopping website which has a desktop and a mobile application.
(user_id, spend_date, platform) is the primary key of this table.
The platform column is an ENUM type of ('desktop', 'mobile').
Write an SQL query to find the total number of users and the total amount spent using mobile only, desktop only and both mobile and desktop together for each date.Programming

The query result format is in the following example:
Spending table:
+---------+------------+----------+--------+
| user_id | spend_date | platform | amount |
+---------+------------+----------+--------+
| 1       | 2019-07-01 | mobile   | 100    |
| 1       | 2019-07-01 | desktop  | 100    |
| 2       | 2019-07-01 | mobile   | 100    |
| 2       | 2019-07-02 | mobile   | 100    |
| 3       | 2019-07-01 | desktop  | 100    |
| 3       | 2019-07-02 | desktop  | 100    |
+---------+------------+----------+--------+
Result table:
+------------+----------+--------------+-------------+
| spend_date | platform | total_amount | total_users |
+------------+----------+--------------+-------------+
| 2019-07-01 | desktop  | 100          | 1           |
| 2019-07-01 | mobile   | 100          | 1           |
| 2019-07-01 | both     | 200          | 1           |
| 2019-07-02 | desktop  | 100          | 1           |
| 2019-07-02 | mobile   | 100          | 1           |
| 2019-07-02 | both     | 0            | 0           |
+------------+----------+--------------+-------------+
On 2019-07-01, user 1 purchased using both desktop and mobile, user 2 purchased using mobile only and user 3 purchased using desktop only.
On 2019-07-02, user 2 purchased using mobile only, user 3 purchased using desktop only and no one purchased using both platforms.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS SPENDS;
GO

CREATE TABLE SPENDS (
    USER_ID INT,
    SPEND_DATE DATE,
    PLATFORM VARCHAR(20),
    AMOUNT INT
);

GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO SPENDS (USER_ID, SPEND_DATE, PLATFORM, AMOUNT) VALUES
(1, '2019-07-01', 'mobile', 100),
(1, '2019-07-01', 'desktop', 100),
(2, '2019-07-01', 'mobile', 100),
(2, '2019-07-02', 'mobile', 100),
(3, '2019-07-01', 'desktop', 100),
(3, '2019-07-02', 'desktop', 100);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM SPENDS;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
-- Step 1: Identify device usage per user per date
WITH DEVICE_IND AS (
    SELECT 
        USER_ID,
        SPEND_DATE,
        SUM(AMOUNT) AS TOTAL_AMOUNT,
        MAX(CASE WHEN UPPER(PLATFORM) = 'MOBILE'  THEN 1 ELSE 0 END) AS MOB_IND,
        MAX(CASE WHEN UPPER(PLATFORM) = 'DESKTOP' THEN 1 ELSE 0 END) AS DESK_IND,
        CASE 
            WHEN MAX(CASE WHEN UPPER(PLATFORM) = 'MOBILE'  THEN 1 ELSE 0 END) = 1
             AND MAX(CASE WHEN UPPER(PLATFORM) = 'DESKTOP' THEN 1 ELSE 0 END) = 1 
            THEN 1 ELSE 0 
        END AS BOTH_IND
    FROM SPENDS
    GROUP BY USER_ID, SPEND_DATE
),

-- Step 2: Build mapping of all spend dates × all device categories
SPEND_DATE_DEVICE_MAP AS (
    SELECT SPEND_DATE, PLATFORM
    FROM (SELECT DISTINCT SPEND_DATE FROM DEVICE_IND) A
    CROSS JOIN (
        SELECT 'Mobile' AS PLATFORM 
        UNION SELECT 'Desktop' 
        UNION SELECT 'Both'
    ) B
),

-- Step 3: Aggregate spend and user counts by device type
DERIVED_DATA AS (
    SELECT SPEND_DATE, 'Mobile' AS PLATFORM, SUM(TOTAL_AMOUNT) AS TOTAL_AMOUNT, COUNT(*) AS TOTAL_USER
    FROM DEVICE_IND WHERE MOB_IND = 1 AND BOTH_IND = 0 GROUP BY SPEND_DATE
    UNION
    SELECT SPEND_DATE, 'Desktop' AS PLATFORM, SUM(TOTAL_AMOUNT), COUNT(*)
    FROM DEVICE_IND WHERE DESK_IND = 1 AND BOTH_IND = 0 GROUP BY SPEND_DATE
    UNION
    SELECT SPEND_DATE, 'Both' AS PLATFORM, SUM(TOTAL_AMOUNT), COUNT(*)
    FROM DEVICE_IND WHERE BOTH_IND = 1 GROUP BY SPEND_DATE
)

-- Step 4: Join mapping with derived data to ensure all categories appear
SELECT 
    A.SPEND_DATE,
    A.PLATFORM,
    ISNULL(B.TOTAL_AMOUNT,0) AS TOTAL_AMOUNT,
    ISNULL(B.TOTAL_USER,0)   AS TOTAL_USER
FROM SPEND_DATE_DEVICE_MAP A
LEFT JOIN DERIVED_DATA B 
    ON A.SPEND_DATE = B.SPEND_DATE 
   AND A.PLATFORM   = B.PLATFORM
ORDER BY A.SPEND_DATE, A.PLATFORM DESC;
-----------------------------------------------------
WITH USERTOAMOUNT AS (
    SELECT
        USER_ID,
        SPEND_DATE,
        CASE 
            WHEN COUNT(DISTINCT PLATFORM) = 2 THEN 'both'
            ELSE MAX(PLATFORM)
        END AS PLATFORM,
        SUM(AMOUNT) AS AMOUNT
    FROM SPENDS
    GROUP BY USER_ID, SPEND_DATE
),
DATEANDPLATFORMS AS (
    SELECT DISTINCT SPEND_DATE, 'desktop' AS PLATFORM FROM SPENDS
    UNION ALL
    SELECT DISTINCT SPEND_DATE, 'mobile' AS PLATFORM FROM SPENDS
    UNION ALL
    SELECT DISTINCT SPEND_DATE, 'both' AS PLATFORM FROM SPENDS
)
SELECT
    DP.SPEND_DATE,
    DP.PLATFORM,
    ISNULL(SUM(UA.AMOUNT), 0) AS TOTAL_AMOUNT,
    COUNT(DISTINCT UA.USER_ID) AS TOTAL_USERS
FROM DATEANDPLATFORMS DP
LEFT JOIN USERTOAMOUNT UA
    ON DP.SPEND_DATE = UA.SPEND_DATE AND DP.PLATFORM = UA.PLATFORM
GROUP BY DP.SPEND_DATE, DP.PLATFORM ORDER BY 1 ,2 DESC;
```

# [1159. Market Analysis II](https://leetcode.com/problems/market-analysis-ii/)
#### Schema

Table: Users

| Column Name    | Type    |
|----------------|---------|
| user_id        | int     |
| favorite_brand | varchar |

user_id is the primary key for this table.

Table: Orders

| Column Name | Type    |
|-------------|---------|
| order_id    | int     |
| order_date  | date    |
| item_id     | int     |
| seller_id   | int     |

order_id is the primary key for this table.

Table: Items

| Column Name | Type    |
|-------------|---------|
| item_id     | int     |
| item_brand  | varchar |

item_id is the primary key for this table.

#### Description

A seller's second sold item is the item they sold in their second earliest order. Write an SQL query to find whether the seller's favorite brand matches the brand of their second sold item. Return seller_id and 'yes' if their favorite brand matches, 'no' otherwise.

#### Sample Input

Users table:

| user_id | favorite_brand |
|---------|---------------|
| 1       | Lenovo        |
| 2       | Samsung       |

Orders table:

| order_id | order_date | item_id | seller_id |
|----------|------------|---------|-----------|
| 1        | 2020-01-01 | 2       | 1         |
| 2        | 2020-01-02 | 3       | 1         |
| 3        | 2020-01-02 | 4       | 2         |

Items table:

| item_id | item_brand |
|---------|------------|
| 2       | Lenovo     |
| 3       | Samsung    |
| 4       | Samsung    |

#### Sample Output

| seller_id | second_item_fav_brand |
|-----------|----------------------|
| 1         | no                   |
| 2         | yes                  |

**Explanation:**  
- Seller 1's second item is item 3 (Samsung), favorite is Lenovo → "no"
- Seller 2's second item is item 4 (Samsung), favorite is Samsung → "yes"

```sql
-- Rank each seller's orders by order_date
WITH RANKEDORDERS AS (
    SELECT
        ORDER_DATE,
        ITEM_ID,
        SELLER_ID,
        RANK() OVER (
            PARTITION BY SELLER_ID
            ORDER BY ORDER_DATE
        ) AS RK
    FROM ORDERS
)

-- Join users with their second sold item and compare brands
SELECT
    U.USER_ID AS SELLER_ID,
    CASE
        WHEN U.FAVORITE_BRAND = I.ITEM_BRAND THEN 'yes'
        ELSE 'no'
    END AS SECOND_ITEM_FAV_BRAND
FROM USERS AS U
LEFT JOIN RANKEDORDERS AS O
    ON U.USER_ID = O.SELLER_ID AND O.RK = 2
LEFT JOIN ITEMS AS I
    ON O.ITEM_ID = I.ITEM_ID;
```

# [1194. Tournament Winners](https://leetcode.com/problems/tournament-winners/)
#### Schema

Table: Players

| Column Name | Type |
|-------------|------|
| player_id   | int  |
| group_id    | int  |

player_id is the primary key for this table.

Table: Matches

| Column Name   | Type |
|---------------|------|
| match_id      | int  |
| first_player  | int  |
| second_player | int  |
| first_score   | int  |
| second_score  | int  |

match_id is the primary key for this table.

#### Description

Given the results of matches in a tournament, find the player(s) with the highest total score in each group.

#### Sample Input

Players table:

| player_id | group_id |
|-----------|----------|
| 15        | 1        |
| 25        | 1        |
| 30        | 1        |
| 45        | 2        |
| 50        | 2        |
| 60        | 2        |

Matches table:

| match_id | first_player | second_player | first_score | second_score |
|----------|-------------|--------------|-------------|--------------|
| 1        | 15          | 45           | 3           | 5            |
| 2        | 30          | 60           | 2           | 6            |
| 3        | 15          | 60           | 7           | 0            |
| 4        | 25          | 45           | 1           | 2            |
| 5        | 30          | 25           | 5           | 5            |

#### Sample Output

| group_id | player_id |
|----------|-----------|
| 1        | 15        |
| 2        | 45        |
| 2        | 60        |

**Explanation:**  
- Group 1: 15 (3+7=10), 25 (1+5=6), 30 (2+5=7) → 15 wins  
- Group 2: 45 (5+2=7), 50 (0), 60 (6+0=6) → 45 and 60 tie


```sql
-- CTE TO GATHER ALL SCORES FOR EACH PLAYER (AS FIRST OR SECOND PLAYER)
WITH PLAYERTOSCORE AS (
    SELECT 
        P.PLAYER_ID, 
        P.GROUP_ID, 
        M.FIRST_SCORE AS SCORE
    FROM PLAYERS P
    LEFT JOIN MATCHES M ON P.PLAYER_ID = M.FIRST_PLAYER

    UNION ALL

    SELECT 
        P.PLAYER_ID, 
        P.GROUP_ID, 
        M.SECOND_SCORE AS SCORE
    FROM PLAYERS P
    LEFT JOIN MATCHES M ON P.PLAYER_ID = M.SECOND_PLAYER
),

-- CTE TO RANK PLAYERS BY TOTAL SCORE WITHIN EACH GROUP
RANKEDPLAYERS AS (
    SELECT 
        PLAYER_ID, 
        GROUP_ID,
        RANK() OVER (
            PARTITION BY GROUP_ID 
            ORDER BY SUM(SCORE) DESC, PLAYER_ID
        ) AS RANK
    FROM PLAYERTOSCORE
    GROUP BY PLAYER_ID, GROUP_ID
)

-- SELECT TOP-RANKED PLAYER(S) FROM EACH GROUP
SELECT 
    GROUP_ID, 
    PLAYER_ID
FROM RANKEDPLAYERS
WHERE RANK = 1;
```

# [1225. Report Contiguous Dates](https://leetcode.com/problems/report-contiguous-dates/)
#### Schema

Table: Failed

| Column Name | Type    |
|-------------|---------|
| fail_date   | date    |

fail_date is the primary key for this table.

Table: Succeeded

| Column Name   | Type    |
|---------------|---------|
| success_date  | date    |

success_date is the primary key for this table.

#### Description

Given two tables Failed and Succeeded, each with dates in 2019 when a process failed or succeeded, report every contiguous period of days where the process had the same status (either succeeded or failed).

#### Sample Input

Failed table:

| fail_date   |
|-------------|
| 2019-01-01  |
| 2019-01-02  |
| 2019-01-03  |
| 2019-01-07  |
| 2019-01-08  |

Succeeded table:

| success_date |
|--------------|
| 2019-01-04   |
| 2019-01-05   |
| 2019-01-06   |
| 2019-01-09   |

#### Sample Output

| period_state | start_date | end_date   |
|--------------|------------|------------|
| failed       | 2019-01-01 | 2019-01-03 |
| succeeded    | 2019-01-04 | 2019-01-06 |
| failed       | 2019-01-07 | 2019-01-08 |
| succeeded    | 2019-01-09 | 2019-01-09 |

**Explanation:**  
- 2019-01-01 to 2019-01-03: failed  
- 2019-01-04 to 2019-01-06: succeeded  
- 2019-01-07 to 2019-01-08: failed  
- 2019-01-09: succeeded

```sql
-- COMBINE FAILED AND SUCCEEDED DATES WITH STATUS
WITH COMBINED AS (
    SELECT FAIL_DATE AS DT, 'failed' AS ST FROM FAILED WHERE YEAR(FAIL_DATE) = 2019
    UNION ALL
    SELECT SUCCESS_DATE AS DT, 'succeeded' AS ST FROM SUCCEEDED WHERE YEAR(SUCCESS_DATE) = 2019
),

-- ASSIGN RANK TO EACH DATE PER STATUS
RANKED AS (
    SELECT 
        DT, 
        ST,
        RANK() OVER (PARTITION BY ST ORDER BY DT) AS RK
    FROM COMBINED
),

-- CREATE GROUP IDENTIFIER BY SUBTRACTING RANK FROM DATE
GROUPED AS (
    SELECT 
        DT, 
        ST, 
        DATEADD(DAY, -RK, DT) AS GRP
    FROM RANKED
)

-- GROUP BY STATUS AND GROUP IDENTIFIER TO GET CONTIGUOUS RANGES
SELECT 
    ST AS PERIOD_STATE,
    MIN(DT) AS START_DATE,
    MAX(DT) AS END_DATE
FROM GROUPED
GROUP BY ST, GRP
ORDER BY START_DATE;
```

# [1336. Number of Transactions per Visit](https://leetcode.com/problems/number-of-transactions-per-visit/)
#### Schema

Table: Visits

| Column Name  | Type |
|--------------|------|
| user_id      | int  |
| visit_date   | date |

Primary key: (user_id, visit_date)

Table: Transactions

| Column Name      | Type |
|------------------|------|
| user_id          | int  |
| transaction_date | date |

Primary key: (user_id, transaction_date)

#### Description

For each possible number of transactions (including zero), report how many visits had that many transactions. Return the result ordered by transactions_count.

#### Sample Input

Visits table:

| user_id | visit_date  |
|---------|-------------|
| 1       | 2020-01-01  |
| 2       | 2020-01-01  |
| 3       | 2020-01-01  |
| 4       | 2020-01-01  |

Transactions table:

| user_id | transaction_date |
|---------|-----------------|
| 1       | 2020-01-01      |
| 2       | 2020-01-01      |
| 2       | 2020-01-01      |
| 3       | 2020-01-01      |

#### Sample Output

| transactions_count | visits_count |
|--------------------|-------------|
| 0                  | 1           |
| 1                  | 2           |
| 2                  | 1           |

**Explanation:**  
- User 4 had 0 transactions, users 1 and 3 had 1, user 2 had 2.

```sql
-- GENERATE SEQUENCE FROM 0 TO MAX TRANSACTIONS PER VISIT
WITH S AS (
    SELECT 0 AS N
    UNION ALL
    SELECT N + 1
    FROM S
    WHERE N < (
        SELECT MAX(CNT)
        FROM (
            SELECT COUNT(*) AS CNT
            FROM TRANSACTIONS
            GROUP BY USER_ID, TRANSACTION_DATE
        ) AS T
    )
),

-- COUNT TRANSACTIONS PER VISIT (INCLUDE VISITS WITH 0 TRANSACTIONS)
T AS (
    SELECT 
        V.USER_ID, 
        V.VISIT_DATE, 
        ISNULL(T.CNT, 0) AS CNT
    FROM VISITS V
    LEFT JOIN (
        SELECT 
            USER_ID, 
            TRANSACTION_DATE, 
            COUNT(*) AS CNT
        FROM TRANSACTIONS
        GROUP BY USER_ID, TRANSACTION_DATE
    ) T ON V.USER_ID = T.USER_ID AND V.VISIT_DATE = T.TRANSACTION_DATE
)

-- JOIN GENERATED COUNTS WITH ACTUAL VISIT DATA
SELECT 
    S.N AS TRANSACTIONS_COUNT, 
    COUNT(T.USER_ID) AS VISITS_COUNT
FROM S
LEFT JOIN T ON S.N = T.CNT
GROUP BY S.N
ORDER BY S.N;
```

# [1369. Get the Second Most Recent Activity](https://leetcode.com/problems/get-the-second-most-recent-activity/)
```
Table: UserActivity
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| username      | varchar |
| activity      | varchar |
| startDate     | Date    |
| endDate       | Date    |
+---------------+---------+
This table does not contain primary key.
This table contain information about the activity performed of each user in a period of time.
A person with username performed a activity from startDate to endDate.

Write an SQL query to show the second most recent activity of each user.Programming
If the user only has one activity, return that one. 
A user can't perform more than one activity at the same time. Return the result table in any order.
The query result format is in the following example:

UserActivity table:
+------------+--------------+-------------+-------------+
| username   | activity     | startDate   | endDate     |
+------------+--------------+-------------+-------------+
| Alice      | Travel       | 2020-02-12  | 2020-02-20  |
| Alice      | Dancing      | 2020-02-21  | 2020-02-23  |
| Alice      | Travel       | 2020-02-24  | 2020-02-28  |
| Bob        | Travel       | 2020-02-11  | 2020-02-18  |
+------------+--------------+-------------+-------------+

Result table:
+------------+--------------+-------------+-------------+
| username   | activity     | startDate   | endDate     |
+------------+--------------+-------------+-------------+
| Alice      | Dancing      | 2020-02-21  | 2020-02-23  |
| Bob        | Travel       | 2020-02-11  | 2020-02-18  |
+------------+--------------+-------------+-------------+

The most recent activity of Alice is Travel from 2020-02-24 to 2020-02-28, before that she was dancing from 2020-02-21 to 2020-02-23.
Bob only has one record, we just take that one.
```
```sql
SELECT
    USERNAME,
    ACTIVITY,
    STARTDATE,
    ENDDATE
FROM
(
    SELECT
        *,
        -- RANK ACTIVITIES PER USER BY STARTDATE (LATEST FIRST)
        RANK() OVER (
            PARTITION BY USERNAME
            ORDER BY STARTDATE DESC
        ) AS RK,

        -- COUNT TOTAL ACTIVITIES PER USER
        COUNT(USERNAME) OVER (
            PARTITION BY USERNAME
        ) AS CNT
    FROM USERACTIVITY
) AS A
-- GET SECOND LATEST ACTIVITY OR ONLY ACTIVITY IF USER HAS ONE
WHERE A.RK = 2 OR A.CNT = 1;
```

# [1384. Total Sales Amount by Year](https://leetcode.com/problems/total-sales-amount-by-year/)
```
Table: Product
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| product_name  | varchar |
+---------------+---------+
product_id is the primary key for this table.
product_name is the name of the product.
 
Table: Sales
+---------------------+---------+
| Column Name         | Type    |
+---------------------+---------+
| product_id          | int     |
| period_start        | varchar |
| period_end          | date    |
| average_daily_sales | int     |
+---------------------+---------+
product_id is the primary key for this table.
period_start and period_end indicates the start and end date for sales period, both dates are inclusive.
The average_daily_sales column holds the average daily sales amount of the items for the period.

Write an SQL query to report the Total sales amount of each item for each year, with corresponding product name, product_id, product_name and report_year.Programming

Dates of the sales years are between 2018 to 2020. Return the result table ordered by product_id and report_year.

The query result format is in the following example:

Product table:
+------------+--------------+
| product_id | product_name |
+------------+--------------+
| 1          | LC Phone     |
| 2          | LC T-Shirt   |
| 3          | LC Keychain  |
+------------+--------------+

Sales table:
+------------+--------------+-------------+---------------------+
| product_id | period_start | period_end  | average_daily_sales |
+------------+--------------+-------------+---------------------+
| 1          | 2019-01-25   | 2019-02-28  | 100                 |
| 2          | 2018-12-01   | 2020-01-01  | 10                  |
| 3          | 2019-12-01   | 2020-01-31  | 1                   |
+------------+--------------+-------------+---------------------+

Result table:
+------------+--------------+-------------+--------------+
| product_id | product_name | report_year | total_amount |
+------------+--------------+-------------+--------------+
| 1          | LC Phone     |    2019     | 3500         |
| 2          | LC T-Shirt   |    2018     | 310          |
| 2          | LC T-Shirt   |    2019     | 3650         |
| 2          | LC T-Shirt   |    2020     | 10           |
| 3          | LC Keychain  |    2019     | 31           |
| 3          | LC Keychain  |    2020     | 31           |
+------------+--------------+-------------+--------------+
LC Phone was sold for the period of 2019-01-25 to 2019-02-28, and there are 35 days for this period. Total amount 35*100 = 3500. 
LC T-shirt was sold for the period of 2018-12-01 to 2020-01-01, and there are 31, 365, 1 days for years 2018, 2019 and 2020 respectively.
LC Keychain was sold for the period of 2019-12-01 to 2020-01-31, and there are 31, 31 days for years 2019 and 2020 respectively.
```
```sql
-- DEFINE CALENDAR YEARS WITH START AND END DATES
WITH CALENDAR AS (
    SELECT '2018' AS YEAR, CAST('2018-01-01' AS DATE) AS START_DATE, CAST('2018-12-31' AS DATE) AS END_DATE
    UNION ALL
    SELECT '2019', '2019-01-01', '2019-12-31'
    UNION ALL
    SELECT '2020', '2020-01-01', '2020-12-31'
)

-- CALCULATE TOTAL SALES PER PRODUCT PER YEAR BASED ON OVERLAPPING DAYS
SELECT
    P.PRODUCT_ID,
    P.PRODUCT_NAME,
    C.YEAR AS REPORT_YEAR,

    -- OVERLAPPING DAYS × AVERAGE DAILY SALES
    (DATEDIFF(
        DAY,
        CASE WHEN S.PERIOD_START > C.START_DATE THEN S.PERIOD_START ELSE C.START_DATE END,
        CASE WHEN S.PERIOD_END < C.END_DATE THEN S.PERIOD_END ELSE C.END_DATE END
    ) + 1) * S.AVERAGE_DAILY_SALES AS TOTAL_AMOUNT

FROM SALES S
INNER JOIN CALENDAR C
    ON YEAR(S.PERIOD_START) <= CAST(C.YEAR AS INT)
    AND YEAR(S.PERIOD_END) >= CAST(C.YEAR AS INT)
INNER JOIN PRODUCT P
    ON P.PRODUCT_ID = S.PRODUCT_ID
ORDER BY P.PRODUCT_ID, C.YEAR;
```

# [1412. Find the Quiet Students in All Exams](https://leetcode.com/problems/find-the-quiet-students-in-all-exams/)
```
Table: Student
+---------------------+---------+
| Column Name         | Type    |
+---------------------+---------+
| student_id          | int     |
| student_name        | varchar |
+---------------------+---------+
student_id is the primary key for this table.
student_name is the name of the student.
 
Table: Exam
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| exam_id       | int     |
| student_id    | int     |
| score         | int     |
+---------------+---------+
(exam_id, student_id) is the primary key for this table.
Student with student_id got score points in exam with id exam_id.
 
A "quite" student is the one who took at least one exam and didn't score neither the high score nor the low score.
Write an SQL query to report the students (student_id, student_name) being "quiet" in ALL exams.Programming
Don't return the student who has never taken any exam. Return the result table ordered by student_id.
The query result format is in the following example.

Student table:
+-------------+---------------+
| student_id  | student_name  |
+-------------+---------------+
| 1           | Daniel        |
| 2           | Jade          |
| 3           | Stella        |
| 4           | Jonathan      |
| 5           | Will          |
+-------------+---------------+

Exam table:
+------------+--------------+-----------+
| exam_id    | student_id   | score     |
+------------+--------------+-----------+
| 10         |     1        |    70     |
| 10         |     2        |    80     |
| 10         |     3        |    90     |
| 20         |     1        |    80     |
| 30         |     1        |    70     |
| 30         |     3        |    80     |
| 30         |     4        |    90     |
| 40         |     1        |    60     |
| 40         |     2        |    70     |
| 40         |     4        |    80     |
+------------+--------------+-----------+

Result table:
+-------------+---------------+
| student_id  | student_name  |
+-------------+---------------+
| 2           | Jade          |
+-------------+---------------+

For exam 1: Student 1 and 3 hold the lowest and high score respectively.
For exam 2: Student 1 hold both highest and lowest score.
For exam 3 and 4: Studnet 1 and 4 hold the lowest and high score respectively.
Student 2 and 5 have never got the highest or lowest in any of the exam.
Since student 5 is not taking any exam, he is excluded from the result.
So, we only return the information of Student 2.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS STUDENT;
DROP TABLE IF EXISTS EXAM;
GO

CREATE TABLE STUDENT (
    STUDENT_ID INT PRIMARY KEY,
    STUDENT_NAME VARCHAR(50) NOT NULL
);

CREATE TABLE EXAM (
    EXAM_ID INT NOT NULL,
    STUDENT_ID INT NOT NULL,
    SCORE INT NOT NULL
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO STUDENT (STUDENT_ID, STUDENT_NAME) VALUES
(1, 'Daniel'),
(2, 'Jade'),
(3, 'Stella'),
(4, 'Jonathan'),
(5, 'Will');
GO

INSERT INTO EXAM (EXAM_ID, STUDENT_ID, SCORE) VALUES
(10, 1, 70),
(10, 2, 80),
(10, 3, 90),
(20, 1, 80),
(30, 1, 70),
(30, 3, 80),
(30, 4, 90),
(40, 1, 60),
(40, 2, 70),
(40, 4, 80);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM STUDENT;
SELECT * FROM EXAM;
/*******************************************************************************
4. SOLUTION: Find the Quiet Students in All Exams
*******************************************************************************/
-- Step 1: Build a CTE that calculates min and max scores per exam
WITH MIN_MAX_SCORE_PER_EXAM AS (
    SELECT 
        S.STUDENT_ID,
        S.STUDENT_NAME,
        EXAM_ID,
        SCORE,
        -- Highest score in this exam
        MAX(SCORE) OVER(PARTITION BY EXAM_ID) AS MAX_PER_SUBJECT,
        -- Lowest score in this exam
        MIN(SCORE) OVER(PARTITION BY EXAM_ID) AS MIN_PER_SUBJECT 
    FROM STUDENT S 
    INNER JOIN EXAM E ON S.STUDENT_ID = E.STUDENT_ID
),

-- Step 2: Build another CTE to count exams taken and how many times
-- the student was "silent" (not min, not max)
SILENT_STUDENT AS (
    SELECT 
        STUDENT_ID,
        STUDENT_NAME,
        -- Number of distinct exams this student appeared in
        COUNT(DISTINCT EXAM_ID) AS EXAM_TAKEN,
        -- Count how many times their score was strictly between min and max
        SUM(
            CASE 
                WHEN MIN_PER_SUBJECT < SCORE AND SCORE < MAX_PER_SUBJECT 
                THEN 1 ELSE 0 
            END
        ) AS SILENT_STUDENT
    FROM MIN_MAX_SCORE_PER_EXAM 
    GROUP BY STUDENT_ID, STUDENT_NAME
)

-- Step 3: Final selection
-- Only return students who were "silent" in ALL exams they took
SELECT STUDENT_ID, STUDENT_NAME 
FROM SILENT_STUDENT 
WHERE EXAM_TAKEN = SILENT_STUDENT;
```

# [1479. Sales by Day of the Week](https://leetcode.com/problems/sales-by-day-of-the-week/)
```
Table: Orders
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| order_id      | int     |
| customer_id   | int     |
| order_date    | date    |
| item_id       | varchar |
| quantity      | int     |
+---------------+---------+
(ordered_id, item_id) is the primary key for this table.
This table contains information of the orders placed.
order_date is the date when item_id was ordered by the customer with id customer_id.
Table: Items
+---------------------+---------+
| Column Name         | Type    |
+---------------------+---------+
| item_id             | varchar |
| item_name           | varchar |
| item_category       | varchar |
+---------------------+---------+
item_id is the primary key for this table.
item_name is the name of the item.
item_category is the category of the item.
 
You are the business owner and would like to obtain a sales report for category items and day of the week

Write an SQL query to report how many units in each category have been ordered on each day of the week.

Return the result table ordered by category.Programming
The query result format is in the following example:
Orders table:
+------------+--------------+-------------+--------------+-------------+
| order_id   | customer_id  | order_date  | item_id      | quantity    |
+------------+--------------+-------------+--------------+-------------+
| 1          | 1            | 2020-06-01  | 1            | 10          |
| 2          | 1            | 2020-06-08  | 2            | 10          |
| 3          | 2            | 2020-06-02  | 1            | 5           |
| 4          | 3            | 2020-06-03  | 3            | 5           |
| 5          | 4            | 2020-06-04  | 4            | 1           |
| 6          | 4            | 2020-06-05  | 5            | 5           |
| 7          | 5            | 2020-06-05  | 1            | 10          |
| 8          | 5            | 2020-06-14  | 4            | 5           |
| 9          | 5            | 2020-06-21  | 3            | 5           |
+------------+--------------+-------------+--------------+-------------+
Items table:
+------------+----------------+---------------+
| item_id    | item_name      | item_category |
+------------+----------------+---------------+
| 1          | LC Alg. Book   | Book          |
| 2          | LC DB. Book    | Book          |
| 3          | LC SmarthPhone | Phone         |
| 4          | LC Phone 2020  | Phone         |
| 5          | LC SmartGlass  | Glasses       |
| 6          | LC T-Shirt XL  | T-Shirt       |
+------------+----------------+---------------+

Result table:
+------------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+
| Category   | Monday    | Tuesday   | Wednesday | Thursday  | Friday    | Saturday  | Sunday    |
+------------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+
| Book       | 20        | 5         | 0         | 0         | 10        | 0         | 0         |
| Glasses    | 0         | 0         | 0         | 0         | 5         | 0         | 0         |
| Phone      | 0         | 0         | 5         | 1         | 0         | 0         | 10        |
| T-Shirt    | 0         | 0         | 0         | 0         | 0         | 0         | 0         |
+------------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+
On Monday (2020-06-01, 2020-06-08) were sold a total of 20 units (10 + 10) in the category Book (ids: 1, 2).
On Tuesday (2020-06-02) were sold a total of 5 units  in the category Book (ids: 1, 2).
On Wednesday (2020-06-03) were sold a total of 5 units in the category Phone (ids: 3, 4).
On Thursday (2020-06-04) were sold a total of 1 unit in the category Phone (ids: 3, 4).
On Friday (2020-06-05) were sold 10 units in the category Book (ids: 1, 2) and 5 units in Glasses (ids: 5).
On Saturday there are no items sold.
On Sunday (2020-06-14, 2020-06-21) were sold a total of 10 units (5 +5) in the category Phone (ids: 3, 4).
There are no sales of T-Shirt.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS ITEMS;
DROP TABLE IF EXISTS ORDERS;
GO

CREATE TABLE ITEMS (
    ITEM_ID INT PRIMARY KEY,
    ITEM_NAME VARCHAR(50),
    ITEM_CATEGORY VARCHAR(20)
);

CREATE TABLE ORDERS (
    ORDER_ID INT PRIMARY KEY,
    CUSTOMER_ID INT,
    ORDER_DATE DATE,
    ITEM_ID INT,
    QUANTITY INT,
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO ITEMS (ITEM_ID, ITEM_NAME, ITEM_CATEGORY) VALUES
(1, 'LC Alg. Book', 'Book'),
(2, 'LC DB. Book', 'Book'),
(3, 'LC SmarthPhone', 'Phone'),
(4, 'LC Phone 2020', 'Phone'),
(5, 'LC SmartGlass', 'Glasses'),
(6, 'LC T-Shirt XL', 'T-Shirt');
GO

INSERT INTO ORDERS (ORDER_ID, CUSTOMER_ID, ORDER_DATE, ITEM_ID, QUANTITY) VALUES
(1, 1, '2020-06-01', 1, 10),
(2, 1, '2020-06-08', 2, 10),
(3, 2, '2020-06-02', 1, 5),
(4, 3, '2020-06-03', 3, 5),
(5, 4, '2020-06-04', 4, 1),
(6, 4, '2020-06-05', 5, 5),
(7, 5, '2020-06-05', 1, 10),
(8, 5, '2020-06-14', 4, 5),
(9, 5, '2020-06-21', 3, 5);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM ITEMS;
SELECT * FROM ORDERS;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
SELECT 
    ITEM_CATEGORY AS CATEGORY,
    SUM(CASE WHEN DATENAME(WEEKDAY, ORDER_DATE) = 'Monday' THEN QUANTITY ELSE 0 END) AS MONDAY,
    SUM(CASE WHEN DATENAME(WEEKDAY, ORDER_DATE) = 'Tuesday' THEN QUANTITY ELSE 0 END) AS TUESDAY,
    SUM(CASE WHEN DATENAME(WEEKDAY, ORDER_DATE) = 'Wednesday' THEN QUANTITY ELSE 0 END) AS WEDNESDAY,
    SUM(CASE WHEN DATENAME(WEEKDAY, ORDER_DATE) = 'Thursday' THEN QUANTITY ELSE 0 END) AS THURSDAY,
    SUM(CASE WHEN DATENAME(WEEKDAY, ORDER_DATE) = 'Friday' THEN QUANTITY ELSE 0 END) AS FRIDAY,
    SUM(CASE WHEN DATENAME(WEEKDAY, ORDER_DATE) = 'Saturday' THEN QUANTITY ELSE 0 END) AS SATURDAY,
    SUM(CASE WHEN DATENAME(WEEKDAY, ORDER_DATE) = 'Sunday' THEN QUANTITY ELSE 0 END) AS SUNDAY
FROM ITEMS I
LEFT JOIN ORDERS O ON O.ITEM_ID = I.ITEM_ID
GROUP BY ITEM_CATEGORY
ORDER BY ITEM_CATEGORY;
```

# [1635. Hopper Company Queries I](https://leetcode.com/problems/hopper-company-queries-i/)
```
Table: Drivers
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| driver_id   | int     |
| join_date   | date    |
+-------------+---------+
driver_id is the primary key for this table.
Each row of this table contains the driver's ID and the date they joined the Hopper company.

Table: Rides
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| ride_id      | int     |
| user_id      | int     |
| requested_at | date    |
+--------------+---------+
ride_id is the primary key for this table.
Each row of this table contains the ID of a ride, the user's ID that requested it, and the day they requested it.
There may be some ride requests in this table that were not accepted.
 
Table: AcceptedRides
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| ride_id       | int     |
| driver_id     | int     |
| ride_distance | int     |
| ride_duration | int     |
+---------------+---------+
ride_id is the primary key for this table.
Each row of this table contains some information about an accepted ride.
It is guaranteed that each accepted ride exists in the Rides table.
 
Write an SQL query to report the following statistics for each month of 2020:

The number of drivers currently with the Hopper company by the end of the month (active_drivers).
The number of accepted rides in that month (accepted_rides).
Return the result table ordered by month in ascending order, where month is the month's number (January is 1, February is 2, etc.).Programming

The query result format is in the following example.
Drivers table:
+-----------+------------+
| driver_id | join_date  |
+-----------+------------+
| 10        | 2019-12-10 |
| 8         | 2020-1-13  |
| 5         | 2020-2-16  |
| 7         | 2020-3-8   |
| 4         | 2020-5-17  |
| 1         | 2020-10-24 |
| 6         | 2021-1-5   |
+-----------+------------+
Rides table:
+---------+---------+--------------+
| ride_id | user_id | requested_at |
+---------+---------+--------------+
| 6       | 75      | 2019-12-9    |
| 1       | 54      | 2020-2-9     |
| 10      | 63      | 2020-3-4     |
| 19      | 39      | 2020-4-6     |
| 3       | 41      | 2020-6-3     |
| 13      | 52      | 2020-6-22    |
| 7       | 69      | 2020-7-16    |
| 17      | 70      | 2020-8-25    |
| 20      | 81      | 2020-11-2    |
| 5       | 57      | 2020-11-9    |
| 2       | 42      | 2020-12-9    |
| 11      | 68      | 2021-1-11    |
| 15      | 32      | 2021-1-17    |
| 12      | 11      | 2021-1-19    |
| 14      | 18      | 2021-1-27    |
+---------+---------+--------------+
AcceptedRides table:
+---------+-----------+---------------+---------------+
| ride_id | driver_id | ride_distance | ride_duration |
+---------+-----------+---------------+---------------+
| 10      | 10        | 63            | 38            |
| 13      | 10        | 73            | 96            |
| 7       | 8         | 100           | 28            |
| 17      | 7         | 119           | 68            |
| 20      | 1         | 121           | 92            |
| 5       | 7         | 42            | 101           |
| 2       | 4         | 6             | 38            |
| 11      | 8         | 37            | 43            |
| 15      | 8         | 108           | 82            |
| 12      | 8         | 38            | 34            |
| 14      | 1         | 90            | 74            |
+---------+-----------+---------------+---------------+
Result table:
+-------+----------------+----------------+
| month | active_drivers | accepted_rides |
+-------+----------------+----------------+
| 1     | 2              | 0              |
| 2     | 3              | 0              |
| 3     | 4              | 1              |
| 4     | 4              | 0              |
| 5     | 5              | 0              |
| 6     | 5              | 1              |
| 7     | 5              | 1              |
| 8     | 5              | 1              |
| 9     | 5              | 0              |
| 10    | 6              | 0              |
| 11    | 6              | 2              |
| 12    | 6              | 1              |
+-------+----------------+----------------+

By the end of January --> two active drivers (10, 8) and no accepted rides.
By the end of February --> three active drivers (10, 8, 5) and no accepted rides.
By the end of March --> four active drivers (10, 8, 5, 7) and one accepted ride (10).
By the end of April --> four active drivers (10, 8, 5, 7) and no accepted rides.
By the end of May --> five active drivers (10, 8, 5, 7, 4) and no accepted rides.
By the end of June --> five active drivers (10, 8, 5, 7, 4) and one accepted ride (13).
By the end of July --> five active drivers (10, 8, 5, 7, 4) and one accepted ride (7).
By the end of August --> five active drivers (10, 8, 5, 7, 4) and one accepted ride (17).
By the end of Septemeber --> five active drivers (10, 8, 5, 7, 4) and no accepted rides.
By the end of October --> six active drivers (10, 8, 5, 7, 4, 1) and no accepted rides.
By the end of November --> six active drivers (10, 8, 5, 7, 4, 1) and two accepted rides (20, 5).
By the end of December --> six active drivers (10, 8, 5, 7, 4, 1) and one accepted ride (2).
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS DRIVERS;
DROP TABLE IF EXISTS RIDES;
DROP TABLE IF EXISTS ACCEPTEDRIDES;
GO

CREATE TABLE DRIVERS (
    DRIVER_ID INT PRIMARY KEY,
    JOIN_DATE DATE
);

CREATE TABLE RIDES (
    RIDE_ID INT PRIMARY KEY,
    USER_ID INT,
    REQUESTED_AT DATE
);

CREATE TABLE ACCEPTEDRIDES (
    RIDE_ID INT,
    DRIVER_ID INT,
    RIDE_DISTANCE INT,
    RIDE_DURATION INT
);

GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO RIDES (RIDE_ID, USER_ID, REQUESTED_AT) VALUES
(6, 75, '2019-12-09'),
(1, 54, '2020-02-09'),
(10, 63, '2020-03-04'),
(19, 39, '2020-04-06'),
(3, 41, '2020-06-03'),
(13, 52, '2020-06-22'),
(7, 69, '2020-07-16'),
(17, 70, '2020-08-25'),
(20, 81, '2020-11-02'),
(5, 57, '2020-11-09'),
(2, 42, '2020-12-09'),
(11, 68, '2021-01-11'),
(15, 32, '2021-01-17'),
(12, 11, '2021-01-19'),
(14, 18, '2021-01-27');
GO

INSERT INTO DRIVERS (DRIVER_ID, JOIN_DATE) VALUES
(10, '2019-12-10'),
(8, '2020-01-13'),
(5, '2020-02-16'),
(7, '2020-03-08'),
(4, '2020-05-17'),
(1, '2020-10-24'),
(6, '2021-01-05');
GO

INSERT INTO ACCEPTEDRIDES (RIDE_ID, DRIVER_ID, RIDE_DISTANCE, RIDE_DURATION) VALUES
(10, 10, 63, 38),
(13, 10, 73, 96),
(7, 8, 100, 28),
(17, 7, 119, 68),
(20, 1, 121, 92),
(5, 7, 42, 101),
(2, 4, 6, 38),
(11, 8, 37, 43),
(15, 8, 108, 82),
(12, 8, 38, 34),
(14, 1, 90, 74);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM ACCEPTEDRIDES;
SELECT * FROM DRIVERS;
SELECT * FROM RIDES;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
-- MONTHLY REPORT FOR 2020 USING CTES
WITH CALENDAR AS (
  SELECT 1 AS MONTH
  UNION ALL
  SELECT MONTH + 1
  FROM CALENDAR
  WHERE MONTH < 12
),
ACTIVEDRIVERS AS (
  SELECT 
    M.MONTH,
    COUNT(*) AS ACTIVE_DRIVERS
  FROM CALENDAR M
  LEFT JOIN DRIVERS D
    ON YEAR(D.JOIN_DATE) < 2020
    OR (YEAR(D.JOIN_DATE) = 2020 AND MONTH(D.JOIN_DATE) <= M.MONTH)
  GROUP BY M.MONTH
),
ACCEPTEDPERMONTH AS (
  SELECT 
    M.MONTH,
    COUNT(*) AS ACCEPTED_RIDES
  FROM CALENDAR M
  INNER JOIN RIDES R
    ON YEAR(R.REQUESTED_AT) = 2020
   AND MONTH(R.REQUESTED_AT) = M.MONTH
  INNER JOIN ACCEPTEDRIDES AR
    ON R.RIDE_ID = AR.RIDE_ID
  GROUP BY M.MONTH
)
SELECT 
  C.MONTH,
  COALESCE(AD.ACTIVE_DRIVERS,0) AS ACTIVE_DRIVERS,
  COALESCE(APM.ACCEPTED_RIDES,0) AS ACCEPTED_RIDES
FROM CALENDAR C
LEFT JOIN ACTIVEDRIVERS AD ON C.MONTH = AD.MONTH
LEFT JOIN ACCEPTEDPERMONTH APM ON C.MONTH = APM.MONTH
ORDER BY C.MONTH;
```

# [1645. Hopper Company Queries II](https://leetcode.com/problems/hopper-company-queries-ii/)
```
Table: Drivers
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| driver_id   | int     |
| join_date   | date    |
+-------------+---------+
driver_id is the primary key for this table.
Each row of this table contains the driver's ID and the date they joined the Hopper company.
 
Table: Rides
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| ride_id      | int     |
| user_id      | int     |
| requested_at | date    |
+--------------+---------+
ride_id is the primary key for this table.
Each row of this table contains the ID of a ride, the user's ID that requested it, and the day they requested it.
There may be some ride requests in this table that were not accepted.
 
Table: AcceptedRides
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| ride_id       | int     |
| driver_id     | int     |
| ride_distance | int     |
| ride_duration | int     |
+---------------+---------+
ride_id is the primary key for this table.
Each row of this table contains some information about an accepted ride.
It is guaranteed that each accepted ride exists in the Rides table.
 
Write an SQL query to report the percentage of working drivers (working_percentage) for each month of 2020 where
```
$$\text{percentage}_{\text{month}} = \frac{\\text{ drivers that accepted at least one ride during the month}}{\\text{ available drivers during the month}} \times 100.0$$
```
Note that if the number of available drivers during a month is zero, we consider the working_percentage to be 0.
Return the result table ordered by month in ascending order, where month is the month's number (January is 1, February is 2, etc.).
Round working_percentage to the nearest 2 decimal places.
The query result format is in the following example.

Drivers table:
+-----------+------------+
| driver_id | join_date  |
+-----------+------------+
| 10        | 2019-12-10 |
| 8         | 2020-1-13  |
| 5         | 2020-2-16  |
| 7         | 2020-3-8   |
| 4         | 2020-5-17  |
| 1         | 2020-10-24 |
| 6         | 2021-1-5   |
+-----------+------------+

Rides table:
+---------+---------+--------------+
| ride_id | user_id | requested_at |
+---------+---------+--------------+
| 6       | 75      | 2019-12-9    |
| 1       | 54      | 2020-2-9     |
| 10      | 63      | 2020-3-4     |
| 19      | 39      | 2020-4-6     |
| 3       | 41      | 2020-6-3     |
| 13      | 52      | 2020-6-22    |
| 7       | 69      | 2020-7-16    |
| 17      | 70      | 2020-8-25    |
| 20      | 81      | 2020-11-2    |
| 5       | 57      | 2020-11-9    |
| 2       | 42      | 2020-12-9    |
| 11      | 68      | 2021-1-11    |
| 15      | 32      | 2021-1-17    |
| 12      | 11      | 2021-1-19    |
| 14      | 18      | 2021-1-27    |
+---------+---------+--------------+

AcceptedRides table:
+---------+-----------+---------------+---------------+
| ride_id | driver_id | ride_distance | ride_duration |
+---------+-----------+---------------+---------------+
| 10      | 10        | 63            | 38            |
| 13      | 10        | 73            | 96            |
| 7       | 8         | 100           | 28            |
| 17      | 7         | 119           | 68            |
| 20      | 1         | 121           | 92            |
| 5       | 7         | 42            | 101           |
| 2       | 4         | 6             | 38            |
| 11      | 8         | 37            | 43            |
| 15      | 8         | 108           | 82            |
| 12      | 8         | 38            | 34            |
| 14      | 1         | 90            | 74            |
+---------+-----------+---------------+---------------+

Result table:
+-------+--------------------+
| month | working_percentage |
+-------+--------------------+
| 1     | 0.00               |
| 2     | 0.00               |
| 3     | 25.00              |
| 4     | 0.00               |
| 5     | 0.00               |
| 6     | 20.00              |
| 7     | 20.00              |
| 8     | 20.00              |
| 9     | 0.00               |
| 10    | 0.00               |
| 11    | 33.33              |
| 12    | 16.67              |
+-------+--------------------+

By the end of January --> two active drivers (10, 8) and no accepted rides. The percentage is 0%.
By the end of February --> three active drivers (10, 8, 5) and no accepted rides. The percentage is 0%.
By the end of March --> four active drivers (10, 8, 5, 7) and one accepted ride by driver (10). The percentage is (1 / 4) * 100 = 25%.
By the end of April --> four active drivers (10, 8, 5, 7) and no accepted rides. The percentage is 0%.
By the end of May --> five active drivers (10, 8, 5, 7, 4) and no accepted rides. The percentage is 0%.
By the end of June --> five active drivers (10, 8, 5, 7, 4) and one accepted ride by driver (10). The percentage is (1 / 5) * 100 = 20%.
By the end of July --> five active drivers (10, 8, 5, 7, 4) and one accepted ride by driver (8). The percentage is (1 / 5) * 100 = 20%.
By the end of August --> five active drivers (10, 8, 5, 7, 4) and one accepted ride by driver (7). The percentage is (1 / 5) * 100 = 20%.
By the end of Septemeber --> five active drivers (10, 8, 5, 7, 4) and no accepted rides. The percentage is 0%.
By the end of October --> six active drivers (10, 8, 5, 7, 4, 1) and no accepted rides. The percentage is 0%.
By the end of November --> six active drivers (10, 8, 5, 7, 4, 1) and two accepted rides by two different drivers (1, 7). The percentage is (2 / 6) * 100 = 33.33%.
By the end of December --> six active drivers (10, 8, 5, 7, 4, 1) and one accepted ride by driver (4). The percentage is (1 / 6) * 100 = 16.67%.
```
```sql
-- SQL Server Recursive CTE
WITH MONTHCTE AS (
    SELECT 1 AS MONTH
    UNION ALL
    SELECT MONTH + 1
    FROM MONTHCTE
    WHERE MONTH < 12
),
-- S: Drivers available for each month
S AS (
    SELECT M.MONTH, D.DRIVER_ID, D.JOIN_DATE
    FROM MONTHCTE AS M
    LEFT JOIN DRIVERS AS D
        ON YEAR(D.JOIN_DATE) < 2020
        OR (YEAR(D.JOIN_DATE) = 2020 AND MONTH(D.JOIN_DATE) <= M.MONTH)
),
-- T: Successful rides in 2020
T AS (
    SELECT AR.DRIVER_ID, R.REQUESTED_AT
    FROM RIDES R
    INNER JOIN ACCEPTEDRIDES AR ON R.RIDE_ID = AR.RIDE_ID
    WHERE YEAR(R.REQUESTED_AT) = 2020
)
SELECT
    S.MONTH,
    ISNULL(
        ROUND(
            -- Use CAST to FLOAT to ensure decimal division
            COUNT(DISTINCT T.DRIVER_ID) * 100.0 / 
            NULLIF(COUNT(DISTINCT S.DRIVER_ID), 0), 
            2
        ),
        0
    ) AS WORKING_PERCENTAGE
FROM S AS S
LEFT JOIN T AS T
    ON S.DRIVER_ID = T.DRIVER_ID
    AND S.JOIN_DATE <= T.REQUESTED_AT
    AND S.MONTH = MONTH(T.REQUESTED_AT)
GROUP BY S.MONTH
ORDER BY S.MONTH;
```

# [1651. Hopper Company Queries III](https://leetcode.com/problems/hopper-company-queries-iii/)
```
Table: Drivers
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| driver_id   | int     |
| join_date   | date    |
+-------------+---------+
driver_id is the primary key for this table.
Each row of this table contains the driver's ID and the date they joined the Hopper company.
 
Table: Rides
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| ride_id      | int     |
| user_id      | int     |
| requested_at | date    |
+--------------+---------+
ride_id is the primary key for this table.
Each row of this table contains the ID of a ride, the user's ID that requested it, and the day they requested it.
There may be some ride requests in this table that were not accepted.
 
Table: AcceptedRides
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| ride_id       | int     |
| driver_id     | int     |
| ride_distance | int     |
| ride_duration | int     |
+---------------+---------+
ride_id is the primary key for this table.
Each row of this table contains some information about an accepted ride.
It is guaranteed that each accepted ride exists in the Rides table.
 
Write an SQL query to compute the average_ride_distance and average_ride_duration of every 3-month window starting from January - March 2020 to October - December 2020. Round average_ride_distance and average_ride_duration to the nearest two decimal places.
The average_ride_distance is calculated by summing up the total ride_distance values from the three months and dividing it by 3. The average_ride_duration is calculated in a similar way.
Return the result table ordered by month in ascending order, where month is the starting month's number (January is 1, February is 2, etc.).
The query result format is in the following example.

Drivers table:
+-----------+------------+
| driver_id | join_date  |
+-----------+------------+
| 10        | 2019-12-10 |
| 8         | 2020-1-13  |
| 5         | 2020-2-16  |
| 7         | 2020-3-8   |
| 4         | 2020-5-17  |
| 1         | 2020-10-24 |
| 6         | 2021-1-5   |
+-----------+------------+

Rides table:
+---------+---------+--------------+
| ride_id | user_id | requested_at |
+---------+---------+--------------+
| 6       | 75      | 2019-12-9    |
| 1       | 54      | 2020-2-9     |
| 10      | 63      | 2020-3-4     |
| 19      | 39      | 2020-4-6     |
| 3       | 41      | 2020-6-3     |
| 13      | 52      | 2020-6-22    |
| 7       | 69      | 2020-7-16    |
| 17      | 70      | 2020-8-25    |
| 20      | 81      | 2020-11-2    |
| 5       | 57      | 2020-11-9    |
| 2       | 42      | 2020-12-9    |
| 11      | 68      | 2021-1-11    |
| 15      | 32      | 2021-1-17    |
| 12      | 11      | 2021-1-19    |
| 14      | 18      | 2021-1-27    |
+---------+---------+--------------+

AcceptedRides table:
+---------+-----------+---------------+---------------+
| ride_id | driver_id | ride_distance | ride_duration |
+---------+-----------+---------------+---------------+
| 10      | 10        | 63            | 38            |
| 13      | 10        | 73            | 96            |
| 7       | 8         | 100           | 28            |
| 17      | 7         | 119           | 68            |
| 20      | 1         | 121           | 92            |
| 5       | 7         | 42            | 101           |
| 2       | 4         | 6             | 38            |
| 11      | 8         | 37            | 43            |
| 15      | 8         | 108           | 82            |
| 12      | 8         | 38            | 34            |
| 14      | 1         | 90            | 74            |
+---------+-----------+---------------+---------------+

Result table:
+-------+-----------------------+-----------------------+
| month | average_ride_distance | average_ride_duration |
+-------+-----------------------+-----------------------+
| 1     | 21.00                 | 12.67                 |
| 2     | 21.00                 | 12.67                 |
| 3     | 21.00                 | 12.67                 |
| 4     | 24.33                 | 32.00                 |
| 5     | 57.67                 | 41.33                 |
| 6     | 97.33                 | 64.00                 |
| 7     | 73.00                 | 32.00                 |
| 8     | 39.67                 | 22.67                 |
| 9     | 54.33                 | 64.33                 |
| 10    | 56.33                 | 77.00                 |
+-------+-----------------------+-----------------------+

By the end of January --> average_ride_distance = (0+0+63)/3=21, average_ride_duration = (0+0+38)/3=12.67
By the end of February --> average_ride_distance = (0+63+0)/3=21, average_ride_duration = (0+38+0)/3=12.67
By the end of March --> average_ride_distance = (63+0+0)/3=21, average_ride_duration = (38+0+0)/3=12.67
By the end of April --> average_ride_distance = (0+0+73)/3=24.33, average_ride_duration = (0+0+96)/3=32.00
By the end of May --> average_ride_distance = (0+73+100)/3=57.67, average_ride_duration = (0+96+28)/3=41.33
By the end of June --> average_ride_distance = (73+100+119)/3=97.33, average_ride_duration = (96+28+68)/3=64.00
By the end of July --> average_ride_distance = (100+119+0)/3=73.00, average_ride_duration = (28+68+0)/3=32.00
By the end of August --> average_ride_distance = (119+0+0)/3=39.67, average_ride_duration = (68+0+0)/3=22.67
By the end of Septemeber --> average_ride_distance = (0+0+163)/3=54.33, average_ride_duration = (0+0+193)/3=64.33
By the end of October --> average_ride_distance = (0+163+6)/3=56.33, average_ride_duration = (0+193+38)/3=77.00
```
```sql
WITH MONTHS AS (
    -- generate months 1..12
    SELECT 1 AS [MONTH]
    UNION ALL
    SELECT [MONTH] + 1
    FROM MONTHS
    WHERE [MONTH] < 12
),
MONTHLYTOTALS AS (
    -- sum accepted rides per month (year 2020); missing months become 0
    SELECT
        M.[MONTH],
        COALESCE(SUM(A.RIDE_DISTANCE), 0) AS RIDE_DISTANCE,
        COALESCE(SUM(A.RIDE_DURATION), 0) AS RIDE_DURATION
    FROM MONTHS AS M
    LEFT JOIN RIDES AS R
        ON DATEPART(YEAR, R.REQUESTED_AT) = 2020
        AND DATEPART(MONTH, R.REQUESTED_AT) = M.[MONTH]
    LEFT JOIN ACCEPTEDRIDES AS A
        ON R.RIDE_ID = A.RIDE_ID
    GROUP BY M.[MONTH]
)
SELECT
    [MONTH],
    -- forward-looking 3-month average: current month + next 2 months
    ROUND(
      AVG(CAST(RIDE_DISTANCE AS DECIMAL(18,6))) 
        OVER (ORDER BY [MONTH] ROWS BETWEEN CURRENT ROW AND 2 FOLLOWING),
      2
    ) AS AVERAGE_RIDE_DISTANCE,
    ROUND(
      AVG(CAST(RIDE_DURATION AS DECIMAL(18,6))) 
        OVER (ORDER BY [MONTH] ROWS BETWEEN CURRENT ROW AND 2 FOLLOWING),
      2
    ) AS AVERAGE_RIDE_DURATION
FROM MONTHLYTOTALS
ORDER BY [MONTH]
OPTION (MAXRECURSION 0);
```

# [1767. Find the Subtasks That Did Not Execute](https://leetcode.com/problems/find-the-subtasks-that-did-not-execute/)
```
Table: Tasks
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| task_id        | int     |
| subtasks_count | int     |
+----------------+---------+
task_id is the primary key for this table.
Each row in this table indicates that task_id was divided into subtasks_count subtasks labelled from 1 to subtasks_count.
It is guaranteed that 2 <= subtasks_count <= 20.

Table: Executed
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| task_id       | int     |
| subtask_id    | int     |
+---------------+---------+
(task_id, subtask_id) is the primary key for this table.
Each row in this table indicates that for the task task_id, the subtask with ID subtask_id was executed successfully.
It is guaranteed that subtask_id <= subtasks_count for each task_id.
Write an SQL query to report the IDs of the missing subtasks for each task_id.

Return the result table in any order.

The query result format is in the following example:Programming

Tasks table:
+---------+----------------+
| task_id | subtasks_count |
+---------+----------------+
| 1       | 3              |
| 2       | 2              |
| 3       | 4              |
+---------+----------------+

Executed table:
+---------+------------+
| task_id | subtask_id |
+---------+------------+
| 1       | 2          |
| 3       | 1          |
| 3       | 2          |
| 3       | 3          |
| 3       | 4          |
+---------+------------+

Result table:
+---------+------------+
| task_id | subtask_id |
+---------+------------+
| 1       | 1          |
| 1       | 3          |
| 2       | 1          |
| 2       | 2          |
+---------+------------+
Task 1 was divided into 3 subtasks (1, 2, 3). Only subtask 2 was executed successfully, so we include (1, 1) and (1, 3) in the answer.
Task 2 was divided into 2 subtasks (1, 2). No subtask was executed successfully, so we include (2, 1) and (2, 2) in the answer.
Task 3 was divided into 4 subtasks (1, 2, 3, 4). All of the subtasks were executed successfully.
```
```sql
-- GENERATE ALL POSSIBLE (TASK_ID, SUBTASK_ID) COMBINATIONS
WITH TASKTOSUBTASK AS (
    SELECT TASK_ID, 1 AS SUBTASK_ID
    FROM TASKS
    UNION ALL
    SELECT TASK_ID, SUBTASK_ID + 1
    FROM TASKTOSUBTASK
    JOIN TASKS ON TASKTOSUBTASK.TASK_ID = TASKS.TASK_ID
    WHERE SUBTASK_ID + 1 <= SUBTASKS_COUNT
)
-- GET SUBTASKS THAT WERE NOT EXECUTED
SELECT TASK_ID, SUBTASK_ID
FROM TASKTOSUBTASK
EXCEPT
SELECT TASK_ID, SUBTASK_ID FROM EXECUTED;
```

# [185. Department Top Three Salaries](https://leetcode.com/problems/department-top-three-salaries/)
```
Table: Employee
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |
+--------------+---------+
id is the primary key (column with unique values) for this table.
departmentId is a foreign key (reference column) of the ID from the Department table.
Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.
 

Table: Department
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
id is the primary key (column with unique values) for this table.
Each row of this table indicates the ID of a department and its name.

A company's executives are interested in seeing who earns the most money in each of the company's departments. A high earner in a department is an employee who has a salary in the top three unique salaries for that department.
Write a solution to find the employees who are high earners in each of the departments.
Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
Employee table:
+----+-------+--------+--------------+
| id | name  | salary | departmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |
+----+-------+--------+--------------+
Department table:
+----+-------+
| id | name  |
+----+-------+
| 1  | IT    |
| 2  | Sales |
+----+-------+
Output: 
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Joe      | 85000  |
| IT         | Randy    | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
Explanation: 
In the IT department:
- Max earns the highest unique salary
- Both Randy and Joe earn the second-highest unique salary
- Will earns the third-highest unique salary

In the Sales department:
- Henry earns the highest salary
- Sam earns the second-highest salary
- There is no third-highest salary as there are only two employees

Constraints:
There are no employees with the exact same name, salary and department
```
```sql
SELECT DEPARTMENT, EMPLOYEE, SALARY
FROM (
    SELECT 
        D.NAME AS DEPARTMENT,
        E.NAME AS EMPLOYEE,
        E.SALARY AS SALARY,
        -- RANK EMPLOYEES BY SALARY WITHIN EACH DEPARTMENT
        DENSE_RANK() OVER (
            PARTITION BY D.NAME
            ORDER BY E.SALARY DESC
        ) AS RK
    FROM EMPLOYEE E
    JOIN DEPARTMENT D ON E.DEPARTMENTID = D.ID
) AS TAB
-- GET TOP 3 HIGHEST-PAID EMPLOYEES PER DEPARTMENT
WHERE RK <= 3;
```

# [1892. Page Recommendations II](https://leetcode.com/problems/page-recommendations-ii/)
```
Table: Friendship
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user1_id      | int     |
| user2_id      | int     |
+---------------+---------+
(user1_id, user2_id) is the primary key for this table.
Each row of this table indicates that the users user1_id and user2_id are friends.

Table: Likes
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| page_id     | int     |
+-------------+---------+
(user_id, page_id) is the primary key for this table.
Each row of this table indicates that user_id likes page_id.
You are implementing a page recommendation system for a social media website. Your system will recommend a page to user_id if the page is liked by at least one friend of user_id and is not liked by user_id.

Write an SQL query to find all the possible page recommendations for every user. Each recommendation should appear as a row in the result table with these columns:Programming

user_id: The ID of the user that your system is making the recommendation to.
page_id: The ID of the page that will be recommended to user_id.
friends_likes: The number of the friends of user_id that like page_id.
Return result table in any order.

The query result format is in the following example:

Friendship table:
+----------+----------+
| user1_id | user2_id |
+----------+----------+
| 1        | 2        |
| 1        | 3        |
| 1        | 4        |
| 2        | 3        |
| 2        | 4        |
| 2        | 5        |
| 6        | 1        |
+----------+----------+
Likes table:
+---------+---------+
| user_id | page_id |
+---------+---------+
| 1       | 88      |
| 2       | 23      |
| 3       | 24      |
| 4       | 56      |
| 5       | 11      |
| 6       | 33      |
| 2       | 77      |
| 3       | 77      |
| 6       | 88      |
+---------+---------+

Result table:
+---------+---------+---------------+
| user_id | page_id | friends_likes |
+---------+---------+---------------+
| 1       | 77      | 2             |
| 1       | 23      | 1             |
| 1       | 24      | 1             |
| 1       | 56      | 1             |
| 1       | 33      | 1             |
| 2       | 24      | 1             |
| 2       | 56      | 1             |
| 2       | 11      | 1             |
| 2       | 88      | 1             |
| 3       | 88      | 1             |
| 3       | 23      | 1             |
| 4       | 88      | 1             |
| 4       | 77      | 1             |
| 4       | 23      | 1             |
| 5       | 77      | 1             |
| 5       | 23      | 1             |
+---------+---------+---------------+
Take user 1 as an example:
  - User 1 is friends with users 2, 3, 4, and 6.
  - Recommended pages are 23 (user 2 liked it), 24 (user 3 liked it), 56 (user 3 liked it), 33 (user 6 liked it), and 77 (user 2 and user 3 liked it).
  - Note that page 88 is not recommended because user 1 already liked it.

Another example is user 6:
  - User 6 is friends with user 1.
  - User 1 only liked page 88, but user 6 already liked it. Hence, user 6 has no recommendations.

You can recommend pages for users 2, 3, 4, and 5 using a similar process.
```
```sql
WITH FRIENDPAIRS AS (
    SELECT USER1_ID, USER2_ID FROM FRIENDSHIP
    UNION
    SELECT USER2_ID, USER1_ID FROM FRIENDSHIP
)
SELECT
    F.USER1_ID AS USER_ID,
    L.PAGE_ID,
    COUNT(*) AS FRIENDS_LIKES
FROM FRIENDPAIRS AS F
INNER JOIN LIKES AS L
    ON F.USER2_ID = L.USER_ID
WHERE NOT EXISTS (
    SELECT 1
    FROM LIKES AS LX
    WHERE LX.USER_ID = F.USER1_ID
      AND LX.PAGE_ID = L.PAGE_ID
)
GROUP BY F.USER1_ID, L.PAGE_ID;
```

# [1917. Leetcodify Friends Recommendations](https://leetcode.com/problems/leetcodify-friends-recommendations/)
```
Table: Listens
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| song_id     | int     |
| day         | date    |
+-------------+---------+
There is no primary key for this table. It may contain duplicates.
Each row of this table indicates that the user user_id listened to the song song_id on the day day.
Table: Friendship
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user1_id      | int     |
| user2_id      | int     |
+---------------+---------+
(user1_id, user2_id) is the primary key for this table.
Each row of this table indicates that the users user1_id and user2_id are friends.
Note that user1_id < user2_id.

Write an SQL query to recommend friends to Leetcodify users. We recommend user x to user y if:
Users x and y are not friends, and
Users x and y listened to the same three or more different songs on the same day.

Note that friend recommendations are unidirectional, meaning if user x and user y should be recommended to each other, the result table should have both user x recommended to user y and user y recommended to user x. Also, note that the result table should not contain duplicates (i.e., user y should not be recommended to user x multiple times.).Programming
Return the result table in any order.
The query result format is in the following example:

Listens table:
+---------+---------+------------+
| user_id | song_id | day        |
+---------+---------+------------+
| 1       | 10      | 2021-03-15 |
| 1       | 11      | 2021-03-15 |
| 1       | 12      | 2021-03-15 |
| 2       | 10      | 2021-03-15 |
| 2       | 11      | 2021-03-15 |
| 2       | 12      | 2021-03-15 |
| 3       | 10      | 2021-03-15 |
| 3       | 11      | 2021-03-15 |
| 3       | 12      | 2021-03-15 |
| 4       | 10      | 2021-03-15 |
| 4       | 11      | 2021-03-15 |
| 4       | 13      | 2021-03-15 |
| 5       | 10      | 2021-03-16 |
| 5       | 11      | 2021-03-16 |
| 5       | 12      | 2021-03-16 |
+---------+---------+------------+
Friendship table:
+----------+----------+
| user1_id | user2_id |
+----------+----------+
| 1        | 2        |
+----------+----------+
Result table:
+---------+----------------+
| user_id | recommended_id |
+---------+----------------+
| 1       | 3              |
| 2       | 3              |
| 3       | 1              |
| 3       | 2              |
+---------+----------------+
Users 1 and 2 listened to songs 10, 11, and 12 on the same day, but they are already friends.
Users 1 and 3 listened to songs 10, 11, and 12 on the same day. Since they are not friends, we recommend them to each other.
Users 1 and 4 did not listen to the same three songs.
Users 1 and 5 listened to songs 10, 11, and 12, but on different days.

Similarly, we can see that users 2 and 3 listened to songs 10, 11, and 12 on the same day and are not friends, so we recommend them to each other.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS LISTENS;
DROP TABLE IF EXISTS FRIENDSHIP;
GO

CREATE TABLE LISTENS (
    USER_ID INT,
    SONG_ID INT,
    DAY DATE
);
GO

CREATE TABLE FRIENDSHIP (
    USER1_ID INT,
    USER2_ID INT
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO FRIENDSHIP (USER1_ID, USER2_ID) VALUES
(1, 2)
GO

INSERT INTO LISTENS (USER_ID, SONG_ID, DAY) VALUES
(1, 10, '2021-03-15'),
(1, 11, '2021-03-15'),
(1, 12, '2021-03-15'),
(2, 10, '2021-03-15'),
(2, 11, '2021-03-15'),
(2, 12, '2021-03-15'),
(3, 10, '2021-03-15'),
(3, 11, '2021-03-15'),
(3, 12, '2021-03-15'),
(4, 10, '2021-03-15'),
(4, 11, '2021-03-15'),
(4, 13, '2021-03-15'),
(5, 10, '2021-03-16'),
(5, 11, '2021-03-16'),
(5, 12, '2021-03-16');
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM LISTENS;
SELECT * FROM FRIENDSHIP;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
-- Create a symmetric friendship table (both directions)
WITH FRIENDS_PAIR AS (
    SELECT USER1_ID, USER2_ID 
    FROM FRIENDSHIP
    UNION
    SELECT USER2_ID, USER1_ID 
    FROM FRIENDSHIP
)

SELECT 
    L1.USER_ID AS USER_ID, 
    L2.USER_ID AS RECOMMENDED_ID
FROM LISTENS L1
-- Join LISTENS table to itself to find users listening to the same song on the same day
LEFT JOIN LISTENS L2 
    ON L1.USER_ID != L2.USER_ID
    AND L1.DAY = L2.DAY
    AND L1.SONG_ID = L2.SONG_ID

-- Exclude pairs that are already friends
LEFT JOIN FRIENDS_PAIR FP 
    ON FP.USER1_ID = L1.USER_ID 
    AND FP.USER2_ID = L2.USER_ID 

WHERE FP.USER1_ID IS NULL 
  AND FP.USER2_ID IS NULL   -- ensures they are not friends

-- Group by user pairs
GROUP BY L1.USER_ID, L2.USER_ID

-- Keep only pairs who share at least 3 distinct songs listened to on the same day
HAVING COUNT(DISTINCT L2.SONG_ID) >= 3;
```
# [1919. Leetcodify Similar Friends](https://leetcode.com/problems/leetcodify-similar-friends/)
```
Table: Listens
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| song_id     | int     |
| day         | date    |
+-------------+---------+
There is no primary key for this table. It may contain duplicates.
Each row of this table indicates that the user user_id listened to the song song_id on the day day.
Table: Friendship
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user1_id      | int     |
| user2_id      | int     |
+---------------+---------+
(user1_id, user2_id) is the primary key for this table.
Each row of this table indicates that the users user1_id and user2_id are friends.
Note that user1_id < user2_id.
Write an SQL query to report the similar friends of Leetcodify users. A user x and user y are similar friends if:Programming

Users x and y are friends, and
Users x and y listened to the same three or more different songs on the same day.
Return the result table in any order. Note that you must return the similar pairs of friends the same way they were represented in the input (i.e., always user1_id < user2_id).

The query result format is in the following example:
Listens table:
+---------+---------+------------+
| user_id | song_id | day        |
+---------+---------+------------+
| 1       | 10      | 2021-03-15 |
| 1       | 11      | 2021-03-15 |
| 1       | 12      | 2021-03-15 |
| 2       | 10      | 2021-03-15 |
| 2       | 11      | 2021-03-15 |
| 2       | 12      | 2021-03-15 |
| 3       | 10      | 2021-03-15 |
| 3       | 11      | 2021-03-15 |
| 3       | 12      | 2021-03-15 |
| 4       | 10      | 2021-03-15 |
| 4       | 11      | 2021-03-15 |
| 4       | 13      | 2021-03-15 |
| 5       | 10      | 2021-03-16 |
| 5       | 11      | 2021-03-16 |
| 5       | 12      | 2021-03-16 |
+---------+---------+------------+
Friendship table:
+----------+----------+
| user1_id | user2_id |
+----------+----------+
| 1        | 2        |
| 2        | 4        |
| 2        | 5        |
+----------+----------+

Result table:
+----------+----------+
| user1_id | user2_id |
+----------+----------+
| 1        | 2        |
+----------+----------+
Users 1 and 2 are friends, and they listened to songs 10, 11, and 12 on the same day. They are similar friends.
Users 1 and 3 listened to songs 10, 11, and 12 on the same day, but they are not friends.
Users 2 and 4 are friends, but they did not listen to the same three different songs.
Users 2 and 5 are friends and listened to songs 10, 11, and 12, but they did not listen to them on the same day.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS LISTENS;
DROP TABLE IF EXISTS FRIENDSHIP;
GO

CREATE TABLE LISTENS (
    USER_ID INT,
    SONG_ID INT,
    DAY DATE
);;
GO

CREATE TABLE FRIENDSHIP (
    USER1_ID INT,
    USER2_ID INT
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO FRIENDSHIP (USER1_ID, USER2_ID) VALUES
(1, 2),
(2, 4),
(2, 5);
GO

INSERT INTO LISTENS (USER_ID, SONG_ID, DAY) VALUES
(1, 10, '2021-03-15'),
(1, 11, '2021-03-15'),
(1, 12, '2021-03-15'),
(2, 10, '2021-03-15'),
(2, 11, '2021-03-15'),
(2, 12, '2021-03-15'),
(3, 10, '2021-03-15'),
(3, 11, '2021-03-15'),
(3, 12, '2021-03-15'),
(4, 10, '2021-03-15'),
(4, 11, '2021-03-15'),
(4, 13, '2021-03-15'),
(5, 10, '2021-03-16'),
(5, 11, '2021-03-16'),
(5, 12, '2021-03-16');
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM LISTENS;
SELECT * FROM FRIENDSHIP;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
WITH FRIENDS_PAIR AS (
SELECT USER1_ID,USER2_ID FROM FRIENDSHIP
UNION
SELECT USER2_ID,USER1_ID FROM FRIENDSHIP
),
FINAL_RESULT AS (
SELECT USER1_ID,USER2_ID FROM FRIENDS_PAIR FP 
INNER JOIN LISTENS L1 ON FP.USER1_ID = L1.USER_ID
INNER JOIN LISTENS L2 ON FP.USER2_ID = L2.USER_ID AND L1.DAY = L2.DAY AND L1.SONG_ID = L2.SONG_ID AND L1.USER_ID <> L2.USER_ID
GROUP BY USER1_ID,USER2_ID HAVING COUNT(DISTINCT L2.SONG_ID) >= 3
)
SELECT DISTINCT LEAST(USER1_ID, USER2_ID) AS USER1_ID, GREATEST(USER1_ID, USER2_ID) AS USER2_ID FROM FINAL_RESULT
```
# [2004. The Number of Seniors and Juniors to Join the Company](https://leetcode.com/problems/the-number-of-seniors-and-juniors-to-join-the-company/)
```
Table: Candidates
+-------------+------+
| Column Name | Type |
+-------------+------+
| employee_id | int  |
| experience  | enum |
| salary      | int  |
+-------------+------+
employee_id is the primary key column for this table.
experience is an enum with one of the values ('Senior', 'Junior').
Each row of this table indicates the id of a candidate, their monthly salary, and their experience.

A company wants to hire new employees. The budget of the company for the salaries is $70000. The company's criteria for hiring are:

Hiring the largest number of seniors.
After hiring the maximum number of seniors, use the remaining budget to hire the largest number of juniors.
Write an SQL query to find the number of seniors and juniors hired under the mentioned criteria.Programming
Return the result table in any order.
The query result format is in the following example.

Example 1:
Input: 
Candidates table:
+-------------+------------+--------+
| employee_id | experience | salary |
+-------------+------------+--------+
| 1           | Junior     | 10000  |
| 9           | Junior     | 10000  |
| 2           | Senior     | 20000  |
| 11          | Senior     | 20000  |
| 13          | Senior     | 50000  |
| 4           | Junior     | 40000  |
+-------------+------------+--------+
Output: 
+------------+---------------------+
| experience | accepted_candidates |
+------------+---------------------+
| Senior     | 2                   |
| Junior     | 2                   |
+------------+---------------------+
Explanation: 
We can hire 2 seniors with IDs (2, 11). Since the budget is $70000 and the sum of their salaries is $40000, we still have $30000 but they are not enough to hire the senior candidate with ID 13.
We can hire 2 juniors with IDs (1, 9). Since the remaining budget is $30000 and the sum of their salaries is $20000, we still have $10000 but they are not enough to hire the junior candidate with ID 4.
Example 2:
Input: 
Candidates table:
+-------------+------------+--------+
| employee_id | experience | salary |
+-------------+------------+--------+
| 1           | Junior     | 10000  |
| 9           | Junior     | 10000  |
| 2           | Senior     | 80000  |
| 11          | Senior     | 80000  |
| 13          | Senior     | 80000  |
| 4           | Junior     | 40000  |
+-------------+------------+--------+
Output: 
+------------+---------------------+
| experience | accepted_candidates |
+------------+---------------------+
| Senior     | 0                   |
| Junior     | 3                   |
+------------+---------------------+
Explanation: 
We cannot hire any seniors with the current budget as we need at least $80000 to hire one senior.
We can hire all three juniors with the remaining budget.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS CANDIDATES;
GO

CREATE TABLE CANDIDATES (
    EMPLOYEE_ID INT PRIMARY KEY,
    EXPERIENCE VARCHAR(20),
    SALARY INT
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO CANDIDATES (EMPLOYEE_ID, EXPERIENCE, SALARY) VALUES
(1, 'JUNIOR', 10000),
(9, 'JUNIOR', 10000),
(2, 'SENIOR', 20000),
(11, 'SENIOR', 20000),
(13, 'SENIOR', 50000),
(4, 'JUNIOR', 40000);

--- Second Example
INSERT INTO CANDIDATES (EMPLOYEE_ID, EXPERIENCE, SALARY) VALUES
(1, 'JUNIOR', 10000),
(9, 'JUNIOR', 10000),
(2, 'SENIOR', 80000),
(11, 'SENIOR', 80000),
(13, 'SENIOR', 80000),
(4, 'JUNIOR', 40000);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM CANDIDATES;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
WITH ACCUMULATEDCANDIDATES AS (
  SELECT
    EMPLOYEE_ID,
    EXPERIENCE,
    ROW_NUMBER() OVER (
      PARTITION BY EXPERIENCE
      ORDER BY SALARY, EMPLOYEE_ID
    ) AS CANDIDATE_COUNT, -- Rank candidates by salary within experience level
    SUM(SALARY) OVER (
      PARTITION BY EXPERIENCE
      ORDER BY SALARY, EMPLOYEE_ID
    ) AS ACCUMULATED_SALARY -- Running total of salary within experience level
  FROM CANDIDATES
),
MAXHIREDSENIORS AS (
  SELECT
    ISNULL(MAX(CANDIDATE_COUNT), 0) AS ACCEPTED_CANDIDATES, -- Max seniors hired under budget
    ISNULL(MAX(ACCUMULATED_SALARY), 0) AS ACCUMULATED_SALARY -- Total salary of hired seniors
  FROM ACCUMULATEDCANDIDATES
  WHERE EXPERIENCE = 'SENIOR' AND ACCUMULATED_SALARY < 70000
)
SELECT
  'SENIOR' AS EXPERIENCE,
  ACCEPTED_CANDIDATES
FROM MAXHIREDSENIORS
UNION ALL
SELECT
  'JUNIOR' AS EXPERIENCE,
  COUNT(*) AS ACCEPTED_CANDIDATES
FROM ACCUMULATEDCANDIDATES AS JUNIORS
WHERE EXPERIENCE = 'JUNIOR'
  AND JUNIORS.ACCUMULATED_SALARY < (
    SELECT 70000 - ACCUMULATED_SALARY FROM MAXHIREDSENIORS
  ); -- Hire juniors within remaining budget
```

# [2010. The Number of Seniors and Juniors to Join the Company II](https://leetcode.com/problems/the-number-of-seniors-and-juniors-to-join-the-company-ii/)
```sql
WITH ACCUMULATEDCANDIDATES AS (
  SELECT
    EMPLOYEE_ID,
    EXPERIENCE,
    ROW_NUMBER() OVER (
      PARTITION BY EXPERIENCE
      ORDER BY SALARY, EMPLOYEE_ID
    ) AS CANDIDATE_COUNT, -- Rank candidates by salary within experience
    SUM(SALARY) OVER (
      PARTITION BY EXPERIENCE
      ORDER BY SALARY, EMPLOYEE_ID
    ) AS ACCUMULATED_SALARY -- Running total of salary
  FROM CANDIDATES
),
HIREDSENIORS AS (
  SELECT
    EMPLOYEE_ID,
    ACCUMULATED_SALARY
  FROM ACCUMULATEDCANDIDATES
  WHERE EXPERIENCE = 'Senior' AND ACCUMULATED_SALARY < 70000
)
SELECT EMPLOYEE_ID
FROM HIREDSENIORS
UNION ALL
SELECT EMPLOYEE_ID
FROM ACCUMULATEDCANDIDATES AS JUNIORS
WHERE EXPERIENCE = 'Junior'
  AND JUNIORS.ACCUMULATED_SALARY < (
    SELECT 70000 - ISNULL(MAX(ACCUMULATED_SALARY), 0)
    FROM ACCUMULATEDCANDIDATES
    WHERE EXPERIENCE = 'SENIOR' AND ACCUMULATED_SALARY < 70000
  ); -- Hire juniors within remaining budget
```

# [2118. Build the Equation](https://leetcode.com/problems/build-the-equation/)
### Table: Terms

| Column Name | Type |
|-------------|------|
| `power`     | int  |
| `factor`    | int  |

- `power` is the **primary key** for this table.
- Each row represents one term of a polynomial equation.
- `power` is an integer in the range **[0, 100]**.
- `factor` is a non-zero integer in the range **[-100, 100]**.

You have a powerful program that can solve any equation of one variable. To use it, the equation must be formatted as follows:

- The **left-hand side (LHS)** should contain all the terms.
- The **right-hand side (RHS)** should be `= 0`.
- Each term in the LHS must follow the format:  
  `"<sign><fact>X^<pow>"` where:
  - `<sign>` is either `+` or `-`
  - `<fact>` is the **absolute value** of the factor
  - `<pow>` is the **power** value

- If `power = 1`, omit `^<pow>` → e.g., `+3X`
- If `power = 0`, omit both `X` and `^<pow>` → e.g., `-3`
- Terms must be ordered by **descending power**
- The final equation must end with `=0`

### Example 1

**Input:**

```text
Terms table:
+-------+--------+
| power | factor |
+-------+--------+
|   2   |   1    |
|   1   |  -4    |
|   0   |   2    |
+-------+--------+
```

**Output:**

```text
+--------------+
| equation     |
+--------------+
| +1X^2-4X+2=0 |
+--------------+
```

### Example 2

**Input:**

```text
Terms table:
+-------+--------+
| power | factor |
+-------+--------+
|   4   |  -4    |
|   2   |   1    |
|   1   |  -1    |
+-------+--------+
```

**Output:**

```text
+-----------------+
| equation        |
+-----------------+
| -4X^4+1X^2-1X=0 |
+-----------------+
```

```sql
SELECT 
  STRING_AGG(
    CASE 
      WHEN FACTOR > 0 THEN '+' ELSE ''
    END +
    CAST(FACTOR AS VARCHAR) +
    CASE 
      WHEN POWER = 0 THEN ''
      ELSE 'X'
    END +
    CASE 
      WHEN POWER IN (0, 1) THEN ''
      ELSE '^' + CAST(POWER AS VARCHAR)
    END,
    ''
  ) WITHIN GROUP (ORDER BY POWER DESC) + '=0' AS EQUATION
FROM TERMS;
```

# [2153. The Number of Passengers in Each Bus II](https://leetcode.com/problems/the-number-of-passengers-in-each-bus-ii/)
```
Table: Buses
+--------------+------+
| Column Name  | Type |
+--------------+------+
| bus_id       | int  |
| arrival_time | int  |
| capacity     | int  |
+--------------+------+
bus_id is the primary key column for this table.
Each row of this table contains information about the arrival time of a bus at the LeetCode station and its capacity (the number of empty seats it has).
No two buses will arrive at the same time and all bus capacities will be positive integers.
 
Table: Passengers
+--------------+------+
| Column Name  | Type |
+--------------+------+
| passenger_id | int  |
| arrival_time | int  |
+--------------+------+
passenger_id is the primary key column for this table.
Each row of this table contains information about the arrival time of a passenger at the LeetCode station.
 
Buses and passengers arrive at the LeetCode station. If a bus arrives at the station at a time tbus and a passenger arrived at a time tpassenger where tpassenger <= tbus and the passenger did not catch any bus, the passenger will use that bus. In addition, each bus has a capacity. If at the moment the bus arrives at the station there are more passengers waiting than its capacity capacity, only capacity passengers will use the bus.

Write an SQL query to report the number of users that used each bus.Programming
Return the result table ordered by bus_id in ascending order.
The query result format is in the following example.

Example 1:

Input: 
Buses table:
+--------+--------------+----------+
| bus_id | arrival_time | capacity |
+--------+--------------+----------+
| 1      | 2            | 1        |
| 2      | 4            | 10       |
| 3      | 7            | 2        |
+--------+--------------+----------+
Passengers table:
+--------------+--------------+
| passenger_id | arrival_time |
+--------------+--------------+
| 11           | 1            |
| 12           | 1            |
| 13           | 5            |
| 14           | 6            |
| 15           | 7            |
+--------------+--------------+
Output: 
+--------+----------------+
| bus_id | passengers_cnt |
+--------+----------------+
| 1      | 1              |
| 2      | 1              |
| 3      | 2              |
+--------+----------------+
Explanation: 
- Passenger 11 arrives at time 1.
- Passenger 12 arrives at time 1.
- Bus 1 arrives at time 2 and collects passenger 11 as it has one empty seat.

- Bus 2 arrives at time 4 and collects passenger 12 as it has ten empty seats.

- Passenger 12 arrives at time 5.
- Passenger 13 arrives at time 6.
- Passenger 14 arrives at time 7.
- Bus 3 arrives at time 7 and collects passengers 12 and 13 as it has two empty seats.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS BUSES;
DROP TABLE IF EXISTS PASSENGERS;
GO

CREATE TABLE BUSES (
    BUS_ID INT PRIMARY KEY,
    ARRIVAL_TIME INT NOT NULL,
    CAPACITY INT NOT NULL
);
GO

CREATE TABLE PASSENGERS (
    PASSENGER_ID INT PRIMARY KEY,
    ARRIVAL_TIME INT NOT NULL
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO BUSES (BUS_ID, ARRIVAL_TIME, CAPACITY) VALUES
(1, 2, 1),
(2, 4, 10),
(3, 7, 2);
GO

INSERT INTO PASSENGERS (PASSENGER_ID, ARRIVAL_TIME) VALUES
(11, 1),
(12, 1),
(13, 5),
(14, 6),
(15, 7);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM BUSES;
SELECT * FROM PASSENGERS;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
-- STEP 1: Calculate how many passengers could potentially board each bus
WITH POSSIBLE_PAQS AS (
    SELECT 
        B.BUS_ID,
        B.CAPACITY,
        B.ARRIVAL_TIME,
        COUNT(P.PASSENGER_ID) AS PAQS_COUNT_AT_TIME,
        ROW_NUMBER() OVER (ORDER BY B.ARRIVAL_TIME ASC) AS BUS_ARRIVAL_ORDER
    FROM BUSES B
    LEFT JOIN PASSENGERS P 
        ON P.ARRIVAL_TIME <= B.ARRIVAL_TIME
    GROUP BY B.BUS_ID, B.CAPACITY, B.ARRIVAL_TIME
),

-- STEP 2: Process boarding bus by bus using recursion
PROCESS AS (
    -- FIRST BUS: handle initial boarding
    SELECT 
        BUS_ID,
        CAPACITY,
        CASE 
            WHEN PAQS_COUNT_AT_TIME > CAPACITY 
            THEN CAPACITY 
            ELSE PAQS_COUNT_AT_TIME 
        END AS BOARDER_PAQS,
        CASE 
            WHEN PAQS_COUNT_AT_TIME > CAPACITY 
            THEN PAQS_COUNT_AT_TIME - CAPACITY 
            ELSE 0 
        END AS LEFT_OVER_PAQS,
        PAQS_COUNT_AT_TIME,
        BUS_ARRIVAL_ORDER
    FROM POSSIBLE_PAQS
    WHERE BUS_ARRIVAL_ORDER = 1

    UNION ALL

    -- SUBSEQUENT BUSES: carry forward leftover passengers + new arrivals
    SELECT 
        CB.BUS_ID,
        CB.CAPACITY,
        CASE 
            WHEN (CB.PAQS_COUNT_AT_TIME - PB.PAQS_COUNT_AT_TIME + PB.LEFT_OVER_PAQS) > CB.CAPACITY 
            THEN CB.CAPACITY 
            ELSE (CB.PAQS_COUNT_AT_TIME - PB.PAQS_COUNT_AT_TIME + PB.LEFT_OVER_PAQS) 
        END AS BOARDER_PAQS,
        CASE 
            WHEN (CB.PAQS_COUNT_AT_TIME - PB.PAQS_COUNT_AT_TIME + PB.LEFT_OVER_PAQS) > CB.CAPACITY 
            THEN (CB.PAQS_COUNT_AT_TIME - PB.PAQS_COUNT_AT_TIME + PB.LEFT_OVER_PAQS) - CB.CAPACITY 
            ELSE 0 
        END AS LEFT_OVER_PAQS,
        CB.PAQS_COUNT_AT_TIME,
        CB.BUS_ARRIVAL_ORDER
    FROM POSSIBLE_PAQS CB
    INNER JOIN PROCESS PB 
        ON CB.BUS_ARRIVAL_ORDER = PB.BUS_ARRIVAL_ORDER + 1
)

-- STEP 3: Final result showing how many passengers boarded each bus
SELECT 
    BUS_ID, 
    BOARDER_PAQS AS PASSENGERS_CNT
FROM PROCESS
ORDER BY BUS_ID;
```

# [2173. Longest Winning Streak](https://leetcode.com/problems/longest-winning-streak/)
```
Table: Matches
| Column Name | Type |
|-------------|------|
| player_id   | int  |
| match_day   | date |
| result      | enum |

- (player_id, match_day) is the primary key for this table.  
- Each row of this table contains the ID of a player, the day of the match they played, and the result of that match.  
- The result column is an ENUM type of ('Win', 'Draw', 'Lose').

The winning streak of a player is the number of consecutive wins uninterrupted by draws or losses.
Write an SQL query to count the longest winning streak for each player.
Return the result table in any order.
The query result format is in the following example.

### Example 1:
**Input:**  
Matches table:
| player_id | match_day  | result |
|-----------|------------|--------|
| 1         | 2022-01-17 | Win    |
| 1         | 2022-01-18 | Win    |
| 1         | 2022-01-25 | Win    |
| 1         | 2022-01-31 | Draw   |
| 1         | 2022-02-08 | Win    |
| 2         | 2022-02-06 | Lose   |
| 2         | 2022-02-08 | Lose   |
| 3         | 2022-03-30 | Win    |

**Output:**
| player_id | longest_streak |
|-----------|----------------|
| 1         | 3              |
| 2         | 0              |
| 3         | 1              |

**Explanation:**  
**Player 1:**  
From 2022-01-17 to 2022-01-25, player 1 won 3 consecutive matches.  
On 2022-01-31, player 1 had a draw.  
On 2022-02-08, player 1 won a match.  
The longest winning streak was 3 matches.

**Player 2:**  
From 2022-02-06 to 2022-02-08, player 2 lost 2 consecutive matches.  
The longest winning streak was 0 matches.

**Player 3:**  
On 2022-03-30, player 3 won a match.  
The longest winning streak was 1 match.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLE
*******************************************************************************/
DROP TABLE IF EXISTS MATCHES;
GO

CREATE TABLE MATCHES (
    PLAYER_ID  INT,
    MATCH_DAY  DATE,
    RESULT     VARCHAR(10)
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT MATCH RESULTS
*******************************************************************************/
INSERT INTO MATCHES (PLAYER_ID, MATCH_DAY, RESULT)
VALUES 
    (1, '2022-01-17', 'WIN'),
    (1, '2022-01-18', 'WIN'),
    (1, '2022-01-25', 'WIN'),
    (1, '2022-01-31', 'DRAW'),
    (1, '2022-02-08', 'WIN'),
    (2, '2022-02-06', 'LOSE'),
    (2, '2022-02-08', 'LOSE'),
    (3, '2022-03-30', 'WIN');
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM MATCHES;

/*******************************************************************************
4. SOLUTION: LONGEST WINNING STREAK (GAPS & ISLANDS)
*******************************************************************************/
WITH CALCULATED_GROUPS AS (
    SELECT 
        PLAYER_ID,
        MATCH_DAY,
        RESULT,
        /* 
           SUBTRACTING THE RESULT-SPECIFIC ROW NUMBER FROM THE OVERALL ROW NUMBER 
           CREATES A CONSTANT 'STREAK_ID' FOR CONSECUTIVE IDENTICAL RESULTS.
        */
        ROW_NUMBER() OVER(PARTITION BY PLAYER_ID ORDER BY MATCH_DAY) - 
        ROW_NUMBER() OVER(PARTITION BY PLAYER_ID, RESULT ORDER BY MATCH_DAY) AS STREAK_ID
    FROM MATCHES
),
STREAK_COUNTS AS (
    SELECT 
        PLAYER_ID, 
        COUNT(*) AS WIN_COUNT
    FROM CALCULATED_GROUPS
    WHERE RESULT = 'WIN'
    GROUP BY PLAYER_ID, STREAK_ID
)
SELECT 
    'RESULT' AS DATA_TYPE,
    P.PLAYER_ID, 
    ISNULL(MAX(S.WIN_COUNT), 0) AS LONGEST_STREAK
FROM (SELECT DISTINCT PLAYER_ID FROM MATCHES) AS P
LEFT JOIN STREAK_COUNTS AS S 
    ON P.PLAYER_ID = S.PLAYER_ID
GROUP BY P.PLAYER_ID
ORDER BY LONGEST_STREAK DESC, PLAYER_ID ASC;
```

# [2199. Finding the Topic of Each Post](https://leetcode.com/problems/finding-the-topic-of-each-post/)
```
Table: Keywords
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| topic_id    | int     |
| word        | varchar |
+-------------+---------+
(topic_id, word) is the primary key for this table.
Each row of this table contains the id of a topic and a word that is used to express this topic.
There may be more than one word to express the same topic and one word may be used to express multiple topics.
 
Table: Posts
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| post_id     | int     |
| content     | varchar |
+-------------+---------+
post_id is the primary key for this table.
Each row of this table contains the ID of a post and its content.
Content will consist only of English letters and spaces.
 
Leetcode has collected some posts from its social media website and is interested in finding the topics of each post. Each topic can be expressed by one or more keywords. If a keyword of a certain topic exists in the content of a post (case insensitive) then the post has this topic.
Write an SQL query to find the topics of each post according to the following rules:Programming

If the post does not have keywords from any topic, its topic should be "Ambiguous!".
If the post has at least one keyword of any topic, its topic should be a string of the IDs of its topics sorted in ascending order and separated by commas ','. The string should not contain duplicate IDs.
Return the result table in any order.

The query result format is in the following example.

Example 1:

Input: 
Keywords table:
+----------+----------+
| topic_id | word     |
+----------+----------+
| 1        | handball |
| 1        | football |
| 3        | WAR      |
| 2        | Vaccine  |
+----------+----------+
Posts table:
+---------+------------------------------------------------------------------------+
| post_id | content                                                                |
+---------+------------------------------------------------------------------------+
| 1       | We call it soccer They call it football hahaha                         |
| 2       | Americans prefer basketball while Europeans love handball and football |
| 3       | stop the war and play handball                                         |
| 4       | warning I planted some flowers this morning and then got vaccinated    |
+---------+------------------------------------------------------------------------+
Output: 
+---------+------------+
| post_id | topic      |
+---------+------------+
| 1       | 1          |
| 2       | 1          |
| 3       | 1,3        |
| 4       | Ambiguous! |
+---------+------------+
Explanation: 
1: "We call it soccer They call it football hahaha"
"football" expresses topic 1. There is no other word that expresses any other topic.

2: "Americans prefer basketball while Europeans love handball and football"
"handball" expresses topic 1. "football" expresses topic 1. 
There is no other word that expresses any other topic.

3: "stop the war and play handball"
"war" expresses topic 3. "handball" expresses topic 1.
There is no other word that expresses any other topic.

4: "warning I planted some flowers this morning and then got vaccinated"
There is no word in this sentence that expresses any topic. Note that "warning" is different from "war" although they have a common prefix. 
This post is ambiguous.

Note that it is okay to have one word that expresses more than one topic.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS KEYWORDS;
DROP TABLE IF EXISTS POSTS;
GO

CREATE TABLE KEYWORDS (
    TOPIC_ID INT NOT NULL,
    WORD     VARCHAR(50) NOT NULL
);
GO

CREATE TABLE POSTS (
    POST_ID  INT NOT NULL,
    CONTENT  VARCHAR(500) NOT NULL
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO KEYWORDS (TOPIC_ID, WORD)
VALUES
    (1, 'handball'),
    (1, 'football'),
    (3, 'WAR'),
    (2, 'Vaccine');
GO

INSERT INTO POSTS (POST_ID, CONTENT)
VALUES
    (1, 'We call it soccer They call it football hahaha'),
    (2, 'Americans prefer basketball while Europeans love handball and football'),
    (3, 'stop the war and play handball'),
    (4, 'warning I planted some flowers this morning and then got vaccinated');
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM KEYWORDS;
SELECT * FROM POSTS;
/*******************************************************************************
4. SOLUTION: LONGEST WINNING STREAK (GAPS & ISLANDS)
*******************************************************************************/
WITH DISTINCTTOPICS AS (
    SELECT DISTINCT 
        P.POST_ID, 
        K.TOPIC_ID
    FROM POSTS P
    LEFT JOIN KEYWORDS K
      ON CHARINDEX(' ' + LOWER(K.WORD) + ' ', ' ' + LOWER(P.CONTENT) + ' ') > 0
)
SELECT 
    POST_ID,
    ISNULL(STRING_AGG(CAST(TOPIC_ID AS VARCHAR), ',') 
           WITHIN GROUP (ORDER BY TOPIC_ID), 'Ambiguous!') AS TOPIC
FROM DISTINCTTOPICS
GROUP BY POST_ID;
```

# [2252. Dynamic Pivoting of a Table](https://leetcode.com/problems/dynamic-pivoting-of-a-table/)
```sql
```

# [2253. Dynamic Unpivoting of a Table](https://leetcode.com/problems/dynamic-unpivoting-of-a-table/)
```sql
```

# [2362. Generate the Invoice](https://leetcode.com/problems/generate-the-invoice/)
```
Table: Products
+-------------+------+
| Column Name | Type |
+-------------+------+
| product_id  | int  |
| price       | int  |
+-------------+------+
product_id is the primary key for this table.
Each row in this table shows the ID of a product and the price of one unit.
 
Table: Purchases
+-------------+------+
| Column Name | Type |
+-------------+------+
| invoice_id  | int  |
| product_id  | int  |
| quantity    | int  |
+-------------+------+
(invoice_id, product_id) is the primary key for this table.
Each row in this table shows the quantity ordered from one product in an invoice. 

Write an SQL query to show the details of the invoice with the highest price. If two or more invoices have the same price, return the details of the one with the smallest invoice_id.
Return the result table in any order.
The query result format is shown in the following example.Programming

Example 1:
Input: 
Products table:
+------------+-------+
| product_id | price |
+------------+-------+
| 1          | 100   |
| 2          | 200   |
+------------+-------+
Purchases table:
+------------+------------+----------+
| invoice_id | product_id | quantity |
+------------+------------+----------+
| 1          | 1          | 2        |
| 3          | 2          | 1        |
| 2          | 2          | 3        |
| 2          | 1          | 4        |
| 4          | 1          | 10       |
+------------+------------+----------+
Output: 
+------------+----------+-------+
| product_id | quantity | price |
+------------+----------+-------+
| 2          | 3        | 600   |
| 1          | 4        | 400   |
+------------+----------+-------+
Explanation: 
Invoice 1: price = (2 * 100) = $200
Invoice 2: price = (4 * 100) + (3 * 200) = $1000
Invoice 3: price = (1 * 200) = $200
Invoice 4: price = (10 * 100) = $1000

The highest price is $1000, and the invoices with the highest prices are 2 and 4. We return the details of the one with the smallest ID, which is invoice 2.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS PRODUCTS;
DROP TABLE IF EXISTS PURCHASES;
GO

CREATE TABLE PRODUCTS (
    PRODUCT_ID INT PRIMARY KEY,
    PRICE DECIMAL(10,2) NOT NULL
);

CREATE TABLE PURCHASES (
    INVOICE_ID INT NOT NULL,
    PRODUCT_ID INT NOT NULL,
    QUANTITY INT NOT NULL,
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO PRODUCTS (PRODUCT_ID, PRICE) VALUES
(1, 100),
(2, 200);
GO

INSERT INTO PURCHASES (INVOICE_ID, PRODUCT_ID, QUANTITY) VALUES
(1, 1, 2),
(3, 2, 1),
(2, 2, 3),
(2, 1, 4),
(4, 1, 10);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM PRODUCTS;
SELECT * FROM PURCHASES;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
-- Step 1: Find the invoice with the highest total bill
WITH HIGHEST_INVOICE AS (
    SELECT P.INVOICE_ID, SUM(P.QUANTITY * PR.PRICE) AS BILL
    FROM PURCHASES P
    LEFT JOIN PRODUCTS PR 
        ON P.PRODUCT_ID = PR.PRODUCT_ID
    GROUP BY P.INVOICE_ID
    ORDER BY BILL DESC, P.INVOICE_ID ASC   -- sort by highest bill, then lowest invoice ID
    OFFSET 0 ROWS FETCH NEXT 1 ROWS ONLY   -- pick only the top 1 invoice
)

-- Step 2: Get all products from that invoice
SELECT 
    P.PRODUCT_ID, 
    P.QUANTITY, 
    P.QUANTITY * PR.PRICE AS LINE_TOTAL    -- line-item total for each product
FROM PURCHASES P
INNER JOIN HIGHEST_INVOICE H 
    ON H.INVOICE_ID = P.INVOICE_ID         -- filter only rows from the highest invoice
LEFT JOIN PRODUCTS PR 
    ON P.PRODUCT_ID = PR.PRODUCT_ID;       -- bring in product price
```

# [2474. Customers With Strictly Increasing Purchases](https://leetcode.com/problems/customers-with-strictly-increasing-purchases/)
```
Table: Orders
+--------------+------+
| Column Name  | Type |
+--------------+------+
| order_id     | int  |
| customer_id  | int  |
| order_date   | date |
| price        | int  |
+--------------+------+
order_id is the primary key for this table.
Each row contains the id of an order, the id of customer that ordered it, the date of the order, and its price.

Write an SQL query to report the IDs of the customers with the total purchases strictly increasing yearly.Programming

The total purchases of a customer in one year is the sum of the prices of their orders in that year. If for some year the customer did not make any order, we consider the total purchases 0.
The first year to consider for each customer is the year of their first order.
The last year to consider for each customer is the year of their last order.
Return the result table in any order.

The query result format is in the following example.
Example 1:

Input: 
Orders table:
+----------+-------------+------------+-------+
| order_id | customer_id | order_date | price |
+----------+-------------+------------+-------+
| 1        | 1           | 2019-07-01 | 1100  |
| 2        | 1           | 2019-11-01 | 1200  |
| 3        | 1           | 2020-05-26 | 3000  |
| 4        | 1           | 2021-08-31 | 3100  |
| 5        | 1           | 2022-12-07 | 4700  |
| 6        | 2           | 2015-01-01 | 700   |
| 7        | 2           | 2017-11-07 | 1000  |
| 8        | 3           | 2017-01-01 | 900   |
| 9        | 3           | 2018-11-07 | 900   |
+----------+-------------+------------+-------+
Output: 
+-------------+
| customer_id |
+-------------+
| 1           |
+-------------+
Explanation: 
Customer 1: The first year is 2019 and the last year is 2022
  - 2019: 1100 + 1200 = 2300
  - 2020: 3000
  - 2021: 3100
  - 2022: 4700
  We can see that the total purchases are strictly increasing yearly, so we include customer 1 in the answer.

Customer 2: The first year is 2015 and the last year is 2017
  - 2015: 700
  - 2016: 0
  - 2017: 1000
  We do not include customer 2 in the answer because the total purchases are not strictly increasing. Note that customer 2 did not make any purchases in 2016.

Customer 3: The first year is 2017, and the last year is 2018
  - 2017: 900
  - 2018: 900
  We can see that the total purchases are strictly increasing yearly, so we include customer 1 in the answer.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS ORDERS;
GO

CREATE TABLE ORDERS (
    ORDER_ID INT PRIMARY KEY,
    CUSTOMER_ID INT NOT NULL,
    ORDER_DATE DATE NOT NULL,
    PRICE INT NOT NULL
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO ORDERS (ORDER_ID, CUSTOMER_ID, ORDER_DATE, PRICE) VALUES
(1, 1, '2019-07-01', 1100),
(2, 1, '2019-11-01', 1200),
(3, 1, '2020-05-26', 3000),
(4, 1, '2021-08-31', 3100),
(5, 1, '2022-12-07', 4700),
(6, 2, '2015-01-01', 700),
(7, 2, '2017-11-07', 1000),
(8, 3, '2017-01-01', 900),
(9, 3, '2018-11-07', 900);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM ORDERS;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
WITH PROCESSING AS (
    SELECT 
        CUSTOMER_ID,
        YEAR(ORDER_DATE) AS YEAR,
        SUM(PRICE) AS PURCHASE, 
        -- Calculate total purchase amount per customer per year

        DENSE_RANK() OVER (
            PARTITION BY CUSTOMER_ID 
            ORDER BY SUM(PRICE) ASC
        ) AS RN
        -- Assign a rank to each year's purchase total for a customer
        -- Lowest purchase = rank 1, next higher = rank 2, etc.
    FROM ORDERS 
    GROUP BY CUSTOMER_ID, YEAR(ORDER_DATE)
)
SELECT 
    CUSTOMER_ID
FROM PROCESSING
GROUP BY CUSTOMER_ID 
HAVING COUNT(DISTINCT(YEAR - RN)) = 1;
-- The trick: YEAR - RN should be constant if purchases grow steadily year by year
-- If this condition holds, the customer shows consistent growth
-- Output: IDs of customers with strictly increasing yearly purchase totals
```

# [2494. Merge Overlapping Events in the Same Hall](https://leetcode.com/problems/merge-overlapping-events-in-the-same-hall/)
```
Table: HallEvents
+-------------+------+
| Column Name | Type |
+-------------+------+
| hall_id     | int  |
| start_day   | date |
| end_day     | date |
+-------------+------+
There is no primary key in this table. It may contain duplicates.
Each row of this table indicates the start day and end day of an event and the hall in which the event is held.
 
Write an SQL query to merge all the overlapping events that are held in the same hall. Two events overlap if they have at least one day in common.Programming
Return the result table in any order.The query result format is in the following example.
Example 1:

Input: 
HallEvents table:
+---------+------------+------------+
| hall_id | start_day  | end_day    |
+---------+------------+------------+
| 1       | 2023-01-13 | 2023-01-14 |
| 1       | 2023-01-14 | 2023-01-17 |
| 1       | 2023-01-18 | 2023-01-25 |
| 2       | 2022-12-09 | 2022-12-23 |
| 2       | 2022-12-13 | 2022-12-17 |
| 3       | 2022-12-01 | 2023-01-30 |
+---------+------------+------------+
Output: 
+---------+------------+------------+
| hall_id | start_day  | end_day    |
+---------+------------+------------+
| 1       | 2023-01-13 | 2023-01-17 |
| 1       | 2023-01-18 | 2023-01-25 |
| 2       | 2022-12-09 | 2022-12-23 |
| 3       | 2022-12-01 | 2023-01-30 |
+---------+------------+------------+
Explanation: There are three halls.
Hall 1:
- The two events ["2023-01-13", "2023-01-14"] and ["2023-01-14", "2023-01-17"] overlap. We merge them in one event ["2023-01-13", "2023-01-17"].
- The event ["2023-01-18", "2023-01-25"] does not overlap with any other event, so we leave it as it is.
Hall 2:
- The two events ["2022-12-09", "2022-12-23"] and ["2022-12-13", "2022-12-17"] overlap. We merge them in one event ["2022-12-09", "2022-12-23"].
Hall 3:
- The hall has only one event, so we return it. Note that we only consider the events of each hall separately.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLE
*******************************************************************************/
DROP TABLE IF EXISTS HALLEVENTS;
GO

-- CREATE HALLEVENTS TABLE
CREATE TABLE HALLEVENTS (
    HALL_ID INT NOT NULL,
    START_DAY DATE NOT NULL,
    END_DAY DATE NOT NULL
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO HALLEVENTS (HALL_ID, START_DAY, END_DAY) VALUES
(1, '2023-01-13', '2023-01-14'),
(1, '2023-01-14', '2023-01-17'),
(1, '2023-01-18', '2023-01-25'),
(2, '2022-12-09', '2022-12-23'),
(2, '2022-12-13', '2022-12-17'),
(3, '2022-12-01', '2023-01-30');
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM HALLEVENTS;
/*******************************************************************************
4. SOLUTION: Merge overlapping or consecutive hall events
*******************************************************************************/
WITH HALLEVENTSWITHISNEWEVENT AS (
SELECT
    HALL_ID,
    START_DAY,
    END_DAY,
    ISNULL(
        CASE 
            WHEN START_DAY > LAG(END_DAY) OVER (PARTITION BY HALL_ID ORDER BY START_DAY, END_DAY DESC) THEN 1 
            ELSE 0 
        END,1) AS IS_NEW_EVENT
        -- FLAG AS NEW EVENT IF START_DAY IS STRICTLY GREATER THAN THE PREVIOUS END_DAY
        -- ISNULL ENSURES THE VERY FIRST EVENT PER HALL IS TREATED AS NEW
    FROM HALLEVENTS
),
HALLEVENTSWITHGROUPID AS (
SELECT
    HALL_ID,
    START_DAY,
    END_DAY,
    SUM(IS_NEW_EVENT) OVER (PARTITION BY HALL_ID ORDER BY START_DAY,END_DAY DESC) AS GROUP_ID
        -- ASSIGN GROUP_ID BY CUMULATIVELY SUMMING IS_NEW_EVENT
        -- ALL OVERLAPPING/CONSECUTIVE EVENTS SHARE THE SAME GROUP_ID
    FROM HALLEVENTSWITHISNEWEVENT
)
SELECT
    HALL_ID,
    MIN(START_DAY) AS START_DAY,
    MAX(END_DAY) AS END_DAY
FROM HALLEVENTSWITHGROUPID
GROUP BY HALL_ID, GROUP_ID
ORDER BY HALL_ID, START_DAY;
```

# [262. Trips and Users](https://leetcode.com/problems/trips-and-users/)
```sql
SELECT 
    REQUEST_AT AS DAY,
    ROUND(
        -- CALCULATE CANCELLATION RATE
        SUM(CASE WHEN STATUS != 'COMPLETED' THEN 1 ELSE 0 END) * 1.00 / COUNT(*),
        2
    ) AS 'CANCELLATION RATE'
FROM 
    TRIPS
    INNER JOIN USERS AS CLIENTS ON TRIPS.CLIENT_ID = CLIENTS.USERS_ID
    INNER JOIN USERS AS DRIVERS ON TRIPS.DRIVER_ID = DRIVERS.USERS_ID
WHERE 
    CLIENTS.BANNED = 'No'
    AND DRIVERS.BANNED = 'No'
    AND REQUEST_AT BETWEEN '2013-10-01' AND '2013-10-03'
GROUP BY 
    REQUEST_AT
ORDER BY 
    REQUEST_AT;
```

# [2701. Consecutive Transactions with Increasing Amounts](https://leetcode.com/problems/consecutive-transactions-with-increasing-amounts/)
```
Table: Transactions
+------------------+------+
| Column Name      | Type |
+------------------+------+
| transaction_id   | int  |
| customer_id      | int  |
| transaction_date | date |
| amount           | int  |
+------------------+------+
transaction_id is the primary key of this table. 
Each row contains information about transactions that includes unique (customer_id, transaction_date) along with the corresponding customer_id and amount.  
Write an SQL query to find the customers who have made consecutive transactions with increasing amount for at least three consecutive days. Include the customer_id, start date of the consecutive transactions period and the end date of the consecutive transactions period. There can be multiple consecutive transactions by a customer.Programming

Return the result table ordered by customer_id in ascending order.
The query result format is in the following example.

Example 1:

Input: 
Transactions table:
+----------------+-------------+------------------+--------+
| transaction_id | customer_id | transaction_date | amount |
+----------------+-------------+------------------+--------+
| 1              | 101         | 2023-05-01       | 100    |
| 2              | 101         | 2023-05-02       | 150    |
| 3              | 101         | 2023-05-03       | 200    |
| 4              | 102         | 2023-05-01       | 50     |
| 5              | 102         | 2023-05-03       | 100    |
| 6              | 102         | 2023-05-04       | 200    |
| 7              | 105         | 2023-05-01       | 100    |
| 8              | 105         | 2023-05-02       | 150    |
| 9              | 105         | 2023-05-03       | 200    |
| 10             | 105         | 2023-05-04       | 300    |
| 11             | 105         | 2023-05-12       | 250    |
| 12             | 105         | 2023-05-13       | 260    |
| 13             | 105         | 2023-05-14       | 270    |
+----------------+-------------+------------------+--------+
Output: 
+-------------+-------------------+-----------------+
| customer_id | consecutive_start | consecutive_end | 
+-------------+-------------------+-----------------+
| 101         |  2023-05-01       | 2023-05-03      | 
| 105         |  2023-05-01       | 2023-05-04      |
| 105         |  2023-05-12       | 2023-05-14      | 
+-------------+-------------------+-----------------+
Explanation: 
- customer_id 101 has made consecutive transactions with increasing amounts from May 1st, 2023, to May 3rd, 2023
- customer_id 102 does not have any consecutive transactions for at least 3 days. 
- customer_id 105 has two sets of consecutive transactions: from May 1st, 2023, to May 4th, 2023, and from May 12th, 2023, to May 14th, 2023. 
customer_id is sorted in ascending order.
```
```sql
-- STEP 1: BRING IN THE PREVIOUS DAY’S DATE AND AMOUNT PER CUSTOMER
WITH CTE AS (
    SELECT
        CUSTOMER_ID,
        TRANSACTION_DATE,
        AMOUNT,
        LAG(TRANSACTION_DATE) OVER (
            PARTITION BY CUSTOMER_ID
            ORDER BY TRANSACTION_DATE
        ) AS PREV_DATE,
        LAG(AMOUNT) OVER (
            PARTITION BY CUSTOMER_ID
            ORDER BY TRANSACTION_DATE
        ) AS PREV_AMOUNT
    FROM TRANSACTIONS
),

-- STEP 2: FLAG WHERE A NEW STREAK BEGINS (BREAK IN CONSECUTIVENESS OR NON-INCREASING AMOUNT)
FLAGGED AS (
    SELECT
        CUSTOMER_ID,
        TRANSACTION_DATE,
        AMOUNT,
        CASE 
            WHEN PREV_DATE IS NOT NULL
              AND DATEDIFF(DAY, PREV_DATE, TRANSACTION_DATE) = 1
              AND AMOUNT > PREV_AMOUNT 
            THEN 0 
            ELSE 1 
        END AS IS_NEW_GROUP
    FROM CTE
),

-- STEP 3: BUILD A RUNNING SUM OF THOSE FLAGS TO ASSIGN A GROUP IDENTIFIER TO EACH STREAK
GROUPED AS (
    SELECT
        CUSTOMER_ID,
        TRANSACTION_DATE,
        SUM(IS_NEW_GROUP) 
          OVER (PARTITION BY CUSTOMER_ID 
                ORDER BY TRANSACTION_DATE 
                ROWS UNBOUNDED PRECEDING) AS GRP
    FROM FLAGGED
)

-- STEP 4: AGGREGATE BY GROUP, FILTER FOR STREAKS OF LENGTH ≥ 3, AND REPORT THEIR BOUNDS
SELECT
    CUSTOMER_ID,
    MIN(TRANSACTION_DATE) AS CONSECUTIVE_START,
    MAX(TRANSACTION_DATE) AS CONSECUTIVE_END
FROM GROUPED
GROUP BY CUSTOMER_ID, GRP
HAVING COUNT(*) >= 3
ORDER BY CUSTOMER_ID;
```

# [2720. Popularity Percentage](https://leetcode.com/problems/popularity-percentage/)
```sql
WITH F AS (
    -- MAKE FRIENDSHIPS BIDIRECTIONAL
    SELECT * FROM FRIENDS
    UNION
    SELECT USER2, USER1 FROM FRIENDS
),
T AS (
    -- COUNT TOTAL UNIQUE USERS
    SELECT COUNT(DISTINCT USER1) AS CNT FROM F
)
SELECT DISTINCT
    USER1,
    ROUND(
        -- CALCULATE PERCENTAGE POPULARITY
        (COUNT(1) OVER (PARTITION BY USER1)) * 100.0 / (SELECT CNT FROM T),
        2
    ) AS PERCENTAGE_POPULARITY
FROM F
ORDER BY USER1;
```

# [2752. Customers with Maximum Number of Transactions on Consecutive Days](https://leetcode.com/problems/customers-with-maximum-number-of-transactions-on-consecutive-days/)
```
Table: Transactions
+------------------+------+
| Column Name      | Type |
+------------------+------+
| transaction_id   | int  |
| customer_id      | int  |
| transaction_date | date |
| amount           | int  |
+------------------+------+
transaction_id is the column with unique values of this table.
Each row contains information about transactions that includes unique (customer_id, transaction_date) along with the corresponding customer_id and amount.   
Write a solution to find all customer_id who made the maximum number of transactions on consecutive days.

Return all customer_id with the maximum number of consecutive transactions. Order the result table by customer_id in ascending order.
The result format is in the following example.
Example 1:

Input: 
Transactions table:
+----------------+-------------+------------------+--------+
| transaction_id | customer_id | transaction_date | amount |
+----------------+-------------+------------------+--------+
| 1              | 101         | 2023-05-01       | 100    |
| 2              | 101         | 2023-05-02       | 150    |
| 3              | 101         | 2023-05-03       | 200    |
| 4              | 102         | 2023-05-01       | 50     |
| 5              | 102         | 2023-05-03       | 100    |
| 6              | 102         | 2023-05-04       | 200    |
| 7              | 105         | 2023-05-01       | 100    |
| 8              | 105         | 2023-05-02       | 150    |
| 9              | 105         | 2023-05-03       | 200    |
+----------------+-------------+------------------+--------+
Output: 
+-------------+
| customer_id | 
+-------------+
| 101         | 
| 105         | 
+-------------+
Explanation: 
- customer_id 101 has a total of 3 transactions, and all of them are consecutive.
- customer_id 102 has a total of 3 transactions, but only 2 of them are consecutive. 
- customer_id 105 has a total of 3 transactions, and all of them are consecutive.
In total, the highest number of consecutive transactions is 3, achieved by customer_id 101 and 105. The customer_id are sorted in ascending order.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLE
*******************************************************************************/
DROP TABLE IF EXISTS TRANSACTIONS;
GO

CREATE TABLE TRANSACTIONS (
    TRANSACTION_ID INT PRIMARY KEY,
    CUSTOMER_ID INT NOT NULL,
    TRANSACTION_DATE DATE NOT NULL,
    AMOUNT DECIMAL(10,2) NOT NULL
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT MATCH RESULTS
*******************************************************************************/
INSERT INTO TRANSACTIONS (TRANSACTION_ID, CUSTOMER_ID, TRANSACTION_DATE, AMOUNT) VALUES
(1, 101, '2023-05-01', 100),
(2, 101, '2023-05-02', 150),
(3, 101, '2023-05-03', 200),
(4, 102, '2023-05-01', 50),
(5, 102, '2023-05-03', 100),
(6, 102, '2023-05-04', 200),
(7, 105, '2023-05-01', 100),
(8, 105, '2023-05-02', 150),
(9, 105, '2023-05-03', 200);

GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM TRANSACTIONS;

/*******************************************************************************
4. SOLUTION
*******************************************************************************/
-- Step 1: Identify consecutive groups of transactions per customer
WITH CONSECUTIVE_GROUP AS (
    SELECT 
        *,
        -- Trick: subtract row_number (per customer, ordered by date) from the transaction_date
        -- This creates a "group key" (GRP) that stays constant for consecutive dates
        DATEADD(
            DAY, 
            -1 * ROW_NUMBER() OVER (PARTITION BY CUSTOMER_ID ORDER BY TRANSACTION_DATE), 
            TRANSACTION_DATE
        ) AS GRP
    FROM TRANSACTIONS
),

-- Step 2: Count how many transactions fall into each consecutive group
MAX_TRAN_COUNT AS (
    SELECT 
        CUSTOMER_ID,
        GRP,
        COUNT(*) AS CNT
    FROM CONSECUTIVE_GROUP
    GROUP BY CUSTOMER_ID, GRP
)

-- Step 3: Find customers whose longest streak equals the global maximum streak
SELECT DISTINCT CUSTOMER_ID
FROM MAX_TRAN_COUNT
WHERE CNT IN (
    SELECT MAX(CNT) FROM MAX_TRAN_COUNT
);
```

# [2793. Status of Flight Tickets](https://leetcode.com/problems/status-of-flight-tickets/)
```
Table: Flights
+-------------+------+
| Column Name | Type |
+-------------+------+
| flight_id   | int  |
| capacity    | int  |
+-------------+------+
flight_id column contains distinct values.
Each row of this table contains flight id and capacity.
Table: Passengers
+--------------+----------+
| Column Name  | Type     |
+--------------+----------+
| passenger_id | int      |
| flight_id    | int      |
| booking_time | datetime |
+--------------+----------+
passenger_id column contains distinct values.
booking_time column contains distinct values.
Each row of this table contains passenger id, booking time, and their flight id.
Passengers book tickets for flights in advance. If a passenger books a ticket for a flight and there are still empty seats available on the flight, the passenger's ticket will be confirmed. However, the passenger will be on a waitlist if the flight is already at full capacity.

Write a solution to determine the current status of flight tickets for each passenger.
Return the result table ordered by passenger_id in ascending order.

The result format is in the following example.
Example 1:

Input: 
Flights table:
+-----------+----------+
| flight_id | capacity |
+-----------+----------+
| 1         | 2        |
| 2         | 2        |
| 3         | 1        |
+-----------+----------+
Passengers table:
+--------------+-----------+---------------------+
| passenger_id | flight_id | booking_time        |
+--------------+-----------+---------------------+
| 101          | 1         | 2023-07-10 16:30:00 |
| 102          | 1         | 2023-07-10 17:45:00 |
| 103          | 1         | 2023-07-10 12:00:00 |
| 104          | 2         | 2023-07-05 13:23:00 |
| 105          | 2         | 2023-07-05 09:00:00 |
| 106          | 3         | 2023-07-08 11:10:00 |
| 107          | 3         | 2023-07-08 09:10:00 |
+--------------+-----------+---------------------+
Output: 
+--------------+-----------+
| passenger_id | Status    |
+--------------+-----------+
| 101          | Confirmed | 
| 102          | Waitlist  | 
| 103          | Confirmed | 
| 104          | Confirmed | 
| 105          | Confirmed | 
| 106          | Waitlist  | 
| 107          | Confirmed | 
+--------------+-----------+
Explanation: 
- Flight 1 has a capacity of 2 passengers. Passenger 101 and Passenger 103 were the first to book tickets, securing the available seats. Therefore, their bookings are confirmed. However, Passenger 102 was the third person to book a ticket for this flight, which means there are no more available seats. Passenger 102 is now placed on the waitlist, 
- Flight 2 has a capacity of 2 passengers, Flight 2 has exactly two passengers who booked tickets,  Passenger 104 and Passenger 105. Since the number of passengers who booked tickets matches the available seats, both bookings are confirmed.
- Flight 3 has a capacity of 1 passenger. Passenger 107 booked earlier and secured the only available seat, confirming their booking. Passenger 106, who booked after Passenger 107, is on the waitlist.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS PASSENGERS;
DROP TABLE IF EXISTS FLIGHTS;
GO

-- CREATE FLIGHTS TABLE
CREATE TABLE FLIGHTS (
    FLIGHT_ID INT PRIMARY KEY,
    CAPACITY INT NOT NULL
);
GO

-- CREATE PASSENGERS TABLE
CREATE TABLE PASSENGERS (
    PASSENGER_ID INT PRIMARY KEY,
    FLIGHT_ID INT NOT NULL,
    BOOKING_TIME DATETIME NOT NULL,
    FOREIGN KEY (FLIGHT_ID) REFERENCES FLIGHTS(FLIGHT_ID)
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
-- INSERT INTO FLIGHTS
INSERT INTO FLIGHTS (FLIGHT_ID, CAPACITY) VALUES
(1, 2),
(2, 2),
(3, 1);

-- INSERT INTO PASSENGERS
INSERT INTO PASSENGERS (PASSENGER_ID, FLIGHT_ID, BOOKING_TIME) VALUES
(101, 1, '2023-07-10 16:30:00'),
(102, 1, '2023-07-10 17:45:00'),
(103, 1, '2023-07-10 12:00:00'),
(104, 2, '2023-07-05 13:23:00'),
(105, 2, '2023-07-05 09:00:00'),
(106, 3, '2023-07-08 11:10:00'),
(107, 3, '2023-07-08 09:10:00');
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM FLIGHTS;
SELECT * FROM PASSENGERS;
/*******************************************************************************
4. SOLUTION: Identify passengers exceeding flight capacity
*******************************************************************************/
/*******************************************************************************
4. SOLUTION: Identify passengers exceeding flight capacity
*******************************************************************************/
WITH BOOKING_ORDER AS (
    SELECT 
        F.FLIGHT_ID,
        PASSENGER_ID,
        CAPACITY,
        ROW_NUMBER() OVER (PARTITION BY P.FLIGHT_ID ORDER BY BOOKING_TIME ASC) AS BOOKING_ORDER 
        -- Assigns a sequential number to each passenger per flight
        -- Ordered by booking_time (earliest = 1, next = 2, etc.)
    FROM PASSENGERS P 
    LEFT JOIN FLIGHTS F 
      ON F.FLIGHT_ID = P.FLIGHT_ID
)
SELECT
    PASSENGER_ID,
    CASE 
        WHEN BOOKING_ORDER <= CAPACITY 
            THEN 'Confirmed'   -- Passenger fits within flight capacity
        ELSE 'Waitlist'        -- Passenger exceeds capacity, placed on waitlist
    END AS STATUS 
FROM BOOKING_ORDER 
ORDER BY PASSENGER_ID ASC;
```

# [2991. Top Three Wineries](https://leetcode.com/problems/top-three-wineries/)
```sql
-- STEP 1: AGGREGATE AND RANK WINERIES BY COUNTRY
WITH RANKEDWINERIES AS (
    SELECT 
        COUNTRY,
        WINERY,
        SUM(POINTS) AS TOTAL_POINTS,
        RANK() OVER (
            PARTITION BY COUNTRY 
            ORDER BY SUM(POINTS) DESC, WINERY ASC
        ) AS RK
    FROM WINERIES
    GROUP BY COUNTRY, WINERY
)

-- STEP 2: PIVOT TOP 3 WINERIES PER COUNTRY WITH FALLBACK VALUES
SELECT 
    COUNTRY,
    ISNULL(MAX(CASE WHEN RK = 1 THEN CONCAT(WINERY, ' (', TOTAL_POINTS, ')') END), 'no first winery') AS TOP_WINERY,
    ISNULL(MAX(CASE WHEN RK = 2 THEN CONCAT(WINERY, ' (', TOTAL_POINTS, ')') END), 'No second winery') AS SECOND_WINERY,
    ISNULL(MAX(CASE WHEN RK = 3 THEN CONCAT(WINERY, ' (', TOTAL_POINTS, ')') END), 'No third winery') AS THIRD_WINERY
FROM RANKEDWINERIES
WHERE RK <= 3
GROUP BY COUNTRY
ORDER BY COUNTRY;
```

# [2994. Friday Purchases II](https://leetcode.com/problems/friday-purchases-ii/)

### Description

The `Purchases` table has the following schema:  
- user_id       int  
- purchase_date date  
- amount_spend int  

The primary key is the combination `(user_id, purchase_date, amount_spend)`.  
The `purchase_date` ranges from November 1, 2023, to November 30, 2023.  

Write a query to compute, for each Friday in November 2023, the total amount spent by all users on that day.  
If no purchases occurred on a given Friday, report 0.  
Return the result ordered by `week_of_month` in ascending order, where `week_of_month` is 1 for the first Friday, 2 for the second, and so on.

### Sample Input

Purchases table:

| user_id | purchase_date | amount_spend |
|---------|---------------|--------------|
| 11      | 2023-11-07    | 1126         |
| 15      | 2023-11-30    | 7473         |
| 17      | 2023-11-14    | 2414         |
| 12      | 2023-11-24    | 9692         |
| 8       | 2023-11-03    | 5117         |
| 1       | 2023-11-16    | 5241         |
| 10      | 2023-11-12    | 8266         |
| 13      | 2023-11-24    | 12000        |

### Sample Output

| week_of_month | purchase_date | total_amount |
|---------------|---------------|--------------|
| 1             | 2023-11-03    | 5117         |
| 2             | 2023-11-10    | 0            |
| 3             | 2023-11-17    | 0            |
| 4             | 2023-11-24    | 21692        |

### Explanation

- Week 1’s Friday is 2023-11-03, total spending = 5,117.  
- Week 2’s Friday is 2023-11-10, no purchases → 0.  
- Week 3’s Friday is 2023-11-17, no purchases → 0.  
- Week 4’s Friday is 2023-11-24, two purchases (9,692 + 12,000) → 21,692.

```sql
WITH FRIDAYS AS (
    -- SEED WITH THE FIRST FRIDAY OF NOVEMBER 2023
    SELECT CAST('2023-11-03' AS DATE) AS FRIDAY
    UNION ALL
    -- ADD 7 DAYS UNTIL THE END OF NOVEMBER
    SELECT DATEADD(DAY, 7, FRIDAY)
    FROM FRIDAYS
    WHERE DATEADD(DAY, 7, FRIDAY) <= '2023-11-30'
)
SELECT
    -- CALCULATE WEEK_OF_MONTH AS 1 FOR THE FIRST FRIDAY, 2 FOR THE NEXT, ETC.
    DATEDIFF(DAY, '2023-11-03', F.FRIDAY) / 7 + 1 AS WEEK_OF_MONTH,
    F.FRIDAY           AS PURCHASE_DATE,
    ISNULL(SUM(P.AMOUNT_SPEND), 0) AS TOTAL_AMOUNT
FROM FRIDAYS AS F
LEFT JOIN PURCHASES AS P
    ON P.PURCHASE_DATE = F.FRIDAY
GROUP BY
    F.FRIDAY
ORDER BY
    WEEK_OF_MONTH
OPTION (MAXRECURSION 0);
```

# [2995. Viewers Turned Streamers](https://leetcode.com/problems/viewers-turned-streamers/)
```sql
WITH T AS (
    SELECT
        USER_ID,
        SESSION_TYPE,
        RANK() OVER (
            PARTITION BY USER_ID
            ORDER BY SESSION_START
        ) AS RK
    FROM SESSIONS
)
SELECT 
    T.USER_ID, 
    COUNT(*) AS SESSIONS_COUNT
FROM 
    T T
    INNER JOIN SESSIONS S ON T.USER_ID = S.USER_ID
WHERE 
    T.RK = 1 
    AND T.SESSION_TYPE = 'Viewer' 
    AND S.SESSION_TYPE = 'Streamer'
GROUP BY 
    T.USER_ID
ORDER BY 
    SESSIONS_COUNT DESC, 
    T.USER_ID DESC;
```

# [3052. Maximize Items](https://leetcode.com/problems/maximize-items/)
```
Table: Inventory
+----------------+---------+ 
| Column Name    | Type    | 
+----------------+---------+ 
| item_id        | int     | 
| item_type      | varchar |
| item_category  | varchar |
| square_footage | decimal |
+----------------+---------+
item_id is the column of unique values for this table.
Each row includes item id, item type, item category and sqaure footage.
Leetcode warehouse wants to maximize the number of items it can stock in a 500,000 square feet warehouse. It wants to stock as many prime items as possible, and afterwards use the remaining square footage to stock the most number of non-prime items.

Write a solution to find the number of prime and non-prime items that can be stored in the 500,000 square feet warehouse. Output the item type with prime_eligible followed by not_prime and the maximum number of items that can be stocked.

Note:
Item count must be a whole number (integer).
If the count for the not_prime category is 0, you should output 0 for that particular category.
Return the result table ordered by item count in ascending order.
The result format is in the following example.

Example 1:

Input: 
Inventory table:
+---------+----------------+---------------+----------------+
| item_id | item_type      | item_category | square_footage | 
+---------+----------------+---------------+----------------+
| 1374    | prime_eligible | Watches       | 68.00          | 
| 4245    | not_prime      | Art           | 26.40          | 
| 5743    | prime_eligible | Software      | 325.00         | 
| 8543    | not_prime      | Clothing      | 64.50          |  
| 2556    | not_prime      | Shoes         | 15.00          |
| 2452    | prime_eligible | Scientific    | 85.00          |
| 3255    | not_prime      | Furniture     | 22.60          | 
| 1672    | prime_eligible | Beauty        | 8.50           |  
| 4256    | prime_eligible | Furniture     | 55.50          |
| 6325    | prime_eligible | Food          | 13.20          | 
+---------+----------------+---------------+----------------+
Output: 
+----------------+-------------+
| item_type      | item_count  | 
+----------------+-------------+
| prime_eligible | 5400        | 
| not_prime      | 8           | 
+----------------+-------------+
Explanation: 
- The prime-eligible category comprises a total of 6 items, amounting to a combined square footage of 555.20 (68 + 325 + 85 + 8.50 + 55.50 + 13.20). It is possible to store 900 combinations of these 6 items, totaling 5400 items and occupying 499,680 square footage.
- In the not_prime category, there are a total of 4 items with a combined square footage of 128.50. After deducting the storage used by prime-eligible items (500,000 - 499,680 = 320), there is room for 2 combinations of non-prime items, accommodating a total of 8 non-prime items within the available 320 square footage.
Output table is ordered by item count in descending order.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLE
*******************************************************************************/
DROP TABLE IF EXISTS INVENTORY;
GO

CREATE TABLE INVENTORY (
    ITEM_ID INT PRIMARY KEY,
    ITEM_TYPE VARCHAR(20),
    ITEM_CATEGORY VARCHAR(50),
    SQUARE_FOOTAGE DECIMAL(10,2)
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT MATCH RESULTS
*******************************************************************************/
INSERT INTO INVENTORY (ITEM_ID, ITEM_TYPE, ITEM_CATEGORY, SQUARE_FOOTAGE) VALUES
(1374, 'prime_eligible', 'Watches', 68.00),
(4245, 'not_prime', 'Art', 26.40),
(5743, 'prime_eligible', 'Software', 325.00),
(8543, 'not_prime', 'Clothing', 64.50),
(2556, 'not_prime', 'Shoes', 15.00),
(2452, 'prime_eligible', 'Scientific', 85.00),
(3255, 'not_prime', 'Furniture', 22.60),
(1672, 'prime_eligible', 'Beauty', 8.50),
(4256, 'prime_eligible', 'Furniture', 55.50),
(6325, 'prime_eligible', 'Food', 13.20);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM INVENTORY;

/*******************************************************************************
4. SOLUTION
*******************************************************************************/
-- Precompute totals for prime and non-prime
WITH PRIME AS (
    SELECT SUM(SQUARE_FOOTAGE) AS PRIME_TOTAL
    FROM INVENTORY
    WHERE ITEM_TYPE = 'prime_eligible'
),
NONPRIME AS (
    SELECT SUM(SQUARE_FOOTAGE) AS NONPRIME_TOTAL
    FROM INVENTORY
    WHERE ITEM_TYPE = 'not_prime'
),
COUNTS AS (
    SELECT 
        SUM(CASE WHEN ITEM_TYPE = 'prime_eligible' THEN 1 ELSE 0 END) AS PRIME_COUNT,
        SUM(CASE WHEN ITEM_TYPE = 'not_prime' THEN 1 ELSE 0 END) AS NONPRIME_COUNT
    FROM INVENTORY
)

-- Final allocation
SELECT
    'prime_eligible' AS ITEM_TYPE,
    COUNTS.PRIME_COUNT * FLOOR(500000.0 / PRIME.PRIME_TOTAL) AS ITEM_COUNT
FROM PRIME, COUNTS

UNION ALL

SELECT
    'not_prime' AS ITEM_TYPE,
    COUNTS.NONPRIME_COUNT * FLOOR((500000.0 % PRIME.PRIME_TOTAL) / NONPRIME.NONPRIME_TOTAL) AS ITEM_COUNT
FROM PRIME, NONPRIME, COUNTS;
```

# [3057. Employees Project Allocation](https://leetcode.com/problems/employees-project-allocation/)
```sql
WITH EMPLOYEESWITHAVGWORKLOAD AS (
    SELECT
        E.EMPLOYEE_ID,
        E.NAME AS EMPLOYEE_NAME,
        E.TEAM,
        P.PROJECT_ID,
        P.WORKLOAD AS PROJECT_WORKLOAD,
        AVG(P.WORKLOAD) OVER (PARTITION BY E.TEAM) AS AVG_TEAM_WORKLOAD
    FROM PROJECT P
    INNER JOIN EMPLOYEES E ON P.EMPLOYEE_ID = E.EMPLOYEE_ID
)
SELECT
    EMPLOYEE_ID,
    PROJECT_ID,
    EMPLOYEE_NAME,
    PROJECT_WORKLOAD
FROM EMPLOYEESWITHAVGWORKLOAD
WHERE PROJECT_WORKLOAD > AVG_TEAM_WORKLOAD
ORDER BY EMPLOYEE_ID, PROJECT_ID;
```

# [3060. User Activities within Time Bounds](https://leetcode.com/problems/user-activities-within-time-bounds/)
```
Table: Sessions
+---------------+----------+
| Column Name   | Type     |
+---------------+----------+
| user_id       | int      |
| session_start | datetime |
| session_end   | datetime |
| session_id    | int      |
| session_type  | enum     |
+---------------+----------+
session_id is column of unique values for this table.
session_type is an ENUM (category) type of (Viewer, Streamer).
This table contains user id, session start, session end, session id and session type.
Write a solution to find the the users who have had at least one consecutive session of the same type (either 'Viewer' or 'Streamer') with a maximum gap of 12 hours between sessions.

Return the result table ordered by user_id in ascending order.
The result format is in the following example.

Example:

Input: 
Sessions table:
+---------+---------------------+---------------------+------------+--------------+
| user_id | session_start       | session_end         | session_id | session_type | 
+---------+---------------------+---------------------+------------+--------------+
| 101     | 2023-11-01 08:00:00 | 2023-11-01 09:00:00 | 1          | Viewer       |  
| 101     | 2023-11-01 10:00:00 | 2023-11-01 11:00:00 | 2          | Streamer     |   
| 102     | 2023-11-01 13:00:00 | 2023-11-01 14:00:00 | 3          | Viewer       | 
| 102     | 2023-11-01 15:00:00 | 2023-11-01 16:00:00 | 4          | Viewer       | 
| 101     | 2023-11-02 09:00:00 | 2023-11-02 10:00:00 | 5          | Viewer       | 
| 102     | 2023-11-02 12:00:00 | 2023-11-02 13:00:00 | 6          | Streamer     | 
| 101     | 2023-11-02 13:00:00 | 2023-11-02 14:00:00 | 7          | Streamer     | 
| 102     | 2023-11-02 16:00:00 | 2023-11-02 17:00:00 | 8          | Viewer       | 
| 103     | 2023-11-01 08:00:00 | 2023-11-01 09:00:00 | 9          | Viewer       | 
| 103     | 2023-11-02 20:00:00 | 2023-11-02 23:00:00 | 10         | Viewer       | 
| 103     | 2023-11-03 09:00:00 | 2023-11-03 10:00:00 | 11         | Viewer       | 
+---------+---------------------+---------------------+------------+--------------+
Output: 
+---------+
| user_id |
+---------+
| 102     |
| 103     |
+---------+
Explanation:
- User ID 101 will not be included in the final output as they do not have any consecutive sessions of the same session type.
- User ID 102 will be included in the final output as they had two viewer sessions with session IDs 3 and 4, respectively, and the time gap between them was less than 12 hours.
- User ID 103 participated in two viewer sessions with a gap of less than 12 hours between them, identified by session IDs 10 and 11. Therefore, user 103 will be included in the final output.
Output table is ordered by user_id in increasing order.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS SESSIONS;
GO

CREATE TABLE SESSIONS (
    USER_ID INT NOT NULL,
    SESSION_START DATETIME NOT NULL,
    SESSION_END DATETIME NOT NULL,
    SESSION_ID INT PRIMARY KEY,
    SESSION_TYPE VARCHAR(20) NOT NULL
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO SESSIONS (USER_ID, SESSION_START, SESSION_END, SESSION_ID, SESSION_TYPE) VALUES
(101, '2023-11-01 08:00:00', '2023-11-01 09:00:00', 1, 'Viewer'),
(101, '2023-11-01 10:00:00', '2023-11-01 11:00:00', 2, 'Streamer'),
(102, '2023-11-01 13:00:00', '2023-11-01 14:00:00', 3, 'Viewer'),
(102, '2023-11-01 15:00:00', '2023-11-01 16:00:00', 4, 'Viewer'),
(101, '2023-11-02 09:00:00', '2023-11-02 10:00:00', 5, 'Viewer'),
(102, '2023-11-02 12:00:00', '2023-11-02 13:00:00', 6, 'Streamer'),
(101, '2023-11-02 13:00:00', '2023-11-02 14:00:00', 7, 'Streamer'),
(102, '2023-11-02 16:00:00', '2023-11-02 17:00:00', 8, 'Viewer'),
(103, '2023-11-01 08:00:00', '2023-11-01 09:00:00', 9, 'Viewer'),
(103, '2023-11-02 20:00:00', '2023-11-02 23:00:00', 10, 'Viewer'),
(103, '2023-11-03 09:00:00', '2023-11-03 10:00:00', 11, 'Viewer');
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM SESSIONS;
/*******************************************************************************
4. SOLUTION: LONGEST WINNING STREAK (GAPS & ISLANDS)
*******************************************************************************/
-- This will only work when session_ids are consecutive
SELECT DISTINCT S1.USER_ID 
FROM SESSIONS S1 
INNER JOIN SESSIONS S2 
    ON S1.USER_ID = S2.USER_ID 
   AND S1.SESSION_TYPE = S2.SESSION_TYPE 
   AND DATEDIFF(HOUR, S2.SESSION_START, S1.SESSION_END) <= 12
   AND S1.SESSION_ID + 1 = S2.SESSION_ID;
-----------------------------------------------
WITH TIME_DIFF AS (
SELECT 
USER_ID,
DATEDIFF(HOUR,
         LAG(SESSION_END) OVER (PARTITION BY USER_ID, SESSION_TYPE ORDER BY SESSION_START),
         SESSION_START) AS TIME_DIFF
FROM SESSIONS
)
SELECT DISTINCT USER_ID
FROM TIME_DIFF
WHERE TIME_DIFF <= 12;
```

# [3061. Calculate Trapping Rain Water](https://leetcode.com/problems/calculate-trapping-rain-water/)
```sql
-- CTE TO COMPUTE LEFT AND RIGHT MAX HEIGHTS FOR EACH POSITION
WITH T AS (
    SELECT
        *,
        MAX(HEIGHT) OVER (ORDER BY ID ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS L, -- MAX TO THE LEFT
        MAX(HEIGHT) OVER (ORDER BY ID DESC ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS R -- MAX TO THE RIGHT
    FROM HEIGHTS
)

-- CALCULATE TRAPPED WATER AT EACH POSITION AND SUM IT
SELECT SUM(
    CASE 
        WHEN L < R THEN L - HEIGHT
        ELSE R - HEIGHT
    END
) AS TOTAL_TRAPPED_WATER
FROM T
WHERE L > HEIGHT AND R > HEIGHT; -- ONLY COUNT WHERE WATER CAN BE TRAPPED
```

# [3103. Find Trending Hashtags II](https://leetcode.com/problems/find-trending-hashtags-ii/)
```
Table: Tweets
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| tweet_id    | int     |
| tweet_date  | date    |
| tweet       | varchar |
+-------------+---------+
tweet_id is the primary key (column with unique values) for this table.
Each row of this table contains user_id, tweet_id, tweet_date and tweet.
It is guaranteed that all tweet_date are valid dates in February 2024.

Write a solution to find the top 3 trending hashtags in February 2024. Every tweet may contain several hashtags.
Return the result table ordered by count of hashtag, hashtag in descending order.
The result format is in the following example.

Example 1:
Input:
Tweets table:
+---------+----------+------------------------------------------------------------+------------+
| user_id | tweet_id | tweet                                                      | tweet_date |
+---------+----------+------------------------------------------------------------+------------+
| 135     | 13       | Enjoying a great start to the day. #HappyDay #MorningVibes | 2024-02-01 |
| 136     | 14       | Another #HappyDay with good vibes! #FeelGood               | 2024-02-03 |
| 137     | 15       | Productivity peaks! #WorkLife #ProductiveDay               | 2024-02-04 |
| 138     | 16       | Exploring new tech frontiers. #TechLife #Innovation        | 2024-02-04 |
| 139     | 17       | Gratitude for today's moments. #HappyDay #Thankful         | 2024-02-05 |
| 140     | 18       | Innovation drives us. #TechLife #FutureTech                | 2024-02-07 |
| 141     | 19       | Connecting with nature's serenity. #Nature #Peaceful       | 2024-02-09 |
+---------+----------+------------------------------------------------------------+------------+
Output:
+-----------+-------+
| hashtag   | count |
+-----------+-------+
| #HappyDay | 3     |
| #TechLife | 2     |
| #WorkLife | 1     |
+-----------+-------+

Explanation:
#HappyDay: Appeared in tweet IDs 13, 14, and 17, with a total count of 3 mentions.
#TechLife: Appeared in tweet IDs 16 and 18, with a total count of 2 mentions.
#WorkLife: Appeared in tweet ID 15, with a total count of 1 mention.
Note: Output table is sorted in descending order by count and hashtag respectively.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS TWEETS;
GO

CREATE TABLE TWEETS (
    USER_ID INT,
    TWEET_ID INT PRIMARY KEY,
    TWEET VARCHAR(255),
    TWEET_DATE DATE
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO TWEETS (USER_ID, TWEET_ID, TWEET, TWEET_DATE) VALUES
(135, 13, 'Enjoying a great start to the day. #HappyDay #MorningVibes', '2024-02-01'),
(136, 14, 'Another #HappyDay with good vibes! #FeelGood', '2024-02-03'),
(137, 15, 'Productivity peaks! #WorkLife #ProductiveDay', '2024-02-04'),
(138, 16, 'Exploring new tech frontiers. #TechLife #Innovation', '2024-02-04'),
(139, 17, 'Gratitude for today''s moments. #HappyDay #Thankful', '2024-02-05'),
(140, 18, 'Innovation drives us. #TechLife #FutureTech', '2024-02-07'),
(141, 19, 'Connecting with nature''s serenity. #Nature #Peaceful', '2024-02-09');
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM TWEETS;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
-- Recursive CTE to extract hashtags from tweets
WITH TAGS AS (
    -- Base case: extract first hashtag from each tweet
    SELECT
        -- Extract hashtag (from '#' to next space or end of string)
        CASE 
            WHEN CHARINDEX(' ', TWEET, CHARINDEX('#', TWEET) + 1) = 0 
                THEN SUBSTRING(TWEET, CHARINDEX('#', TWEET), LEN(TWEET) - CHARINDEX('#', TWEET) + 1)
            ELSE SUBSTRING(TWEET, CHARINDEX('#', TWEET), CHARINDEX(' ', TWEET, CHARINDEX('#', TWEET)) - CHARINDEX('#', TWEET))
        END AS HASH_TAG,
        -- Remaining text after the hashtag
        CASE 
            WHEN CHARINDEX(' ', TWEET, CHARINDEX('#', TWEET)) = 0 
                THEN ''
            ELSE SUBSTRING(TWEET, CHARINDEX(' ', TWEET, CHARINDEX('#', TWEET)) + 1, LEN(TWEET) - CHARINDEX(' ', TWEET, CHARINDEX('#', TWEET)))
        END AS TWEET
    FROM TWEETS
    WHERE CHARINDEX('#', TWEET) > 0

    UNION ALL

    -- Recursive case: extract next hashtag from remaining text
    SELECT
        CASE 
            WHEN CHARINDEX(' ', TWEET, CHARINDEX('#', TWEET) + 1) = 0 
                THEN SUBSTRING(TWEET, CHARINDEX('#', TWEET), LEN(TWEET) - CHARINDEX('#', TWEET) + 1)
            ELSE SUBSTRING(TWEET, CHARINDEX('#', TWEET), CHARINDEX(' ', TWEET, CHARINDEX('#', TWEET)) - CHARINDEX('#', TWEET))
        END AS HASH_TAG,
        CASE 
            WHEN CHARINDEX(' ', TWEET, CHARINDEX('#', TWEET)) = 0 
                THEN ''
            ELSE SUBSTRING(TWEET, CHARINDEX(' ', TWEET, CHARINDEX('#', TWEET)) + 1, LEN(TWEET) - CHARINDEX(' ', TWEET, CHARINDEX('#', TWEET)))
        END AS TWEET
    FROM TAGS
    WHERE CHARINDEX('#', TWEET) > 0
)

-- Final aggregation: count hashtags and return top 3
SELECT HASH_TAG AS HASHTAG, COUNT(*) AS COUNT
FROM TAGS
GROUP BY HASH_TAG
ORDER BY COUNT DESC, HASH_TAG DESC
OFFSET 0 ROWS FETCH NEXT 3 ROWS ONLY;
```

# [3156. Employee Task Duration and Concurrent Tasks](https://leetcode.com/problems/employee-task-duration-and-concurrent-tasks/)
```sql
-- STEP 1: COLLECT ALL UNIQUE TIME POINTS (START AND END) FOR EACH EMPLOYEE
WITH EMPLOYEETIMES AS (
    SELECT DISTINCT EMPLOYEE_ID, START_TIME AS TM FROM TASKS
    UNION
    SELECT DISTINCT EMPLOYEE_ID, END_TIME AS TM FROM TASKS
),

-- STEP 2: CREATE SEGMENTS BETWEEN CONSECUTIVE TIME POINTS USING LEAD()
SEGMENTS AS (
    SELECT
        EMPLOYEE_ID,
        TM AS START_TIME,
        LEAD(TM) OVER (PARTITION BY EMPLOYEE_ID ORDER BY TM) AS END_TIME
    FROM EMPLOYEETIMES
),

-- STEP 3: COUNT HOW MANY TASKS ARE ACTIVE DURING EACH SEGMENT
SEGMENTSCOUNT AS (
    SELECT
        S.EMPLOYEE_ID,
        S.START_TIME,
        S.END_TIME,
        COUNT(*) AS CONCURRENT_COUNT
    FROM SEGMENTS S
    JOIN TASKS T ON S.EMPLOYEE_ID = T.EMPLOYEE_ID
    WHERE S.START_TIME >= T.START_TIME AND S.END_TIME <= T.END_TIME
    GROUP BY S.EMPLOYEE_ID, S.START_TIME, S.END_TIME
)

-- FINAL STEP: AGGREGATE TOTAL TASK HOURS AND MAX CONCURRENCY PER EMPLOYEE
SELECT
    EMPLOYEE_ID,
    FLOOR(SUM(DATEDIFF(SECOND, START_TIME, END_TIME)) / 3600.0) AS TOTAL_TASK_HOURS,
    MAX(CONCURRENT_COUNT) AS MAX_CONCURRENT_TASKS
FROM SEGMENTSCOUNT
GROUP BY EMPLOYEE_ID
ORDER BY EMPLOYEE_ID;
```

# [3188. Find Top Scoring Students II](https://leetcode.com/problems/find-top-scoring-students-ii/)
```sql
-- STEP 1: FILTER STUDENTS WITH AVERAGE GPA >= 2.5
WITH T AS (
    SELECT STUDENT_ID
    FROM ENROLLMENTS
    GROUP BY STUDENT_ID
    HAVING AVG(GPA) >= 2.5
)

-- STEP 2: JOIN WITH STUDENTS AND THEIR MAJOR COURSES
SELECT S.STUDENT_ID
FROM T
JOIN STUDENTS S ON T.STUDENT_ID = S.STUDENT_ID
JOIN COURSES C ON S.MAJOR = C.MAJOR
LEFT JOIN ENROLLMENTS E ON S.STUDENT_ID = E.STUDENT_ID AND C.COURSE_ID = E.COURSE_ID
GROUP BY S.STUDENT_ID
HAVING
    -- ALL MANDATORY COURSES MUST HAVE GRADE 'A'
    SUM(CASE WHEN C.MANDATORY = 'yes' AND E.GRADE = 'A' THEN 1 ELSE 0 END) = 
    SUM(CASE WHEN C.MANDATORY = 'yes' THEN 1 ELSE 0 END)

    -- All optional courses with grades must be 'A' or 'B'
    AND SUM(CASE WHEN C.MANDATORY = 'no' AND E.GRADE IS NOT NULL THEN 1 ELSE 0 END) = 
        SUM(CASE WHEN C.MANDATORY = 'no' AND E.GRADE IN ('A', 'B') THEN 1 ELSE 0 END)

    -- At least 2 optional courses must be graded
    AND SUM(CASE WHEN C.MANDATORY = 'no' AND E.GRADE IS NOT NULL THEN 1 ELSE 0 END) >= 2
ORDER BY S.STUDENT_ID;
```

# [3214. Year on Year Growth Rate](https://leetcode.com/problems/year-on-year-growth-rate/)
```sql
-- Step 1: Aggregate spend per product per year
WITH T AS (
    SELECT 
        PRODUCT_ID,
        YEAR(TRANSACTION_DATE) AS YEAR,
        SUM(SPEND) AS CURR_YEAR_SPEND
    FROM USER_TRANSACTIONS
    GROUP BY PRODUCT_ID, YEAR(TRANSACTION_DATE)
),

-- Step 2: Join current year with previous year for same product
S AS (
    SELECT 
        T1.YEAR,
        T1.PRODUCT_ID,
        T1.CURR_YEAR_SPEND,
        T2.CURR_YEAR_SPEND AS PREV_YEAR_SPEND
    FROM T T1
    LEFT JOIN T T2 
        ON T1.PRODUCT_ID = T2.PRODUCT_ID 
        AND T1.YEAR = T2.YEAR + 1
)

-- Step 3: Calculate YoY growth rate
SELECT 
    *,
    ROUND(
        (CURR_YEAR_SPEND - PREV_YEAR_SPEND) * 100.0 / NULLIF(PREV_YEAR_SPEND, 0), 
        2
    ) AS YOY_RATE
FROM S
ORDER BY PRODUCT_ID, YEAR;
```

# [3236. CEO Subordinate Hierarchy](https://leetcode.com/problems/ceo-subordinate-hierarchy/)
```
Table: Employees
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| employee_id   | int     |
| employee_name | varchar |
| manager_id    | int     |
| salary        | int     |
+---------------+---------+
employee_id is the unique identifier for this table.
manager_id is the employee_id of the employee's manager. The CEO has a NULL manager_id.
Write a solution to find subordinates of the CEO (both direct and indirect), along with their level in the hierarchy and their salary difference from the CEO.

The result should have the following columns:

The query result format is in the following example.

subordinate_id: The employee_id of the subordinate
subordinate_name: The name of the subordinate
hierarchy_level: The level of the subordinate in the hierarchy (1 for direct reports, 2 for their direct reports, and so on)
salary_difference: The difference between the subordinate's salary and the CEO's salary
Return the result table ordered by hierarchy_level ascending, and then by subordinate_id ascending.

The query result format is in the following example.

Example:

Input:

Employees table:
+-------------+----------------+------------+---------+
| employee_id | employee_name  | manager_id | salary  |
+-------------+----------------+------------+---------+
| 1           | Alice          | NULL       | 150000  |
| 2           | Bob            | 1          | 120000  |
| 3           | Charlie        | 1          | 110000  |
| 4           | David          | 2          | 105000  |
| 5           | Eve            | 2          | 100000  |
| 6           | Frank          | 3          | 95000   |
| 7           | Grace          | 3          | 98000   |
| 8           | Helen          | 5          | 90000   |
+-------------+----------------+------------+---------+
Output:
+----------------+------------------+------------------+-------------------+
| subordinate_id | subordinate_name | hierarchy_level  | salary_difference |
+----------------+------------------+------------------+-------------------+
| 2              | Bob              | 1                | -30000            |
| 3              | Charlie          | 1                | -40000            |
| 4              | David            | 2                | -45000            |
| 5              | Eve              | 2                | -50000            |
| 6              | Frank            | 2                | -55000            |
| 7              | Grace            | 2                | -52000            |
| 8              | Helen            | 3                | -60000            |
+----------------+------------------+------------------+-------------------+
Explanation:

Bob and Charlie are direct subordinates of Alice (CEO) and thus have a hierarchy_level of 1.
David and Eve report to Bob, while Frank and Grace report to Charlie, making them second-level subordinates (hierarchy_level 2).
Helen reports to Eve, making Helen a third-level subordinate (hierarchy_level 3).
Salary differences are calculated relative to Alice's salary of 150000.
The result is ordered by hierarchy_level ascending, and then by subordinate_id ascending.
Note: The output is ordered first by hierarchy_level in ascending order, then by subordinate_id in ascending order.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS EMPLOYEES;
GO

CREATE TABLE EMPLOYEES (
    EMPLOYEE_ID   INT PRIMARY KEY,
    EMPLOYEE_NAME VARCHAR(50) NOT NULL,
    MANAGER_ID    INT NULL,
    SALARY        INT NOT NULL
);

GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO EMPLOYEES (EMPLOYEE_ID, EMPLOYEE_NAME, MANAGER_ID, SALARY) VALUES
(1, 'Alice',   NULL, 150000),
(2, 'Bob',     1,    120000),
(3, 'Charlie', 1,    110000),
(4, 'David',   2,    105000),
(5, 'Eve',     2,    100000),
(6, 'Frank',   3,     95000),
(7, 'Grace',   3,     98000),
(8, 'Helen',   5,     90000);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM EMPLOYEES;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
WITH HIERARCHY AS (
    -- BASE CASE: CEO
    SELECT
        0 AS HIERARCHY_LEVEL,
        E.EMPLOYEE_ID AS EMPLOYEE_ID,
        E.EMPLOYEE_NAME AS EMPLOYEE_NAME,
        0 AS SALARY_DIFFERENCE_FROM_CEO,
        E.SALARY AS CEO_SALARY
    FROM EMPLOYEES E
    WHERE E.MANAGER_ID IS NULL

    UNION ALL

    -- RECURSIVE CASE: SUBORDINATES
    SELECT
        H.HIERARCHY_LEVEL + 1 AS HIERARCHY_LEVEL,
        E.EMPLOYEE_ID AS EMPLOYEE_ID,
        E.EMPLOYEE_NAME AS EMPLOYEE_NAME,
        E.SALARY - H.CEO_SALARY AS SALARY_DIFFERENCE_FROM_CEO,
        H.CEO_SALARY
    FROM EMPLOYEES E
    INNER JOIN HIERARCHY H
        ON E.MANAGER_ID = H.EMPLOYEE_ID
)
SELECT 
    EMPLOYEE_ID AS SUBORDINATE_ID,
    EMPLOYEE_NAME AS SUBORDINATE_NAME,
	HIERARCHY_LEVEL AS HIERARCHY_LEVEL,
    SALARY_DIFFERENCE_FROM_CEO AS SALARY_DIFFERENCE
FROM HIERARCHY
WHERE HIERARCHY_LEVEL <> 0
ORDER BY HIERARCHY_LEVEL, EMPLOYEE_NAME;
```

# [3268. Find Overlapping Shifts II](https://leetcode.com/problems/find-overlapping-shifts-ii/)
```sql
-- STEP 1: COLLECT ALL UNIQUE TIME POINTS (START AND END TIMES)
WITH T AS (
    SELECT DISTINCT EMPLOYEE_ID, START_TIME AS ST
    FROM EMPLOYEESHIFTS
    UNION
    SELECT DISTINCT EMPLOYEE_ID, END_TIME AS ST
    FROM EMPLOYEESHIFTS
),

-- STEP 2: CREATE TIME INTERVALS BETWEEN EACH TIME POINT
P AS (
    SELECT
        EMPLOYEE_ID,
        ST,
        LEAD(ST) OVER (
            PARTITION BY EMPLOYEE_ID
            ORDER BY ST
        ) AS ED
    FROM T
),

-- STEP 3: COUNT HOW MANY SHIFTS OVERLAP EACH TIME INTERVAL
S AS (
    SELECT
        P.EMPLOYEE_ID,
        P.ST,
        P.ED,
        COUNT(*) AS CONCURRENT_COUNT
    FROM P
    INNER JOIN EMPLOYEESHIFTS E ON P.EMPLOYEE_ID = E.EMPLOYEE_ID
    WHERE P.ST >= E.START_TIME AND P.ED <= E.END_TIME
    GROUP BY P.EMPLOYEE_ID, P.ST, P.ED
),

-- STEP 4: CALCULATE TOTAL OVERLAPPING DURATION BETWEEN SHIFTS
U AS (
    SELECT
        T1.EMPLOYEE_ID,
        SUM(
            DATEDIFF(
                MINUTE,
                T2.START_TIME,
                CASE
                    WHEN T1.END_TIME < T2.END_TIME THEN T1.END_TIME
                    ELSE T2.END_TIME
                END
            )
        ) AS TOTAL_OVERLAP_DURATION
    FROM EMPLOYEESHIFTS T1
    JOIN EMPLOYEESHIFTS T2
        ON T1.EMPLOYEE_ID = T2.EMPLOYEE_ID
        AND T1.START_TIME < T2.START_TIME
        AND T1.END_TIME > T2.START_TIME
    GROUP BY T1.EMPLOYEE_ID
)

-- STEP 5: FINAL OUTPUT - MAX OVERLAPPING SHIFTS AND AVERAGE OVERLAP DURATION
SELECT
    S.EMPLOYEE_ID,
    MAX(S.CONCURRENT_COUNT) AS MAX_OVERLAPPING_SHIFTS,
    ISNULL(AVG(U.TOTAL_OVERLAP_DURATION), 0) AS TOTAL_OVERLAP_DURATION
FROM S
LEFT JOIN U ON S.EMPLOYEE_ID = U.EMPLOYEE_ID
GROUP BY S.EMPLOYEE_ID
ORDER BY S.EMPLOYEE_ID;
```

# [3368. First Letter Capitalization](https://leetcode.com/problems/first-letter-capitalization/)
```sql
-- Step 1: Recursive CTE to capitalize each word in content_text
WITH CAPITALIZED_WORDS AS (
    -- ANCHOR MEMBER: PROCESS THE FIRST WORD
    SELECT
        CONTENT_ID,
        CONTENT_TEXT,
        CAST(LEFT(CONTENT_TEXT, CHARINDEX(' ', CONTENT_TEXT + ' ') - 1) AS VARCHAR(MAX)) AS WORD,
        CAST(
            LTRIM(SUBSTRING(
                CONTENT_TEXT,
                CHARINDEX(' ', CONTENT_TEXT + ' ') + 1,
                LEN(CONTENT_TEXT)
            )) AS VARCHAR(MAX)
        ) AS REMAINING_TEXT,
        CAST(
            UPPER(LEFT(LEFT(CONTENT_TEXT, CHARINDEX(' ', CONTENT_TEXT + ' ') - 1), 1)) +
            LOWER(SUBSTRING(LEFT(CONTENT_TEXT, CHARINDEX(' ', CONTENT_TEXT + ' ') - 1), 2, LEN(CONTENT_TEXT)))
            AS VARCHAR(MAX)
        ) AS PROCESSED_WORD
    FROM USER_CONTENT

    UNION ALL

    -- RECURSIVE MEMBER: PROCESS NEXT WORD AND APPEND TO PROCESSED_WORD
    SELECT
        CW.CONTENT_ID,
        CW.CONTENT_TEXT,
        LEFT(CW.REMAINING_TEXT, CHARINDEX(' ', CW.REMAINING_TEXT + ' ') - 1),
        LTRIM(SUBSTRING(
            CW.REMAINING_TEXT,
            CHARINDEX(' ', CW.REMAINING_TEXT + ' ') + 1,
            LEN(CW.REMAINING_TEXT)
        )),
        CW.PROCESSED_WORD + ' ' +
        UPPER(LEFT(LEFT(CW.REMAINING_TEXT, CHARINDEX(' ', CW.REMAINING_TEXT + ' ') - 1), 1)) +
        LOWER(SUBSTRING(LEFT(CW.REMAINING_TEXT, CHARINDEX(' ', CW.REMAINING_TEXT + ' ') - 1), 2, LEN(CW.REMAINING_TEXT)))
    FROM CAPITALIZED_WORDS CW
    WHERE CW.REMAINING_TEXT <> ''
)

-- STEP 2: FINAL OUTPUT WITH ORIGINAL AND CONVERTED TEXT
SELECT
    CONTENT_ID,
    CONTENT_TEXT AS ORIGINAL_TEXT,
    MAX(PROCESSED_WORD) AS CONVERTED_TEXT
FROM CAPITALIZED_WORDS
GROUP BY CONTENT_ID, CONTENT_TEXT;
```

# [3374. First Letter Capitalization II](https://leetcode.com/problems/first-letter-capitalization-ii/)
```sql
-- STEP 1: RECURSIVELY SPLIT CONTENT_TEXT INTO WORDS USING SUBSTRING
WITH WORDS AS (
    SELECT
        CONTENT_ID,
        SUBSTRING(CONTENT_TEXT, 1, CHARINDEX(' ', CONTENT_TEXT + ' ') - 1) AS WORD, -- FIRST WORD
        SUBSTRING(CONTENT_TEXT, CHARINDEX(' ', CONTENT_TEXT + ' ') + 1, LEN(CONTENT_TEXT)) AS REMAINING_TEXT, -- REMAINING TEXT
        1 AS TOKEN_INDEX
    FROM USER_CONTENT
    WHERE CONTENT_TEXT IS NOT NULL

    UNION ALL

    SELECT
        CONTENT_ID,
        SUBSTRING(REMAINING_TEXT, 1, CHARINDEX(' ', REMAINING_TEXT + ' ') - 1), -- NEXT WORD
        SUBSTRING(REMAINING_TEXT, CHARINDEX(' ', REMAINING_TEXT + ' ') + 1, LEN(REMAINING_TEXT)), -- REMAINING TEXT
        TOKEN_INDEX + 1
    FROM WORDS
    WHERE REMAINING_TEXT <> ''
),

-- STEP 2: CAPITALIZE EACH WORD, HANDLE HYPHENATED WORDS
FORMATTED AS (
    SELECT
        CONTENT_ID,
        TOKEN_INDEX,
        CASE
            WHEN WORD LIKE '%-%' THEN
                -- CAPITALIZE BOTH PARTS OF HYPHENATED WORD
                UPPER(SUBSTRING(WORD, 1, 1)) +
                LOWER(SUBSTRING(WORD, 2, CHARINDEX('-', WORD) - 2)) +
                '-' +
                UPPER(SUBSTRING(WORD, CHARINDEX('-', WORD) + 1, 1)) +
                LOWER(SUBSTRING(WORD, CHARINDEX('-', WORD) + 2, LEN(WORD)))
            ELSE
                -- CAPITALIZE REGULAR WORD
                UPPER(SUBSTRING(WORD, 1, 1)) + LOWER(SUBSTRING(WORD, 2, LEN(WORD)))
        END AS FORMATTED_WORD
    FROM WORDS
),

-- STEP 3: RECONSTRUCT SENTENCE USING STRING_AGG
CONVERTED AS (
    SELECT
        CONTENT_ID,
        STRING_AGG(FORMATTED_WORD, ' ') WITHIN GROUP (ORDER BY TOKEN_INDEX) AS CONVERTED_TEXT
    FROM FORMATTED
    GROUP BY CONTENT_ID
)

-- FINAL OUTPUT
SELECT
    U.CONTENT_ID,
    U.CONTENT_TEXT AS ORIGINAL_TEXT,
    C.CONVERTED_TEXT
FROM USER_CONTENT U
JOIN CONVERTED C ON U.CONTENT_ID = C.CONTENT_ID;
```

# [3384. Team Dominance by Pass Success](https://leetcode.com/problems/team-dominance-by-pass-success/)
```sql
-- COMMON TABLE EXPRESSION TO PREPARE PASS DATA WITH TEAM AND HALF INFO
WITH T AS (
    SELECT
        T1.TEAM_NAME,
        -- DETERMINE HALF: 1 FOR FIRST HALF, 2 FOR SECOND HALF
        CASE 
            WHEN TRY_CAST(P.TIME_STAMP AS TIME) <= '00:45:00' THEN 1 
            ELSE 2 
        END AS HALF_NUMBER,
        -- +1 IF PASS WITHIN SAME TEAM, -1 IF TO OPPONENT
        CASE 
            WHEN T1.TEAM_NAME = T2.TEAM_NAME THEN 1 
            ELSE -1 
        END AS DOMINANCE
    FROM PASSES P
    JOIN TEAMS T1 ON P.PASS_FROM = T1.PLAYER_ID
    JOIN TEAMS T2 ON P.PASS_TO = T2.PLAYER_ID
)

-- AGGREGATE DOMINANCE PER TEAM PER HALF
SELECT 
    TEAM_NAME, 
    HALF_NUMBER, 
    SUM(DOMINANCE) AS DOMINANCE
FROM T
GROUP BY TEAM_NAME, HALF_NUMBER
ORDER BY TEAM_NAME, HALF_NUMBER;
```

# [3390. Longest Team Pass Streak](https://leetcode.com/problems/longest-team-pass-streak/)
```sql
WITH PASSESWITHTEAMS AS (
    SELECT
        P.PASS_FROM,
        P.PASS_TO,
        T1.TEAM_NAME AS TEAM_FROM,
        T2.TEAM_NAME AS TEAM_TO,
        CASE 
            WHEN T1.TEAM_NAME = T2.TEAM_NAME THEN 1 
            ELSE 0 
        END AS SAME_TEAM_FLAG,
        P.TIME_STAMP
    FROM PASSES P
    JOIN TEAMS T1 ON P.PASS_FROM = T1.PLAYER_ID
    JOIN TEAMS T2 ON P.PASS_TO = T2.PLAYER_ID
),
STREAKGROUPS AS (
    SELECT
        TEAM_FROM AS TEAM_NAME,
        TIME_STAMP,
        SAME_TEAM_FLAG,
        SUM(CASE WHEN SAME_TEAM_FLAG = 0 THEN 1 ELSE 0 END) 
            OVER (PARTITION BY TEAM_FROM ORDER BY TIME_STAMP ROWS UNBOUNDED PRECEDING) AS GROUP_ID
    FROM PASSESWITHTEAMS
),
STREAKLENGTHS AS (
    SELECT
        TEAM_NAME,
        GROUP_ID,
        COUNT(*) AS STREAK_LENGTH
    FROM STREAKGROUPS
    WHERE SAME_TEAM_FLAG = 1
    GROUP BY TEAM_NAME, GROUP_ID
),
LONGESTSTREAKS AS (
    SELECT
        TEAM_NAME,
        MAX(STREAK_LENGTH) AS LONGEST_STREAK
    FROM STREAKLENGTHS
    GROUP BY TEAM_NAME
)
SELECT
    TEAM_NAME,
    LONGEST_STREAK
FROM LONGESTSTREAKS
ORDER BY TEAM_NAME;
```

# [3401. Find Circular Gift Exchange Chains](https://leetcode.com/problems/find-circular-gift-exchange-chains/)
```sql
-- BUILD GIFT CHAINS RECURSIVELY
WITH RECURSIVE CHAINS AS (
  -- BASE CASE: EACH GIVER STARTS A CHAIN
  SELECT *, GIVER_ID AS START_ID
  FROM SECRETSANTA

  UNION ALL

  -- RECURSIVE CASE: EXTEND CHAIN IF NO CYCLE
  SELECT S.*, C.START_ID
  FROM SECRETSANTA S
  JOIN CHAINS C
    ON S.GIVER_ID = C.RECEIVER_ID
   AND S.GIVER_ID != C.START_ID
),

-- SUMMARIZE EACH CHAIN BY START_ID
CHAINSUMMARY AS (
  SELECT
    START_ID,
    COUNT(*) AS CHAIN_LENGTH,
    SUM(GIFT_VALUE) AS TOTAL_GIFT_VALUE
  FROM CHAINS
  GROUP BY START_ID
),

-- GET UNIQUE CHAIN PATTERNS
UNIQUECHAINS AS (
  SELECT DISTINCT CHAIN_LENGTH, TOTAL_GIFT_VALUE
  FROM CHAINSUMMARY
)

-- RANK CHAINS BY LENGTH AND VALUE
SELECT
  RANK() OVER (
    ORDER BY CHAIN_LENGTH DESC, TOTAL_GIFT_VALUE DESC
  ) AS CHAIN_ID,
  CHAIN_LENGTH,
  TOTAL_GIFT_VALUE
FROM UNIQUECHAINS;
```

# [3451. Find Invalid IP Addresses](https://leetcode.com/problems/find-invalid-ip-addresses/)
```sql
SELECT L.IP,
       COUNT(*) AS INVALID_COUNT
FROM LOGS L
WHERE (
    -- IP MUST HAVE EXACTLY 4 PARTS
    (SELECT COUNT(*) FROM STRING_SPLIT(L.IP, '.')) <> 4
    OR
    -- ANY PART > 255
    EXISTS (
        SELECT 1
        FROM STRING_SPLIT(L.IP, '.')
        WHERE TRY_CAST([VALUE] AS INT) > 255
    )
    OR
    -- ANY PART HAS LEADING ZEROS
    EXISTS (
        SELECT 1
        FROM STRING_SPLIT(L.IP, '.')
        WHERE LEN([VALUE]) > 1 AND LEFT([VALUE], 1) = '0'
    )
)
GROUP BY L.IP
ORDER BY INVALID_COUNT DESC, L.IP DESC;
```

# [3482. Analyze Organization Hierarchy](https://leetcode.com/problems/analyze-organization-hierarchy/)
```sql
WITH HIERARCHY AS (
    SELECT EMPLOYEE_ID, EMPLOYEE_NAME, MANAGER_ID, SALARY, DEPARTMENT, 1 AS LEVEL
    FROM EMPLOYEES
    WHERE MANAGER_ID IS NULL

    UNION ALL

    SELECT E.EMPLOYEE_ID, E.EMPLOYEE_NAME, E.MANAGER_ID, E.SALARY, E.DEPARTMENT, H.LEVEL + 1
    FROM EMPLOYEES E
    INNER JOIN HIERARCHY H ON E.MANAGER_ID = H.EMPLOYEE_ID
),
TEAMSIZE AS (
    SELECT EMPLOYEE_ID AS MANAGER_ID, EMPLOYEE_ID AS MEMBER_ID
    FROM EMPLOYEES

    UNION ALL

    SELECT TS.MANAGER_ID, E.EMPLOYEE_ID
    FROM TEAMSIZE TS
    INNER JOIN EMPLOYEES E ON E.MANAGER_ID = TS.MEMBER_ID
),
BUDGET AS (
    SELECT TS.MANAGER_ID, COUNT(*) - 1 AS TEAM_SIZE, SUM(E.SALARY) AS BUDGET
    FROM TEAMSIZE TS
    INNER JOIN EMPLOYEES E ON TS.MEMBER_ID = E.EMPLOYEE_ID
    GROUP BY TS.MANAGER_ID
)
SELECT 
    E.EMPLOYEE_ID,
    E.EMPLOYEE_NAME,
    H.LEVEL,
    B.TEAM_SIZE,
    B.BUDGET
FROM EMPLOYEES E
INNER JOIN HIERARCHY H ON E.EMPLOYEE_ID = H.EMPLOYEE_ID
INNER JOIN BUDGET B ON E.EMPLOYEE_ID = B.MANAGER_ID
ORDER BY H.LEVEL ASC, B.BUDGET DESC, E.EMPLOYEE_NAME ASC;
```

# [3554. Find Category Recommendation Pairs](https://leetcode.com/problems/find-category-recommendation-pairs/)
```
Table: ProductPurchases
+-------------+------+
| Column Name | Type |
+-------------+------+
| user_id     | int  |
| product_id  | int  |
| quantity    | int  |
+-------------+------+
(user_id, product_id) is the unique identifier for this table.
Each row represents a purchase of a product by a user in a specific quantity.

Table: ProductInfo
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_id  | int     |
| category    | varchar |
| price       | decimal |
+-------------+---------+
product_id is the unique identifier for this table.
Each row assigns a category and price to a product.

Amazon wants to understand shopping patterns across product categories. Write a solution to:

Find all category pairs (where category1 < category2)
For each category pair, determine the number of unique customers who purchased products from both categories

A category pair is considered reportable if at least 3 different customers have purchased products from both categories.
Return the result table of reportable category pairs ordered by customer_count in descending order, and in case of a tie, by category1 in ascending order lexicographically, and then by category2 in ascending order.
The result format is in the following example.

Example:

Input:
ProductPurchases table:
+---------+------------+----------+
| user_id | product_id | quantity |
+---------+------------+----------+
| 1       | 101        | 2        |
| 1       | 102        | 1        |
| 1       | 201        | 3        |
| 1       | 301        | 1        |
| 2       | 101        | 1        |
| 2       | 102        | 2        |
| 2       | 103        | 1        |
| 2       | 201        | 5        |
| 3       | 101        | 2        |
| 3       | 103        | 1        |
| 3       | 301        | 4        |
| 3       | 401        | 2        |
| 4       | 101        | 1        |
| 4       | 201        | 3        |
| 4       | 301        | 1        |
| 4       | 401        | 2        |
| 5       | 102        | 2        |
| 5       | 103        | 1        |
| 5       | 201        | 2        |
| 5       | 202        | 3        |
+---------+------------+----------+

ProductInfo table:
+------------+-------------+-------+
| product_id | category    | price |
+------------+-------------+-------+
| 101        | Electronics | 100   |
| 102        | Books       | 20    |
| 103        | Books       | 35    |
| 201        | Clothing    | 45    |
| 202        | Clothing    | 60    |
| 301        | Sports      | 75    |
| 401        | Kitchen     | 50    |
+------------+-------------+-------+

Output:
+-------------+-------------+----------------+
| category1   | category2   | customer_count |
+-------------+-------------+----------------+
| Books       | Clothing    | 3              |
| Books       | Electronics | 3              |
| Clothing    | Electronics | 3              |
| Electronics | Sports      | 3              |
+-------------+-------------+----------------+

Explanation:
Books-Clothing:

User 1 purchased products from Books (102) and Clothing (201)
User 2 purchased products from Books (102, 103) and Clothing (201)
User 5 purchased products from Books (102, 103) and Clothing (201, 202)
Total: 3 customers purchased from both categories

Books-Electronics:

User 1 purchased products from Books (102) and Electronics (101)
User 2 purchased products from Books (102, 103) and Electronics (101)
User 3 purchased products from Books (103) and Electronics (101)
Total: 3 customers purchased from both categories

Clothing-Electronics:

User 1 purchased products from Clothing (201) and Electronics (101)
User 2 purchased products from Clothing (201) and Electronics (101)
User 4 purchased products from Clothing (201) and Electronics (101)
Total: 3 customers purchased from both categories

Electronics-Sports:

User 1 purchased products from Electronics (101) and Sports (301)
User 3 purchased products from Electronics (101) and Sports (301)
User 4 purchased products from Electronics (101) and Sports (301)
Total: 3 customers purchased from both categories

Other category pairs like Clothing-Sports (only 2 customers: Users 1 and 4) and Books-Kitchen (only 1 customer: User 3) have fewer than 3 shared customers and are not included in the result.

The result is ordered by customer_count in descending order. Since all pairs have the same customer_count of 3, they are ordered by category1 (then category2) in ascending order.
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS PRODUCTINFO;
DROP TABLE IF EXISTS PRODUCTPURCHASES;
GO

CREATE TABLE PRODUCTINFO (
    PRODUCT_ID INT PRIMARY KEY, 
    CATEGORY   VARCHAR(50),
    PRICE      DECIMAL(10,2)
);

CREATE TABLE PRODUCTPURCHASES (
    USER_ID    INT,                  
    PRODUCT_ID INT,                   
    QUANTITY   INT
);
GO

/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO PRODUCTINFO (PRODUCT_ID, CATEGORY, PRICE) VALUES
(101, 'Electronics', 100),
(102, 'Books', 20),
(103, 'Books', 35),
(201, 'Clothing', 45),
(202, 'Clothing', 60),
(301, 'Sports', 75),
(401, 'Kitchen', 50);
GO

INSERT INTO PRODUCTPURCHASES (USER_ID, PRODUCT_ID, QUANTITY) VALUES
(1, 101, 2),
(1, 102, 1),
(1, 201, 3),
(1, 301, 1),
(2, 101, 1),
(2, 102, 2),
(2, 103, 1),
(2, 201, 5),
(3, 101, 2),
(3, 103, 1),
(3, 301, 4),
(3, 401, 2),
(4, 101, 1),
(4, 201, 3),
(4, 301, 1),
(4, 401, 2),
(5, 102, 2),
(5, 103, 1),
(5, 201, 2),
(5, 202, 3);
GO

/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM PRODUCTINFO;
SELECT * FROM PRODUCTPURCHASES;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
WITH USERCATEGORY AS (
    SELECT DISTINCT PP.USER_ID, PI.CATEGORY
    FROM PRODUCTPURCHASES PP
    JOIN PRODUCTINFO PI ON PP.PRODUCT_ID = PI.PRODUCT_ID
),
CATEGORYPAIRS AS (
    SELECT 
        UC1.USER_ID,
        UC1.CATEGORY AS CATEGORY1,
        UC2.CATEGORY AS CATEGORY2
    FROM USERCATEGORY UC1
    JOIN USERCATEGORY UC2 
        ON UC1.USER_ID = UC2.USER_ID 
        AND UC1.CATEGORY < UC2.CATEGORY
)
SELECT 
    CATEGORY1,
    CATEGORY2,
    COUNT(DISTINCT USER_ID) AS CUSTOMER_COUNT
FROM CATEGORYPAIRS
GROUP BY CATEGORY1, CATEGORY2
HAVING COUNT(DISTINCT USER_ID) >= 3
ORDER BY CUSTOMER_COUNT DESC, CATEGORY1 ASC, CATEGORY2 ASC;
```

# [3617. Find Students with Study Spiral Pattern](https://leetcode.com/problems/find-students-with-study-spiral-pattern/)
```
Table: students
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| student_id   | int     |
| student_name | varchar |
| major        | varchar |
+--------------+---------+
student_id is the unique identifier for this table.
Each row contains information about a student and their academic major.

Table: study_sessions
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| session_id    | int     |
| student_id    | int     |
| subject       | varchar |
| session_date  | date    |
| hours_studied | decimal |
+---------------+---------+
session_id is the unique identifier for this table.
Each row represents a study session by a student for a specific subject.
Write a solution to find students who follow the Study Spiral Pattern - students who consistently study multiple subjects in a rotating cycle.

A Study Spiral Pattern means a student studies at least 3 different subjects in a repeating sequence
The pattern must repeat for at least 2 complete cycles (minimum 6 study sessions)
Sessions must be consecutive dates with no gaps longer than 2 days between sessions
Calculate the cycle length (number of different subjects in the pattern)
Calculate the total study hours across all sessions in the pattern
Only include students with cycle length of at least 3 subjects
Return the result table ordered by cycle length in descending order, then by total study hours in descending order.

The result format is in the following example.
Example:

Input:

students table:
+------------+--------------+------------------+
| student_id | student_name | major            |
+------------+--------------+------------------+
| 1          | Alice Chen   | Computer Science |
| 2          | Bob Johnson  | Mathematics      |
| 3          | Carol Davis  | Physics          |
| 4          | David Wilson | Chemistry        |
| 5          | Emma Brown   | Biology          |
+------------+--------------+------------------+
study_sessions table:
+------------+------------+------------+--------------+---------------+
| session_id | student_id | subject    | session_date | hours_studied |
+------------+------------+------------+--------------+---------------+
| 1          | 1          | Math       | 2023-10-01   | 2.5           |
| 2          | 1          | Physics    | 2023-10-02   | 3.0           |
| 3          | 1          | Chemistry  | 2023-10-03   | 2.0           |
| 4          | 1          | Math       | 2023-10-04   | 2.5           |
| 5          | 1          | Physics    | 2023-10-05   | 3.0           |
| 6          | 1          | Chemistry  | 2023-10-06   | 2.0           |
| 7          | 2          | Algebra    | 2023-10-01   | 4.0           |
| 8          | 2          | Calculus   | 2023-10-02   | 3.5           |
| 9          | 2          | Statistics | 2023-10-03   | 2.5           |
| 10         | 2          | Geometry   | 2023-10-04   | 3.0           |
| 11         | 2          | Algebra    | 2023-10-05   | 4.0           |
| 12         | 2          | Calculus   | 2023-10-06   | 3.5           |
| 13         | 2          | Statistics | 2023-10-07   | 2.5           |
| 14         | 2          | Geometry   | 2023-10-08   | 3.0           |
| 15         | 3          | Biology    | 2023-10-01   | 2.0           |
| 16         | 3          | Chemistry  | 2023-10-02   | 2.5           |
| 17         | 3          | Biology    | 2023-10-03   | 2.0           |
| 18         | 3          | Chemistry  | 2023-10-04   | 2.5           |
| 19         | 4          | Organic    | 2023-10-01   | 3.0           |
| 20         | 4          | Physical   | 2023-10-05   | 2.5           |
+------------+------------+------------+--------------+---------------+
Output:
+------------+--------------+------------------+--------------+-------------------+
| student_id | student_name | major            | cycle_length | total_study_hours |
+------------+--------------+------------------+--------------+-------------------+
| 2          | Bob Johnson  | Mathematics      | 4            | 26.0              |
| 1          | Alice Chen   | Computer Science | 3            | 15.0              |
+------------+--------------+------------------+--------------+-------------------+
Explanation:

Alice Chen (student_id = 1):
Study sequence: Math → Physics → Chemistry → Math → Physics → Chemistry
Pattern: 3 subjects (Math, Physics, Chemistry) repeating for 2 complete cycles
Consecutive dates: Oct 1-6 with no gaps > 2 days
Cycle length: 3 subjects
Total hours: 2.5 + 3.0 + 2.0 + 2.5 + 3.0 + 2.0 = 15.0 hours
Bob Johnson (student_id = 2):
Study sequence: Algebra → Calculus → Statistics → Geometry → Algebra → Calculus → Statistics → Geometry
Pattern: 4 subjects (Algebra, Calculus, Statistics, Geometry) repeating for 2 complete cycles
Consecutive dates: Oct 1-8 with no gaps > 2 days
Cycle length: 4 subjects
Total hours: 4.0 + 3.5 + 2.5 + 3.0 + 4.0 + 3.5 + 2.5 + 3.0 = 26.0 hours
Students not included:
Carol Davis (student_id = 3): Only 2 subjects (Biology, Chemistry) - doesn't meet minimum 3 subjects requirement
David Wilson (student_id = 4): Only 2 study sessions with a 4-day gap - doesn't meet consecutive dates requirement
Emma Brown (student_id = 5): No study sessions recorded
The result table is ordered by cycle_length in descending order, then by total_study_hours in descending order.
```
```sql
WITH GEN_SPRL_ID AS (
  SELECT 
    STUDENT_ID,
    SUBJECT,
    SESSION_ID, 
    HOURS_STUDIED, 
    ROW_NUMBER() OVER (PARTITION BY STUDENT_ID, SUBJECT ORDER BY SESSION_DATE) AS SPRL_ID, 
    DATEDIFF(DAY, LAG(SESSION_DATE) OVER (PARTITION BY STUDENT_ID ORDER BY SESSION_DATE),SESSION_DATE) AS DAY_GAP 
  FROM 
    STUDY_SESSIONS
), 
SUBJECT_GRP AS (
  SELECT 
    STUDENT_ID,
    SPRL_ID, 
    SUM(HOURS_STUDIED) AS HOURS_STUDIED, 
    COUNT(DISTINCT SUBJECT) AS CYCLE_LENGTH, 
    STRING_AGG(SUBJECT, ',') WITHIN GROUP (ORDER BY SESSION_ID ASC) AS SUBJECT_GRP 
  FROM 
    GEN_SPRL_ID 
  GROUP BY 
    STUDENT_ID,SPRL_ID 
  HAVING 
    COUNT(DISTINCT SUBJECT) >= 3 AND MAX(DAY_GAP) < 3
), 
PRE_RESULT AS (
  SELECT 
    STUDENT_ID, 
    SUBJECT_GRP, 
    CYCLE_LENGTH, 
    SUM(HOURS_STUDIED) AS TOTAL_STUDY_HOURS 
  FROM 
    SUBJECT_GRP 
  GROUP BY 
    STUDENT_ID,SUBJECT_GRP,CYCLE_LENGTH 
  HAVING 
    COUNT(*)> 1
) 
SELECT 
  S.STUDENT_ID,S.STUDENT_NAME,S.MAJOR,F.CYCLE_LENGTH,F.TOTAL_STUDY_HOURS 
FROM 
  PRE_RESULT F 
  JOIN STUDENTS S ON S.STUDENT_ID = F.STUDENT_ID 
ORDER BY 
  CYCLE_LENGTH DESC,TOTAL_STUDY_HOURS DESC;
```
# [3673. Find Zombie Sessions](https://leetcode.com/problems/find-zombie-sessions/)
```
Table: app_events
+------------------+----------+
| Column Name      | Type     | 
+------------------+----------+
| event_id         | int      |
| user_id          | int      |
| event_timestamp  | datetime |
| event_type       | varchar  |
| session_id       | varchar  |
| event_value      | int      |
+------------------+----------+
event_id is the unique identifier for this table.
event_type can be app_open, click, scroll, purchase, or app_close.
session_id groups events within the same user session.
event_value represents: for purchase - amount in dollars, for scroll - pixels scrolled, for others - NULL.
Write a solution to identify zombie sessions, sessions where users appear active but show abnormal behavior patterns. A session is considered a zombie session if it meets ALL the following criteria:

The session duration is more than 30 minutes.
Has at least 5 scroll events.
The click-to-scroll ratio is less than 0.20 .
No purchases were made during the session.
Return the result table ordered by scroll_count in descending order, then by session_id in ascending order.
The result format is in the following example.
Example:

Input:

app_events table:

+----------+---------+---------------------+------------+------------+-------------+
| event_id | user_id | event_timestamp     | event_type | session_id | event_value |
+----------+---------+---------------------+------------+------------+-------------+
| 1        | 201     | 2024-03-01 10:00:00 | app_open   | S001       | NULL        |
| 2        | 201     | 2024-03-01 10:05:00 | scroll     | S001       | 500         |
| 3        | 201     | 2024-03-01 10:10:00 | scroll     | S001       | 750         |
| 4        | 201     | 2024-03-01 10:15:00 | scroll     | S001       | 600         |
| 5        | 201     | 2024-03-01 10:20:00 | scroll     | S001       | 800         |
| 6        | 201     | 2024-03-01 10:25:00 | scroll     | S001       | 550         |
| 7        | 201     | 2024-03-01 10:30:00 | scroll     | S001       | 900         |
| 8        | 201     | 2024-03-01 10:35:00 | app_close  | S001       | NULL        |
| 9        | 202     | 2024-03-01 11:00:00 | app_open   | S002       | NULL        |
| 10       | 202     | 2024-03-01 11:02:00 | click      | S002       | NULL        |
| 11       | 202     | 2024-03-01 11:05:00 | scroll     | S002       | 400         |
| 12       | 202     | 2024-03-01 11:08:00 | click      | S002       | NULL        |
| 13       | 202     | 2024-03-01 11:10:00 | scroll     | S002       | 350         |
| 14       | 202     | 2024-03-01 11:15:00 | purchase   | S002       | 50          |
| 15       | 202     | 2024-03-01 11:20:00 | app_close  | S002       | NULL        |
| 16       | 203     | 2024-03-01 12:00:00 | app_open   | S003       | NULL        |
| 17       | 203     | 2024-03-01 12:10:00 | scroll     | S003       | 1000        |
| 18       | 203     | 2024-03-01 12:20:00 | scroll     | S003       | 1200        |
| 19       | 203     | 2024-03-01 12:25:00 | click      | S003       | NULL        |
| 20       | 203     | 2024-03-01 12:30:00 | scroll     | S003       | 800         |
| 21       | 203     | 2024-03-01 12:40:00 | scroll     | S003       | 900         |
| 22       | 203     | 2024-03-01 12:50:00 | scroll     | S003       | 1100        |
| 23       | 203     | 2024-03-01 13:00:00 | app_close  | S003       | NULL        |
| 24       | 204     | 2024-03-01 14:00:00 | app_open   | S004       | NULL        |
| 25       | 204     | 2024-03-01 14:05:00 | scroll     | S004       | 600         |
| 26       | 204     | 2024-03-01 14:08:00 | scroll     | S004       | 700         |
| 27       | 204     | 2024-03-01 14:10:00 | click      | S004       | NULL        |
| 28       | 204     | 2024-03-01 14:12:00 | app_close  | S004       | NULL        |
+----------+---------+---------------------+------------+------------+-------------+
Output:

+------------+---------+--------------------------+--------------+
| session_id | user_id | session_duration_minutes | scroll_count |
+------------+---------+--------------------------+--------------+
| S001       | 201     | 35                       | 6            |
+------------+---------+--------------------------+--------------+
Explanation:

Session S001 (User 201):
- Duration: 10:00:00 to 10:35:00 = 35 minutes (more than 30) 
- Scroll events: 6 (at least 5) 
- Click events: 0
 Click-to-scroll ratio: 0/6 = 0.00 (less than 0.20) 
- Purchases: 0 (no purchases) 
S001 is a zombie session (meets all criteria)

Session S002 (User 202):
- Duration: 11:00:00 to 11:20:00 = 20 minutes (less than 30) 
- Has a purchase event 
- S002 is not a zombie session 
- Session S003 (User 203):
- Duration: 12:00:00 to 13:00:00 = 60 minutes (more than 30) 
- Scroll events: 5 (at least 5) 
- Click events: 1
- Click-to-scroll ratio: 1/5 = 0.20 (not less than 0.20) 
- Purchases: 0 (no purchases) 
S003 is not a zombie session (click-to-scroll ratio equals 0.20, needs to be less)

Session S004 (User 204):
- Duration: 14:00:00 to 14:12:00 = 12 minutes (less than 30) 
- Scroll events: 2 (less than 5) 
S004  is not a zombie session 
The result table is ordered by scroll_count in descending order, then by session_id in ascending order.
```
```sql
SELECT 
  SESSION_ID, 
  MAX(USER_ID) AS USER_ID, 
  DATEDIFF(
    MINUTE, 
    MIN(
      CASE WHEN EVENT_TYPE = 'app_open' THEN EVENT_TIMESTAMP END
    ), 
    MAX(
      CASE WHEN EVENT_TYPE = 'app_close' THEN EVENT_TIMESTAMP END
    )
  ) AS SESSION_DURATION_MINUTES, 
  COUNT(
    CASE WHEN EVENT_TYPE = 'scroll' THEN 1 END
  ) AS SCROLL_COUNT 
FROM 
  APP_EVENTS 
GROUP BY 
  SESSION_ID 
HAVING 
  DATEDIFF(
    MINUTE, 
    MIN(
      CASE WHEN EVENT_TYPE = 'app_open' THEN EVENT_TIMESTAMP END
    ), 
    MAX(
      CASE WHEN EVENT_TYPE = 'app_close' THEN EVENT_TIMESTAMP END
    )
  ) > 30 
  AND COUNT(
    CASE WHEN EVENT_TYPE = 'scroll' THEN 1 END
  ) >= 5 
  AND COUNT(
    CASE WHEN EVENT_TYPE = 'click' THEN 1 END
  ) * 1.0 / COUNT(
    CASE WHEN EVENT_TYPE = 'scroll' THEN 1 END
  ) < 0.2 
  AND COUNT(
    CASE WHEN EVENT_TYPE = 'purchase' THEN 1 END
  ) = 0
  ORDER BY 4 DESC, 1 ASC
```
# [569. Median Employee Salary](https://leetcode.com/problems/median-employee-salary/)
```sql
WITH T AS (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY COMPANY ORDER BY SALARY ASC) AS RK,
           COUNT(ID) OVER (PARTITION BY COMPANY) AS N
    FROM EMPLOYEE
)
SELECT ID, COMPANY, SALARY
FROM T
WHERE RK >= N / 2 AND RK <= N / 2 + 1;
```

# [571. Find Median Given Frequency of Numbers](https://leetcode.com/problems/find-median-given-frequency-of-numbers/)
```sql
WITH
    T AS (
        SELECT
            *,
            SUM(FREQUENCY) OVER (ORDER BY NUM ASC) AS RK1,
            SUM(FREQUENCY) OVER (ORDER BY NUM DESC) AS RK2,
            SUM(FREQUENCY) OVER () AS S
        FROM NUMBERS
    )
SELECT
    ROUND(AVG(NUM), 1) AS MEDIAN
FROM T
WHERE RK1 >= S / 2 AND RK2 >= S / 2;
```

# [579. Find Cumulative Salary of an Employee](https://leetcode.com/problems/find-cumulative-salary-of-an-employee/)
```sql
WITH T AS (
    SELECT 
        ID, 
        MONTH, 
        SUM(SALARY) OVER (
            PARTITION BY ID 
            ORDER BY MONTH 
            ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
        ) AS SALARY,
        RANK() OVER (
            PARTITION BY ID 
            ORDER BY MONTH DESC
        ) AS RK
    FROM EMPLOYEE
)
SELECT ID, MONTH, SALARY
FROM T
WHERE RK > 1
ORDER BY ID, MONTH DESC;
```

# [601. Human Traffic of Stadium](https://leetcode.com/problems/human-traffic-of-stadium/)
```sql
WITH S AS (
    SELECT *,
           ID - ROW_NUMBER() OVER (ORDER BY ID) AS RK
    FROM STADIUM
    WHERE PEOPLE >= 100
),
T AS (
    SELECT *,
           COUNT(1) OVER (PARTITION BY RK) AS CNT
    FROM S
)
SELECT ID, VISIT_DATE, PEOPLE
FROM T
WHERE CNT >= 3
```

# [615. Average Salary: Departments VS Company](https://leetcode.com/problems/average-salary-departments-vs-company/)
```sql
WITH T AS (
    SELECT
        FORMAT(PAY_DATE, 'yyyy-MM') AS PAY_MONTH,
        DEPARTMENT_ID,
        AVG(AMOUNT) OVER (PARTITION BY PAY_DATE) AS COMPANY_AVG_AMOUNT,
        AVG(AMOUNT) OVER (PARTITION BY PAY_DATE, DEPARTMENT_ID) AS DEPARTMENT_AVG_AMOUNT
    FROM
        SALARY AS S
        JOIN EMPLOYEE AS E ON S.EMPLOYEE_ID = E.EMPLOYEE_ID
)
SELECT DISTINCT
    PAY_MONTH,
    DEPARTMENT_ID,
    CASE
        WHEN COMPANY_AVG_AMOUNT = DEPARTMENT_AVG_AMOUNT THEN 'same'
        WHEN COMPANY_AVG_AMOUNT < DEPARTMENT_AVG_AMOUNT THEN 'higher'
        ELSE 'lower'
    END AS COMPARISON
FROM T;
```

# [618. Students Report By Geography](https://leetcode.com/problems/students-report-by-geography/)
```
A U.S graduate school has students from Asia, Europe and America. The students' location information are stored in table student as below.

| name   | continent |
|--------|-----------|
| Jack   | America   |
| Pascal | Europe    |
| Xi     | Asia      |
| Jane   | America   |
 
Pivot the continent column in this table so that each name is sorted alphabetically and displayed underneath its corresponding continent. The output headers should be America, Asia and Europe respectively. It is guaranteed that the student number from America is no less than either Asia or Europe.
For the sample input, the output is:

| America | Asia | Europe |
|---------|------|--------|
| Jack    | Xi   | Pascal |
| Jane    |      |        |
```
```sql
/*******************************************************************************
1. SETUP: CLEAN UP AND RECREATE TABLES
*******************************************************************************/
DROP TABLE IF EXISTS STUDENT;
GO
CREATE TABLE STUDENT (
    NAME VARCHAR(50),
    CONTINENT VARCHAR(50)
);
GO
/*******************************************************************************
2. DATA ENTRY: INSERT SAMPLE DATA
*******************************************************************************/
INSERT INTO STUDENT (NAME, CONTINENT) VALUES
('Jack', 'America'),
('Pascal', 'Europe'),
('Xi', 'Asia'),
('Jane', 'America');
GO
/*******************************************************************************
3. DISPLAY INPUT DATA
*******************************************************************************/
SELECT * FROM STUDENT;
/*******************************************************************************
4. SOLUTION:
*******************************************************************************/
-- CREATE RANKED LIST PER CONTINENT
WITH T AS (
    SELECT NAME, CONTINENT,
           ROW_NUMBER() OVER (PARTITION BY CONTINENT ORDER BY NAME) AS RK
    FROM STUDENT
)
-- PIVOT NAMES INTO CONTINENT COLUMNS
SELECT MAX(CASE WHEN UPPER(CONTINENT) = 'AMERICA' THEN NAME END) AS AMERICA,
       MAX(CASE WHEN UPPER(CONTINENT) = 'ASIA' THEN NAME END) AS ASIA,
       MAX(CASE WHEN UPPER(CONTINENT) = 'EUROPE' THEN NAME END) AS EUROPE
FROM T
GROUP BY RK
ORDER BY RK;
```





















