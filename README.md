# NYCDOE-High-School-Data
This is a project repository containing SQL code specifically pulling data from three NYCDOE datasets looking at NYC High Schools - their demographics of the student population, graduation rates, and breaking down those trends by borough. 


**Overview

What inspired this project? I was thinking about an SQL project I could use NYCDOE datasets to analyze large trends across the largest education system in the United States with 1.1 million students. I wanted to focus on specifically high schools within New York City as this is an interesting area where many policies are developed to help prepare students for college readiness, college success, and career exploration. In fact, many nonprofits within New York City exist serving the high school student population within with the same areas listed above, as students often lack the resources necessary to improve their understanding of college or career choices post-graduation. In this analysis, I seek out to gain a holistic understanding to see where do the high school student population within New York City stand when it comes to these critical areas. 

You can view my full dashboard using this link: https://public.tableau.com/shared/MJ2YRMY8J?:display_count=n&:origin=viz_share_link

**Datasets
For the datasets - I used information from three different datasets provided by the DOE InfoHUB and NYC Open Data.

1. 2021 NYC High School Directory - I used the 2021 NYC High School Directory to find out how many high schools exist within the NYCDOE educational system and to gain a broader understanding of how those schools perform in terms of college readiness, attendance, academic success, and many other factors. This dataset offers a descriptive understanding of what the high school has to offer for prospective high school students and their families. The link for the dataset is :([url](https://data.cityofnewyork.us/Education/2021-DOE-High-School-Directory/8b6c-7uty)).

2. NYCDOE Demographic Snapshot - I used the NYCDOE Demographic Snapshot to provide the demographic information for all high schools within the NYCDOE educational system. This dataset contains information describing the race/ethnicity and gender characteristics of the student population, total enrollment per grade level and the school itself, along with the amount of students in the poverty and are economically disadvantaged. The link for the dataset is ([url](https://infohub.nyced.org/reports/school-quality/information-and-data-overview)). 

3. 2021 NYC Graduation Rates - I used the NYCDOE 2021 Graduation Rates spreadsheet to provide the graudation information for the high schools within New York City. This dataset contains the important characterisitcs of how many students graduated the high school, the graduation rate for specific hgih school diplomas such as regents and advanced regents, while at the same providing the total percentage and count of students dropping out from the high school. The link for this spreadsheet is found (url):https://infohub.nyced.org/reports/school-quality/information-and-data-overview.

**The process for analyzing the datasets. 
For the datasets I used, I decided to focus on only on the high schools within New York as this would be easier to analyze the major trends I was interested in figuirng out for the school year of 2020-2021. 

**Steps to prepare the datasets for analysis
1. As each of the datasets contains multiple tables within the workbook, I deleted tables that were not necessary and providing information that was necessary to pull using SQL. 
2. By using the DBN code present in the 2021 NYC High School Directory, I was able to find all of the high schools in the NYCDOE Demographic Snapshot dataset which allows me to see the demographics for all high schools. 
3. Lastly, for the 2021 NYC High School Directory, I removed multiple columns that did not provide the information I was looking to use in the analysis. 


**Developing KPI Questions
For many of the questions I developed, I wanted to find larger trends by the borough, the top performing schools, and schools with a high economic need index and graduation rate. For this section, I devised multiple SQL queries to the multiple questions I created for each dataset and then for multiple datasets using left joins for one or more tables. I used Google Big Query to analyze the datasets using SQL. 

**Queries for each dataset

The first set of queries are for the NYCDOE Demographic Snapshot. I will state the business questions used for the dataset along with the query syntax used. 

1. Find the 10 high schools with the highest total enrollment ordered in descending by total enrollment. 

SELECT 
  School_Name,
  Total_Enrollment as Enrollment
FROM `nycdoe-high-schools.2021_Graduation_Rates.2021_Demographic` 
ORDER BY 
  Total_Enrollment DESC
LIMIT 10;

2. Find the 10 high schools with the lowest total enrollment ordered in descending by total enrollment. 

SELECT 
  School_Name,
  Total_Enrollment as Enrollment
FROM `nycdoe-high-schools.2021_Graduation_Rates.2021_Demographics` 
WHERE 
  SCHOOL_NAME != '#N/A'
ORDER BY 
  Total_Enrollment ASC
LIMIT 10;

3. Find the total number of schools. 

SELECT 
  COUNT(School_Name)
FROM `nycdoe-high-schools.2021_Graduation_Rates.2021_Demographics`;

4. Find the total number of schools with an economic need index above 95% and where the school name is null. 

SELECT 
  COUNT(School_Name)
FROM `nycdoe-high-schools.2021_Graduation_Rates.2021_Demographics`
WHERE 
  SCHOOL_NAME != '#N/A'
  AND 
  Economic_Need_Index != 'Above 95%';
  
5. Find the total number of schools with an economic need index above 95%. 

SELECT 
  COUNT (School_Name)
FROM `nycdoe-high-schools.2021_Graduation_Rates.2021_Demographics`
WHERE 
  Economic_Need_Index = 'Above 95%';
  
6. Find the total number of high schools in the dataset and total number of students. 

SELECT 
  COUNT (School_Name) AS NUM_OF_SCHOOLS,
  SUM (Total_Enrollment) AS ENROLLMENT
FROM `nycdoe-high-schools.2021_Graduation_Rates.2021_Demographics`;

7. Find the total number and percentage of males and females students across all 441 high schools. 

SELECT 
   SUM(__FEMALE)  AS NUM_FEMALE_ENROLLMENT, 
  (SUM(__FEMALE)/ SUM (Total_Enrollment)) * 100 AS PERC_FEMALE_ENROLLMENT,
  (SUM(__MALE)  AS NUM_MALE_ENROLLMENT, 
  (SUM(__MALE)/ SUM (Total_Enrollment)) * 100 AS PERC_MALE_ENROLLMENT
FROM `nycdoe-high-schools.2021_Graduation_Rates.2021_Demographics`;

The second set of queries are for the 2021 NYC High School Directory. I will state the business questions used for the dataset along with the query syntax used. 

1. Show all records in the dataset. 

SELECT 
   *
FROM `nycdoe-high-schools.2021_Graduation_Rates.2021_Directory` ;

2. Count the total number of neighborhoods in the dataset. 

SELECT 
   COUNT(DISTINCT (NTA)) AS COUNT_NEIGHBORHOODS
FROM
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Directory`;
  
3. Show the average graduation rate, average attendance rate, average college and career rate, all rounded to the nearest whole number as well as the total number of schools grouped by the borough and ordered by the total number of schools descending. 

SELECT 
   BOROUGH,
   COUNT (SCHOOL_NAME) AS NUM_SCHOOLS,
   ROUND(AVG(graduation_rate) *100,0) AS AVG_GRAD_RATE,
   ROUND(AVG(attendance_rate) *100,0) AS AVG_ATTENDANCE_RATE,
   ROUND(AVG(college_career_rate) *100,0) AS AVG_COLLEGE_CAREER_RATE
FROM `nycdoe-high-schools.2021_Graduation_Rates.2021_Directory` 
GROUP BY Borough
ORDER BY NUM_SCHOOLS DESC;

4. Show the average graduation rate, average attendance rate, average college and career rate, all rounded to the nearest whole number as well as the total number of schools and students grouped by the borough and ordered by the total number of students descending. 

SELECT 
   DIR.BOROUGH,
   COUNT (DIR.SCHOOL_NAME) AS NUM_SCHOOLS,
   SUM(DEM.TOTAL_ENROLLMENT) AS SUM_ENROLLMENT,
   ROUND(AVG(DIR.graduation_rate) *100,0) AS AVG_GRAD_RATE,
   ROUND(AVG(DIR.attendance_rate) *100,0) AS AVG_ATTENDANCE_RATE,
   ROUND(AVG(DIR.college_career_rate) *100,0) AS AVG_COLLEGE_CAREER_RATE
FROM 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Directory` AS DIR 
LEFT JOIN 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Demographics` AS DEM
ON 
  DIR.DBN = DEM.DBN
GROUP BY 
  DIR.Borough
ORDER BY 
  SUM_ENROLLMENT DESC;
  
4. Show the average graduation rate, average attendance rate, average college and career rate, all rounded to the nearest whole number as well as the total number of schools and students grouped by the borough and ordered by the total number of students descending. Exclude schools with an economic need index above 95% and schools with a null name.

SELECT 
   DIR.BOROUGH,
   COUNT (DIR.SCHOOL_NAME) AS NUM_SCHOOLS,
   SUM(DEM.TOTAL_ENROLLMENT) AS SUM_ENROLLMENT,
   SUM(DEM.__FEMALE) AS SUM_FEMALE_ENROLLMENT,
   ROUND(SUM(DEM.__FEMALE)/SUM(DEM.TOTAL_ENROLLMENT)*100,0) AS SUM_PER_FEMALE,
   SUM(DEM.__MALE) AS SUM_MALE_ENROLLMENT,
   ROUND(SUM(DEM.__MALE)/SUM(DEM.TOTAL_ENROLLMENT)*100,0) AS SUM_PER_MALE,
   ROUND(AVG(DIR.graduation_rate) *100,0) AS AVG_GRAD_RATE,
   ROUND(AVG(DIR.attendance_rate) *100,0) AS AVG_ATTENDANCE_RATE,
   ROUND(AVG(DIR.college_career_rate) *100,0) AS AVG_COLLEGE_CAREER_RATE
FROM 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Directory` AS DIR 
LEFT JOIN 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Demographics` AS DEM
ON 
  DIR.DBN = DEM.DBN
WHERE
  DEM.Economic_Need_Index != 'Above 95%' AND
  DEM.SCHOOL_NAME IS NOT NULL
GROUP BY 
  DIR.Borough
ORDER BY 
  NUM_SCHOOLS DESC, SUM_ENROLLMENT DESC;

The third set of queries are for the 2021 NYC Graduation Rates. I will state the business questions used for the dataset along with the query syntax used. 

1. Count the total number of cells with the graduation % incomplete. 

SELECT 
  COUNT(__Grads_6) 
FROM 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Graduation_Rates`
WHERE 
  __Grads_6 = 's';
  
 2. Show the first 10 records in the dataset. 

SELECT 
  *
FROM 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Graduation_Rates`
LIMIT 10;

3. Count the number of high schools with a graduation percentage above 90%. 

SELECT 
  COUNT(SCHOOL_NAME) AS COUNT_OF_SCHOOLS
FROM 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Graduation_Rates`
WHERE
  __Grads_6 > '90';
  
 4. Count the number of schools with a drop out rate above 5%. 

SELECT 
  COUNT(SCHOOL_NAME) AS COUNT_OF_SCHOOLS
FROM 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Graduation_Rates`
WHERE
  __Dropout_22 > '5';

5. 4. Show the average graduation rate, average attendance rate, average college and career rate, all rounded to the nearest whole number as well as the total number of schools, and the total number of potential graduates and students grouped by the borough and ordered by the total number of schools and students descending. Exclude schools with an economic need index above 95% and schools with a null name. 

SELECT 
   DIR.BOROUGH,
   COUNT (DIR.SCHOOL_NAME) AS NUM_SCHOOLS,
   SUM(GRAD.__Total_Cohort) AS TOTAL_POTENTIAL_GRADUATES,
   SUM(DEM.TOTAL_ENROLLMENT) AS SUM_ENROLLMENT,
   SUM(DEM.__FEMALE) AS SUM_FEMALE_ENROLLMENT,
   ROUND(SUM(DEM.__FEMALE)/SUM(DEM.TOTAL_ENROLLMENT)*100,0) AS SUM_PER_FEMALE,
   SUM(DEM.__MALE) AS SUM_MALE_ENROLLMENT,
   ROUND(SUM(DEM.__MALE)/SUM(DEM.TOTAL_ENROLLMENT)*100,0) AS SUM_PER_MALE,
   ROUND(AVG(DIR.graduation_rate) *100,0) AS AVG_GRAD_RATE,
   ROUND(AVG(DIR.attendance_rate) *100,0) AS AVG_ATTENDANCE_RATE,
   ROUND(AVG(DIR.college_career_rate) *100,0) AS AVG_COLLEGE_CAREER_RATE,
FROM 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Directory` AS DIR 
LEFT JOIN 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Demographics` AS DEM
ON 
  DIR.DBN = DEM.DBN
LEFT JOIN 
  `nycdoe-high-schools.2021_Graduation_Rates.2021_Graduation_Rates` AS GRAD
ON 
  DIR.DBN = GRAD.DBN
WHERE
  DEM.Economic_Need_Index != 'Above 95%' AND
  DEM.SCHOOL_NAME IS NOT NULL
GROUP BY 
  DIR.Borough
ORDER BY 
  NUM_SCHOOLS DESC, SUM_ENROLLMENT DESC;

