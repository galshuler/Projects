#  Exploratory Data Analysis : EchoSphere Optimizing App Installations

<span style="font-size:18px;"> 
<p align="justify">
  
## Background

'EchoSphere Networks', a renowned mobile casual app, approached us with a challenge: they needed to optimize their app's market penetration in California. Our task was to delve into their raw data, identify key markets for targeted marketing, understand platform-based revenue contributions, and ensure data integrity. 
This case study outlines our approach using SQL to analyze the data and the insights we derived.

## Ask

**Objective:** 

The primary goal was to use data analysis to guide 'EchoSphere Networks' in optimizing their app installations and revenue generation. 

**The specific objectives were to:**

1. Determine cities in California with the highest app install rates.
   
2. Identify top cities for targeted marketing to maximize installations.
 
3. Categorize unique users by their operating systems.
   
4. Compare revenue streams between New York and California.
   
5. Rank operating systems based on revenue generation.
    
6. Investigate any data anomalies and suggest improvements.

## Prepare

**Data Sources:**

**Events Table**

| Column Name | Description                                            | Example Values           |
|-------------|--------------------------------------------------------|--------------------------|
| user_id     | Unique identifier for each user                        | u-kq5atd8ral851b3g       |
| event_type  | Type of event recorded (e.g., level up, install, etc.) | level_up                 |
| timestamp   | Date and time when the event occurred                  | 2021-08-26 19:17:01.664071 |
| location    | User's location, formatted as City_State               | Santa Cruz_California    |
| device      | Type and version of the user's device                  | IOS_10.1.1, ANDROID_26   |


**Revenue Table**

| Column Name          | Description                                       | Example Values           |
|----------------------|---------------------------------------------------|--------------------------|
| user_id              | Unique identifier for each user                   | u-kq31q7pwb1o8sye        |
| local_transaction_at | Timestamp of the transaction                      | 2021-08-15 20:49:55.997  |
| revenue_id           | Unique identifier for each revenue transaction    | o_swl668-kse3md25        |
| usd                  | Amount of transaction in USD                      | 0.09                    |
| revenue_type         | Type of revenue (e.g., banner, IAP, interstitial) | banner                   |


**Data Cleaning and Validation:**

- Addressed missing values and duplicate records in both tables.
- Ensured data integrity for accurate analysis.

## Analyze

- All analyzes were performed using PostgreSQL.

### 1. Installation Rate by City:
   
**Objective:**

Identify cities in California with their corresponding app install rates, ranked from highest to lowest.

**Query:**

```sql
SELECT
  -- Extracts the city name from the location.
  split_part(location, '_', 1) AS city_name,
  -- Calculates the install rate.
100 * COUNT(DISTINCT CASE WHEN event_type = 'install' THEN user_id END)::float / COUNT(DISTINCT user_id) AS install_rate
FROM Events
WHERE 
  location LIKE '%_California' AND  -- Filters for cities in California.
  event_type IS NOT NULL AND  -- Excludes rows where event_type is null.
  location IS NOT NULL  -- Excludes rows where location is null.
GROUP BY  city_name
-- Orders by the highest install rate to the lowest.
ORDER BY install_rate DESC;
```

**Insight:** 

-	Installation Rate Calculation: The query calculates the installation rate as the percentage of 'install' events out of all events recorded for each city in California. This metric provides a clear indicator of user engagement and app acceptance in different regions.

-	Exclusion of Null Values: The query includes conditions to exclude rows where 'event_type' or 'location' is null. These conditions ensure that rows with null values in critical fields are excluded, thereby improving the accuracy and reliability of the install rate calculation


#### 2. Top 3 Cities for Targeted Marketing:

**Objective:**

Identify the top 3 cities in California to target for maximizing app installations.

**Query:**

```sql
-- CTE for calculating install rates and unique user counts for California cities.
WITH california_installations AS(
     SELECT
       -- Extract city names from the location field.
       split_part(location,'_', 1) AS city_name,
       -- Calculate install rate as the percentage of 'install' events.
       SUM(CASE WHEN event_type = 'install' THEN 1 ELSE 0 END)::float/COUNT(*) AS install_rate,
       -- Count distinct users to estimate potential reach.
       COUNT(DISTINCT user_id) as total_unique_users
     FROM Events
     WHERE 
       location LIKE '%_California' AND  -- Filters for cities in California.
       event_type IS NOT NULL AND  -- Excludes rows where event_type is null.
       location IS NOT NULL  -- Excludes rows where location is null.
     GROUP BY city_name
     ORDER BY install_rate DESC, total_unique_users DESC
)
-- Selecting the top 3 cities for targeted marketing to maximize installations.
SELECT
  city_name, 
  install_rate,
  total_unique_users,
-- Estimated total installations
  install_rate * total_unique_users AS expected_install_users
FROM california_installations
-- Prioritize cities with the highest estimated installations.
ORDER BY expected_install_users DESC  
LIMIT 3;
```

