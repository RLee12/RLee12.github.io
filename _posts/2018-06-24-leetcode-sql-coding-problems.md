---
layout: post
title: "LeetCode SQL Coding Problems"
date: "2018-06-24"
slug: "leetcode-sql-coding-problems"
description: "The Enron financial and email data provides a great reservoir for data analysis. This blog is the first piece of the following posts to explore the Enron dataset. Exploratory data analysis is carried out and paves the way for future feature engineering and modeling."
category: 
  - coding
# tags will also be used as html meta keywords.
tags:
  - leet code
  - sql
mathjax: true
gistembed: true
published: true
hide_printmsg: true
show_meta: false
---

## LeetCode SQL Problem #185

The `Employee` table holds all employees. Every employee has an Id, and there is also a column for the department Id.

{% highlight html %}
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
+----+-------+--------+--------------+
{% endhighlight %}

The `Department` table holds all departments of the company.

{% highlight html %}
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
{% endhighlight %}

Write a SQL query to find employees who earn the top three salaries in each of the department. For the above tables, your SQL query should return the following rows.

{% highlight html %}
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Randy    | 85000  |
| IT         | Joe      | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
{% endhighlight %}

MS SQL solution: 

{% highlight sql %}
SELECT Name AS Department, Employee, Salary 
FROM 
(
	SELECT 
		DepartmentId, Name AS Employee, Salary, 
		DENSE_RANK() OVER (PARTITION BY DepartmentId ORDER BY Salary DESC) AS SalaryRank
	FROM Employee
) AS e
JOIN Department d ON e.DepartmentId = d.Id
WHERE SalaryRank <= 3
ORDER BY Department, Salary DESC
{% endhighlight %}

## LeetCode SQL Problem #262

The `Trips` table holds all taxi trips. Each trip has a unique Id, while Client_Id and Driver_Id are both foreign keys to the Users_Id at the `Users` table. Status is an ENUM type of (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’).

{% highlight html %}
+----+-----------+-----------+---------+--------------------+----------+
| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|
+----+-----------+-----------+---------+--------------------+----------+
| 1  |     1     |    10     |    1    |     completed      |2013-10-01|
| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|
| 3  |     3     |    12     |    6    |     completed      |2013-10-01|
| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|
| 5  |     1     |    10     |    1    |     completed      |2013-10-02|
| 6  |     2     |    11     |    6    |     completed      |2013-10-02|
| 7  |     3     |    12     |    6    |     completed      |2013-10-02|
| 8  |     2     |    12     |    12   |     completed      |2013-10-03|
| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 
| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|
+----+-----------+-----------+---------+--------------------+----------+
{% endhighlight %}

The `Users` table holds all users. Each user has an unique Users_Id, and Role is an ENUM type of (‘client’, ‘driver’, ‘partner’).

{% highlight html %}
+----------+--------+--------+
| Users_Id | Banned |  Role  |
+----------+--------+--------+
|    1     |   No   | client |
|    2     |   Yes  | client |
|    3     |   No   | client |
|    4     |   No   | client |
|    10    |   No   | driver |
|    11    |   No   | driver |
|    12    |   No   | driver |
|    13    |   No   | driver |
+----------+--------+--------+
{% endhighlight %}

Write a SQL query to find the cancellation rate of requests made by unbanned users between <strong>Oct 1, 2013</strong> and <strong>Oct 3, 2013</strong>. For the above tables, your SQL query should return the following rows with the cancellation rate being rounded to two decimal places.

{% highlight html %}
+------------+-------------------+
|     Day    | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |
+------------+-------------------+
{% endhighlight %}

MS SQL solution: 

{% highlight sql %}
SELECT 
	Request_at AS Day, 
	ROUND(SUM(CancelCount)/SUM(TotalCount), 2) AS [Cancellation Rate]
FROM 
(
	SELECT Client_Id, Request_at,
		CASE WHEN Status IN ('cancelled_by_driver', 'cancelled_by_client') THEN 1.0 ELSE 0.0 END AS CancelCount,
		CASE WHEN Status = Status THEN 1.0 ELSE 0.0 END AS TotalCount
	FROM Trips t
) AS t
JOIN Users u ON t.Client_Id = u.Users_Id
WHERE Banned <> 'Yes'
AND CAST(Request_at AS DATE) BETWEEN '2013-10-01' AND '2013-10-03' 
GROUP BY Request_at
ORDER BY Day
{% endhighlight %}
