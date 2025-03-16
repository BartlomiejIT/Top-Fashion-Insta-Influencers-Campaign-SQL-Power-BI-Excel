# Top Fashion Insta-Influencers Campaign: SQL-Power BI-Excel

## Table of Contents

- [Objective](#objective)
- [Data Source](#data-source)
- [Stages](#stages)
- [Design](#design)
- [Dashboard Mockup](#dashboard-mockup)
- [Tools](#tools)
- [Development](#development)
  - [Pseudocode](#pseudocode)
  - [Data Exploration notes](#data-exploration-notes)
  - [Data Cleaning](#data-cleaning)
  - [Transform the Data](#transform-the-data)
  - [Create the SQL View](#create-the-sql-view)
- [Testing](#testing)
- [Visualization](#visualization)
  - [Results](#results)
  - [DAX Measures](#dax-measures)
- [Analysis](#analysis)
  - [Findings](#findings)
  - [Validation](#validation)
  - [Discovery](#discovery)
  - [Recommendations](#recommendations)
  - [Potential ROI](#potential-roi)
  - [Potential Courses of Actions](#action-plan)

# Objective

- What is the key pain point?

The Head of Marketing wants to find out who the top-performing Instagram Fashion Influencers are in 2024 to decide on which Influencers would be best to run marketing campaigns throughout the rest of the year.

- What is the ideal solution

To create a dashboard that provides insights into the top U.S. Influencers in 2024 that includes their

- followers count
- average authentic engagement 
- engagement rate

This will help marketing team make informed decisions about which Influencers to collaborate with for their marketing campaigns.

## User story

As the Head of Marketing, I want to use a dashboard that analyses Influencer data in the United States.

This dashboard should allow me to identify the top performing accounts based on metrics like followers, average authentic engagment and engagement rate.

With this information, I can make more informed decisions about which Instagrammers are right to collaborate with, and therefore maximize how effective each marketing campaign is.

# Data source

- What data is needed to achieve our objective?

We need data on the top US Instagrammers in 2024 that includes their

- account names
- total followers
- category
- audience country
- authentic engagement

- Where is the data coming from? The data is sourced from Kaggle (an Excel extract), [see here to find it]((https://www.kaggle.com/datasets/syedjaferk/top-1000-instagrammers-world-cleaned)).

# Stages

- Design
- Development
- Testing
- Analysis
  

# Design

- What should the dashboard contain based on the requirements provided?

To understand what it should contain, we need to figure out what questions we need the dashboard to answer:

1. Who are the top 10 Instagram influencers with the highest number of followers?

2. Which 3 influencers have the highest average engagement?

3. Which 3 influencers have the highest engagement rates?

For now, these are some of the questions we need to answer, this may change as we progress down our analysis.

# Dashboard mockup

- What should it look like?

Some of the data visuals that may be appropriate in answering our questions include:

1. Table
2. Treemap
3. Scorecards
4. Horizontal bar chart

![image](https://github.com/user-attachments/assets/8066f014-2636-4fbb-91f7-766fb760eb6b)

## Tools

| Tool | Purpose |
|-----------|-----------|
| Excel  | Exploring the data   |
| SQL Server  | leaning, testing, and analyzing the data    |
| Power BI  | Visualizing the data via interactive dashboards    |
| GitHub  | Hosting the project documentation and version control    |
| Mokkup AI  | Designing the wireframe/mockup of the dashboard    |

# Development

## Pseudocode


- What's the general approach in creating this solution from start to finish?

1. Get the data
2. Explore the data in Excel
3. Load the data into SQL Server
4. Clean the data with SQL
5. Test the data with SQL
6. Visualize the data in Power BI
7. Generate the findings based on the insights
8. Write the documentation + commentary
9. Publish the data to GitHub Pages

## Data Exploration notes

This is the stage where you have a scan of what's in the data, errors, inconcsistencies, bugs, weird and corrupted characters etc

- What are your initial observations with this dataset? What's caught your attention so far?

1. Data size and structure:

- Number of rows: 1000 records.

- Columns:

	- name – influencer name / id
	- rank – position in the ranking
	- category – category or combination of categories (e.g. Music, Lifestyle, Cinema & Actors/actresses)
	- followers – number of followers on Instagram
	- audience_country – primary country of the audience
	- authentic_engagement – measures real, valuable interactions with real users
	- avg_engagement – the overall average engagement, which may also include artificial or less valuable interactions

2. The collection is large and appears to be merged from different sources – resulting in duplicate rows, inconsistent categories and gaps in data.

3. Cleaning and standardization will be necessary (removing duplicates, standardizing values ​​in columns, eliminating/filling in empty fields).

4. It is worth analyzing the realism of the given numbers (e.g. high engagement with low followers or vice versa) and decide whether they should be corrected or rejected.

## Data cleaning


- What do we expect the clean data to look like? (What should it contain? What contraints should we apply to it?)
The aim is to refine our dataset to ensure it is structured and ready for analysis.

The cleaned data should meet the following criteria and constraints:

Only relevant columns should be retained.
- All data types should be appropriate for the contents of each column.
- No column should contain null values, indicating complete data for all records.

Below is a table outlining the constraints on our cleaned dataset:

| Property            | Description |
| ------------------- | ----------- |
| Number of Rows      | 875         |
| Number of Columns   | 5           |

And here 

And here is a tabular representation of the expected schema for the clean data:

| Column Name           | Data Type | Nullable |
| --------------------- | --------- | -------- |
| name                  | varchar   | no       |
| followers             | integer   | no       |
| audience_country      | varchar   | no       |
| authentic_engagement  | float     | no       |

- What steps are needed to clean and shape the data into the desired format?

1. Remove unnecessary columns by only selecting the ones you need
2. Remove duplicates rows and empty value rows

- Then create view with filtered data

## Transform the data

```sql
-- Select the required columns
-- Filtered data with no duplicates and empty values

WITH cleaned_data AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY name
            ORDER BY rank
        ) AS rn
    FROM instagrammers
    WHERE
        -- Exclude NULL or empty values in text columns
        name IS NOT NULL AND name <> ''
        AND category IS NOT NULL AND category <> ''
        AND audience_country IS NOT NULL AND audience_country <> ''

        -- Exclude NULL or zero values in numeric columns
        AND rank IS NOT NULL
        AND followers IS NOT NULL AND followers > 0
        AND authentic_engagement IS NOT NULL AND authentic_engagement > 0
)
SELECT
name,
category,
audience_country,
followers,
authentic_engagement
FROM cleaned_data
WHERE rn = 1;
```
## Create the SQL view

```sql
-- Create view as Filtered Table with no duplicates and any missing values limited to 50 rows in descending order.

CREATE VIEW top_50 AS
WITH cleaned_data AS (
    SELECT 
        *,
        ROW_NUMBER() OVER (
            PARTITION BY name
            ORDER BY rank
        ) AS rn
    FROM instagrammers
    WHERE
        -- Exclude NULL or empty values in text columns
        name IS NOT NULL AND name <> ''
        AND category IS NOT NULL AND category <> ''
        AND audience_country IS NOT NULL AND audience_country <> ''
        
        -- Exclude NULL or zero values in numeric columns
        AND rank IS NOT NULL
        AND followers IS NOT NULL AND followers > 0
        AND authentic_engagement IS NOT NULL AND authentic_engagement > 0
)
SELECT
    name,
    category,
    followers,
    audience_country,
    authentic_engagement
FROM cleaned_data
WHERE
    -- Keep only the first occurrence for each 'name' (lowest rank)
    rn = 1
    
    -- Filter by 'United States' only
    AND audience_country = 'United States'
    
    -- Filter categories containing 'Lifestyle', 'Fashion', or 'Modeling'
    AND (
        category LIKE '%Lifestyle%' 
        OR category LIKE '%Fashion%'
        OR category LIKE '%Modeling%'
    )
ORDER BY 
    authentic_engagement DESC
LIMIT 50;
```

# Testing

- What data quality and validation checks are you going to create?

Here are the data quality tests conducted:

## Row Count check

```sql
-- Count the total number of records (or rows) are in the SQL view

select
    COUNT(*) AS no_of_rows
from
    instagrammers;
```

![image](https://github.com/user-attachments/assets/32b3acd2-f136-45a4-b13d-964e605932d6)


## Column count check

```sql
-- Count the total number of columns (or fields) are in the SQL view

select
    count(*) as column_count
from
    information_schema.columns
where
    table_name = 'instagrammers'
   	and table_schema = 'public';
```

![image](https://github.com/user-attachments/assets/53ba0c17-6eae-476d-a87d-d73d18c4d603)

## Data type check

```sql
-- Check the data types of each column from the view by checking the INFORMATION SCHEMA view

select
    COLUMN_NAME,
    DATA_TYPE
from
    INFORMATION_SCHEMA.columns
where
    TABLE_NAME = 'instagrammers';
```

![image](https://github.com/user-attachments/assets/bc6d8a93-d066-4f7b-b30e-2973170fe665)

## Duplicate count check

```sql
-- Check for duplicate rows
    
SELECT
	COUNT(*) - COUNT(distinct name) as total_duplicates
FROM 
    instagrammers;
```

![image](https://github.com/user-attachments/assets/2d97511d-f208-4283-9dfe-9bf2b94275d0)

# Visualization

## Results

- What does the dashboard look like?

![image](https://github.com/user-attachments/assets/9a155e25-8227-4584-871f-5c48e835b241)

This shows the Top U.S. Fashion Instagrammers in 2024 so far.

## DAX Measures

### 1 Total Engagement (M)

```sql
Total Engagement (M) = DIVIDE(SUM(top_50_influencers[avg_authentic_engagement]), 1000000)
```

### 2. Total Followers (M)

```sql
Total Followers (M) = DIVIDE(SUM(top_50_influencers[followers]), 1000000)
```

### 3. Avg Authentic Engagement (M)

```sql
Avg Authentic Engagement (M) = 
VAR AvgEngagement = AVERAGE(top_50_influencers[avg_authentic_engagement])

RETURN DIVIDE(AvgEngagement, 1000000, BLANK())
```

### 4. Engagement Rate

```sql
Engagement Rate = 
VAR TotalEngagement = SUM(top_50_influencers[avg_authentic_engagement])
VAR TotalFollowers = SUM(top_50_influencers[followers])

RETURN DIVIDE(TotalEngagement, TotalFollowers, 0) * 100
```

# Analysis

## Findings

- What did we find?

For this analysis, we're going to focus on the questions below to get the information we need for our marketing client -

1. Who are the top 10 Instagram influencers with the highest number of followers?

2. Which 3 influencers have the highest average engagement?

3. Which 3 influencers have the highest engagement rates?



1. Who are the top 10 Instagram influencers with the highest number of followers?

| Rank | Influencer Name   | Total Followers (M) |
|-----:|-------------------|---------------------|
| 1    | kyliejenner       | 357.00              |
| 2    | selenagomez       | 334.90              |
| 3    | kimkardashian     | 323.60              |
| 4    | beyonce           | 267.40              |
| 5    | khloekardashian   | 260.40              |
| 6    | kendalljenner     | 247.60              |
| 7    | kourtneykardash   | 189.00              |
| 8    | zendaya           | 147.00              |
| 9    | iamcardib         | 137.30              |
| 10   | gigihadid         | 74.90               |

2. Which 3 influencers have the highest average engagement?

| Rank | Influencer Name | Avg Engagement (M) |
|-----:|-----------------|--------------------|
| 1    | zendaya         | 4.30               |
| 2    | kendalljenner   | 3.00               |
| 3    | harrystyles     | 2.10               |

3. Which 3 influencers have the highest engagement rates?

| Rank | Influencer Name | Engagement Rate |
|-----:|-----------------|-----------------|
| 1    | vinniehacker    | 11.99           |
| 2    | anguscloud      | 10.13           |
| 3    | barbieferreira  | 9.84            |

## Notes

For this analysis, we'll prioritize analysing the metrics that are important in generating the expected ROI for our marketing client, which are the Instagram profiles with the most

- followers
- authentic engagement
- egagement ratio 

## Validation

### 1. Influencers with the most followers.

#### Calculation breakdown:

Campaign idea = ***product placement***

Conversion Rate = 0.02

Product Cost = $300

Campaign Cost (one-time fee) = $75,000 

##### 1. **Kylie Jenner**

AVG Engagement per Post = 1,200,000

Potential Product Sales per Interaction = AVG Engagement per Post x Conversion Rate -> 1,200,000 x 0.02 = 24,000

Potential Revenue per Post = Potential Product Sales x Product Cost -> 24,000 x $300 = $7,200,000

Net Profit = Potential Revenue per Post - Campaign Cost = $7,200,000 - $75,000 = $7,125,000

##### 2. **Selena Gomez**

AVG Engagement per Post = 1,400,000

Potential Product Sales per Interaction = AVG Engagement per Post x Conversion Rate -> 1,400,000 x 0.02 = 28,000

Potential Revenue per Post = Potential Product Sales x Product Cost -> 28,000 x 300 = $8,400,000 

Net Profit = Potential Revenue per Post - Campaign Cost = 8,400,000 - 75,000 = 8,325,000

##### 3. **Kim Kardashian**

AVG Engagement per Post = 1,700,000

Potential Product Sales per Interaction = AVG Engagement per Post x Conversion Rate -> 1,700,000 x 0.02 = 34,000

Potential Revenue per Post = Potential Product Sales x Product Cost -> 34,000 x $300 = $10,200,000 

Net Profit = Potential Revenue per Post - Campaign Cost = $10,200,000 - $75,000 = $10,125,000

Best option from category: Kim Kardashian

SQL query

```sql
-- Top 3 by Followers Analysis

-- Campaign Idea = Product Placement
-- The conversion rate 0.02
-- The product cost $300
-- The campaign cost Campaign Cost (one-time fee) $75,000

WITH top_influ_by_followers AS (
    SELECT
        name,
        authentic_engagement,
        followers
    FROM
        top_50
)
select
	name,
	authentic_engagement,
	(authentic_engagement * 0.02) as potential_units_per_interactions,
	(authentic_engagement * 0.02 * 300) as potential_revenue_per_post,
	(authentic_engagement * 0.02 * 300 - 75000) as net_profit
from
	top_influ_by_followers
where 
	name in ('kyliejenner', 'selenagomez', 'kimkardashian')
order by
	net_profit desc;
```

Output

![image](https://github.com/user-attachments/assets/28598d2b-b281-48fe-959d-d6c8aced0fb6)

## 2. Influencers with highest average engagement.

Calculation breakdown

Campaign idea = ***influencer marketing***

Conversion Rate = 0.02

Product Cost = $300

Campaign Cost (3-month contract) = $140,000 

##### 1. **Zendaya**

AVG Engagement per Post = 4,300,000

Potential Product Sales per Interaction = AVG Engagement per Post x Conversion Rate -> 4,300,000 x 0.02 = 86,000

Potential Revenue per Post = Potential Product Sales x Product Cost -> 86,000 x $300 = $25,800,000

Net Profit = Potential Revenue per Post - Campaign Cost = $25,800,000 - $140,000 = 25,660,000

##### 2. **Kendall Jenner**

AVG Engagement per Post = 3,000,000

Potential Product Sales per Interaction = AVG Engagement per Post x Conversion Rate -> 3,000,000 x 0.02 = 60,000

Potential Revenue per Post = Potential Product Sales x Product Cost -> 60,000 x $300 = $18,000,000 

Net Profit = Potential Revenue per Post - Campaign Cost = $18,000,000 - $140,000 = $17,860,000

##### 3. **Harry Styles**

AVG Engagement per Post = 2,100,000

Potential Product Sales per Interaction = AVG Engagement per Post x Conversion Rate -> 2,100,000 x 0.02 = 42,000

Potential Revenue per Post = Potential Product Sales x Product Cost -> 42,000 x $300 = $12,600,000 

Net Profit = Potential Revenue per Post - Campaign Cost = $12,600,000 - $140,000 = $12,460,000

Best option from category: Zendaya

SQL query

```sql
-- Top 3 by avg authentic engagement Analysis
 
-- Capmpaign Idea = Influencer Marketing
-- The conversion rate 0.02
-- The product cost $300
-- The campaign cost Campaign Cost (3-month contract) $140,000 
 
WITH top_influ_by_eng AS (
    SELECT
        name,
        authentic_engagement,
        followers
    FROM
        top_50
)
select
	name,
	authentic_engagement,
	(authentic_engagement * 0.02) as potential_units_per_interactions,
	(authentic_engagement * 0.02 * 300) as potential_revenue_per_post,
	(authentic_engagement * 0.02 * 300 - 140000) as net_profit
from
	top_influ_by_eng
where 
	name in ('zendaya', 'kendalljenner', 'harrystyles')
order by
	net_profit desc;
```

Output

![image](https://github.com/user-attachments/assets/1a3b7173-691e-4480-a718-68b7d6aa1830)

## 3. Influencers with highest engagament rate. 

Calculation breakdown:

Campaign idea = ***Sponsored Post Series***

Conversion Rate = 0.02

Product Cost = $300

Campaign Cost (per post) = $7,500

Number of Posts = 11

##### 1. **Vinnie Hacker**

AVG Engagement per Post = 695,700

Potential Product Sales per Interaction = AVG Engagement per Post x Conversion Rate -> 695,700 x 0.02 = 13,914

Potential Revenue per Post = Potential Product Sales x Product Cost -> 13,914 x $300 = $4,174,200

Net Profit = Potential Revenue per Post - Campaign Cost ->  $4,174,200 - $7,500 = $4,166,700

Total Net Profit = Net Profit x Number of Posts -> $4,166,700 x 11 = $45,833,700

##### 2. **Angus Cloud**

AVG Engagement per Post = 831,000 

Potential Product Sales per Interaction = AVG Engagement per Post x Conversion Rate -> 831,000 x 0.02 = 16,620

Potential Revenue per Post = Potential Product Sales x Product Cost -> 16,620 x $300 = $4,986,000

Net Profit = Potential Revenue per Post - Campaign Cost -> $4,986,000 - $7,500 = $4,978,500 

Total Net Profit = Net Profit x Number of Posts -> $4,978,500 x 11 = $54,763,500

##### 3. **Barbie Ferreira**

AVG Engagement per Post = 620,000

Potential Product Sales per Interaction = AVG Engagement per Post x Conversion Rate ->  620,000 x 0.02 = 12,400

Potential Revenue per Post = Potential Product Sales x Product Cost -> 12,400 x $300 = $3,720,000

Net Profit = Potential Revenue per Post - Campaign Cost x Post -> $3,720,000 - $7,500 =  $3,712,500

Total Net Profit = Net Profit x Number of Posts -> $3,712,500 x 11 = $40,837,500

Best option from category: Angus Cloud

SQL query

```sql
-- Top 3 by Engagement Rate Analysis

-- Campaign Idea = Sponsored Post Series
-- The conversion rate = 0.02
-- The product cost = $300
-- The campaign cost (per-post) = $7,500
-- The number of posts 11

with top_influ_by_eng_rate as (
	SELECT
        name,
        authentic_engagement,
        followers
    FROM
        top_50
)
select
	name,
	authentic_engagement,
	(authentic_engagement * 0.02) as potential_units_per_interactions,
	(authentic_engagement * 0.02 * 300) as potential_revenue_per_post,
	(authentic_engagement * 0.02 * 300 - 7500) as net_profit_per_post,
	((authentic_engagement * 0.02 * 300 - 7500) * 11) as total_net_profit
from
	top_influ_by_eng_rate
where 
	name in ('vinniehacker', 'anguscloud', 'barbieferreira')
order by
	total_net_profit desc;
```
Output

![image](https://github.com/user-attachments/assets/4232db56-d436-47b9-8fcc-d95ce0941294)

## Discovery

- What did we learn?

We discovered that 

1. **Kylie Jenner**, **Selena Gomez**, and **Kim Kardashian** dominate in terms of follower count, which in cooperation may have an impact on visibility.

2. **Zendaya**, **Kendall Jenner**, and **Harry Styles** lead in authentic engagement, making them strong candidates for campaigns focused on interaction rather than pure visibility.

3. **Angus Cloud**, **Vinnie Hacker**, **Barbie Ferreira** have the highest engagement rate, which may indicate about regular posting and frequent interactions with followers.

- While there is a moderate correlation between followers and engagement, it is clear that having more followers does not always translate to higher engagement. Strategic selection of influencers based on campaign goals is crucial.

## Recommendations

- What do we recommend based on the insights gathered?

1. **Zendaya** and **Kendal Jenner** are the best instagrammers for long-term collaboration if we aim to maximize reach and profit because this profiles have the highest average engagement per post.

2. While **Kim Kardashian** and **Selena Gomez** have the largest number of followers in the United States, which could help boost visibility. We need to notice that collaboration in long-term is not profitable as in the case of **Zendaya** and **Kendal Jenner**.

3. A profile like **Angus Cloud** has the highest engagement to followers ratio. Kind of that influencer work with based on series posts could lead to higher profits with lower campaign costs.

4. The top channels for potential cooperation are **Zendaya** and **Kendall Jenner** for their generating engagement and profit. **Angus Cloud** for lower costs and higher returns. We may also include **Kim Kardashian** to consider a one-time product placement collaboration.

## Potential ROI:

- What ROI do we expect if we take this course of action?

- An **influencer marketing** contract with **Zendaya** could generate a net profit of **$25,660,000 per post**.  

- A collaboration with **Kendall Jenner** could bring a net profit of **$17,860,000 per post**.  

- A **sponsored post series** campaign featuring **Angus Cloud** could deliver **$4,978,500 per post**
  and **$54,763,500** when we count total result from all scheduled eleven posts.  

- A **product placement** deal with **Kim Kardashian** could result in a net profit of **$10,125,000**, making it a good option to consider.


## Action plan:

- What course of action should we take and why?

The best influencer to advance a long-term partnership deal with to promote the client's products is the **Zendaya**.
If we assume that influencer will share the post at least 3 times while of 3 months contract.

We'll have conversations with the marketing client to forecast what they also expect from this collaboration.

Once we observe we're hitting the expected key stages, we'll advance with potential partnerships with **Kendal Jenner**, **Angus Cloud** and **Kim Kardashian** instagrammers in the future.

- What stepds do we take to implement the recommended decisions effectively?

Reach out to the teams behind each of these profiles, starting with **Zendaya**.

Negotiate contracts within the budgets allocated to each marketing campaign.

Kick off the campaigns and track each of their performances against the KPIs (Key Performance Indicators).

Review how the campaigns have gone, gather insights and optimize based on feedback from converted customers and each profile's audiences.











