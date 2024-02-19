# HR Data Analysis in SQL Server and Power BI

This project delves into the realm of data analysis utilizing SQL and Power BI to reveal significant insights regarding human resources that can greatly enhance the company's performance.
The report feautures dashboards that offer crucial HR metrics like employee turnover, recruitment efficacy and performance evaluations, that would help HR professionals make informed decisions and strategic workforce planning.

## Data Cleaning & Analysis:
SQL server 
- Data loading & inspection
- Handling missing values
- Data cleaning and analysis

## Data Visualization:
Power BI Desktop
![pbi report 1](https://github.com/eleonora6/hr_analysis/assets/160444566/f289b38f-abec-4d98-b622-d5f4d3c77b5d)
![pbi report 2](https://github.com/eleonora6/hr_analysis/assets/160444566/881f4a20-9266-4891-b324-ba56423c9172)


The .pbix file is included in the repository.

## Exploratory Data Analysis

1)	What's the age distribution in the company?
  The number of male employees surpasses that of female or non-conforming employees.
    
 - age distribution
         
 ``` SQL
SELECT
 MIN(age) AS youngest,
 MAX(age) AS OLDEST
FROM hr_data;
```

- age group count

``` SQL
SELECT age_group,
count(*) AS count 
FROM
(SELECT 
 CASE 
  WHEN age >=21 AND age <=30 THEN '21 to 30'
  WHEN age >=31 AND age <=40 THEN '31 to 40'
  WHEN age >=41 AND age <=50 THEN '41 to 50'
  ELSE '50+'
 END AS age_group
FROM hr_data
WHERE new_termdate IS NULL OR new_termdate> GETDATE()) 
AS subquery
GROUP BY age_group
ORDER BY age_group;
```

- age group by gender

``` SQL
SELECT age_group, gender,
count(*) AS count 
FROM
(SELECT 
 CASE 
  WHEN age >=21 AND age <=30 THEN '21 to 30'
  WHEN age >=31 AND age <=40 THEN '31 to 40'
  WHEN age >=41 AND age <=50 THEN '41 to 50'
  ELSE '50+'
 END AS age_group, gender
FROM hr_data
WHERE new_termdate IS NULL OR new_termdate> GETDATE()) 
AS subquery
GROUP BY age_group, gender
ORDER BY age_group, gender;

```
 
2)	What's the gender breakdown across departments?

    The distribution of genders across departments is relatively balanced. Overall, there is a slightly higher number of male employees.
    
 ``` SQL
SELECT gender,
COUNT(gender) AS count
FROM hr_data
WHERE new_termdate IS NULL OR new_termdate> GETDATE()
GROUP BY gender
ORDER BY gender ASC;
```

3) What's the age group distribution in the company and how is it distributed by gender?

    The company has a relatively small number of employees who are 50 years old or older. The majority of the workforce falls within the age range of 31 to 40 years old. Among all age groups, the 31-40 age range has the highest number of employees in the company.
  
``` SQL
SELECT age_group,
count(*) AS count 
FROM
(SELECT 
 CASE 
  WHEN age >=21 AND age <=30 THEN '21 to 30'
  WHEN age >=31 AND age <=40 THEN '31 to 40'
  WHEN age >=41 AND age <=50 THEN '41 to 50'
  ELSE '50+'
 END AS age_group
FROM hr_data
WHERE new_termdate IS NULL OR new_termdate> GETDATE()) 
AS subquery
GROUP BY age_group
ORDER BY age_group;
```

 The distribution of gender across age groups is relatively balanced. As mentioned before, the majority of the workforce falls within the age range of 31 to 40 years old.
``` SQL
SELECT age_group, gender,
count(*) AS count 
FROM
(SELECT 
 CASE 
  WHEN age >=21 AND age <=30 THEN '21 to 30'
  WHEN age >=31 AND age <=40 THEN '31 to 40'
  WHEN age >=41 AND age <=50 THEN '41 to 50'
  ELSE '50+'
 END AS age_group, gender
FROM hr_data
WHERE new_termdate IS NULL OR new_termdate> GETDATE()) 
AS subquery
GROUP BY age_group, gender
ORDER BY age_group, gender;
```

4)	How does gender vary across departments and job titles?

    Most employees belong to the engeneering department, whereas the auditing department has the least employees.