**Insight:** 

In the strategic evaluation of potential markets, I employed a methodical approach to calculate 'expected_install_users' by multiplying the installation rate with the total number of unique users in each city. This methodology effectively harmonizes the quality of user engagement, as indicated by the installation rate, with the quantity of the potential user base. The objective was to identify cities that not only exhibit a high potential for installations but also possess a significant user base, thereby ensuring that any targeted marketing campaigns deliver the desired impact. Additionally, I explored the possibility of implementing a weighted scoring system. This system would assign varying degrees of importance to key metrics such as user base size and installation rate, thereby refining our market targeting strategy to prioritize the most impactful factors.

### 3. Unique User Identification:

**Objective:**

Produce a list of unique users with their respective operating systems to understand the distribution of Android and iOS users, as well as those using both.

**Query:**

```sql
SELECT
user_id,
CASE
-- User has both Android and iOS devices
WHEN SUM(CASE WHEN device ILIKE 'ANDROID%' THEN 1 ELSE 0 END) > 0
     AND SUM(CASE WHEN device ILIKE 'IOS%' THEN 1 ELSE 0 END) > 0 THEN 'Both'
-- User has only Android devices
WHEN SUM(CASE WHEN device ILIKE 'ANDROID%' THEN 1 ELSE 0 END) > 0 THEN 'ANDROID'
-- User has only iOS devices
WHEN SUM(CASE WHEN device ILIKE 'IOS%' THEN 1 ELSE 0 END) > 0 THEN 'IOS'
-- If none of the above conditions are met
ELSE 'Other' END AS user_operating_system
FROM Events
-- Each user is being presented only once
GROUP BY user_id;
```

**Insight:** 

•	Preventing Overlaps: The query is designed to prevent category overlaps by prioritizing the check for 'Both' before looking at individual operating systems. This approach ensures that users with both Android and iOS devices are correctly identified as 'Both' before the query considers the other cases. This order is crucial to prevent the 'Android' and 'iOS' conditions from overshadowing the 'Both' condition, ensuring accurate categorization.

•	Handling Nulls and Other Types: By using SUM() and checking if it's greater than 0, the query effectively ignores nulls and other non-Android/iOS entries. This ensures users with only null or unrecognized device entries are categorized as 'Other', addressing potential issues with null values.

•	Validation of 'Other' Category: An additional validation step was conducted to assess the prevalence of the 'Other' category. It was found that only one case (Null) fell into this category. This ensures that the 'Other' category does not conceal a significant number of users, thereby validating the robustness of our categorization approach.


### 4. Revenue Analysis - New York vs. California:

**Objective:**

Determine and compare the total revenue generated in New York and California, broken down by revenue type, to understand regional market performance.

**Query:**

```sql
-- CTE to identify the state for each user based on the 'Events' table
WITH user_state AS(
    SELECT
        user_id,
        -- Extracts the state name from the location field.
        split_part(location,'_',2) AS state_name
    FROM Events
    -- Filters for users in 'New York' and 'California'.
    WHERE split_part(location,'_',2) IN ('New York','California')
    -- Groups data by user and state for uniqueness
    GROUP BY user_id, state_name)

-- Main query to calculate the total USD amount per revenue type for New York and California
SELECT
    us.state_name,
    r.revenue_type,
    -- calculate the total USD amount per revenue type for New York and California
    SUM(r.usd) total_usd
FROM user_state us
JOIN Revenue r
    ON us.user_id = r.user_id
-- Excludes entries without a specified revenue type
WHERE r.revenue_type IS NOT NULL
GROUP BY us.state_name, r.revenue_type
ORDER BY r.revenue_type, us.state_name;
```

**Insight:**

•	By pre-filtering the 'Events' table to include only users from New York and California, the CTE user_state becomes more efficient and focused, ensuring that only relevant data is processed in the main query.

•	Grouping by state_name and revenue_type allows for a detailed comparison of the total USD amount per revenue type between the two states.

•	The query evolved to explicitly exclude null revenue_type values, ensuring that the focus remains on meaningful and comparable data.

### 5. Operating System Revenue Ranking:

**Objective:**
Rank operating system groups by their total revenue contribution to identify the most lucrative platforms.

**Query:**

