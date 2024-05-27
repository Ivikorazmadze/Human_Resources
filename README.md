# Human_Resources

## Table of Contents

- [Project Overview](#project-overview)
- [Tools](#tools)
- [Data Preparation](#data-preparation)
- [Explaratory Data Analysis](#explaratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results/Findings](#resultsfindings)
- [Recommendations](#recommendations)
- [Limitations](#limitations)

### Project Overview 
---

The report provides insights about employees distribution in different fields  

<img width="603" alt="Overview" src="https://github.com/Ivikorazmadze/Human_Resources/assets/115480932/871d87ff-8f4b-42c0-957e-eeb51285a59f">


### Tools
---

- Data Source - Excel [Download Here](https://www.microsoft.com/en-us/microsoft-365/p/excel-home-and-student/CFQ7TTC0HLKR?activetab=pivot:overviewtab)

- Data Cleaning & Analysis - MySQL Workbench [Download Here](https://dev.mysql.com/downloads/workbench/)

- Data Visualization - PowerBI [Download here](https://dev.mysql.com/downloads/workbench/)

### Data Preparation
---

The initial data took several tasks to elaborate
1) Data Loading and inspection
  
2) Handling Missing Values
  
3) Data formating

### Explaratory Data Analysis 
---

EDA involved in the following report to answer key questions, such as:

- Average Length of Employment

- Gender Distribution

- Locations of Work

- Termination Rate Via Departments

- Change in Total Employees Number

- Age Group Distribution

### Data Analysis
---

Data Analysis featured following syntaxes:

```sql 
create database project
```
```sql
select * from project
```
```sql
alter table project change column ï»¿id  emp_id varchar(20) null
```
```sql
describe project
```
```sql
UPDATE project
SET birthdate = CASE
  WHEN birthdate LIKE '%/%' THEN DATE_FORMAT(STR_TO_DATE(birthdate, '%m/%d/%Y'), '%Y-%m-%d')
  WHEN birthdate LIKE '%-%' THEN DATE_FORMAT(STR_TO_DATE(birthdate, '%m-%d-%y'), '%Y-%m-%d')
  ELSE NULL
END
```
```sql
alter table project modify birthdate date
```
```sql
UPDATE project
SET hire_date = CASE
  WHEN hire_date LIKE '%/%' THEN DATE_FORMAT(STR_TO_DATE(hire_date, '%m/%d/%Y'), '%Y-%m-%d')
  WHEN hire_date LIKE '%-%' THEN DATE_FORMAT(STR_TO_DATE(hire_date, '%m-%d-%y'), '%Y-%m-%d')
  ELSE NULL
END
```
```sql
update project
set termdate = date(str_to_date(termdate, '%Y-%m-%d %H:%i:%s UTC'))
where termdate is not null and termdate !=''
```
```sql
alter table project modify column hire_date date
```
```sql
alter table project add column age int
```
```sql
select gender, count(*) as count from project where age >= 18  group by gender
```
```sql
select min(age) as youngest, max(age) as oldest from project where age >= 18
```
```sql
select 
	case 
    when age >=18 and age <= 24 then '18-24'
    when age >=25 and age <= 34 then '25-34'
    when age >=35 and age <= 44 then '35-44'
    when age >=45 and age <= 54 then '46-54'
    when age >=55 and age <= 64 then '55-64'
    else '65+'
    end as group_age, gender,
    count(*) as count
from project
where age >=18 
group by group_age, gender
order by group_age, gender
```
```sql
select round(avg(datediff(termdate, hire_date)) / 365.0) as avg_length_employment
from project 
where age >=18 and termdate <curdate()
```
```sql
select 
	  department,
      total_count,
	  terminated_count,
      terminated_count/total_count as termination_rate
from
(select department,
		count(*) as total_count,
        sum(case when termdate is not null and termdate <= curdate() then 1 else 0 end) as terminated_count 
        from project
        where age >=18  
        group by department) as subquery
order by termination_rate desc
```
```sql
select
year,
hires,
terminations,
hires - terminations as net_change,
round((hires-terminations)/hires * 100,2) as net_change_percent
from(
select year(hire_date) as year,
count(*) as hires,
sum(case when termdate <= curdate() then 1 else 0 end) as terminations
from project 
where age >= 18
group by year(hire_date)
) as subquery  
order by year
```
```sql
SELECT 
    department,
    ROUND(AVG(DATEDIFF(termdate, hire_date) / 365),
            0) AS avg_tenure
FROM
    project
WHERE
    age >= 18 AND termdate < CURDATE()
GROUP BY department
```

### Results/Findings
---

The analysis are as follows:
- 84.1 % of employees are between 25 and 54 years
- A large number of employees work at the headquarters versus remotely.
- Number of employees have been rising since 2000
- Gender Distribution is almost same but males are slightly more
- The Marketing department has the highest turnover rate followed by Training. The least turn over rate are in the Research and development, Support and Legal departments
- 5 age groups were created (18-24, 25-34, 35-44, 45-54, 55-64). A large number of employees were between 25-34 followed by 35-44 while the smallest group was 55-64

### Recommendations
---

Based on the analysis we recommend the following:
- prioritize remote working to attract 18-24 age group employees
- Take a deeper look at marketing department to eliminate the turnover ratio

### Limitations
---

- I had to remove blank cells from termdate column as it would have effected the accuracy of the report
- I concluded that people <= 18 could not accepted as an employees






