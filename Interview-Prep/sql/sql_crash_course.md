# 📊 Deliverable 1: SQL Crash Course + 15 Practice Problems

> [!TIP]
> Eightfold's PSE round tests **practical SQL** — extracting data, finding duplicates, debugging data syncs, aggregating metrics. NOT trick questions. If you can do JOINs, GROUP BY, Window Functions, and CTEs confidently, you'll crush it.

---

## Part A: SQL Cheat Sheet (Memorize This)

### 1. SELECT Basics
```sql
-- Basic structure (order matters!)
SELECT columns          -- 5. What to show
FROM table              -- 1. Where to look
WHERE condition         -- 2. Filter rows BEFORE grouping
GROUP BY columns        -- 3. Group rows
HAVING condition        -- 4. Filter groups AFTER grouping
ORDER BY columns        -- 6. Sort results
LIMIT n                 -- 7. Cap results
```

### 2. JOINs (Most Asked Topic)
```sql
-- INNER JOIN: Only matching rows from BOTH tables
SELECT e.name, d.dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.id;

-- LEFT JOIN: ALL rows from left table + matching from right (NULL if no match)
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.id;
-- ^ This returns employees EVEN IF they have no department

-- RIGHT JOIN: ALL rows from right table + matching from left
-- (Less common — you can always rewrite as LEFT JOIN by swapping table order)

-- FULL OUTER JOIN: ALL rows from BOTH tables (NULL where no match)
SELECT e.name, d.dept_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.id;
```

**When to use which:**
| Scenario | JOIN Type |
|:---------|:----------|
| "Find employees AND their departments" | INNER JOIN |
| "Find ALL employees, even those without a department" | LEFT JOIN |
| "Find departments that have NO employees" | LEFT JOIN + WHERE right IS NULL |
| "Find ALL records from both sides" | FULL OUTER JOIN |

### 3. GROUP BY + Aggregate Functions
```sql
-- COUNT, SUM, AVG, MIN, MAX
SELECT dept_id, COUNT(*) as employee_count, AVG(salary) as avg_salary
FROM employees
GROUP BY dept_id
HAVING COUNT(*) > 5;    -- Filter AFTER grouping (WHERE filters BEFORE)
```

**Key Rule:** Every column in SELECT must either be in GROUP BY or inside an aggregate function.

### 4. Window Functions (Frequently Asked)
```sql
-- ROW_NUMBER: Unique sequential number per partition
SELECT name, dept_id, salary,
       ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) as rank
FROM employees;
-- Use case: "Find the top earner in each department" → WHERE rank = 1

-- RANK: Same rank for ties, skips next number
-- DENSE_RANK: Same rank for ties, does NOT skip
SELECT name, salary,
       RANK() OVER (ORDER BY salary DESC) as rank,        -- 1, 2, 2, 4 (skips 3)
       DENSE_RANK() OVER (ORDER BY salary DESC) as drank  -- 1, 2, 2, 3 (no skip)
FROM employees;

-- LAG / LEAD: Access previous/next row's value
SELECT name, salary,
       LAG(salary) OVER (ORDER BY hire_date) as prev_salary,
       LEAD(salary) OVER (ORDER BY hire_date) as next_salary
FROM employees;
-- Use case: "Compare each employee's salary with the previous hire"
```

### 5. CTEs (Common Table Expressions)
```sql
-- Named temporary result set — makes complex queries readable
WITH high_earners AS (
    SELECT name, dept_id, salary
    FROM employees
    WHERE salary > 100000
),
dept_counts AS (
    SELECT dept_id, COUNT(*) as cnt
    FROM high_earners
    GROUP BY dept_id
)
SELECT d.dept_name, dc.cnt
FROM dept_counts dc
JOIN departments d ON dc.dept_id = d.id
ORDER BY dc.cnt DESC;
```

### 6. Subqueries
```sql
-- In WHERE clause
SELECT name FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- In FROM clause (derived table)
SELECT dept_id, avg_sal
FROM (SELECT dept_id, AVG(salary) as avg_sal FROM employees GROUP BY dept_id) sub
WHERE avg_sal > 80000;

-- EXISTS (check if related rows exist)
SELECT d.dept_name FROM departments d
WHERE EXISTS (SELECT 1 FROM employees e WHERE e.dept_id = d.id);
```

