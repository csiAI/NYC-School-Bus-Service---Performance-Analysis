# NYC-School-Bus-Service---Performance-Analysis
This is an indepth analysis of the past and current performance of the NYC School Bus Service. Them aim is to understand the challenges it faces and  suggest potential areas of improvement to provide a better service to their users.

I have performed Data Cleaning and Exploratory Data Analysis using SQL. I then created a dashboard in Tableau.

The csv file with the data can be found here: https://drive.google.com/file/d/1Bq89xxzxvSu-_GNwjIZGt4jgnpVDSymp/view?usp=sharing

![image](https://github.com/user-attachments/assets/4a93d9db-1386-47ce-83d6-333487f68891)
![image](https://github.com/user-attachments/assets/e051df29-5b7c-45d3-ab39-d20e72e11936)


Summary:
        This analysis reveals significant challenges in service reliability and efficiency. Key findings include high delay frequencies, substantial accumulated delay times, and notable mechanical issues across bus companies. 

KPIs & Insights:

    Average Delay Time: 37 minutes.
        
    Delay Frequency:

      - Highest in 16-30 min range (74K occurrences).
      - 31-45 min delays also frequent (69K occurrences).
      - Most delays fall in the 16-45 minute range, suggesting systemic issues beyond occasional traffic.

    Delay Causes:

      - Heavy Traffic: 67%
      - Mechanical Problems: 5%
      - Won't Start: 1%

    Accumulated Delay Times:
    
      - Peak in 2022 (907 days), declining to 273 days in 2023.
      - While improving, accumulated delays remain significant.
      
    Geographic Impact:

      - Manhattan (736 days) and Brooklyn (586 days) most affected.
      - Focus on these boroughs could yield the highest impact.


    Mechanical Issues:

      - Top 2 companies: PRIDE TRANS (20.7K incidents) and L&M (16K incidents).
      - These companies require immediate attention for fleet maintenance.


    Day of Week Performance:

      - Mondays through Wednesdays show highest breakdown rate.
      - Gradual improvement through the week.
      - Enhanced preparation for Monday operations could improve weekly performance.

Recommendations:

    Traffic Management:

      - Collaborate with city traffic department to optimize bus routes.
      - Implement real-time traffic monitoring for dynamic route adjustments.
      - Focus on Manhattan and Brooklyn for targeted improvements.

    Mechanical Reliability:

      - Conduct a comprehensive audit of PRIDE TRANS, L&M and other high-incident companies.
      - Implement stricter preventative maintenance schedules, especially before Mondays.

    Performance Incentives:

      - Introduce a performance-based contract system for bus companies.
      - Recognize and reward companies and drivers with the best on-time records.

Conclusion:

        While the NYC School Bus Service faces significant challenges, the data suggests areas where targeted interventions 
        can lead to substantial improvements. By focusing on reducing delays, enhancing mechanical reliability, 
        and optimizing routes, particularly in high-impact areas, the service can significantly enhance 
        its performance and reliability for New York City's students and families.

Here's the SQL code used to perform Data Cleaning and Exploratory Data Analysis:

SELECT * FROM nyc_bus_service.nyc_bus_service;

-- DATA CLEANING:

-- Let's check if there are any duplicates:

SELECT Busbreakdown_ID, COUNT(*)
FROM nyc_bus_service
GROUP BY Busbreakdown_ID
HAVING COUNT(*) > 1;

-- There are no duplicates.

-- Let's select the first year from the year range in School_Year:

SELECT School_Year, SUBSTRING_INDEX(School_Year,'-',1)
FROM nyc_bus_service;

-- Let's update the column School_Year:

UPDATE nyc_bus_service
SET School_Year = SUBSTRING_INDEX(School_Year,'-',1);

-- Let's change the data type from VARCHAR to INTEGER:

ALTER TABLE nyc_bus_service
CHANGE COLUMN School_Year School_Year INTEGER;

-- Let's split the column How_Long_Delayed into two columns - Short_Time_Estimate and Long_time_estimate:

SELECT How_Long_Delayed, SUBSTRING_INDEX(How_Long_Delayed,'-',1) AS Short_Time_Estimate
FROM nyc_bus_service;

SELECT How_Long_Delayed, SUBSTRING(SUBSTRING_INDEX(How_Long_Delayed,'-',-1),1,2) AS Long_Time_Estimate
FROM nyc_bus_service;

-- Let's add them to the table:

ALTER TABLE nyc_bus_service
ADD COLUMN Short_Time_Estimate INTEGER;

UPDATE nyc_bus_service 
SET Short_Time_Estimate = 
    CASE 
        WHEN TRIM(SUBSTRING_INDEX(How_Long_Delayed, '-', 1)) = '' THEN NULL
        ELSE SUBSTRING_INDEX(How_Long_Delayed,'-',1) 
    END;

ALTER TABLE nyc_bus_service
ADD COLUMN Long_Time_Estimate INTEGER;

UPDATE nyc_bus_service 
SET Long_Time_Estimate = 
    CASE 
        WHEN TRIM(SUBSTRING(SUBSTRING_INDEX(How_Long_Delayed,'-',-1),1,2)) = '' THEN NULL
        ELSE SUBSTRING(SUBSTRING_INDEX(How_Long_Delayed,'-',-1),1,2)
    END;
    
-- Let's average both columns and add a new column with the average:

SELECT Round((Short_Time_Estimate + Long_Time_Estimate) / 2,1) AS Avg_Time_Estimate
FROM nyc_bus_service;

ALTER TABLE nyc_bus_service
ADD COLUMN Avg_Time_Estimate INTEGER;

UPDATE nyc_bus_service
SET Avg_Time_Estimate = Round((Short_Time_Estimate + Long_Time_Estimate) / 2,1);

-- Let's extract the day of the week from the Created_On column:

SELECT *
FROM nyc_bus_service;

SELECT 
    Created_On,
    substr(Created_On, 1, 8) AS Date_Part
FROM nyc_bus_service;

SELECT 
    Created_On,
    STR_TO_DATE(Created_On, '%m/%d/%Y') AS converted_date,
    DAYNAME(STR_TO_DATE(Created_On, '%m/%d/%Y')) AS day_of_week
FROM nyc_bus_service
WHERE Created_On IS NOT NULL;

ALTER TABLE nyc_bus_service
ADD COLUMN Week_Day VARCHAR(20);

UPDATE nyc_bus_service
SET Week_Day = DAYNAME(STR_TO_DATE(LEFT(Created_On, 8), '%m/%d/%Y'));

-- Let's clean the bus company names:

SELECT DISTINCT(Bus_Company_Name)
FROM nyc_bus_service
ORDER BY Bus_Company_Name;

SELECT DISTINCT Bus_Company_Name,
    CASE 
        WHEN Bus_Company_Name LIKE 'ALINA SERVICES CORP%' THEN 'ALINA'
        WHEN Bus_Company_Name LIKE 'ALL AMERICAN SCHOOL BUS CORP%' THEN 'ALL AMERICAN'
        WHEN Bus_Company_Name LIKE 'ALL COUNTY BUS LLC%' THEN 'ALL COUNTY'
        WHEN Bus_Company_Name LIKE 'ALLIED TRANSIT CORP%' THEN 'ALLIED'
        WHEN Bus_Company_Name LIKE 'ANOTHER RIDE INC%' THEN 'ANOTHER RIDE'
        WHEN Bus_Company_Name LIKE 'B & F SKILLED INC%' THEN 'B & F SKILLED'
        WHEN Bus_Company_Name LIKE 'BOBBY`S BUS CO%' THEN 'BOBBYS'
        WHEN Bus_Company_Name LIKE 'BORO TRANSIT%' THEN 'BORO TRANSIT'
        WHEN Bus_Company_Name LIKE 'CAREFUL BUS%' THEN 'CAREFUL'
        WHEN Bus_Company_Name LIKE 'CHILDREN`S TRANS%' THEN 'CHILDRENS TRANS'
        WHEN Bus_Company_Name LIKE 'CONSOLIDATED BUS%' THEN 'CONSOLIDATED'
        WHEN Bus_Company_Name LIKE 'DON THOMAS BUSES%' THEN 'DON THOMAS'
        WHEN Bus_Company_Name LIKE 'EMPIRE CHARTER SERVICE%' THEN 'EMPIRE CHARTER'
        WHEN Bus_Company_Name LIKE 'EMPIRE STATE BUS CORP%' THEN 'EMPIRE STATE'
        WHEN Bus_Company_Name LIKE 'FIRST STEPS TRANS%' THEN 'FIRST STEPS'
        WHEN Bus_Company_Name LIKE 'FORTUNA BUS COMPANY%' THEN 'FORTUNA'
		WHEN Bus_Company_Name LIKE 'G.V%' THEN 'G.V.C.'
        WHEN Bus_Company_Name LIKE 'GV%' THEN 'G.V.C.'
        WHEN Bus_Company_Name LIKE 'GRANDPA`S BUS CO%' THEN 'GRANDPAS'
        WHEN Bus_Company_Name LIKE 'HOYT TRANSPORTATION CORP%' THEN 'HOYT'
        WHEN Bus_Company_Name LIKE 'I & Y TRANSIT CORP%' THEN 'I & Y'
        WHEN Bus_Company_Name LIKE 'IC BUS INC%' THEN 'IC BUS'
        WHEN Bus_Company_Name LIKE 'JOFAZ TRANSPORTATION INC%' THEN 'JOFAZ'
        WHEN Bus_Company_Name LIKE 'L & M BUS CORP%' THEN 'L & M'
        WHEN Bus_Company_Name LIKE 'LEESEL TRANSPORTATION%' THEN 'LEESEL'
        WHEN Bus_Company_Name LIKE 'LITTLE LINDA BUS CO%' THEN 'LITTLE LINDA'
        WHEN Bus_Company_Name LIKE 'LITTLE LISA BUS CO%' THEN 'LITTLE LISA'
        WHEN Bus_Company_Name LIKE 'LITTLE RICHIE BUS SERVICE%' THEN 'LITTLE RICHIE'
        WHEN Bus_Company_Name LIKE 'LOGAN%' THEN 'LOGAN'
        WHEN Bus_Company_Name LIKE 'LORINDA ENTERPRISES%' THEN 'LORINDA'
        WHEN Bus_Company_Name LIKE 'LORISSA BUS SERVICE%' THEN 'LORISSA'
        WHEN Bus_Company_Name LIKE 'MAR-CAN TRANSPORT CO%' THEN 'MAR-CAN'
        WHEN Bus_Company_Name LIKE 'MJT BUS COMPANY%' THEN 'MJT'
        WHEN Bus_Company_Name LIKE 'NEW DAWN TRANSIT%' THEN 'NEW DAWN'
        WHEN Bus_Company_Name LIKE 'NYC SCHOOL BUS UMBRELLA SERVICES%' THEN 'NYC SCHOOL BUS'
        WHEN Bus_Company_Name LIKE 'NYCSBUS%' THEN 'NYCSBUS'
        WHEN Bus_Company_Name LIKE 'PENNY TRANSPORTATION%' THEN 'PENNY'
        WHEN Bus_Company_Name LIKE 'PHIL%' THEN 'PHILIP'
        WHEN Bus_Company_Name LIKE 'PIONEER TRANSPORTATION CORP%' THEN 'PIONEER'
        WHEN Bus_Company_Name LIKE 'PRIDE TRANSPORTATION%' THEN 'PRIDE'
        WHEN Bus_Company_Name LIKE 'QUALITY TRANSPORTATION CORP%' THEN 'QUALITY'
        WHEN Bus_Company_Name LIKE 'RELIANT TRANSPORTATION%' THEN 'RELIANT'
        WHEN Bus_Company_Name LIKE 'SELBY TRANSPORTATION%' THEN 'SELBY'
        WHEN Bus_Company_Name LIKE 'SMART PICK%' THEN 'SMART PICK'
        WHEN Bus_Company_Name LIKE 'SNT BUS INC%' THEN 'SNT'
        WHEN Bus_Company_Name LIKE 'THIRD AVENUE TRANSIT%' THEN 'THIRD AVENUE'
        WHEN Bus_Company_Name LIKE 'THOMAS BUSES%' THEN 'THOMAS'
        WHEN Bus_Company_Name LIKE 'VAN TRANS LLC%' THEN 'VAN TRANS'
        WHEN Bus_Company_Name LIKE 'VINNY`S BUS SERVICES%' THEN 'VINNYS'
        WHEN Bus_Company_Name LIKE 'Y & M TRANSIT%' THEN 'Y & M'
        ELSE Bus_Company_Name
    END AS Bus_Company_Name_Clean
FROM nyc_bus_service
ORDER BY Bus_Company_Name;

-- Let's update the table with the new clean names:

UPDATE nyc_bus_service
SET Bus_Company_Name = 
 CASE 
        WHEN Bus_Company_Name LIKE 'ALINA SERVICES CORP%' THEN 'ALINA'
        WHEN Bus_Company_Name LIKE 'ALL AMERICAN SCHOOL BUS CORP%' THEN 'ALL AMERICAN'
        WHEN Bus_Company_Name LIKE 'ALL COUNTY BUS LLC%' THEN 'ALL COUNTY'
        WHEN Bus_Company_Name LIKE 'ALLIED TRANSIT CORP%' THEN 'ALLIED'
        WHEN Bus_Company_Name LIKE 'ANOTHER RIDE INC%' THEN 'ANOTHER RIDE'
        WHEN Bus_Company_Name LIKE 'B & F SKILLED INC%' THEN 'B & F SKILLED'
        WHEN Bus_Company_Name LIKE 'BOBBY`S BUS CO%' THEN 'BOBBYS'
        WHEN Bus_Company_Name LIKE 'BORO TRANSIT%' THEN 'BORO TRANSIT'
        WHEN Bus_Company_Name LIKE 'CAREFUL BUS%' THEN 'CAREFUL'
        WHEN Bus_Company_Name LIKE 'CHILDREN`S TRANS%' THEN 'CHILDRENS TRANS'
        WHEN Bus_Company_Name LIKE 'CONSOLIDATED BUS%' THEN 'CONSOLIDATED'
        WHEN Bus_Company_Name LIKE 'DON THOMAS BUSES%' THEN 'DON THOMAS'
        WHEN Bus_Company_Name LIKE 'EMPIRE CHARTER SERVICE%' THEN 'EMPIRE CHARTER'
        WHEN Bus_Company_Name LIKE 'EMPIRE STATE BUS CORP%' THEN 'EMPIRE STATE'
        WHEN Bus_Company_Name LIKE 'FIRST STEPS TRANS%' THEN 'FIRST STEPS'
        WHEN Bus_Company_Name LIKE 'FORTUNA BUS COMPANY%' THEN 'FORTUNA'
		WHEN Bus_Company_Name LIKE 'G.V%' THEN 'G.V.C.'
        WHEN Bus_Company_Name LIKE 'GV%' THEN 'G.V.C.'
        WHEN Bus_Company_Name LIKE 'GRANDPA`S BUS CO%' THEN 'GRANDPAS'
        WHEN Bus_Company_Name LIKE 'HOYT TRANSPORTATION CORP%' THEN 'HOYT'
        WHEN Bus_Company_Name LIKE 'I & Y TRANSIT CORP%' THEN 'I & Y'
        WHEN Bus_Company_Name LIKE 'IC BUS INC%' THEN 'IC BUS'
        WHEN Bus_Company_Name LIKE 'JOFAZ TRANSPORTATION INC%' THEN 'JOFAZ'
        WHEN Bus_Company_Name LIKE 'L & M BUS CORP%' THEN 'L & M'
        WHEN Bus_Company_Name LIKE 'LEESEL TRANSPORTATION%' THEN 'LEESEL'
        WHEN Bus_Company_Name LIKE 'LITTLE LINDA BUS CO%' THEN 'LITTLE LINDA'
        WHEN Bus_Company_Name LIKE 'LITTLE LISA BUS CO%' THEN 'LITTLE LISA'
        WHEN Bus_Company_Name LIKE 'LITTLE RICHIE BUS SERVICE%' THEN 'LITTLE RICHIE'
        WHEN Bus_Company_Name LIKE 'LOGAN%' THEN 'LOGAN'
        WHEN Bus_Company_Name LIKE 'LORINDA ENTERPRISES%' THEN 'LORINDA'
        WHEN Bus_Company_Name LIKE 'LORISSA BUS SERVICE%' THEN 'LORISSA'
        WHEN Bus_Company_Name LIKE 'MAR-CAN TRANSPORT CO%' THEN 'MAR-CAN'
        WHEN Bus_Company_Name LIKE 'MJT BUS COMPANY%' THEN 'MJT'
        WHEN Bus_Company_Name LIKE 'NEW DAWN TRANSIT%' THEN 'NEW DAWN'
        WHEN Bus_Company_Name LIKE 'NYC SCHOOL BUS UMBRELLA SERVICES%' THEN 'NYC SCHOOL BUS'
        WHEN Bus_Company_Name LIKE 'NYCSBUS%' THEN 'NYCSBUS'
        WHEN Bus_Company_Name LIKE 'PENNY TRANSPORTATION%' THEN 'PENNY'
        WHEN Bus_Company_Name LIKE 'PHIL%' THEN 'PHILIP'
        WHEN Bus_Company_Name LIKE 'PIONEER TRANSPORTATION CORP%' THEN 'PIONEER'
        WHEN Bus_Company_Name LIKE 'PRIDE TRANSPORTATION%' THEN 'PRIDE'
        WHEN Bus_Company_Name LIKE 'QUALITY TRANSPORTATION CORP%' THEN 'QUALITY'
        WHEN Bus_Company_Name LIKE 'RELIANT TRANSPORTATION%' THEN 'RELIANT'
        WHEN Bus_Company_Name LIKE 'SELBY TRANSPORTATION%' THEN 'SELBY'
        WHEN Bus_Company_Name LIKE 'SMART PICK%' THEN 'SMART PICK'
        WHEN Bus_Company_Name LIKE 'SNT BUS INC%' THEN 'SNT'
        WHEN Bus_Company_Name LIKE 'THIRD AVENUE TRANSIT%' THEN 'THIRD AVENUE'
        WHEN Bus_Company_Name LIKE 'THOMAS BUSES%' THEN 'THOMAS'
        WHEN Bus_Company_Name LIKE 'VAN TRANS LLC%' THEN 'VAN TRANS'
        WHEN Bus_Company_Name LIKE 'VINNY`S BUS SERVICES%' THEN 'VINNYS'
        WHEN Bus_Company_Name LIKE 'Y & M TRANSIT%' THEN 'Y & M'
        ELSE Bus_Company_Name
END;

-- EXPLORATORY DATA ANALYSIS:

SELECT * FROM nyc_bus_service.nyc_bus_service;

-- Let's look into the causes for delays:

SELECT Reason, COUNT(Breakdown_or_Running_Late) AS delays_count
FROM nyc_bus_service
WHERE Breakdown_or_Running_Late = 'Running Late'
GROUP BY Reason
ORDER BY delays_count DESC;

-- Let's look into the causes for breakdowns:

SELECT Reason, COUNT(Breakdown_or_Running_Late) AS breakdowns_count
FROM nyc_bus_service
WHERE Breakdown_or_Running_Late = 'Breakdown'
GROUP BY Reason
ORDER BY breakdowns_count DESC;

-- Let's look into the accumulated delay times over the years:

SELECT School_Year, ROUND(SUM(Avg_Time_Estimate)/3600,1) AS Total_Time_Estimate_Days
FROM nyc_bus_service
GROUP BY School_Year
ORDER BY School_Year;

-- Let's look into the count of Delays per time range:

SELECT How_Long_Delayed, COUNT(How_Long_Delayed) As Number_of_Delays
FROM nyc_bus_service
GROUP BY How_Long_Delayed
HAVING How_Long_Delayed <> '' AND How_Long_Delayed <> 872 
ORDER BY How_Long_Delayed;

-- Let's look into the sum of Delays per Boro:

SELECT Boro, ROUND(SUM(Avg_Time_Estimate) / 3600, 1) AS Delays_per_Boro_Days
FROM nyc_bus_service
WHERE Boro != 'All Boroughs' AND Boro != ''
GROUP BY Boro
ORDER BY Delays_per_Boro_Days DESC;

-- Let's see top 10 companies reporting mechanical problems:

SELECT Bus_Company_Name, COUNT(Reason) AS mech_problem_reports
FROM nyc_bus_service
WHERE Reason = 'Mechanical problem'
GROUP BY Bus_Company_Name
ORDER BY mech_problem_reports DESC
LIMIT 5;

-- Let's look into the number of breakdowns per day of the week:

SELECT Week_Day, COUNT(Busbreakdown_ID) AS num_Breakdowns
FROM nyc_bus_service
GROUP BY Week_Day
ORDER BY CASE Week_Day
    WHEN 'Monday' THEN 1
    WHEN 'Tuesday' THEN 2
    WHEN 'Wednesday' THEN 3
    WHEN 'Thursday' THEN 4
    WHEN 'Friday' THEN 5
    WHEN 'Saturday' THEN 6
    WHEN 'Sunday' THEN 7
END;

-- Let's look into the top companies accumulating delays:

SELECT Bus_Company_Name, ROUND(SUM(Avg_Time_Estimate) / 3600,1) AS Delays_in_Days
FROM nyc_bus_service
GROUP BY Bus_Company_Name
ORDER BY Delays_in_Days DESC;

SELECT 
	School_Year,
    Busbreakdown_ID,
    Reason,
    Boro,
    Bus_Company_Name,
    How_Long_Delayed,
    Breakdown_or_Running_Late,
    Avg_Time_Estimate,
    Week_Day
FROM nyc_bus_service.nyc_bus_service;
