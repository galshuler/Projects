# Maximizing App Installation: A Data-Driven Approach with 'EchoSphere Networks'

<span style="font-size:18px;"> 
<p align="justify">
  
## Background

'EchoSphere Networks', a renowned mobile casual app, approached us with a challenge: they needed to optimize their app's market penetration in California. Our task was to delve into their raw data, identify key markets for targeted marketing, understand platform-based revenue contributions, and ensure data integrity. 
This case study outlines our approach using SQL to analyze the data and the insights we derived.

## Ask

**Objective:** 

The primary goal was to use data analysis to guide 'EchoSphere Networks' in optimizing their app installations and revenue generation. 

The specific objectives were to:
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
| user_id     | Unique identifier for each user                        | u-kq5atd8ral851j5e       |
| event_type  | Type of event recorded (e.g., level up, install, etc.) | level_up                 |
| timestamp   | Date and time when the event occurred                  | 2021-08-26 19:17:01.664071 |
| location    | User's location, formatted as City_State               | Santa Cruz_California    |
| device      | Type and version of the user's device                  | IOS_10.1.1, ANDROID_26   |


**Revenue Table**

| Column Name          | Description                                       | Example Values           |
|----------------------|---------------------------------------------------|--------------------------|
| user_id              | Unique identifier for each user                   | u-kq31q7pwb1o9eye        |
| local_transaction_at | Timestamp of the transaction                      | 2021-08-15 20:49:55.997  |
| revenue_id           | Unique identifier for each revenue transaction    | o_swl668-kse3md25        |
| usd                  | Amount of transaction in USD                      | 0.04                     |
| revenue_type         | Type of revenue (e.g., banner, IAP, interstitial) | banner                   |


**Data Cleaning and Validation:**

- Addressed missing values and duplicate records in both tables.
- Ensured data integrity for accurate analysis.

## Analyze

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

I decided to calculate 'expected_install_users' by multiplying the install rate with the total number of unique users as a strategic move. This approach strikes a balance between the quality of engagement, reflected in the install rate, and the quantity of potential users. It's about choosing cities not just for their high install potential but also for their substantial user base, ensuring that any targeted campaign has the impact we're aiming for. As for other approaches, we could consider weighted scoring. If specific aspects, like user base size or install rate, are prioritized, a weighted system could refine our targeting strategy, giving more weight to the factors that matter most.

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


### 4.Revenue Analysis - New York vs. California:

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

### 5.Operating System Revenue Ranking:

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

### 6.Data Integrity Check:

</p>