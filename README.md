# Data Exploration with T-SQL

## Dataset
<a href="https://github.com/utkarshdatawhiz993/DataExploration_SQL/blob/main/salaries.csv">Salaries</a>

```Lets start```
```
-- Changing the employement type abbreviation to full name of employment type 

update salaries
set employment_type =
	Case employment_type
		WHEN 'PT' THEN 'Part_time'
		WHEN 'FT' THEN 'Full-time'
		WHEN 'CL' THEN 'Contract'
		ELSE 'Freelance'
	END```


-- Changing abbreviation of experience level to full name 

```UPDATE salaries
SET experience_level = 
    CASE experience_level
        WHEN 'SE' THEN 'Senior'
        WHEN 'EN' THEN 'Entry-Level'
        WHEN 'EX' THEN 'Executive'
        WHEN 'MI' THEN 'Mid-Level'
    END;```


-- Finding out top 10 maximum salary earning emploment (job title)? 

select top 10 job_title, max(salary_in_usd) as max_salary
from salaries
group by job_title
order by max_salary desc



-- Finding out top 10 average salary earning emploment (job title)? 

SELECT TOP 10 job_title, 
       AVG(CAST(salary_in_usd AS FLOAT)) AS avg_salary
FROM salaries
GROUP BY job_title
ORDER BY avg_salary DESC;


-- Which job titles are earning more than avg salary?

select top 10
	Job_title, salary_in_usd
from salaries
where salary_in_usd > (select avg(CAST(salary_in_usd AS FLOAT)) from salaries)



-- Comparison between full-time and contract employment_type average salary among various job titles?

select top 3
	job_title, employment_type, 
	avg(salary_in_usd) as avg_salary,
	rank() over (partition by job_title order by salary_in_usd desc) as ranking
from salaries
group by job_title, employment_type



-- Comparison between full-time and contract employment_type average salary among various job titles

SELECT 
       employment_type, 
       AVG(CAST(salary_in_usd AS FLOAT)) AS avg_salary
FROM salaries
WHERE employment_type IN ('Full-time', 'Contract')
GROUP BY employment_type 
ORDER BY avg_salary DESC;



-- Salary trending: average salary trend across years (2020, 2021. 2022, 2023, 2024, 2025)?

select work_year, 
	AVG(CAST(salary_in_usd as float)) as avg_salary
from salaries
GROUP BY work_year
order by avg_salary desc --Amazing finding: average salary in 2024 is highler than 2025 across job titles which is unusual 



-- top 5 ranking of average salary across employee_residence(Country)?

SELECT TOP 5  
    AVG(CAST(salary_in_usd AS FLOAT)) AS avg_salary, 
    CASE employee_residence 
        WHEN 'QA' THEN 'Qatar'
        WHEN 'CD' THEN 'Congo'
        WHEN 'VE' THEN 'Venezuela' 
        WHEN 'US' THEN 'United States'
        WHEN 'IL' THEN 'Israel'
        ELSE 'Other'
    END AS Residence_Country
FROM salaries 
WHERE employee_residence IN ('QA', 'CD', 'VE', 'US', 'IL')
GROUP BY employee_residence
ORDER BY avg_salary DESC; -- Qatar shown highest average salary followed by Congo and Venezuela before United stated and Isreal


-- Which company size paying more to its employees (small, medium, large)?

select company_size,
	AVG(cast(salary_in_usd as decimal (18,2))) as avg_salary
from salaries
group by company_size
order by avg_salary desc -- Large size company paid more than Medium and small size company


-- Top 10 job title earning more in terms of average salary for senior level and Mid-Level?

select top 10 job_title,
	experience_level,
	AVG(cast(salary_in_usd as float)) as avg_salary
from salaries
group by job_title, experience_level
having experience_level IN ('Senior', 'Mid-level')
order by avg_salary desc -- AI Architect average earning at mid-senior level is highest followed by research team lead at senior level. 