```sql
-- CTE: User Operating System Identification
WITH user_operating_system AS(
    SELECT
    user_id,
    CASE
    -- User has both Android and iOS devices
    WHEN SUM(CASE WHEN device ILIKE 'ANDROID%' THEN 1 ELSE 0 END) > 0
         AND SUM(CASE WHEN device ILIKE 'IOS%' THEN 1 ELSE 0 END) > 0 THEN 'Both'
    -- User has only Android devices
    WHEN SUM(CASE WHEN device ILIKE 'ANDROID%' THEN 1 ELSE 0 END) > 0 THEN 'ANDROID'
    -- User has only iOS devices
    WHEN SUM(CASE WHEN device ILIKE 'IOS%' THEN 1 ELSE 0 END) > 0 THEN 'IOS'
    -- If none of the above conditions are met
    ELSE 'Other' END AS user_operating_system
    FROM Events
    -- Each user is being presented only once
    GROUP BY user_id),

-- CTE: Operating System Revenue Calculation
os_revenue AS (
    SELECT 
        uos.user_operating_system,
        -- Sums the total USD revenue for each operating system
        SUM(r.usd) total_usd
    FROM user_operating_system uos
    JOIN Revenue r
    ON uos.user_id = r.user_id
    -- Focus on Positive Revenue
    WHERE r.usd IS NOT NULL AND r.usd > 0
    GROUP BY uos.user_operating_system) 
    
-- Main query to calculate and rank the total USD amount by operating system
SELECT
    user_operating_system,
    ROUND(total_usd) total_usd,
    -- Ranks each operating system by the total USD revenue
    DENSE_RANK() OVER(ORDER BY total_usd DESC) AS operating_system_rank
FROM os_revenue
ORDER BY operating_system_rank;
```

**Insight:**

-	The use of DENSE_RANK() window function provides an efficient and clear method for rating the operating systems, allowing for easy comparison of their revenue contribution.
  
-	The query is designed to consider only relevant, positive revenue figures, ensuring that the rankings are based on meaningful data.
	
-	The categorization method in the CTE accounts for various user scenarios, ensuring that all users are appropriately classified and contributing to the accuracy of the analysis.

### 6. Data Integrity Check:

**Objective:**

Investigate potential anomalies, changes, or problems in the data.

**1.Check for Missing Values in Key Fields**
   
**Finding:**

-	The 'device' column in the 'Events' table has 5,340 null values.
  
-	The 'usd' column in the 'Revenue' table has 3 null values.
  
-	The 'revenue_type' column in the 'Revenue' table has 61 null values.

**Potential Impact:**

-	Missing 'device' Data: Null values in the 'device' column can hinder user segmentation and behavioral analysis, leading to incomplete insights into how different devices impact user engagement and revenue.
  
-	Missing 'usd' Data: Missing revenue data can skew overall financial analysis, underreport total earnings, and affect insights derived from revenue-based metrics.
  
-	Missing 'revenue_type' Data: Nulls in the 'revenue_type' column could obscure the understanding of revenue streams and affect strategies growth decisions.

**Queries used:**

```sql
-- Check for nulls in for the Evenets Table
SELECT
    SUM(CASE WHEN user_id IS NULL THEN 1 ELSE 0 END) AS null_user_id_count,
    SUM(CASE WHEN event_type IS NULL THEN 1 ELSE 0 END) AS null_event_type_count,
    SUM(CASE WHEN timestamp IS NULL THEN 1 ELSE 0 END) AS null_timestamp_count,
    SUM(CASE WHEN location IS NULL THEN 1 ELSE 0 END) AS null_location_count,
    SUM(CASE WHEN device IS NULL THEN 1 ELSE 0 END) AS null_device_count
FROM 
    Events;

-- Check for nulls in for the Revenue Table
SELECT
    SUM(CASE WHEN user_id IS NULL THEN 1 ELSE 0 END) AS null_user_id_count,
    SUM(CASE WHEN local_transaction_at IS NULL THEN 1 ELSE 0 END) AS null_local_transaction_at_count,
    SUM(CASE WHEN revenue_id IS NULL THEN 1 ELSE 0 END) AS null_revenue_id_count,
    SUM(CASE WHEN usd IS NULL THEN 1 ELSE 0 END) AS null_usd_count,
    SUM(CASE WHEN revenue_type IS NULL THEN 1 ELSE 0 END) AS null_revenue_type_count
FROM 
    Revenue;
```

**2. Check for Data Completeness and Integrity**

**Finding**: 

Two users appear in the revenue data but not in the 'Events' data.

**Potential Impact**: 

This discrepancy might indicate issues with data collection or integration between systems, raising questions about the integrity and reliability of user-related analyses.

```sql
Query used:
SELECT DISTINCT r.user_id 
FROM Revenue r
LEFT JOIN Events e ON r.user_id = e.user_id
WHERE e.user_id IS NULL;
```

**3. Detect Duplicate Records**

**Finding:**

