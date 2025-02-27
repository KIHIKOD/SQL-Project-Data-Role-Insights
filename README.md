# Introduction
Exploring the data job market! This project focuses on data analyst roles, examining 💰 the highest-paying positions, 🔥 in-demand skills, and 📈 the intersection of skill demand and salary potential.
🔍 SQL queries? Check them out here: [project_sql folder](/project_sql/)

# Background
Motivated by the need to navigate the data analytics job market efficiently, this project highlights the most lucrative and in-demand skills, making it easier for others to identify optimal job opportunities.

### Key Questions Explored:

1. Which data analyst roles offer the highest salaries?
2. What skills do top-paying jobs require?
3. Which skills are most in demand for data analysts?
4. Which skills correlate with higher salaries?
5.What are the most strategic skills to learn?

# Tools Utilized
To analyze the data analyst job market effectively, I used several key tools:

- **SQL:** Core tool for querying and extracting insights from the database.
- **PostgreSQL:** The selected database management system for handling job postings.
- **Visual Studio Code:** Primary platform for writing and executing SQL queries.
- **Git & GitHub:** Version control tools to document, share, and track my SQL scripts and findings.

# Analysis
Each SQL query in this project was designed to address specific aspects of the data analyst job landscape. Here’s a breakdown of my approach:

### 1. Top Paying Data Analyst Jobs
To determine the top-paying roles, I filtered job postings based on salary, focusing on remote positions.

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
LIMIT 10;
```
## Key Findings:
- **Salary Variation:** Top 10 paying data analyst roles span from $184,000 to $650,000, indicating significant salary potential in the field.
- **Diverse Employers:** Companies such as SmartAsset, Meta, and AT&T offer high salaries, spanning various industries.
- **Job Title Diversity:** Titles range from Data Analyst to Director of Analytics, reflecting the different specializations in the field.

![Uploading 1_top_paying_roles.png…]()
*Bar graph visualizing the salary for the top 10 salaries for data analysts; ChatGPT generated this graph from my SQL query results*

### 2. Skills for Top Paying Jobs
This query identifies the skills required for the highest-paying positions by joining job postings with skills data.

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
    salary_year_avg DESC;
```
## Key Findings:
- **SQL** appears in 8 out of 10 top-paying roles.
- **Python** follows closely, required in 7 of the top roles.
- **Tableau** is in high demand, in 6 of the highest-paying jobs.


![Uploading 2_top_paying_roles_skills.png…]()
*Bar graph visualizing the count of skills for the top 10 paying jobs for data analysts; ChatGPT generated this graph from my SQL query results*

### 3. Most In-Demand Skills for Data Analysts

This query determines which skills are requested most frequently in job postings.

```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND job_work_from_home = True 
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5;
```
## Key Findings 
- **SQL** and **Excel** are foundational for most data analyst roles.
- **Python**, **Tableau**, and **Power BI** are highly sought after for analytics and visualization.

| Skills   | Demand Count |
|----------|--------------|
| SQL      | 7291         |
| Excel    | 4611         |
| Python   | 4330         |
| Tableau  | 3745         |
| Power BI | 2609         |

*Table of the demand for the top 5 skills in data analyst job postings*

### 4. Skills Associated with Higher Salaries
By analyzing salary data by skill, we identify which technical competencies yield the highest earnings.

```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True 
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25;
```
## Key Findings:
- **Big Data & ML Skills:** PySpark, Couchbase, and DataRobot are highly valued.
- **Software Development & Deployment:** GitLab, Kubernetes, and Airflow proficiency boosts earning potential.
- **Cloud Computing:** Familiarity with cloud platforms like Elasticsearch, Databricks, and GCP enhances salaries.

| Skills        | Average Salary ($) |
|---------------|-------------------:|
| pyspark       |            208,172 |
| bitbucket     |            189,155 |
| couchbase     |            160,515 |
| watson        |            160,515 |
| datarobot     |            155,486 |
| gitlab        |            154,500 |
| swift         |            153,750 |
| jupyter       |            152,777 |
| pandas        |            151,821 |
| elasticsearch |            145,000 |

