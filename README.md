# Mini_sql_project
# SQL User Submission Analysis Project

## Overview

This project analyzes user submission data from a coding platform using SQL.  
The goal is to extract meaningful insights about user performance, submission behavior, rankings, and weekly trends.

The project is built using PostgreSQL and demonstrates advanced SQL concepts like:

- Aggregate Functions
- GROUP BY
- CASE Statements
- Common Table Expressions (CTEs)
- Window Functions
- Ranking Functions
- Date & Time Analysis

---

## Database Schema

### Table: `user_submissions`

| Column Name   | Data Type                  | Description |
|---------------|----------------------------|-------------|
| id            | SERIAL PRIMARY KEY         | Unique submission ID |
| user_id       | BIGINT                     | Unique user ID |
| question_id   | INT                        | Question attempted |
| points        | INT                        | Points scored on submission |
| submitted_at  | TIMESTAMP WITH TIME ZONE   | Submission timestamp |
| username      | VARCHAR(50)                | Username |

---

## SQL Concepts Used

- SELECT Statements
- DISTINCT
- Aggregate Functions (`SUM`, `AVG`)
- Conditional Aggregation
- CASE WHEN
- Common Table Expressions (CTEs)
- Window Functions
- DENSE_RANK()
- Date Formatting
- Weekly Analysis

---

## Project Questions & Analysis
-- Mini Project 
```sql
CREATE TABLE user_submissions (
  id SERIAL PRIMARY KEY,
  user_id BIGINT,
  question_id INT,
  points INT,
  submitted_at TIMESTAMP WITH TIME ZONE,
  username VARCHAR(50)
  
);

SELECT * FROM user_submissions;
```
### -- Q1: List All Distinct Users and Their Stats
```sql
SELECT DISTINCT(username) FROM user_submissions;
```
### -- Q2: Calculate the Daily Average Points for Each User
```sql
SELECT 
    TO_CHAR(submitted_at, 'DD-MM') AS day,
    username,
    AVG(points) AS daily_avg_points
FROM user_submissions
GROUP BY 1, 2
ORDER BY username;
```

### -- Q3: Find the Top 3 Users with the Most Correct Submissions for Each Day
```sql
WITH daily_submissions AS (
    SELECT 
        TO_CHAR(submitted_at, 'DD-MM') AS daily,
        username,
        SUM(CASE WHEN points > 0 THEN 1 ELSE 0 END) AS correct_submissions
    FROM user_submissions
    GROUP BY 1, 2
),
users_rank AS (
    SELECT 
        daily,
        username,
        correct_submissions,
        DENSE_RANK() OVER(PARTITION BY daily ORDER BY correct_submissions DESC) AS rank
    FROM daily_submissions
)
SELECT 
    daily,
    username,
    correct_submissions
FROM users_rank
WHERE rank <= 3;
```
### -- Q4: Find the Top 5 Users with the Highest Number of Incorrect Submissions
```sql
SELECT 
    username,
    SUM(CASE WHEN points < 0 THEN 1 ELSE 0 END) AS incorrect_submissions,
    SUM(CASE WHEN points > 0 THEN 1 ELSE 0 END) AS correct_submissions,
    SUM(CASE WHEN points < 0 THEN points ELSE 0 END) AS incorrect_submissions_points,
    SUM(CASE WHEN points > 0 THEN points ELSE 0 END) AS correct_submissions_points_earned,
    SUM(points) AS points_earned
FROM user_submissions
GROUP BY 1
ORDER BY incorrect_submissions DESC;
```
### -- Q5: Find the Top 10 Performers for Each Week
```sql
SELECT *  
FROM (
    SELECT 
        EXTRACT(WEEK FROM submitted_at) AS week_no,
        username,
        SUM(points) AS total_points_earned,
        DENSE_RANK() OVER(PARTITION BY EXTRACT(WEEK FROM submitted_at) ORDER BY SUM(points) DESC) AS rank
    FROM user_submissions
    GROUP BY 1, 2
    ORDER BY week_no, total_points_earned DESC
)
WHERE rank <= 10;
```
---

## Features

- User performance tracking
- Daily and weekly leaderboard generation
- Correct vs incorrect submission analysis
- Time-based SQL analytics
- Ranking users using window functions

---

## Tools & Technologies

- PostgreSQL
- SQL
- pgAdmin / PostgreSQL Shell

---

## Learning Outcomes

Through this project, I practiced:

- Writing optimized SQL queries
- Using CTEs for readable query structure
- Performing ranking analysis with window functions
- Analyzing user activity patterns
- Building portfolio-ready SQL projects

---

## Future Improvements

- Add visual dashboards using Power BI or Tableau
- Create stored procedures and views
- Add indexing for performance optimization
- Build an ETL pipeline for automated data loading
- Connect with Python for deeper analysis

---

## Author

Vasundhara Ranga

---

## How to Run

1. Create the database in PostgreSQL
2. Run the table creation script
3. Insert dataset into `user_submissions`
4. Execute the SQL queries
5. Analyze the generated insights

---

## Sample Insights

- Identify top-performing users
- Detect users with high incorrect attempts
- Compare weekly performance trends
- Analyze consistency in daily submissions

---

## Project Status

Completed ✅
