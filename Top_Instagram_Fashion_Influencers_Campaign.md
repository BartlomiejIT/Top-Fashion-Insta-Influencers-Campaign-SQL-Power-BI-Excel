# Data Portfolio: Excel to Power BI
*excel-to-powerbi-animated-diagram*

---

## Table of Contents
1. [Objective](#objective)  
2. [Data Source](#data-source)  
3. [Stages](#stages)  
4. [Design](#design)  
5. [Mockup](#mockup)  
6. [Tools](#tools)  
7. [Development](#development)  
8. [Pseudocode](#pseudocode)  
9. [Data Exploration](#data-exploration)  
10. [Data Cleaning](#data-cleaning)  
11. [Transform the Data](#transform-the-data)  
12. [Create the SQL View](#create-the-sql-view)  
13. [Testing](#testing)  
14. [Data Quality Tests](#data-quality-tests)  
15. [Visualization](#visualization)  
16. [Results](#results)  
17. [DAX Measures](#dax-measures)  
18. [Analysis](#analysis)  
19. [Findings](#findings)  
20. [Validation](#validation)  
21. [Discovery](#discovery)  
22. [Recommendations](#recommendations)  
23. [Potential ROI](#potential-roi)  
24. [Potential Courses of Action](#potential-courses-of-action)  
25. [Conclusion](#conclusion)

---

## 1. Objective

### Top Instagram Fashion Influencers Campaign Analysis
**Overview**

**Main Goal**  
The main goal of this project is to identify top-performing Instagram influencers in the United States who create content in categories such as *lifestyle*, *fashion*, and *modeling*. The purpose is to effectively reach these audiences with a high-end product line and ensure successful marketing campaigns.

- **Solution**  
  To achieve this, I created an interactive dashboard in **Power BI**, focusing on:  
  1. Number of followers  
  2. Average authentic engagement  
  3. Engagement rate  

  This helps the marketing team make better decisions when choosing which influencers to collaborate with.

- **User Perspective**  
  As a Marketing Manager, I want a dashboard that analyzes influencer performance in the U.S.  
  This dashboard should allow me to quickly identify Instagrammers with the highest follower counts and best engagement, thus maximizing the effectiveness of each marketing campaign.

---

## 2. Data Source
The dataset is called **"top 1000 instagrammers"** and is sourced from Kaggle.

---

## 3. Stages
- **Design**  
- **Development**  
- **Testing**  
- **Analysis**

---

## 4. Design

**Key Questions the Dashboard Answers**  
1. Who are the **Top 10 Instagram influencers** with the highest follower counts?  
2. Which **3 influencers** have the highest average engagement?  
3. Which **3 influencers** have the highest engagement rates?

---

## 5. Mockup
*(Insert any UI mockups or wireframes of the planned dashboard, if available.)*

---

## 6. Tools

| **Tool**             | **Purpose**                                                    |
|----------------------|----------------------------------------------------------------|
| Excel                | Exploration and initial data cleaning                          |
| PostgreSQL           | Further data cleaning, testing, and analysis                  |
| Power BI             | Data visualization through interactive dashboards             |
| Jupyter Notebook     | Interactive notebook for the project                          |

---

## 7. Development

An overview from data import to final visualization in Power BI.

### Data Preparation and Cleanup in Excel
- Convert values like "1.4M" or "500K" into proper numeric values.  
- Remove unnecessary columns.  
- Prepare a CSV file for import into PostgreSQL.

---

## 8. Pseudocode

1. **Obtain the data** from Kaggle in CSV format.  
2. **Clean** in Excel by dropping columns and converting "K"/"M" to integers.  
3. **Import** data into PostgreSQL.  
4. **Create** a schema and table, then **test** and **clean** the data (e.g., duplicates, empty, zero values).  
5. **Generate** an SQL view (e.g., `filtered_top_50_influ_by_country_and_categories`).  
6. **Connect** the database to Power BI and **build** the final dashboard.

---

## 9. Data Exploration

**Example: Creating a schema and table in PostgreSQL**

```sql
CREATE SCHEMA project_sql_excel_power_bi;

CREATE TABLE project_sql_excel_power_bi.top_1000_influencers (
    name VARCHAR(50),
    rank INT,
    category VARCHAR(255),
    audience_country VARCHAR(50),
    followers_as_num INT,
    authentic_engagement_as_num NUMERIC,
    engagement_avg_as_num NUMERIC
);
```
*(Then import data from the CSV into this table.)*

---

## 10. Data Cleaning

- Remove duplicates  
- Skip rows with empty categories or zero engagement  
- Ensure proper columns and rows

**Example**:
```sql
CREATE TABLE view_influ_data_copy AS (
    SELECT *
    FROM view_influ_data
);

WITH no_duplicates_and_missing_values AS (
    SELECT
        ctid,
        ROW_NUMBER() OVER (PARTITION BY influencer_name ORDER BY rank) AS rn
    FROM
        project_sql_excel_power_bi.view_influ_data_copy
)
DELETE
FROM project_sql_excel_power_bi.view_influ_data_copy
WHERE ctid IN (
    SELECT ctid
    FROM no_duplicates_and_missing_values
    WHERE rn > 1
       OR category = ''
       OR avg_authentic_engagement = 0
);
```

---

## 11. Transform the Data
*(Insert additional SELECT or UPDATE snippets, if any exist.)*

---

## 12. Create the SQL View

```sql
CREATE VIEW project_sql_excel_power_bi.view_influ_data AS (
    SELECT
        name AS influencer_name,
        rank,
        category,
        audience_country,
        followers_as_num AS followers,
        authentic_engagement_as_num AS avg_authentic_engagement,
        engagement_avg_as_num AS avg_engagement
    FROM 
        project_sql_excel_power_bi.top_1000_influencers
);
```

---

## 13. Testing
Checking number of rows, columns, duplicates, missing data, etc.

---

## 14. Data Quality Tests

**1. Row Count Check**  
![Row Count (1000)](num_of_rows.png)  

After cleaning:  
![Row Count (877)](num_of_rows_after_drop_values.png)

**2. Column Count Check**  
![Column Count (7)](num_of_columns.png)

**3. Missing Data**  
```sql
SELECT COUNT(*) AS empty_values_count
FROM view_influ_data
WHERE category = '';

SELECT COUNT(*) AS zero_value_count
FROM view_influ_data
WHERE avg_authentic_engagement = 0;
```
![Empty Values](empty_values.png)  
![Zero Values](zero_values.png)

---

## 15. Visualization

**Filter data** and create a view to import into Power BI:

```sql
CREATE VIEW filtered_top_50_influ_by_country_and_categories AS (
    SELECT
        influencer_name,
        category,
        avg_authentic_engagement,
        followers
    FROM
        view_influ_data_copy
    WHERE
        audience_country = 'United States'
        AND (
          category LIKE '%Lifestyle%'
          OR category LIKE '%Fashion%'
          OR category LIKE '%Modeling%'
        )
    ORDER BY
        avg_authentic_engagement DESC
    LIMIT 50
);
```

### Dashboard in Power BI  
![Dashboard Overview](Dashboard_pic.png)

---

## 16. Results

1. **Top 10 Influencers by Followers**  
   ![Top 10 by Followers](Top10_by_followers.png)  

2. **Top 3 Influencers by Engagement Rate**  
   ![Top 3 by Engagement Rate](Top3_by_engagement_rate.png)  

3. **Top 3 Influencers by Average Engagement**  
   ![Top 3 by Average Engagement](Top3_by_avg_engagement.png)

---

## 17. DAX Measures

1. **Total Engagement (M)**
   ```DAX
   Total Engagement (M) =
   DIVIDE(SUM(top_50_influencers[avg_authentic_engagement]), 1000000)
   ```
2. **Total Followers (M)**
   ```DAX
   Total Followers (M) =
   DIVIDE(SUM(top_50_influencers[followers]), 1000000)
   ```
3. **Avg Authentic Engagement (M)**
   ```DAX
   Avg Authentic Engagement (M) =
   VAR AvgEngagement = AVERAGE(top_50_influencers[avg_authentic_engagement])
   RETURN DIVIDE(AvgEngagement, 1000000, BLANK())
   ```
4. **Engagement Rate**
   ```DAX
   Engagement Rate =
   VAR TotalEngagement = SUM(top_50_influencers[avg_authentic_engagement])
   VAR TotalFollowers = SUM(top_50_influencers[followers])
   RETURN DIVIDE(TotalEngagement, TotalFollowers, 0) * 100
   ```
5. **Influencer Percentage Contribution**
   ```DAX
   Influencer Percentage Contribution =
   VAR TotalEngagement =
       CALCULATE(SUM(top_50_influencers[avg_authentic_engagement]),
                 ALL(top_50_influencers))
   VAR IndividualEngagement =
       SUM(top_50_influencers[avg_authentic_engagement])
   RETURN DIVIDE(IndividualEngagement, TotalEngagement, 0) * 100
   ```

---

## 18. Analysis

1. **Top 10 by Followers** → Kylie Jenner, Selena Gomez, Kim Kardashian, etc.  
2. **Highest Average Engagement** → Zendaya, Kendall Jenner, Harry Styles.  
3. **Highest Engagement Rate** → Angus Cloud, Vinnie Hacker, Barbie Ferreira.

---

## 19. Findings

### Influencers with the Most Followers

- **Conversion Rate** = 0.02  
- **Product Cost** = \$300  
- **Campaign Cost (one-time fee)** = \$75,000  

**Excel** vs **SQL**  
![Product Placement Excel](Product_Placement_Excel.png)  
![Product Placement SQL](Product_Placement_SQL.png)

Kim Kardashian yields the highest net profit in this category.

---

### Influencers with Highest Average Engagement

- **Conversion Rate** = 0.02  
- **Product Cost** = \$300  
- **Campaign Cost (3-month contract)** = \$140,000  

**Excel** vs **SQL**  
![Influencer Marketing Excel](Influencer_Marketing_Excel.png)  
![Influencer Marketing SQL](Influencer_Marketing_SQL.png)

Zendaya shows the best net profit here.

---

### Influencers with Highest Engagement Rate

- **Conversion Rate** = 0.02  
- **Product Cost** = \$300  
- **Campaign Cost (per post)** = \$7,500  
- **Number of Posts** = 11

**Excel** vs **SQL**  
![Sponsored Post Series Excel](Sponsored_Post_Series_Excel.png)  
![Sponsored Post Series SQL](Sponsored_Post_Series_SQL.png)

Angus Cloud provides the highest total profit for an 11-post series.

---

## 20. Validation

1. **Kylie Jenner, Selena Gomez, Kim Kardashian** → best for huge visibility (followers).  
2. **Zendaya, Kendall Jenner, Harry Styles** → highest average engagement.  
3. **Angus Cloud, Vinnie Hacker, Barbie Ferreira** → highest engagement rate.

---

## 21. Discovery

**Challenges**  
1. Converting “K”/“M” textual data to numeric.  
2. Removing duplicates and rows with zero engagement.  
3. Choosing the right KPIs and visualizations.  
4. Matching campaign strategies (product placement, influencer marketing, sponsored post series) to each influencer’s profile.

---

## 22. Recommendations

1. **Zendaya** and **Kendall Jenner** → best for a long-term collaboration (high engagement).  
2. **Kim Kardashian / Selena Gomez** → perfect for one-time product placement (huge reach).  
3. **Angus Cloud** → lower cost, high engagement ratio, good ROI in a multiple-post scenario.

---

## 23. Potential ROI

- **Zendaya** ~ \$25,660,000 net profit per post.  
- **Kendall Jenner** ~ \$17,860,000 net profit per post.  
- **Angus Cloud** ~ \$4,978,500 per post; \$54,763,500 total across 11 posts.  
- **Kim Kardashian** ~ \$10,125,000 for a single product placement deal.

---

## 24. Potential Courses of Action

1. **Long-term partnership** with Zendaya or Kendall Jenner for consistently high engagement.  
2. **One-time placement** with Kim Kardashian to leverage broad reach.  
3. **Sponsored post series** with Angus Cloud, maximizing ROI at lower per-post costs.

---

## 25. Conclusion

Focusing on **average engagement** and **engagement rate** tends to be more effective than looking solely at follower count. Using Excel for initial transformations, PostgreSQL for cleaning/testing, and Power BI for visualization provides a solid end-to-end workflow for selecting the right influencers.

**Summary**  
- **Zendaya** → highest net profit among top-engagement influencers.  
- **Kendall Jenner** and **Angus Cloud** → strong returns at relatively lower costs.  
- **Kim Kardashian** → excellent option for immediate reach with product placement.