*Table of the average salary for the top 10 paying skills for data analysts*

### 5. Most Optimal Skills to Learn

Combining both demand and salary data, this query identifies the best skills to focus on for career growth.

```sql
SELECT 
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True 
GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```

| Skill ID | Skills     | Demand Count | Average Salary ($) |
|----------|------------|--------------|-------------------:|
| 8        | go         | 27           |            115,320 |
| 234      | confluence | 11           |            114,210 |
| 97       | hadoop     | 22           |            113,193 |
| 80       | snowflake  | 37           |            112,948 |
| 74       | azure      | 34           |            111,225 |
| 77       | bigquery   | 13           |            109,654 |
| 76       | aws        | 32           |            108,317 |
| 4        | java       | 17           |            106,906 |
| 194      | ssis       | 12           |            106,683 |
| 233      | jira       | 20           |            104,918 |

*Table of the most optimal skills for data analyst sorted by salary*

## Key Skills for Data Analysts: 
- **High-Demand Programming Languages:** Python and R remain top choices in the data analytics field, with demand counts of 236 and 148, respectively. While these languages are highly sought after, their average salaries—approximately $101,397 for Python and $100,499 for R—suggest that while expertise in them is essential, competition is also high.
- **Cloud Tools and Technologies:** The increasing reliance on cloud computing has driven demand for tools like Snowflake, Azure, AWS, and BigQuery. These technologies not only offer lucrative salaries but also underscore the growing role of cloud platforms in handling large-scale data operations.
- **Business Intelligence and Visualization Tools:** Tools such as Tableau (demand count: 230) and Looker (demand count: 49) have become indispensable for data professionals. With average salaries hovering around $99,288 and $103,795, respectively, these skills highlight the importance of visual storytelling in translating complex datasets into actionable insights.
- **Database Technologies:** Expertise in database management remains crucial, with demand for traditional relational databases like Oracle and SQL Server, as well as NoSQL solutions. Salaries for these skills range from $97,786 to $104,534, reflecting their fundamental role in data storage, retrieval, and management.

# What I Gained From This Experience

This deep dive into data analytics sharpened my SQL capabilities in several ways:

- **Complex Query Mastery:** Learned how to craft intricate SQL queries, seamlessly joining tables and leveraging WITH clauses for optimized temp table usage.
- **Data Aggregation:** Developed a solid understanding of GROUP BY operations, making aggregate functions like COUNT() and AVG() second nature.
- **Analytical Thinking:** Strengthened my problem-solving skills by transforming raw data into meaningful insights through well-structured SQL queries.

# Key Takeaways

### Insights
From the analysis, several general insights emerged:

1. **Top-Tier Salaries in Data Analytics**: Some of the highest-paying remote data analyst positions offer salaries reaching up to $650,000.
2. **SQL: A Critical Skill for High Earnings**: Advanced SQL proficiency is a common requirement for top-paying data analyst roles, reinforcing its importance for career growth.
3. **Most Sought-After Skill**: SQL continues to dominate as the most in-demand skill in the job market, making it essential for job seekers.
4. **Specialized Expertise Pays Off**: Niche skills like SVN and Solidity command higher salaries, demonstrating the value of specialized knowledge.
5. **Maximizing Marketability**: Since SQL remains the most requested skill with strong earning potential, mastering it provides a competitive edge in the industry.

### Closing Thoughts

This exploration not only refined my SQL skills but also deepened my understanding of the job market for data analysts. By identifying key skills with high demand and salary potential, this analysis serves as a strategic guide for career planning. Staying ahead in data analytics requires continuous learning and adaptability to evolving industry trends. By prioritizing high-value skills, aspiring data analysts can enhance their job prospects and long-term career success.