68 cases of complete rows duplication in the 'Events' data (out of 545,430 rows). 1,297 cases of complete rows duplication in the 'Revenue' data (out of 32,533 rows).

**Potential Impact:** 

Duplicates can lead to overestimation of user activity, inflated revenue figures, and erroneous conclusions about user engagement and financial performance. To ensure the accuracy of future analyses, a systematic approach to identifying and removing duplicates before analysis should be implemented. This involves defining clear criteria for what constitutes a duplicate record and applying consistent filters to exclude these records. In our case, verifying that each user is consistently associated with a single location is crucial to maintain the accuracy of geographical analyses and prevent any potential skew in our results.

**Queries Used:**

```sql
-- For Events
SELECT COUNT(*)
FROM (
    SELECT user_id, event_type, timestamp, location, device, COUNT(*)
    FROM Events
    GROUP BY user_id, event_type, timestamp, location, device
    HAVING COUNT(*) > 1
) sub;

-- For Revenue
SELECT COUNT(*)
FROM (
    SELECT user_id, revenue_id, local_transaction_at, usd, revenue_type, COUNT(*)
    FROM Revenue
    GROUP BY user_id, revenue_id, local_transaction_at, usd, revenue_type
    HAVING COUNT(*) > 1
) sub;

-- The number of Users with more than one unique Location (there are no such cases in our data)
SELECT
COUNT(*)
FROM(
SELECT
    user_id,
    COUNT(DISTINCT location) AS total_locations
FROM Events
GROUP BY user_id
HAVING COUNT(DISTINCT location) > 1) sub;
```

**4. Analyze Changes Over Time**

**Finding**: 

Noticeable differences in income per event depending on the month. June shows the highest income per event, while September shows the lowest.

**Potential Impact:** 

Seasonal trends, marketing campaigns, or changes in user behavior might explain these fluctuations. Ignoring these trends might lead to misinterpretation of overall performance and user engagement.

**Query Used:**

```sql
-- Monthly event counts
WITH event_month AS (
SELECT 
    DATE_TRUNC('month', CAST(timestamp AS timestamp)) AS month, 
    COUNT(*) AS event_count
FROM Events
GROUP BY DATE_TRUNC('month', CAST(timestamp AS timestamp))
ORDER BY month),

-- Monthly revenue 
revenue_month AS(
SELECT 
    DATE_TRUNC('month', CAST(local_transaction_at AS timestamp)) AS month, 
    SUM(usd) AS total_revenue
FROM Revenue
GROUP BY DATE_TRUNC('month', CAST(local_transaction_at AS timestamp))
ORDER BY month)

-- Calculating the income per event each month
SELECT
    e.month,
    r.total_revenue,
    e.event_count,
    r.total_revenue/e.event_count AS revenue_per_events
FROM event_month e
JOIN revenue_month r
ON e.month = r.month;
```

**Insight:**

Discovering missing values, discrepancies in user data, and duplicate records clearly indicates we need to refine our analysis approach. To tackle these issues, we should start by removing duplicate records, aligning user data across all tables, and addressing any gaps in financial information. These steps are vital for ensuring the precision of our trend analysis and the validity of our financial metrics, which in turn, will lead to more dependable insights. By regularly integrating data quality checks into our analysis process, we'll further protect the integrity of our future findings. By prioritizing these corrective measures, we ensure that our data-driven decisions are based on a solid and reliable foundation.

## Act

1. Target Market Identification:
   
    - Recommended Los Angeles, San Diego, and San Jose for targeted marketing based on high installation rates and user bases.

2. Platform-Specific Strategies:
   
    - Suggested focusing more resources on the most lucrative platform (IOS) as identified by the revenue analysis.
    
3. Data Quality Improvement:
   
    - Advised on enhancing data collection and processing methods to ensure reliable data-driven decisions.
  
## Additional Steps

1. Trend Analysis:

    - Recommended monitoring and analyzing seasonal trends and changes in user behavior.

2. User Segmentation:

    - Proposed further segmentation of the user base for more precise targeting.
    
3. Feedback Loop:

    - Suggested establishing a system to regularly update the analysis with new data and adapt strategies accordingly.
    
## Visuals and Presentation

1. Graphs and Chars:
   
    - Visual representations of installation rates and revenue comparisons.
    
2. Data Flow Diagrams:
   
    - Diagrams illustrating the steps in data preparation and analysis.
    
3. Infographics:
   
    - Summarized key findings and actions in an engaging format.

## Conclusion

This analysis highlighted the significant impact of data-driven decision-making in optimizing app installation and revenue generation. By meticulously analyzing user engagement and financial data, we identified key markets, preferred user platforms, and critical data integrity issues. This project underscores the vital role of SQL and analytics in transforming raw data into actionable insights, steering strategic decisions, and fostering business growth.


</p>