### 7. NULL Handling
```sql
-- Check for NULL (NEVER use = NULL)
WHERE column IS NULL
WHERE column IS NOT NULL

-- Replace NULL with a default
SELECT COALESCE(phone, email, 'No Contact') as contact_info FROM employees;
-- COALESCE returns the first non-NULL value

-- IFNULL (MySQL) / ISNULL (SQL Server) — simpler but less flexible
SELECT IFNULL(phone, 'N/A') FROM employees;
```

### 8. String Functions (Useful for Data Cleanup)
```sql
SELECT 
    UPPER(name),                    -- Uppercase
    LOWER(email),                   -- Lowercase
    CONCAT(first_name, ' ', last_name),  -- Concatenate
    SUBSTRING(name, 1, 3),          -- Extract substring
    TRIM(name),                     -- Remove whitespace
    LENGTH(name),                   -- String length
    REPLACE(phone, '-', '')         -- Replace characters
FROM employees;
```

### 9. Date Functions
```sql
-- Current date/time
SELECT CURRENT_DATE, CURRENT_TIMESTAMP, NOW();

-- Date arithmetic
SELECT DATEDIFF(end_date, start_date) as days_employed FROM employees;
SELECT DATE_ADD(hire_date, INTERVAL 90 DAY) as probation_end FROM employees;

-- Extract parts
SELECT EXTRACT(YEAR FROM hire_date) as hire_year FROM employees;
SELECT YEAR(hire_date), MONTH(hire_date) FROM employees;  -- MySQL shorthand
```

### 10. CASE Statements
```sql
SELECT name, salary,
    CASE
        WHEN salary > 150000 THEN 'Senior'
        WHEN salary > 100000 THEN 'Mid'
        WHEN salary > 50000  THEN 'Junior'
        ELSE 'Entry'
    END as level
FROM employees;
```

---

## Part B: 15 Practice Problems (Eightfold-Themed)

> [!NOTE]
> These problems use HR/talent data tables — exactly what Eightfold's platform deals with. Practice these and you'll be speaking the interviewer's language.

### Schema (Used for all problems)

```sql
-- Candidates who applied for jobs
CREATE TABLE candidates (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    skills TEXT,           -- comma-separated: "Python,SQL,AWS"
    years_experience INT,
    applied_date DATE,
    source VARCHAR(50)     -- 'LinkedIn', 'Referral', 'Career Page', 'Agency'
);

-- Job postings
CREATE TABLE jobs (
    id INT PRIMARY KEY,
    title VARCHAR(100),
    department VARCHAR(50),
    location VARCHAR(50),
    required_experience INT,
    status VARCHAR(20),    -- 'Open', 'Closed', 'On Hold'
    created_date DATE
);

-- Applications linking candidates to jobs
CREATE TABLE applications (
    id INT PRIMARY KEY,
    candidate_id INT,
    job_id INT,
    status VARCHAR(30),    -- 'Applied', 'Screening', 'Interview', 'Offer', 'Rejected', 'Hired'
    applied_date DATE,
    last_updated DATE,
    rejection_reason VARCHAR(100)
);

-- Interview records
CREATE TABLE interviews (
    id INT PRIMARY KEY,
    application_id INT,
    interviewer_name VARCHAR(100),
    interview_date DATE,
    score INT,             -- 1 to 5
    feedback TEXT
);

-- Employees (hired candidates)
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(50),
    hire_date DATE,
    salary DECIMAL(10,2),
    manager_id INT         -- self-referencing FK
);
```

---

### Problem 1: Basic JOIN — Find all applications with candidate and job names
**Question:** List all applications showing the candidate name, job title, and application status.

```sql
SELECT c.name AS candidate_name, 
       j.title AS job_title, 
       a.status AS application_status
FROM applications a
INNER JOIN candidates c ON a.candidate_id = c.id
INNER JOIN jobs j ON a.job_id = j.id;
```

---

### Problem 2: LEFT JOIN — Find jobs with NO applications
**Question:** Find all job postings that haven't received any applications.

```sql
SELECT j.id, j.title, j.department
FROM jobs j
LEFT JOIN applications a ON j.id = a.job_id
WHERE a.id IS NULL;
```
**Why LEFT JOIN?** We want ALL jobs, even those with zero applications. The WHERE a.id IS NULL filters to only those with no match.

---

### Problem 3: GROUP BY + COUNT — Applications per job
**Question:** How many applications has each job received? Show job title and count, sorted by most applications first.

```sql
SELECT j.title, COUNT(a.id) AS application_count
FROM jobs j
LEFT JOIN applications a ON j.id = a.job_id
GROUP BY j.id, j.title
ORDER BY application_count DESC;
```

---

