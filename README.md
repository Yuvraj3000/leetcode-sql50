# LeetCode SQL 50 — Solutions

> Documenting my journey through LeetCode's SQL 50 study plan.
> Each solution includes problem context, table structure, and my query with approach.

![Progress](https://img.shields.io/badge/Progress-29%2F50-orange)
![Language](https://img.shields.io/badge/Language-MySQL-blue)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)

---

## Progress Tracker

| Section | Done | Total |
|---|---|---|
| Select | 5 | 5 |
| Basic Joins | 9 | 9 |
| Basic Aggregate Functions | 8 | 8 |
| Sorting and Grouping | 6 | 6 |
| Advanced Select & Joins | 0 | 7 |
| Subqueries | 0 | 7 |
| String Functions / Regex / Clause | 0 | 8 |
| **Total** | **29** | **50** |

---

## Solutions

---

## Section 1 — Select

---

### 1. Recyclable and Low Fat Products
Easy | [LeetCode #1757](https://leetcode.com/problems/recyclable-and-low-fat-products/)

**Table: Products**
| Column | Type | Notes |
|---|---|---|
| product_id | int | Primary Key |
| low_fats | enum | 'Y' or 'N' |
| recyclable | enum | 'Y' or 'N' |

**Problem:**
Find the product_ids of products that are both low fat (`low_fats = 'Y'`) and recyclable (`recyclable = 'Y'`).

**Solution:**
```sql
SELECT product_id
FROM Products
WHERE low_fats = 'Y'
AND recyclable = 'Y';
```

---

### 2. Find Customer Referee
Easy | [LeetCode #584](https://leetcode.com/problems/find-customer-referee/)

**Table: Customer**
| Column | Type | Notes |
|---|---|---|
| id | int | Primary Key |
| name | varchar | |
| referee_id | int | ID of the customer who referred them |

**Problem:**
Find names of customers who were either not referred by anyone, or referred by someone other than customer with id = 2.

**Solution:**
```sql
SELECT name
FROM Customer
WHERE referee_id != 2
OR referee_id IS NULL;
```

---

### 3. Big Countries
Easy | [LeetCode #595](https://leetcode.com/problems/big-countries/)

**Table: World**
| Column | Type | Notes |
|---|---|---|
| name | varchar | Primary Key |
| continent | varchar | |
| area | int | in km² |
| population | int | |
| gdp | bigint | |

**Problem:**
A country is "big" if it has area >= 3,000,000 km² OR population >= 25,000,000. Find the name, population, and area of all big countries.

**Solution:**
```sql
SELECT name, population, area
FROM World
WHERE area >= 3000000
OR population >= 25000000;
```

---

### 4. Article Views I
Easy | [LeetCode #1148](https://leetcode.com/problems/article-views-i/)

**Table: Views**
| Column | Type | Notes |
|---|---|---|
| article_id | int | |
| author_id | int | |
| viewer_id | int | Equal to author_id means same person |
| view_date | date | |

**Problem:**
Find all authors who viewed at least one of their own articles (i.e., author_id = viewer_id). Return sorted by id ascending.

**Solution:**
```sql
SELECT author_id AS id
FROM Views
WHERE author_id = viewer_id
GROUP BY author_id
ORDER BY id ASC;
```

---

### 5. Invalid Tweets
Easy | [LeetCode #1683](https://leetcode.com/problems/invalid-tweets/)

**Table: Tweets**
| Column | Type | Notes |
|---|---|---|
| tweet_id | int | Primary Key |
| content | varchar | |

**Problem:**
A tweet is invalid if its content has more than 15 characters. Find the tweet_ids of all invalid tweets.

**Solution:**
```sql
SELECT tweet_id
FROM Tweets
WHERE LENGTH(content) > 15;
```

---

## Section 2 — Basic Joins

---

### 6. Replace Employee ID With The Unique Identifier
Easy | [LeetCode #1378](https://leetcode.com/problems/replace-employee-id-with-the-unique-identifier/)

**Tables:**
- `Employees(id PK, name)` — employee id and name
- `EmployeeUNI(id, unique_id)` — maps employee id to unique id

**Problem:**
Show the unique_id for each employee. If an employee does not have a unique id, show NULL instead.

**Solution:**
```sql
SELECT f.unique_id, e.name
FROM Employees e
LEFT JOIN EmployeeUNI f ON e.id = f.id;
```

---

### 7. Product Sales Analysis I
Easy | [LeetCode #1068](https://leetcode.com/problems/product-sales-analysis-i/)

**Tables:**
- `Sales(sale_id, product_id, year, quantity, price)` — (sale_id, year) is PK
- `Product(product_id PK, product_name)` — product details

**Problem:**
For each sale_id in the Sales table, report the product_name, year, and price.

**Solution:**
```sql
SELECT b.product_name, a.year, a.price
FROM Sales a
LEFT JOIN Product b ON a.product_id = b.product_id;
```

---

### 8. Customer Who Visited but Did Not Make Any Transactions
Easy | [LeetCode #1581](https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/)

**Tables:**
- `Visits(visit_id PK, customer_id)` — customers who visited
- `Transactions(transaction_id PK, visit_id, amount)` — transactions during a visit

**Problem:**
Find customer IDs who visited the mall without making any transaction, and how many times they did so.

**Solution:**
```sql
SELECT a.customer_id, COUNT(*) AS count_no_trans
FROM Visits a
LEFT JOIN Transactions b ON a.visit_id = b.visit_id
WHERE b.transaction_id IS NULL
GROUP BY a.customer_id;
```

---

### 9. Rising Temperature
Easy | [LeetCode #197](https://leetcode.com/problems/rising-temperature/)

**Table: Weather**
| Column | Type | Notes |
|---|---|---|
| id | int | Unique |
| recordDate | date | No duplicate dates |
| temperature | int | |

**Problem:**
Find ids of all dates where temperature was higher than the previous day's temperature.

**Solution:**
```sql
SELECT a.id
FROM Weather a
JOIN Weather b ON b.recordDate = a.recordDate - INTERVAL 1 DAY
WHERE a.temperature > b.temperature;
```

---

### 10. Average Time of Process per Machine
Easy | [LeetCode #1661](https://leetcode.com/problems/average-time-of-process-per-machine/)

**Table: Activity**
| Column | Type | Notes |
|---|---|---|
| machine_id | int | |
| process_id | int | |
| activity_type | enum | 'start' or 'end' |
| timestamp | float | time in seconds |

**(machine_id, process_id, activity_type) is the PK**

**Problem:**
Find the average processing time per machine. Processing time = end timestamp - start timestamp. Round to 3 decimal places.

**Solution:**
```sql
SELECT a.machine_id, ROUND(AVG(b.timestamp - a.timestamp), 3) AS processing_time
FROM Activity a
JOIN Activity b ON a.machine_id = b.machine_id
AND a.process_id = b.process_id
AND a.activity_type = 'start'
AND b.activity_type = 'end'
GROUP BY a.machine_id;
```

---

### 11. Employee Bonus
Easy | [LeetCode #577](https://leetcode.com/problems/employee-bonus/)

**Tables:**
- `Employee(empId PK, name, supervisor, salary)`
- `Bonus(empId PK, bonus)` — foreign key to Employee

**Problem:**
Find name and bonus of employees whose bonus is less than 1000, or who did not receive any bonus at all.

**Solution:**
```sql
SELECT a.name, b.bonus
FROM Employee a
LEFT JOIN Bonus b ON a.empId = b.empId
WHERE b.bonus < 1000
OR b.bonus IS NULL;
```

---

### 12. Students and Examinations
Easy | [LeetCode #1280](https://leetcode.com/problems/students-and-examinations/)

**Tables:**
- `Students(student_id PK, student_name)`
- `Subjects(subject_name PK)`
- `Examinations(student_id, subject_name)` — no PK, may have duplicates

**Problem:**
For every student and every subject, find how many times the student attended the exam. Include combinations with 0 attendances.

**Solution:**
```sql
SELECT a.student_id, a.student_name, b.subject_name,
       COUNT(c.student_id) AS attended_exams
FROM Students a
CROSS JOIN Subjects b
LEFT JOIN Examinations c
ON a.student_id = c.student_id
AND b.subject_name = c.subject_name
GROUP BY a.student_id, a.student_name, b.subject_name
ORDER BY a.student_id, b.subject_name;
```

---

### 13. Managers with at Least 5 Direct Reports
Medium | [LeetCode #570](https://leetcode.com/problems/managers-with-at-least-5-direct-reports/)

**Table: Employee**
| Column | Type | Notes |
|---|---|---|
| id | int | Primary Key |
| name | varchar | |
| department | varchar | |
| managerId | int | NULL if no manager |

**Problem:**
Find the names of managers who have at least 5 employees directly reporting to them.

**Solution:**
```sql
-- To be added
```

---

### 14. Confirmation Rate
Medium | [LeetCode #1934](https://leetcode.com/problems/confirmation-rate/)

**Tables:**
- `Signups(user_id PK, time_stamp)` — user signup info
- `Confirmations(user_id, time_stamp, action)` — action is ENUM('confirmed', 'timeout')

**Problem:**
Find the confirmation rate of each user = confirmed messages / total requested messages. Users with no confirmations have rate = 0. Round to 2 decimal places.

**Solution:**
```sql
WITH conf_rate AS (
    SELECT user_id,
           SUM(action = 'confirmed') * 1.0 / COUNT(*) AS confirmation_rate
    FROM Confirmations
    GROUP BY user_id
)
SELECT b.user_id,
       ROUND(COALESCE(a.confirmation_rate, 0), 2) AS confirmation_rate
FROM Signups b
LEFT JOIN conf_rate a ON a.user_id = b.user_id;
```

---

## Section 3 — Basic Aggregate Functions

---

### 15. Not Boring Movies
Easy | [LeetCode #620](https://leetcode.com/problems/not-boring-movies/)

**Table: Cinema**
| Column | Type | Notes |
|---|---|---|
| id | int | Primary Key |
| movie | varchar | |
| description | varchar | |
| rating | float | range [0, 10] |

**Problem:**
Find movies with an odd-numbered id and description that is NOT 'boring'. Return ordered by rating descending.

**Solution:**
```sql
SELECT id, movie, description, rating
FROM Cinema
WHERE id % 2 != 0
AND description != 'boring'
ORDER BY rating DESC;
```

---

### 16. Average Selling Price
Easy | [LeetCode #1251](https://leetcode.com/problems/average-selling-price/)

**Tables:**
- `Prices(product_id, start_date, end_date, price)` — (product_id, start_date, end_date) is PK
- `UnitsSold(product_id, purchase_date, units)` — may have duplicates

**Problem:**
Find the average selling price for each product = total revenue / total units sold. Round to 2 decimal places. If no units sold, return 0.

**Solution:**
```sql
SELECT a.product_id,
       COALESCE(ROUND(SUM(a.price * b.units) / SUM(b.units), 2), 0) AS average_price
FROM Prices a
LEFT JOIN UnitsSold b ON a.product_id = b.product_id
AND b.purchase_date BETWEEN a.start_date AND a.end_date
GROUP BY a.product_id;
```

---

### 17. Project Employees I
Easy | [LeetCode #1075](https://leetcode.com/problems/project-employees-i/)

**Tables:**
- `Project(project_id, employee_id)` — (project_id, employee_id) is PK
- `Employee(employee_id PK, name, experience_years)`

**Problem:**
Find the average experience years of all employees working on each project. Round to 2 decimal places.

**Solution:**
```sql
SELECT a.project_id,
       ROUND(SUM(b.experience_years) / COUNT(a.project_id), 2) AS average_years
FROM Project a
LEFT JOIN Employee b ON a.employee_id = b.employee_id
GROUP BY a.project_id;
```

---

### 18. Percentage of Users Attended a Contest
Easy | [LeetCode #1633](https://leetcode.com/problems/percentage-of-users-attended-a-contest/)

**Tables:**
- `Users(user_id PK, user_name)`
- `Register(contest_id, user_id)` — (contest_id, user_id) is PK

**Problem:**
Find the percentage of users registered in each contest. Round to 2 decimal places. Order by percentage descending, then contest_id ascending.

**Solution:**
```sql
SELECT contest_id,
       ROUND(COUNT(user_id) * 100 / (SELECT COUNT(user_id) FROM Users), 2) AS percentage
FROM Register
GROUP BY contest_id
ORDER BY percentage DESC, contest_id ASC;
```

---

### 19. Queries Quality and Percentage
Easy | [LeetCode #1211](https://leetcode.com/problems/queries-quality-and-percentage/)

**Table: Queries**
| Column | Type | Notes |
|---|---|---|
| query_name | varchar | |
| result | varchar | |
| position | int | 1 to 500 |
| rating | int | 1 to 5; rating < 3 is poor |

**Problem:**
For each query_name find: quality = AVG(rating/position), poor_query_percentage = percentage of queries with rating < 3. Both rounded to 2 decimal places.

**Solution:**
```sql
SELECT query_name,
       ROUND(AVG(rating / position), 2) AS quality,
       ROUND(SUM(rating < 3) * 100 / COUNT(rating), 2) AS poor_query_percentage
FROM Queries
GROUP BY query_name;
```

---

### 20. Monthly Transactions I
Medium | [LeetCode #1193](https://leetcode.com/problems/monthly-transactions-i/)

**Table: Transactions**
| Column | Type | Notes |
|---|---|---|
| id | int | Primary Key |
| country | varchar | |
| state | enum | 'approved' or 'declined' |
| amount | int | |
| trans_date | date | |

**Problem:**
For each month and country, find: total transaction count, total amount, approved transaction count, and approved total amount.

**Solution:**
```sql
SELECT DATE_FORMAT(trans_date, '%Y-%m') AS month,
       country,
       COUNT(*) AS trans_count,
       COUNT(state = 'approved' OR NULL) AS approved_count,
       SUM(amount) AS trans_total_amount,
       SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM Transactions
GROUP BY month, country;
```

---

### 21. Immediate Food Delivery II
Medium | [LeetCode #1174](https://leetcode.com/problems/immediate-food-delivery-ii/)

**Table: Delivery**
| Column | Type | Notes |
|---|---|---|
| delivery_id | int | Primary Key |
| customer_id | int | |
| order_date | date | |
| customer_pref_delivery_date | date | |

**Problem:**
An order is "immediate" if order_date = customer_pref_delivery_date. Find the percentage of immediate orders among each customer's first order. Round to 2 decimal places.

**Solution:**
```sql
WITH cte AS (
    SELECT customer_id,
           CASE WHEN order_date = customer_pref_delivery_date THEN 'immediate'
                ELSE 'scheduled' END AS order_type
    FROM Delivery
    WHERE order_date = (
        SELECT MIN(order_date)
        FROM Delivery d2
        WHERE d2.customer_id = Delivery.customer_id
    )
)
SELECT ROUND(COUNT(CASE WHEN order_type = 'immediate' THEN 1 END) * 100 / COUNT(*), 2)
       AS immediate_percentage
FROM cte;
```

---

### 22. Game Play Analysis IV
Medium | [LeetCode #550](https://leetcode.com/problems/game-play-analysis-iv/)

**Table: Activity**
| Column | Type | Notes |
|---|---|---|
| player_id | int | |
| device_id | int | |
| event_date | date | |
| games_played | int | |

**(player_id, event_date) is the PK**

**Problem:**
Find the fraction of players who logged in again on the day immediately after their first login. Round to 2 decimal places.

**Solution:**
```sql
WITH cte AS (
    SELECT player_id, MIN(event_date) AS first_date
    FROM Activity
    GROUP BY player_id
)
SELECT ROUND(COUNT(DISTINCT b.player_id) / COUNT(DISTINCT a.player_id), 2) AS fraction
FROM cte a
LEFT JOIN Activity b ON a.player_id = b.player_id
AND b.event_date = DATE_ADD(a.first_date, INTERVAL 1 DAY);
```

---

## Section 4 — Sorting and Grouping

---

### 23. Number of Unique Subjects Taught by Each Teacher
Easy | [LeetCode #2356](https://leetcode.com/problems/number-of-unique-subjects-taught-by-each-teacher/)

**Table: Teacher**
| Column | Type | Notes |
|---|---|---|
| teacher_id | int | |
| subject_id | int | |
| dept_id | int | |

**(subject_id, dept_id) is the PK**

**Problem:**
Calculate the number of unique subjects each teacher teaches across all departments.

**Solution:**
```sql
SELECT teacher_id,
       COUNT(DISTINCT subject_id) AS cnt
FROM Teacher
GROUP BY teacher_id;
```

---

### 24. User Activity for the Past 30 Days I
Easy | [LeetCode #1141](https://leetcode.com/problems/user-activity-for-the-past-30-days-i/)

**Table: Activity**
| Column | Type | Notes |
|---|---|---|
| user_id | int | |
| session_id | int | |
| activity_date | date | |
| activity_type | enum | 'open_session', 'end_session', 'scroll_down', 'send_message' |

**Problem:**
Find the daily active user count for the 30-day period ending 2019-07-27 (inclusive). A user is active if they made at least one activity on that day.

**Solution:**
```sql
SELECT activity_date AS day,
       COUNT(DISTINCT user_id) AS active_users
FROM Activity
WHERE activity_date BETWEEN '2019-06-28' AND '2019-07-27'
GROUP BY activity_date
HAVING active_users > 0;
```

---

### 25. Product Sales Analysis III
Medium | [LeetCode #1070](https://leetcode.com/problems/product-sales-analysis-iii/)

**Table: Sales**
| Column | Type | Notes |
|---|---|---|
| sale_id | int | |
| product_id | int | |
| year | int | |
| quantity | int | |
| price | int | per unit |

**(sale_id, year) is the PK**

**Problem:**
For each product, find all sales entries from its first year of sale. Return product_id, first_year, quantity, and price.

**Solution:**
```sql
WITH cte AS (
    SELECT product_id, MIN(year) AS first_year
    FROM Sales
    GROUP BY product_id
)
SELECT a.product_id, a.first_year, b.quantity, b.price
FROM cte a
LEFT JOIN Sales b ON a.product_id = b.product_id
AND a.first_year = b.year;
```

---

### 26. Classes With at Least 5 Students
Easy | [LeetCode #596](https://leetcode.com/problems/classes-with-at-least-5-students/)

**Table: Courses**
| Column | Type | Notes |
|---|---|---|
| student | varchar | |
| class | varchar | |

**(student, class) is the PK**

**Problem:**
Find all classes that have at least 5 students enrolled.

**Solution:**
```sql
SELECT class
FROM Courses
GROUP BY class
HAVING COUNT(student) >= 5;
```

---

### 27. Find Followers Count
Easy | [LeetCode #1729](https://leetcode.com/problems/find-followers-count/)

**Table: Followers**
| Column | Type | Notes |
|---|---|---|
| user_id | int | |
| follower_id | int | |

**(user_id, follower_id) is the PK**

**Problem:**
For each user, return the number of followers. Order by user_id ascending.

**Solution:**
```sql
SELECT user_id,
       COUNT(follower_id) AS followers_count
FROM Followers
GROUP BY user_id
ORDER BY user_id ASC;
```

---

### 28. Biggest Single Number
Easy | [LeetCode #619](https://leetcode.com/problems/biggest-single-number/)

**Table: MyNumbers**
| Column | Type | Notes |
|---|---|---|
| num | int | No PK, may have duplicates |

**Problem:**
A "single number" appears only once in the table. Find the largest single number. If none exists, return null.

**Solution:**
```sql
WITH cte AS (
    SELECT num
    FROM MyNumbers
    GROUP BY num
    HAVING COUNT(num) = 1
)
SELECT MAX(num) AS num
FROM cte;
```

---

### 29. Customers Who Bought All Products
Medium | [LeetCode #1045](https://leetcode.com/problems/customers-who-bought-all-products/)

**Tables:**
- `Customer(customer_id, product_key)` — may have duplicates, customer_id is NOT NULL
- `Product(product_key PK)` — all available products

**Problem:**
Find customer_ids who have purchased every product listed in the Product table.

**Solution:**
```sql
SELECT customer_id
FROM Customer
GROUP BY customer_id
HAVING COUNT(DISTINCT product_key) = (SELECT COUNT(product_key) FROM Product);
```

---

## Section 5 — Advanced Select & Joins
*Coming soon...*

---

## Section 6 — Subqueries
*Coming soon...*

---

## Section 7 — String Functions / Regex / Clause
*Coming soon...*

---

## Key Concepts Learned

| Concept | First Used In |
|---|---|
| WHERE vs HAVING | Q26 - Classes With at Least 5 Students |
| LEFT JOIN + NULL check | Q8 - Customer Who Visited but Did Not Make Transactions |
| COALESCE for NULL handling | Q16 - Average Selling Price |
| DATE_FORMAT | Q20 - Monthly Transactions I |
| CROSS JOIN | Q12 - Students and Examinations |
| INTERVAL for date arithmetic | Q9 - Rising Temperature |
| CTE (WITH clause) | Q25, Q28, Q29 |
| COUNT(DISTINCT ...) | Q23, Q29 |
| CASE WHEN inside aggregation | Q20, Q21 |

---

*Last updated: May 2026 | Yuvraj Pahari*