``` SQL
SELECT gender, department, jobtitle,
count(gender) AS count
FROM hr_data
WHERE new_termdate IS NULL OR new_termdate> GETDATE()
GROUP BY department, jobtitle, gender
ORDER BY department, jobtitle,  gender ASC;

```
5)	What's the Recruitment source distribution in the company?

     Most employees were hired through CareerBuilder and the least amount of employees were hired through On-line web application. The distribution of the other Recruitment sources is relatively balanced.
   
``` SQL
SELECT recruitmentsource,
count(*) AS count
FROM hr_data
WHERE new_termdate IS NULL OR new_termdate> GETDATE()
GROUP BY recruitmentsource
ORDER BY count DESC;

```
6)	What's the average length of employment in the company?

   The average length of employment is 7 years.
   
``` SQL
SELECT 
AVG(DATEDIFF(YEAR, hire_date, new_termdate)) AS tenure
FROM hr_data
WHERE new_termdate IS NOT NULL AND new_termdate <= GETDATE();
```

7) What's the average length of employement by Recruitment source?
 
    The averge tenure for employees that got hired through CareerBuilder and On-line web application is 8 years, whereas for the other sources it's 7 years.

 ``` SQL  
SELECT 
recruitmentsource,
AVG(DATEDIFF(YEAR, hire_date, new_termdate)) AS tenure
FROM hr_data
WHERE new_termdate IS NOT NULL AND new_termdate <= GETDATE()
GROUP BY recruitmentsource 
ORDER BY  tenure DESC;
```
8)	Which department has the highest turnover rate?
	
    The department with the highest turnover rate is Auditing, whereas Legal, Research & Development, and Training follow closely behind. On the other hand, Business Development & Marketing boast the lowest turnover rates.
    
``` SQL
SELECT
department, total_count, terminated_count, ROUND((CAST(terminated_count AS float)/total_count), 2) * 100 AS turnover_rate
FROM 
   (SELECT
   department, 
   count (*) AS total_count,
   SUM(CASE WHEN new_termdate IS NOT NULL AND new_termdate <= GETDATE() THEN 1 ELSE 0
   END) AS terminated_count
   FROM hr_data
   GROUP BY department) AS subquery
ORDER BY turnover_rate DESC;

```
9)	What is the tenure distribution for each department?

   Tenure is quite evenly distributed across departments, however Sales, Marketing and Auditing have the highest tenure(8 years) and Legal and Product Management the lowest(6 years).
    
``` SQL

SELECT 
department, 
AVG(DATEDIFF(YEAR, hire_date, new_termdate)) AS tenure
FROM hr_data
WHERE new_termdate IS NOT NULL AND new_termdate <= GETDATE()
GROUP BY department
ORDER BY tenure DESC;
```

10)	How many employees work remotely?
 
    24.9% of employees work remotely.
    
``` SQL
SELECT location,
COUNT(*) AS count
FROM hr_data
WHERE new_termdate IS NULL OR new_termdate >=GETDATE()
GROUP BY location;

```
11)	What's the distribution of employees across different states?

   	Most employees are from Ohio (15996) and the least amount of employees are from Wisconsin (341).
``` SQL
SELECT
location_state,
COUNT(*) AS count
FROM hr_data
WHERE new_termdate IS NULL OR new_termdate >=GETDATE()
GROUP BY location_state
ORDER BY count DESC;
```
12)	How are job titles distributed in the company?
 
    There are 184 job titles in the company. The most amount of employees have the job titles Research Assistants(690), Business Analyst(632) and HR Anlalyst(543). Only one employee has the title Office Assistant, Statistician, Assistant Professor, Marketing Manager, Executive Secretary, VP of Training and Development and Associate Professor.
   	
 ``` SQL
SELECT 
jobtitle,
COUNT(*) AS count
FROM hr_data
WHERE new_termdate IS NULL OR new_termdate >=GETDATE()
GROUP BY jobtitle
ORDER BY count DESC;
```

13)	How have employee hire counts varied over time?

   Employee hire counts have varied over the years.

 ``` SQL
SELECT
hire_year,
hires,
terminations,
hires - terminations AS net_change,
(ROUND(CAST(hires-terminations AS FLOAT)/hires, 2)) * 100 AS percent_hire_change
FROM
(SELECT
YEAR(hire_date) AS hire_year,
COUNT(*) AS hires, 
SUM(CASE 
    WHEN  new_termdate IS NOT NULL OR new_termdate <=GETDATE() THEN 1 ELSE 0
	END) AS terminations
FROM hr_data
GROUP BY YEAR(hire_date))
AS subquery
ORDER BY percent_hire_change ASC;
```


