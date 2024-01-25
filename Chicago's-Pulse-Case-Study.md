# Analyzing Chicago's Pulse: A Comprehensive Case Study on Urban Data Dynamics

<span style="font-size:18px;"> 
<p align="justify">

## Introduction:

**Background and Purpose**

In "Analyzing Chicago's Pulse," we embark on a meticulous exploration of Chicago's urban tapestry through the lens of data analytics. This case study aims to dissect the intricate social, economic, and safety dynamics of Chicago by analyzing key datasets. Our primary objectives are to:

1. Delve into datasets representing Chicago's socio-economic status, education sector, and public safety.
 
2. Seamlessly integrate these datasets into BigQuery for advanced analysis.
   
3. Utilize SQL queries to extract and interpret meaningful insights, addressing targeted research questions.

## Dataset Overview

**A Narrative of Chicago in Numbers**

The datasets used in this study are more than mere collections of data; they represent the living stories of Chicago. Accessed through the city's Data Portal, these datasets include:

- **Socioeconomic Indicators:** Offering a glimpse into Chicago's public health scenario, this dataset provides six essential socioeconomic indicators along with a "hardship index" across various community areas from 2008 to 2012 [Explore More](https://data.cityofchicago.org/Health-Human-Services/Census-Data-Selected-socioeconomic-indicators-in-C/kn9c-c2s2?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork-20127838&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ).

- **Public Schools Performance:** This dataset provides a comprehensive view of the educational landscape, highlighting the performance metrics of Chicago's public schools for the academic year 2011-2012. [Explore More](https://data.cityofchicago.org/Education/Chicago-Public-Schools-Progress-Report-Cards-2011-/9xs2-f89t?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork-20127838&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ).

- **Crime Statistics:** A crucial dataset that sheds light on the city's public safety by cataloging crime incidents from 2001 to the present, excluding the most recent week. [Explore More](https://data.cityofchicago.org/Public-Safety/Crimes-2001-to-present/ijzp-q8t2?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork-20127838&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ).

## Data Procurement and Preparation

**Tailoring Data for Analysis**

To facilitate our analysis, specific subsets of these datasets in .CSV format were used. These subsets were curated to ensure compatibility and efficiency in database operations. The datasets were procured from modified sources, optimized for our analytical needs.

- [Chicago Census Data](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/FinalModule_Coursera_V5/data/ChicagoCensusData.csv?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01)
  
- [Chicago Public Schools](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/FinalModule_Coursera_V5/data/ChicagoPublicSchools.csv?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01)
  
- [Data Chicago Crime Data](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/FinalModule_Coursera_V5/data/ChicagoCrimeData.csv?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01)

Note: These versions are not directly sourced from the Chicago Data Portal but are modified subsets, primed for our analysis.

## Data Integration into BigQuery

**Setting Up for Advanced Analysis**

The datasets were imported into BigQuery, Google's powerful data warehouse solution. This step was crucial for leveraging BigQuery's data processing capabilities. The datasets were named **CENSUS_DATA**, **CHICAGO_PUBLIC_SCHOOLS**, and **CHICAGO_CRIME_DATA** for clarity and ease of reference in subsequent analyses.

## In-depth Analysis and Insights

**Tackling Key Questions Through SQL Queries**

Utilizing BigQuery's SQL engine, a series of complex queries were executed to address specific research questions. These queries ranged from assessing crime rates and identifying areas with socio-economic challenges to evaluating educational performance metrics. The insights drawn from these queries provided a nuanced understanding of various facets of urban life in Chicago.

**Sample Queries and Outputs**

A total of ten problem statements were addressed, each with its SQL query and resulting outputs. These queries covered diverse aspects such as crime rates, socio-economic indicators, and school performance metrics. The results offered a multifaceted view of Chicago's urban dynamics.

**Problem 1**

Find the total number of crimes recorded in the CRIME table.

```sql
SELECT COUNT(*) AS total_crimes
FROM CHICAGO_CRIME_DATA;
```

**Sample Output:**

| total_crimes |
| ------------ |
| 533          |


**Problem 2**

List community areas with per capita income less than 11000

```sql
SELECT community_area_name, PER_CAPITA_INCOME 
FROM CENSUS_DATA 
WHERE PER_CAPITA_INCOME < 11000;
```

**Sample Output:**

| community_area_name | PER_CAPITA_INCOME |
| ------------------- | ----------------- |
| Riverdale           | 8201              |
| South Lawndale      | 10402             |
| Fuller Park         | 10432             |
| West Garfield Park  | 10934             |


**Problem 3**

List all case numbers for crimes involving minors? (children are not considered minors for the purposes of crime analysis).

```sql
SELECT CASE_NUMBER, DESCRIPTION 
FROM CHICAGO_CRIME_DATA 
WHERE DESCRIPTION LIKE '%MINOR%';
```

**Sample Output:**

| CASE_NUMBER | DESCRIPTION                  |
| ----------- | ---------------------------- |
| HL266884    | SELL/GIVE/DEL LIQUOR TO MINOR|
| HK238408    | ILLEGAL CONSUMPTION BY MINOR |


**Problem 4**

List all kidnapping crimes involving a child?

```sql
SELECT CASE_NUMBER, DATE, DESCRIPTION 
FROM CHICAGO_CRIME_DATA
WHERE PRIMARY_TYPE = 'KIDNAPPING' AND DESCRIPTION LIKE '%CHILD%';
```

**Sample Output:**

