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

The query revealed cities with the highest app install rates, with a focus on eliminating rows with null values for accuracy.


**Non-trivial decision and Explanations:**

-	Installation Rate Calculation: The query calculates the installation rate as the percentage of 'install' events out of all events recorded for each city in California. This metric provides a clear indicator of user engagement and app acceptance in different regions.

-	Exclusion of Null Values: The query includes conditions to exclude rows where 'event_type' or 'location' is null. These conditions ensure that rows with null values in critical fields are excluded, thereby improving the accuracy and reliability of the install rate calculation




</p>
