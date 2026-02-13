# Introduction
###  SQL Project: Data Analyst Job Market Insights

####  Project Overview
This project explores the **Data Analyst job market** using SQL queries to uncover insights about job demand, salaries, and in-demand skills.  
Using multiple datasets, I analyzed trends to answer five key questions:

1. What are the **top-paying data analyst jobs**?  
2. What **skills** are required for these top-paying jobs?  
3. What are the **most in-demand skills** for data analysts?  
4. Which skills are **associated with higher salaries**?  
5. What are the **most optimal skills to learn** for career growth?



SQL queries? Check them out
here: [project SQL folder](/project_sql/)




##  Tools I Used

For my deep dive into the Data Analyst job market,  
I harnessed the power of several key tools:

- **SQL:** The backbone of my analysis, enabling me to query the database and uncover critical insights.  
- **PostgreSQL:** The database management system of choice — reliable and efficient for handling large job posting datasets.  
- **Visual Studio Code:** My go-to environment for managing databases and executing SQL queries seamlessly.  
- **Git & GitHub:** Essential for version control and sharing SQL scripts and analysis, ensuring collaboration, transparency, and project tracking.

# Analysis



###  Query 1: Top-Paying Data Analyst Jobs


**Goal:** Identify the highest-paying Data Analyst positions.
####  SQL Query


```sql
SELECT
        job_id,
        job_title,
        job_location,
        job_schedule_type,
        salary_year_avg,
        job_posted_date,
        name AS company_name
FROM
        job_postings_fact    
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id            
WHERE
       job_title_short = 'Data Analyst' AND 
       job_location = 'Anywhere' AND 
       salary_year_avg IS NOT NULL     
ORDER BY
        salary_year_avg DESC
LIMIT 10
```

| Company | Job Title | Average Yearly Salary (USD) | Job Type |
|----------|------------|-----------------------------|-----------|
| Mantys | Data Analyst | **$650,000** | Full-time |
| Meta | Director of Analytics | $336,500 | Full-time |
| AT&T | Associate Director - Data Insights | $255,829 | Full-time |
| Pinterest | Data Analyst, Marketing | $232,423 | Full-time |
| Uclahealthcareers | Data Analyst (Hybrid/Remote) | $217,000 | Full-time |

**Insights:**
-  **Mantys** offers the **highest-paying** data analyst position at $650K annually.  
- Big tech companies like **Meta** and **AT&T** are among the top payers.  
- Most top-paying positions are **remote (Anywhere)** and **full-time**, showing strong global demand.  
- Leadership-focused analyst roles (like “Director” or “Principal Data Analyst”) consistently command higher salaries.

---

##  Query 2: Skills Required for Top-Paying Roles

**Goal:** Identify which technical skills appear in the highest-paying jobs.
####  SQL Query


```sql
WITH top_paying_jobs AS (
    SELECT
            job_id,
            job_title,
            salary_year_avg,
            name AS company_name
    FROM
            job_postings_fact    
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id            
    WHERE
        job_title_short = 'Data Analyst' AND 
        job_location = 'Anywhere' AND 
        salary_year_avg IS NOT NULL     
    ORDER BY
            salary_year_avg DESC
    LIMIT 10
)

SELECT 
        top_paying_jobs.*,
        skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
        salary_year_avg DESC
```

**Common Skills Among Top Roles:**
- **Core Analytics:** SQL, Python, R  
- **Visualization:** Tableau, Power BI, Excel  
- **Cloud & Big Data:** AWS, Azure, Databricks, Snowflake, Pyspark  
- **Collaboration Tools:** GitLab, Jira, Confluence

**Insights:**
>  High-paying data analyst roles require not only core analytics skills but also **cloud and big data ecosystem knowledge**.  
> These skills separate **senior analysts** from **entry-level analysts**.

---

##  Query 3: Most In-Demand Skills for Data Analysts

**Goal:** Rank skills by their frequency across job postings.
####  SQL Query


