# HR-Analytics

## Project Background/Overview:

This project aims at analyzing Human Resources (HR) related dataset of a fictional company based in USA.

By answering certain business questions, we’ll try to deep dive into employee composition, their salary, their qualifications, performance review, reasons for terminations etc. and come up with suggestions to improve employee satisfaction and improve their productivity thus contributing to company growth.

To view the SQL queries and their outputs, click [here](https://github.com/Curious-Creative-Mind/HR-Analytics/blob/main/ppt%20pdf.pdf) 

### Data Structure and Initial Checks:

The database structure consists of 3 tables as shown below : HR (90 records), Employees (90 records) and Department (7 records) .

![database structure](https://github.com/Curious-Creative-Mind/HR-Analytics/blob/main/data%20modelling.PNG?raw=true)

Prior to beginning the analysis, a variety of checks were conducted for quality control and familiarization with the datasets using Ms Excel.

### Data Quality Check:

-	Check blanks
-	Valid data
-	Replaced blanks in cerain columns with blank values 

## Executive Summary:

### Overview of Findings:

- There is uneven distribution of employees in various departments with **Development (47) and Finance (13 )** being the **top 2 departments with maximum employees** while **Legal (1)** having the **lowest employee count**.

-	The **average age of emplyees is 41 yrs.** while the **average hiring age is 32 yrs.**.

-	**Top 3 cities** with **highest average salary** are **Colorado ($4280.67), Alabama ($4218.40), Missouri ($4189.29)** while New York ($3983.00) and Washington ($3977.33) are at bottom having the lowest average salary.

-	**Resignation and Unfair Dismissal** are the **2 most common reasons for termination** of employees with 11 and 10 counts respectively.

-	The **average salary of female employees is $4124.74**, slightly higher than male employees ($4098.96) despite having similar average performance review (Female- 7.68 and Male- 7.64). This could be due to the company policy of encouraging  more female employees in the workforce as **female employees** constitute only **37.77 % of workforce**.

-	**Average tenure** of **terminated employees** is **1.38 yrs.**.

-	**Leyla , Manager of the Developmnent department** had **maximum** number of employees (11 in count) **terminated under her**. One probable reason behind this could be that the  Developmet department has relatively higher no. of employees under it.

-	Top 5 cities with highest no. of female employees are Montana, Missouri, New York, Ohio (with 5 each) and Washington (4).

-	Administrative Assistant is the highest paid position in Finance/Accounting department with a salary of $4576 while the position of Lawyer within the Legal department is the most paid with the salary of $3698 which is comparatively very low as the salary of highest paid positions in other departments is more than $4200.

-	Educational qualification of top 5 highest paid employees is Majors, Higher Certificate and Bachelor’s completed. This shows that earning does not depend on qualification alone rather it involves other factors as well.

-	Aged **employees with 60 and above age** and young employees in the age group of **21-30 yrs.** have **very good average performance review**. This could be attributed to the experience and expertise of aged employees while the high energy, enthusiasm and motivation of young employees.

-	The yearwise termination rate of employees from 2015-2019 seems to be consistent ranging from 4-6 while in case of hiring new employees, year 2016 saw maximum hirings (total 19) and 2017 saw lowest hirings (total 9). This could be due to the surplus hiring in the previous year(2016) as the average hiring rate per year is 13.

## Recommendations:

Based on the uncovered insights, the following recommendations have been provided:

-	Provide incentives to employees based on their performance.
  
-	Boost the morale and organize motivational sessions for middle aged employees especially targetting age group of 31-60 yrs..
  
-	Ensure no unfair dismissal by mandating managers to provide valid reasons for terminating employees under them. Also trying to understand the reason behind resignation of employees in order to retain good performing employees in the company.
  
-	Hire more young employees and train them to improve the productivity so as to reduce the average hiring age as well as overall average age of employees.

## SQL Queries

To view the SQL queries and their outputs, click [here](https://github.com/Curious-Creative-Mind/HR-Analytics/blob/main/ppt%20pdf.pdf) 

### --------------------------------------------------BUSINESS QUESTIONS-------------------------------------------------

### -- >>>> Basic Level

1. Find out the departmentwise distribution of employees.

```sql

SELECT 
    department, COUNT(employee) AS total_employees
FROM
    hr_database
GROUP BY department 
ORDER BY total_employees DESC ;

```

2. Calculate the average age of employees in the organization.

```sql

SELECT 
    ROUND(AVG(age), 0) AS avg_age
FROM
    (SELECT 
        id,
            employee,
            DATE_FORMAT(FROM_DAYS(DATEDIFF(NOW(), birth_date)), '%y') + 0 AS age
    FROM
        hr_database) AS a;

```
        
3. Find the average salary based on cities.

```sql

SELECT 
    city, ROUND(AVG(salary), 2) AS avg_salary
FROM
    employee_info
GROUP BY city
ORDER BY avg_salary DESC; 

```

4. Find out the top 3 reasons for termination.

```sql

SELECT 
    termination_reason, COUNT(termination_reason) AS count
FROM
    hr_database
GROUP BY termination_reason
ORDER BY count DESC
LIMIT 3;

```

5. Calculate the average age of hiring of employees.

```sql

SELECT 
    ROUND(AVG(age), 0) AS avg_hiring_age
FROM
    (SELECT 
        id,
            employee,
            DATE_FORMAT(FROM_DAYS(DATEDIFF(hire_date, birth_date)), '%y') + 0 AS age
    FROM
        hr_database) AS a;

```
       
### -- >>>> Intermediate Level

1. Calculate the average salary and average performance review based on gender.

```sql

SELECT 
    h.gender,
    ROUND(AVG(e.salary), 2) AS avg_salary,
    ROUND(AVG(e.performance_review), 2) AS avg_performance_review
FROM
    employee_info e
        JOIN
    hr_database h ON e.id = h.id
GROUP BY gender;

```

2. Find the average tenure of employees terminated from organization.

```sql

SELECT 
    ROUND(AVG(tenure), 2) AS avg_tenure
FROM
    (SELECT 
        id,
            employee,
            DATE_FORMAT(FROM_DAYS(DATEDIFF(termination_date, hire_date)), '%y') + 0 AS tenure
    FROM
        hr_database
    WHERE
        termination_date IS NOT NULL) AS a;

```
  
3. Find the educational qualifications of top 5 highest payed employees. 

```sql

SELECT 
    h.employee, h.education, e.salary
FROM
    hr_database h
        JOIN
    employee_info e ON h.id = e.id
ORDER BY e.salary DESC
LIMIT 5; 

```

4. Find the managers in order of highest no. of employees terminated under them.

```sql

with a as (
     select department, count(employee) as count_emp 
     from hr_database where termination_date is not null 
	 group by department order by count_emp)

SELECT 
    d.manager, a.department, a.count_emp
FROM
    department_info d
        JOIN
    a ON d.department = a.department
ORDER BY a.count_emp DESC; 

```

5. Find the top 5 cities with highest no. of female employees.

```sql

with a as (select * from hr_database where gender='Female')

SELECT 
    e.city, COUNT(a.employee)
FROM
    employee_info e
        JOIN
    a ON e.id = a.id
GROUP BY e.city
ORDER BY COUNT(a.employee) DESC
LIMIT 5;

```

### -- >>>> Advanced Level

1. Query the highest paid position in each department.

```sql

select department, position, salary from
(select department, position, salary, rank() over (partition by department order by salary desc) 
as rn from 
(select h.department, h.position, (e.salary) from hr_database h 
join employee_info e on h.id=e.id ) as a) as b where rn=1; 

```

2. Find the average performance review of employees based on the age brackets.

```sql

with a as (SELECT 
	h.id, h.employee,
	DATE_FORMAT(FROM_DAYS(DATEDIFF(NOW(), h.birth_date)), '%y') + 0 AS age, e.performance_review 
	FROM
        hr_database h join employee_info e on h.id=e.id)
        
, b as ( SELECT 
    CASE
        WHEN age BETWEEN 21 AND 30 THEN '21-30'
        WHEN age BETWEEN 31 AND 40 THEN '31-40'
        WHEN age BETWEEN 41 AND 50 THEN '41-50'
        WHEN age BETWEEN 51 AND 60 THEN '51-60'
        else '60 and above'
    END AS age_bucket, performance_review 
FROM
    a)
 
SELECT 
    age_bucket,
    ROUND(AVG(performance_review), 0) AS avg_performance_review
FROM
    b
GROUP BY age_bucket
ORDER BY age_bucket;

```

3. Find out the yearwise hirings and terminations.

```sql
 
with a as (SELECT 
    YEAR(termination_date) AS year,
    COUNT(termination_date) AS terminations
FROM
    hr_database
WHERE
    termination_date IS NOT NULL
GROUP BY year
ORDER BY year DESC)

, b as (SELECT 
    YEAR(hire_date) AS year, COUNT(id) AS hired
    FROM
        hr_database
    GROUP BY YEAR(hire_date)
    ORDER BY YEAR(hire_date)
      )

SELECT 
    b.year, b.hired, a.terminations
FROM
    b
        JOIN
    a ON b.year = a.year;

```

### -----------------------------------------------------xxxxxxxxxxxxxxxxx---------------------------------------------------


### Data Source:

- Kaggle

### Tools used:

- MS Excel – for data cleaning and understanding data
- SQL - for EDA and answering business questions

### Skills learned and used:

- MS Excel
- SQL
- Data Analysis and insights generation
- Project Documentation
  