### Problem 4: HAVING — Jobs with high rejection rates
**Question:** Find jobs where more than 50% of applications were rejected.

```sql
SELECT j.title,
       COUNT(a.id) AS total_apps,
       SUM(CASE WHEN a.status = 'Rejected' THEN 1 ELSE 0 END) AS rejected,
       ROUND(100.0 * SUM(CASE WHEN a.status = 'Rejected' THEN 1 ELSE 0 END) / COUNT(a.id), 1) AS rejection_pct
FROM jobs j
JOIN applications a ON j.id = a.job_id
GROUP BY j.id, j.title
HAVING SUM(CASE WHEN a.status = 'Rejected' THEN 1 ELSE 0 END) * 1.0 / COUNT(a.id) > 0.5;
```

---

### Problem 5: Window Function — Rank candidates by interview score per job
**Question:** For each job, rank candidates by their average interview score.

```sql
SELECT j.title,
       c.name,
       AVG(i.score) AS avg_score,
       RANK() OVER (PARTITION BY a.job_id ORDER BY AVG(i.score) DESC) AS rank
FROM interviews i
JOIN applications a ON i.application_id = a.id
JOIN candidates c ON a.candidate_id = c.id
JOIN jobs j ON a.job_id = j.id
GROUP BY j.title, a.job_id, c.name;
```

---

### Problem 6: CTE — Hiring funnel conversion rates
**Question:** Calculate the conversion rate at each stage of the hiring funnel.

```sql
WITH funnel AS (
    SELECT 
        COUNT(*) AS total_applied,
        SUM(CASE WHEN status IN ('Screening','Interview','Offer','Hired') THEN 1 ELSE 0 END) AS passed_screening,
        SUM(CASE WHEN status IN ('Interview','Offer','Hired') THEN 1 ELSE 0 END) AS reached_interview,
        SUM(CASE WHEN status IN ('Offer','Hired') THEN 1 ELSE 0 END) AS got_offer,
        SUM(CASE WHEN status = 'Hired' THEN 1 ELSE 0 END) AS hired
    FROM applications
)
SELECT 
    total_applied,
    passed_screening,
    ROUND(100.0 * passed_screening / total_applied, 1) AS screening_rate,
    reached_interview,
    ROUND(100.0 * reached_interview / total_applied, 1) AS interview_rate,
    got_offer,
    ROUND(100.0 * got_offer / total_applied, 1) AS offer_rate,
    hired,
    ROUND(100.0 * hired / total_applied, 1) AS hire_rate
FROM funnel;
```

---

### Problem 7: Subquery — Candidates earning above department average
**Question:** Find employees who earn more than their department's average salary.

```sql
SELECT e.name, e.department, e.salary, dept_avg.avg_salary
FROM employees e
JOIN (
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
) dept_avg ON e.department = dept_avg.department
WHERE e.salary > dept_avg.avg_salary;
```

---

### Problem 8: Self-JOIN — Find employees and their managers
**Question:** List each employee with their manager's name.

```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```
**Why LEFT JOIN?** CEO has no manager (manager_id is NULL). LEFT JOIN keeps the CEO row.

---

### Problem 9: Date Functions — Hiring trends by month
**Question:** How many candidates were hired per month in the last year?

```sql
SELECT 
    DATE_FORMAT(a.last_updated, '%Y-%m') AS hire_month,
    COUNT(*) AS hires
FROM applications a
WHERE a.status = 'Hired'
  AND a.last_updated >= DATE_SUB(CURRENT_DATE, INTERVAL 1 YEAR)
GROUP BY DATE_FORMAT(a.last_updated, '%Y-%m')
ORDER BY hire_month;
```

---

### Problem 10: Duplicate Detection — Find duplicate candidate emails
**Question:** Find candidates who applied with the same email address (duplicate accounts).

```sql
SELECT email, COUNT(*) AS count
FROM candidates
GROUP BY email
HAVING COUNT(*) > 1;

-- To see the actual duplicate records:
SELECT c.*
FROM candidates c
JOIN (
    SELECT email FROM candidates GROUP BY email HAVING COUNT(*) > 1
) dupes ON c.email = dupes.email
ORDER BY c.email;
```

---

### Problem 11: Window Function — Time between application stages
**Question:** For each application, calculate how many days it took to move from 'Applied' to 'Interview'.

