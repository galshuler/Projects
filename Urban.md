# Analyzing Chicago's Pulse: A Case Study on Urban Data

<span style="font-size:18px;"> 
<p align="justify">
   
## Introduction:

**Navigating Chicago's Data Landscape**

In this comprehensive case study, I will walk you through an in-depth analysis of three pivotal datasets from the city of Chicago. Our journey will span various dimensions of the city's ecosystem, including its infrastructure, educational framework, and public safety. The objectives are clear-cut:

1. Acquaint ourselves with key datasets reflecting Chicago's socio-economic, educational, and safety realms.
   
2. Seamlessly import these datasets into distinct tables within a BigQuery

3. Employ SQL queries to unravel significant insights and resolve specific queries.

## Dataset Overview: Unveiling Chicago's Multifaceted Data

The datasets at the heart of this study are not just numbers and stats; they are narratives of Chicago, accessible via the city's Data Portal. Let's get acquainted with them:

- **Socioeconomic Indicators:** This dataset is a window into the public health landscape of Chicago, showcasing six vital socioeconomic indicators and a “hardship index” across community areas from 2008 to 2012. [Explore More](https://data.cityofchicago.org/Health-Human-Services/Census-Data-Selected-socioeconomic-indicators-in-C/kn9c-c2s2?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork-20127838&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ).

- **Public Schools Performance:** A lens into the educational pulse of Chicago, this dataset reveals performance data of schools for the 2011-2012 academic year. [Explore More](https://data.cityofchicago.org/Education/Chicago-Public-Schools-Progress-Report-Cards-2011-/9xs2-f89t?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork-20127838&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ).

- **Crime Statistics:** Reflecting the safety blueprint of the city, this dataset captures crime incidents from 2001 to the present, barring the most recent week. [Explore More](https://data.cityofchicago.org/Public-Safety/Crimes-2001-to-present/ijzp-q8t2?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork-20127838&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ).

## Data Procurement: Setting the Stage

For the essence of this study, specific subsets of the original datasets are used. These subsets, available in .CSV format, are tailored to enhance database compatibility, simplifying our analytical journey. The datasets can be downloaded from the following links:

- [Chicago Census Data](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/FinalModule_Coursera_V5/data/ChicagoCensusData.csv?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01)
  
- [Chicago Public Schools](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/FinalModule_Coursera_V5/data/ChicagoPublicSchools.csv?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01)
  
- [Data Chicago Crime Data](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/FinalModule_Coursera_V5/data/ChicagoCrimeData.csv?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkDB0201ENSkillsNetwork20127838-2021-01-01)

Note: These versions are not directly sourced from the Chicago Data Portal but are modified subsets, primed for our analysis.

## Uploading the Data in BigQuery

**Preparing Data for BigQuery**
With our datasets acquired and primed for analysis, the next crucial step is to import them into BigQuery, Google's robust and scalable data warehouse solution. This step is essential for leveraging BigQuery's powerful data processing capabilities to extract valuable insights from our datasets.

**Naming the Tables:**
As I uploaded each dataset, I named them **CENSUS_DATA**, **CHICAGO_PUBLIC_SCHOOLS**, and **CHICAGO_CRIME_DATA** respectively. This was done to ensure clarity and ease of reference in my subsequent queries.


**Analysis**

With our data now in BigQuery, we're set to begin our analysis. BigQuery's powerful SQL engine allows us to run complex queries efficiently.

**Problem 1**

Find the total number of crimes recorded in the CRIME table.

```sql
SELECT COUNT(*) AS total_crimes
FROM CHICAGO_CRIME_DATA;
```

**Sample Output:**

Row	
total_crimes
1	
533

**Problem 2**

List community areas with per capita income less than 11000

```sql
SELECT community_area_name, PER_CAPITA_INCOME 
FROM CENSUS_DATA 
WHERE PER_CAPITA_INCOME < 11000;
```

Row	
community_area_name
PER_CAPITA_INCOME
1	
Riverdale
8201
2	
South Lawndale
10402
3	
Fuller Park
10432
4	
West Garfield Park
10934

**Problem 3**

List all case numbers for crimes involving minors?(children are not considered minors for the purposes of crime analysis)


SELECT CASE_NUMBER, DESCRIPTION 
FROM CHICAGO_CRIME_DATA 
WHERE DESCRIPTION LIKE '%MINOR%';

Row	
CASE_NUMBER
DESCRIPTION
1	
HL266884
SELL/GIVE/DEL LIQUOR TO MINOR
2	
HK238408
ILLEGAL CONSUMPTION BY MIN

**Problem 4**

List all kidnapping crimes involving a child?


SELECT CASE_NUMBER, DATE, DESCRIPTION 
FROM `capstone-411010.medical_doctors.CHICAGO_CRIME_DATA`
WHERE PRIMARY_TYPE = 'KIDNAPPING' AND DESCRIPTION LIKE '%CHILD%';

Row	
CASE_NUMBER
DATE
DESCRIPTION
1	
HN144152
2007-01-26
CHILD ABDUCTION/STRANGER


**Problem 5**

What kinds of crimes were recorded at schools?

SELECT DISTINCT PRIMARY_TYPE, LOCATION_DESCRIPTION 
FROM CHICAGO_CRIME_DATA 
WHERE LOCATION_DESCRIPTION LIKE '%SCHOOL%';

Row	
PRIMARY_TYPE
LOCATION_DESCRIPTION
1	
BATTERY
SCHOOL, PUBLIC, GROUNDS
2	
BATTERY
SCHOOL, PUBLIC, BUILDING
3	
ASSAULT
SCHOOL, PUBLIC, GROUNDS
4	
CRIMINAL DAMAGE
SCHOOL, PUBLIC, GROUNDS
5	
PUBLIC PEACE VIOLATION
SCHOOL, PRIVATE, BUILDING
6	
CRIMINAL TRESPASS
SCHOOL, PUBLIC, GROUNDS
7	
NARCOTICS
SCHOOL, PUBLIC, BUILDING
8	
NARCOTICS
SCHOOL, PUBLIC, GROUNDS
9	
PUBLIC PEACE VIOLATION
SCHOOL, PUBLIC, BUILDING

**Problem 6**

List the average safety score for all types of schools.

SELECT Elementary__Middle__or_High_School, AVG(SAFETY_SCORE) AS average_safety_score
FROM CHICAGO_PUBLIC_SCHOOLS 
GROUP BY 1;

Row	
Elementary__Middle__or_High_School
average_safety_score
1	
HS
49.623529411764707
2	
ES
49.520383693045574
3	
MS
48.0

**Problem 7**

List 5 community areas with highest % of households below poverty line.

SELECT COMMUNITY_AREA_NAME, PERCENT_HOUSEHOLDS_BELOW_POVERTY 
FROM CENSUS_DATA 
ORDER BY PERCENT_HOUSEHOLDS_BELOW_POVERTY DESC 
LIMIT 5;


**Problem 8**

Which 5 community area are most crime prone?

SELECT COMMUNITY_AREA_NUMBER, COUNT(*) AS total_crimes 
FROM CHICAGO_CRIME_DATA 
GROUP BY COMMUNITY_AREA_NUMBER
HAVING COMMUNITY_AREA_NUMBER  IS NOT NULL 
ORDER BY COUNT(*) DESC LIMIT 5;

Row	
COMMUNITY_AREA_NUMBER
total_crimes
1	
25
43
2	
23
22
3	
68
21
4	
29
16
5	
28
16

**Problem 9**

Find the name of the community area with highest hardship index


SELECT COMMUNITY_AREA_NAME, HARDSHIP_INDEX 
FROM CENSUS_DATA 
WHERE HARDSHIP_INDEX = (SELECT MAX(HARDSHIP_INDEX) FROM CENSUS_DATA);

Row	
COMMUNITY_AREA_NAME
HARDSHIP_INDEX
1	
Riverdale
98

**Problem 10**

Determine the Community Area Name with most number of crimes.

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


Row	
COMMUNITY_AREA_NAME
total_crimes
1	
Austin
43


עי





</p>
