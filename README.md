This repository examines Healthcare Encounter Analysis for the year 2019
### Table of Contents
- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning/Preparation](#data-cleaningpreparation)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results/Findings](#resultsfindings)
- [Recommendations](#recommendations)
- [Limitations](#limitations)
- [References](#references)

### Project Overview
### Data Analysis
To answer business insight questions from the data set, specific SQL queries were deployed

```sql
SELECT COUNT(*) As 2019_encounters  -- this line counts the number of records that meet the criteria below
FROM HEALTHCARE.ENCOUNTERS -- this line identifies the table we want to pull from
WHERE START>='2019-01-01' -- this line identifies what start dates we want to pull on and after 
AND START<'2020-01-01'; -- this line makes sure we don't pull farther out than we want
```
Explainer: This aggregate query displays the total number of medical encounters that were had in 2019 under a specified table- 2019_encounters as seen in the first line of the query

```sql
SELECT COUNT(DISTINCT PATIENT) As 2019_Patients -- by adding a distinct and identifying the column here, we can ensure we don't just count rows
FROM HEALTHCARE.ENCOUNTERS
WHERE START>='2019-01-01'
AND START<'2020-01-01';
```
Explainer: Here, we queried the unique number of patients seen in the year 2019

```sql
SELECT *
FROM HEALTHCARE.ENCOUNTERS
WHERE START>='2019-01-01'
AND START<'2020-01-01'
AND ENCOUNTERCLASS='inpatient'; -- this is how we add an additional criteria
```
Explainer: The purpose of this query is to display the type of medical service rendered is to inpatients only in 2019

```sql
Select COUNT(*) AS ambulatory_total_2019 FROM HEALTHCARE.ENCOUNTERS WHERE START>='2019-01-01'
AND START<'2020-01-01'
AND ENCOUNTERCLASS='ambulatory';
```
Explainer: This aggregate query displays the total count of ambulatory services from the encounter class column for the year 2019

```sql
SELECT COUNT(*)
FROM HEALTHCARE.ENCOUNTERS
WHERE START>='2019-01-01'
AND START<'2020-01-01'
AND ENCOUNTERCLASS IN 
('ambulatory','wellness','outpatient',
'urgentcare'); -- this is how we can add a list of criteria without many new lines of code
```
Explainer: This query consolidates the count of all services rendered in the encounterclass column labeled as 'ambulatory','wellness','outpatient',
'urgentcare

```sql
SELECT 
GENDER
,COUNT(*)
FROM HEALTHCARE.PATIENTS
GROUP BY GENDER -- new function introduced called the group by, we want to make sure the column(s) mentioned here also appear in the select statement;
```
Explainer: This query shows the count across genders specified in the data set. Specifically, it is queried from the patients table

```sql
SELECT 
RACE
,COUNT(*)
FROM HEALTHCARE.PATIENTS
GROUP BY RACE;
```
Explainer: This query returns the count of patients per racial groups identified in the data set using the group by function

```sql
SELECT 
GENDER,RACE,ETHNICITY,
COUNT(*) AS COUNT
FROM HEALTHCARE.PATIENTS
GROUP BY GENDER, RACE, ETHNICITY
ORDER BY COUNT desc;
```
Explainer: This query shows the count per gender, race & ethnicity of patients available in the data set. It is queried from the patients table

```sql
SELECT 
ID,BIRTHDATE,FLOOR(DATEDIFF(curdate(),BIRTHDATE)/365.25) AS AGE_IN_YEARS
FROM HEALTHCARE.PATIENTS;
```
Explainer: Using the DatedIFF function, this query shows the age of patients which is calculated from the current date of the query. It uses the unique identifier which is their ID to pull the unique results as available in the patients table

```sql
SELECT DISTINCT STATE
FROM HEALTHCARE.PATIENTS;
```
Explainer: This query displays the specific location of the patients treated in the data set

```sql
SELECT DISTINCT ZIP,
COUNT(*) AS count_per_zip
FROM HEALTHCARE.PATIENTS
GROUP BY ZIP ORDER BY count_per_zip desc;
```
Explainer: This query returns the count of patients seen per zip code in descending order under the column header of count_per_zip as specified in the code

```sql
SELECT DISTINCT COUNTY,
COUNT(*) no_per_county
FROM HEALTHCARE.PATIENTS
GROUP BY COUNTY ORDER BY no_per_county desc;
```
Explainer: This query is intended to display the distinct number of patients per county under the no_per_county column header in a descending order

```sql
SELECT 
GENDER,RACE,ETHNICITY,
COUNT(*) AS VOLUMES
FROM HEALTHCARE.ENCOUNTERS ENC
JOIN HEALTHCARE.patients PAT ON ENC.PATIENT=PAT.ID
WHERE START>='2019-01-01'
AND START<'2020-01-01'
AND ENCOUNTERCLASS='inpatient'
GROUP BY GENDER, RACE, ETHNICITY;
```
Explainer: This query is intended to display the patient mix count per gender, race &  ethnicity. Using the Join function, it queries the data from two tables- patient & encounters table using the unique patient ID available in both tables. The Where clause specifies the exact date range sought as well as the classification under the encounterclass column

```sql
SELECT COUNT(*) AS ER_VOLUMES
FROM HEALTHCARE.ENCOUNTERS
WHERE START>='2019-01-01'
AND START<'2020-01-01'
AND ENCOUNTERCLASS='emergency';
```
Explainer; This query counts the total number of encounters classified as emergency that were had in 2019 under the specified column header ER_VOLUMES from the encounter class column in encounters table

```sql
SELECT CON.DESCRIPTION
,COUNT(*) AS CONDITION_VOLUMES
FROM HEALTHCARE.ENCOUNTERS ENC
LEFT JOIN HEALTHCARE.CONDITIONS CON ON ENC.ID=CON.ENCOUNTER
WHERE ENC.START>='2019-01-01'
AND ENC.START<'2020-01-01'
AND ENC.ENCOUNTERCLASS='emergency'
GROUP BY CON.DESCRIPTION;
```
Explainer: The query counts the number of emergency encounters in 2019 associated with each condition. This analysis helps to understand which medical conditions were most prevalent among patients during emergency visits in that year. The query uses a left join to between Encounters table (ENC) and the Conditions table (CON) with the join being made on the ID column of both tables. The left join ensures that all results including the ones without conditions are displayed

```sql
SELECT *
FROM HEALTHCARE.ENCOUNTERS ENC
LEFT JOIN HEALTHCARE.CONDITIONS CON ON ENC.ID=CON.ENCOUNTER
WHERE ENC.START>='2019-01-01'
AND ENC.START<'2020-01-01'
AND ENC.ENCOUNTERCLASS='emergency'
```
Explainer: The primary purpose of this query is to retrieve all emergency encounters within 2019, along with any associated conditions. The query helps provide a comprehensive view of the emergency visits during that year, including patient demographics, encounter details, and any conditions recorded during the encounters.

```sql
SELECT SUM(TOTAL_CLAIM_COST) AS TOTAL_FOR_2019,
AVG(TOTAL_CLAIM_COST) AS AVG_FOR_2019
FROM HEALTHCARE.ENCOUNTERS 
WHERE START>='2019-01-01'
AND START<'2020-01-01';
```
Explainer: This query returns the total claims and average cost for 2019

```sql
SELECT SUM(TOTAL_CLAIM_COST) AS TOTAL_CLAIMS_FOR_2019
, AVG(TOTAL_CLAIM_COST) AS AVG_CLAIMS_FOR_2019
,SUM(PAYER_COVERAGE) AS TOTAL_PAYER_COVERAGE_FOR_2019
,AVG(PAYER_COVERAGE) AS AVG_PAYER_COVERAGE_FOR_2019
FROM HEALTHCARE.ENCOUNTERS 
WHERE START>='2019-01-01'
AND START<'2020-01-01';
```
Explainer: This query retrieves the total claims cost, average claims cost, total payer coverage and average payer coverage for 2019

```sql
SELECT 
PAYER,NAME,ENCOUNTERCLASS,
SUM(TOTAL_CLAIM_COST)  - SUM(PAYER_COVERAGE) AS DIFFER_CLAIM_TO_PAYER_COV_TOT,
AVG(TOTAL_CLAIM_COST)  - AVG(PAYER_COVERAGE) AS DIFFER_CLAIM_TO_PAYER_COV_AVG
FROM HEALTHCARE.ENCOUNTERS ENC
JOIN HEALTHCARE.PAYERS PAY ON ENC.PAYER=PAY.ID
WHERE START>='2019-01-01'
AND START<'2020-01-01'
GROUP BY PAYER, NAME, ENCOUNTERCLASS
ORDER BY ENCOUNTERCLASS;
```
Explainer: This query analyzes and compares the financial aspects of healthcare encounters, focusing on the costs of claims and the coverage provided by payers for the year 2019. The query calculates total and average claim costs, as well as the total and average amounts covered by payers, for each combination of payer, payer name, and encounter class.

```sql
SELECT 
ENCOUNTERCLASS,
SUM(TOTAL_CLAIM_COST)  - SUM(PAYER_COVERAGE) AS DIFFER_CLAIM_TO_PAYER_COV_TOT,
AVG(TOTAL_CLAIM_COST)  - AVG(PAYER_COVERAGE) AS DIFFER_CLAIM_TO_PAYER_COV_AVG
FROM HEALTHCARE.ENCOUNTERS ENC
WHERE START>='2019-01-01'
AND START<'2020-01-01'
GROUP BY ENCOUNTERCLASS
ORDER BY ENCOUNTERCLASS;
```
Explainer: This query retrieves the data on the encounter class with the highest cost within the specified year- 2019

```sql
SELECT 
THROUGHPUT.DESCRIPTION, ROUND(AVG(THROUGHPUT_IN_MIN),0) AS THR_AVG
FROM (SELECT ENC.ID, CON.DESCRIPTION, TIMESTAMPDIFF(MINUTE,ENC.START,ENC.STOP) THROUGHPUT_IN_MIN 
FROM HEALTHCARE.ENCOUNTERS ENC
LEFT JOIN HEALTHCARE.CONDITIONS CON ON ENC.ID=CON.ENCOUNTER
WHERE ENC.START>='2019-01-01'
AND ENC.START<'2020-01-01'
AND ENC.ENCOUNTERCLASS='emergency') THROUGHPUT
GROUP BY THROUGHPUT.DESCRIPTION;
```

```sql
SELECT * FROM 
(SELECT	DESCRIPTION,
COUNT(*) AS TOTAL_PROCS
FROM HEALTHCARE.PROCEDURES
WHERE DATE>='2019-01-01'
AND DATE<'2020-01-01'
GROUP BY DESCRIPTION) PROCS
ORDER BY TOTAL_PROCS DESC;
```
Explainer: To retrieve the list of procedures and total number of times each procedure was performed in 2019, this query was deployed. The result is grouped by the description of the procedures and presented in descending order

```sql
SELECT ENC.ENCOUNTERCLASS,
COUNT(*) AS TOTAL_PROCS_FOR_CLASS
FROM HEALTHCARE.PROCEDURES PROCS
JOIN HEALTHCARE.ENCOUNTERS ENC ON PROCS.ENCOUNTER=ENC.ID
WHERE DATE>='2019-01-01'
AND DATE<'2020-01-01'
GROUP BY ENC.ENCOUNTERCLASS;
```
Explainer: To determine the summary total of each procedures across the care setting in 2019, this query is deployed.

```sql
SELECT ENC.ORGANIZATION,
COUNT(*) AS TOTAL_PROCS
FROM HEALTHCARE.PROCEDURES PROCS
JOIN HEALTHCARE.ENCOUNTERS ENC ON PROCS.ENCOUNTER=ENC.ID
JOIN HEALTHCARE.ORGANIZATIONS ORG ON ENC.ORGANIZATION=ORG.ID
WHERE DATE>='2019-01-01'
AND DATE<'2020-01-01'
AND ENC.ENCOUNTERCLASS='inpatient'
GROUP BY ENC.ORGANIZATION;
```
Explainer: This query provides data on the organizations that performed the most inpatient procedures in 2019. The join operations matches the procedures table with the encounters table based on the matching encounters ID. It further join the encounters table with the organization table allowing the query to associate each encounter to the corresponding organization where it was performed.

```sql
SELECT DISTINCT PATIENT
FROM HEALTHCARE.OBSERVATIONS
WHERE 
((DESCRIPTION = 'Diastolic Blood Pressure' AND VALUE>90) 
OR (DESCRIPTION = 'Systolic Blood Pressure' AND VALUE>140))
AND DATE>='2018-01-01'
AND DATE<'2020-01-01';
```
Explainer: This query is designed to identify unique patients who had either high diastolic or systolic blood pressure readings between January 1, 2018, and December 31, 2019. It retrieves a list of these patients based on certain criteria related to their blood pressure observations. The output is a list of unique patients who had at least one observation of either: Diastolic Blood Pressure greater than 90, or Systolic Blood Pressure greater than 140 during the specified date range (2018-2019). Each patient who meets the criteria will appear only once in the result set.

```sql
SELECT DISTINCT BP.PATIENT,
PRO.NAME AS PROVIDERNAME,
PRO.SPECIALITY 
FROM HEALTHCARE.OBSERVATIONS BP
JOIN HEALTHCARE.ENCOUNTERS ENC ON BP.PATIENT=ENC.PATIENT
AND ENC.START>=BP.DATE
JOIN HEALTHCARE.PROVIDERS PRO ON ENC.PROVIDER=PRO.ID
WHERE 
((BP.DESCRIPTION = 'Diastolic Blood Pressure' AND BP.VALUE>90)
OR (BP.DESCRIPTION = 'Systolic Blood Pressure' AND BP.VALUE>140))
AND BP.DATE>='2018-01-01'
AND BP.DATE<'2020-01-01';
```
Explainer: To determine which providers treated patients with uncontrolled hypertension in 2018 and 2019, the join operations here link blood pressure observations to the healthcare encounters that occurred on or after the observation date and then connect these encounters to the healthcare providers who were responsible for the patient's care.

```sql
SELECT DISTINCT BP.PATIENT,
MED.DESCRIPTION AS MEDICATION
FROM HEALTHCARE.OBSERVATIONS BP
JOIN HEALTHCARE.MEDICATIONS MED ON BP.PATIENT=MED.PATIENT
AND MED.START>=BP.DATE
WHERE 
((BP.DESCRIPTION = 'Diastolic Blood Pressure' AND BP.VALUE>90)
OR (BP.DESCRIPTION = 'Systolic Blood Pressure' AND BP.VALUE>140))
AND BP.DATE>='2018-01-01'
AND BP.DATE<'2020-01-01';
```
Explainer: This query helps us to retrieve a list of unique patients who had high blood pressure readings during a specific period (2018-2019) and the medications they were prescribed. The join operations fuses the OBSERVATIONS table (BP) with the MEDICATIONS table (MED) based on a matching PATIENT identifier. The additional condition AND MED.START >= BP.DATE ensures that the medication was started on or after the date of the blood pressure observation. This links the high blood pressure reading to medications that the patient was prescribed afterwards.