```sql
SELECT 
    c.name,
    j.title,
    a.applied_date,
    MIN(i.interview_date) AS first_interview_date,
    DATEDIFF(MIN(i.interview_date), a.applied_date) AS days_to_interview
FROM applications a
JOIN candidates c ON a.candidate_id = c.id
JOIN jobs j ON a.job_id = j.id
JOIN interviews i ON i.application_id = a.id
GROUP BY c.name, j.title, a.applied_date
ORDER BY days_to_interview DESC;
```

---

### Problem 12: CASE + Aggregation — Source effectiveness
**Question:** Which candidate source (LinkedIn, Referral, etc.) produces the most hires?

```sql
SELECT 
    c.source,
    COUNT(DISTINCT a.id) AS total_applications,
    SUM(CASE WHEN a.status = 'Hired' THEN 1 ELSE 0 END) AS total_hires,
    ROUND(100.0 * SUM(CASE WHEN a.status = 'Hired' THEN 1 ELSE 0 END) / COUNT(DISTINCT a.id), 1) AS hire_rate_pct
FROM candidates c
JOIN applications a ON c.id = a.candidate_id
GROUP BY c.source
ORDER BY hire_rate_pct DESC;
```

---

### Problem 13: EXISTS — Departments that made no hires last quarter
**Question:** Find departments with open jobs but zero hires in the last 3 months.

```sql
SELECT DISTINCT j.department
FROM jobs j
WHERE j.status = 'Open'
  AND NOT EXISTS (
      SELECT 1 
      FROM applications a
      JOIN jobs j2 ON a.job_id = j2.id
      WHERE j2.department = j.department
        AND a.status = 'Hired'
        AND a.last_updated >= DATE_SUB(CURRENT_DATE, INTERVAL 3 MONTH)
  );
```

---

### Problem 14: Window Function — Running total of hires
**Question:** Show a running total of hires by date.

```sql
SELECT 
    hire_date,
    COUNT(*) AS daily_hires,
    SUM(COUNT(*)) OVER (ORDER BY hire_date) AS running_total
FROM employees
GROUP BY hire_date
ORDER BY hire_date;
```

---

### Problem 15: Complex CTE — Interview pipeline analysis
**Question:** For each department, show: total applications, avg interview score, avg days-to-hire, and rank departments by hire rate.

```sql
WITH dept_stats AS (
    SELECT 
        j.department,
        COUNT(DISTINCT a.id) AS total_apps,
        SUM(CASE WHEN a.status = 'Hired' THEN 1 ELSE 0 END) AS hires,
        AVG(i.score) AS avg_interview_score,
        AVG(
            CASE WHEN a.status = 'Hired' 
            THEN DATEDIFF(a.last_updated, a.applied_date) 
            END
        ) AS avg_days_to_hire
    FROM jobs j
    JOIN applications a ON j.id = a.job_id
    LEFT JOIN interviews i ON i.application_id = a.id
    GROUP BY j.department
)
SELECT 
    department,
    total_apps,
    hires,
    ROUND(100.0 * hires / total_apps, 1) AS hire_rate_pct,
    ROUND(avg_interview_score, 2) AS avg_score,
    ROUND(avg_days_to_hire, 0) AS avg_days_to_hire,
    RANK() OVER (ORDER BY 100.0 * hires / total_apps DESC) AS dept_rank
FROM dept_stats
ORDER BY dept_rank;
```

---

## Part C: Quick-Fire Concept Questions They Might Ask

| Question | Answer |
|:---------|:-------|
| "Difference between WHERE and HAVING?" | WHERE filters rows BEFORE grouping. HAVING filters groups AFTER aggregation. |
| "Difference between RANK() and DENSE_RANK()?" | RANK skips numbers after ties (1,2,2,4). DENSE_RANK doesn't (1,2,2,3). |
| "What's a CTE?" | A named temporary result set defined with WITH. Makes complex queries readable and reusable within the query. |
| "How do you find duplicates?" | GROUP BY the column, HAVING COUNT(*) > 1. |
| "Difference between DELETE, TRUNCATE, DROP?" | DELETE removes specific rows (can have WHERE). TRUNCATE removes ALL rows fast (no WHERE). DROP removes the entire table structure. |
| "What's an index? Why use one?" | A data structure that speeds up lookups on a column. Like a book's index. Trade-off: faster reads, slower writes. |
| "How would you optimize a slow query?" | Check EXPLAIN plan, add indexes on WHERE/JOIN columns, avoid SELECT *, reduce subqueries to JOINs, use LIMIT. |
| "What's NULL?" | NULL means unknown/missing — not zero, not empty string. Any comparison with NULL returns NULL (use IS NULL). |