```sql
SELECT 
        skills,
        COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = True
GROUP BY
    skills
ORDER BY
    demand_count DESC    
LIMIT 5
```



| Skill | Demand Count |
|--------|---------------|
| SQL | **7,291** |
| Excel | 4,611 |
| Python | 4,330 |
| Tableau | 3,745 |
| Power BI | 2,609 |

**Insights:**
-  **SQL** dominates as the most in-demand skill — foundational for almost all analyst roles.  
-  **Excel** remains crucial, showing its ongoing importance in business analytics.  
-  **Python** ranks third, showing its growth in automation and modeling.  
-  **Tableau** and **Power BI** are the leading visualization tools.

**Conclusion:**
> Demand favors versatile analysts who master **SQL + Excel + Python + Visualization tools**.

---

##  Query 4: Skills Associated with Higher Salaries

**Goal:** Find which skills are associated with higher average salaries.
####  SQL Query


```sql
SELECT 
        skills,
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'  AND
    salary_year_avg IS NOT NULL
    AND job_work_from_home = True
GROUP BY
    skills
ORDER BY
     avg_salary DESC
LIMIT 25
```

| Skill | Avg. Salary (USD) |
|--------|-------------------|
| Pyspark | **$208,172** |
| Bitbucket | $189,155 |
| Couchbase | $160,515 |
| Watson | $160,515 |
| Pandas | $151,821 |
| GitLab | $154,500 |
| Databricks | $141,907 |

**Insights:**
-  **Big Data & Engineering skills** (Pyspark, Databricks) are linked to the **highest pay**.  
-  **Version control tools** (Bitbucket, GitLab) appear in senior data roles.  
-  **ML/AI-related tools** (Watson, Pandas, Scikit-learn) are associated with higher salaries.

**Conclusion:**
> The top-paying skills lean toward **data engineering and machine learning ecosystems**, showing how analysis is merging with data science.

---

##  Query 5: Optimal Skills to Learn

**Goal:** Combine **skill demand** and **average salary** to find the most valuable skills.

| Skill | Demand | Avg. Salary (USD) |
|--------|---------|------------------|
| Python | **236** | $101,397 |
| Tableau | 230 | $99,288 |
| R | 148 | $100,499 |
| Snowflake | 37 | $112,948 |
| Azure | 34 | $111,225 |
| AWS | 32 | $108,317 |
| Looker | 49 | $103,795 |
| Go | 27 | $115,320 |

**Insights:**
-  **Python** offers both **high demand** and **high salary** — a must-learn skill.  
-  **Cloud platforms (AWS, Azure, Snowflake)** show strong earning potential.  
-  **Visualization tools (Tableau, Looker)** are key for data storytelling.  
-  **Go (Golang)** provides high salary but low demand — good for niche specialization.

**Conclusion:**
> For the best career ROI, focus on **SQL, Python, Tableau, Power BI, and a cloud platform (AWS/Azure/Snowflake)**.

---

##  Overall Summary

| Focus Area | Key Finding |
|-------------|-------------|
| **Top Paying Roles** | Data Analyst ($650K), Director of Analytics ($336K),  Data Insights ($255K) |
| **Core Skills** | SQL, Python, R, Excel, Tableau |
| **In-Demand Tools** | SQL, Excel, Python, Tableau, Power BI |
| **High-Salary Skills** | Pyspark, Bitbucket, Databricks, Pandas |
| **Optimal Learning Path** | SQL → Python → Tableau → Cloud (AWS/Azure/Snowflake) |

---

##  Final Thoughts

This SQL-based project provides a **data-driven roadmap** for aspiring data analysts:

1. Master the essentials — **SQL, Python, Excel**  
2. Build strong visualization skills — **Tableau / Power BI**  
3. Learn **Cloud & Big Data tools** — AWS, Snowflake, Databricks  
4. Keep expanding — Pandas, Pyspark, GitLab  

> By blending SQL querying, data exploration, and market insights, this project demonstrates real-world analytical and technical skills essential for modern data analysts.