| CASE_NUMBER | DATE       | DESCRIPTION            |
| ----------- | ---------- | ---------------------- |
| HN144152    | 2007-01-26 | CHILD ABDUCTION/STRANGER |



**Problem 5**

What kinds of crimes were recorded at schools?

```sql
SELECT DISTINCT PRIMARY_TYPE, LOCATION_DESCRIPTION 
FROM CHICAGO_CRIME_DATA 
WHERE LOCATION_DESCRIPTION LIKE '%SCHOOL%';
```

**Sample Output:**

| PRIMARY_TYPE           | LOCATION_DESCRIPTION        |
| ---------------------- | --------------------------- |
| BATTERY                | SCHOOL, PUBLIC, GROUNDS     |
| ASSAULT                | SCHOOL, PUBLIC, GROUNDS     |
| CRIMINAL DAMAGE        | SCHOOL, PUBLIC, GROUNDS     |
| PUBLIC PEACE VIOLATION | SCHOOL, PRIVATE, BUILDING   |
| CRIMINAL TRESPASS      | SCHOOL, PUBLIC, GROUNDS     |
| NARCOTICS              | SCHOOL, PUBLIC, BUILDING    |


**Problem 6**

List the average safety score for all types of schools.

```sql
SELECT Elementary__Middle__or_High_School, AVG(SAFETY_SCORE) AS average_safety_score
FROM CHICAGO_PUBLIC_SCHOOLS 
GROUP BY 1;
```

**Sample Output:**

| Elementary__Middle__or_High_School | average_safety_score |
| ---------------------------------- | -------------------- |
| HS                                 | 49.623529411764707   |
| ES                                 | 49.520383693045574   |
| MS                                 | 48.0                 |


**Problem 7**

List the five community areas with highest % of households below poverty line.

```sql
SELECT COMMUNITY_AREA_NAME, PERCENT_HOUSEHOLDS_BELOW_POVERTY 
FROM CENSUS_DATA 
ORDER BY PERCENT_HOUSEHOLDS_BELOW_POVERTY DESC 
LIMIT 5;
```

**Sample Output:**

| COMMUNITY_AREA_NAME | PERCENT_HOUSEHOLDS_BELOW_POVERTY |
| ------------------- | -------------------------------- |
| Riverdale           | 56.5                             |
| Fuller Park         | 51.2                             |
| Englewood           | 46.6                             |
| North Lawndale      | 43.1                             |
| East Garfield Park  | 42.4                             |


**Problem 8**

Which 5 community area are most crime prone?

```sql
SELECT COMMUNITY_AREA_NUMBER, COUNT(*) AS total_crimes 
FROM CHICAGO_CRIME_DATA 
GROUP BY COMMUNITY_AREA_NUMBER
HAVING COMMUNITY_AREA_NUMBER  IS NOT NULL 
ORDER BY COUNT(*) DESC LIMIT 5;
```

**Sample Output:**

| COMMUNITY_AREA_NUMBER | total_crimes |
| --------------------- | ------------ |
| 25                    | 43           |
| 23                    | 22           |
| 68                    | 21           |
| 29                    | 16           |
| 28                    | 16           |


**Problem 9**

Find the name of the community area with highest hardship index

```sql
SELECT COMMUNITY_AREA_NAME, HARDSHIP_INDEX 
FROM CENSUS_DATA 
WHERE HARDSHIP_INDEX = (SELECT MAX(HARDSHIP_INDEX) FROM CENSUS_DATA);
```

**Sample Output:**

| COMMUNITY_AREA_NAME | HARDSHIP_INDEX |
| ------------------- | -------------- |
| Riverdale           | 98             |


**Problem 10**

Determine the Community Area Name with most number of crimes.

```sql
SELECT CD.COMMUNITY_AREA_NAME, COUNT(CRIM.CASE_NUMBER) AS total_crimes
FROM CHICAGO_CRIME_DATA CRIM
JOIN CENSUS_DATA CD ON CRIM.COMMUNITY_AREA_NUMBER = CD.COMMUNITY_AREA_NUMBER
GROUP BY CD.COMMUNITY_AREA_NAME
HAVING COUNT(CRIM.CASE_NUMBER) = (
    SELECT MAX(total_crimes)
    FROM (
        SELECT COUNT(*) AS total_crimes
        FROM CHICAGO_CRIME_DATA
        GROUP BY COMMUNITY_AREA_NUMBER
    ) sub
)
```

**Sample Output:**

| COMMUNITY_AREA_NAME | total_crimes |
| ------------------- | ------------ |
| Austin              | 43           |



## Limitations and Considerations
The study acknowledges limitations such as the time range of data, potential biases in the data sources, and the exclusion of certain variables that might impact the study's findings.

## Recommendations or Actionable Insights
Based on our findings, recommendations will be proposed. For example, areas with high crime rates might benefit from increased policing or community programs, while schools with lower performance scores could be targeted for additional educational resources.

## Conclusions and Implications

**Synthesizing Data-Driven Insights**

The analysis revealed critical insights into Chicago's socio-economic challenges, educational landscape, and public safety issues. The study underscored the value of data analytics in understanding and addressing urban problems. The findings from this case study can inform policy decisions, guide resource allocation, and inspire further research.

**Future Directions**

This case study opens avenues for more detailed research into specific areas like crime prevention strategies, targeted educational reforms, and socio-economic development programs. It also highlights the potential of data analytics in shaping urban planning and policy-making.


</p>
