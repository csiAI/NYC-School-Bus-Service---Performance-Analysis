# NYC School Bus Service | Performance Analysis

# Project Background

The NYC School Bus Service is a transportation program managed by the New York City Department of Education (DOE) that provides free bus transportation for eligible students attending public, charter, or private schools in the city. The service is designed to ensure that students, particularly those who live far from their schools or have special needs, can safely and reliably commute to and from school.

The aim of this project is to perform an indepth analysis of the past and current performance of the NYC School Bus Service, to understand the challenges it faces and  suggest potential areas of improvement to provide a better service to their users. The key areas of focus include:

- Service Scale: Key numbers showing operational scope

- Delay Analysis: Distribution of delays by time ranges and total count

- Geographic Breakdown: Delay patterns across different boroughs/regions

- Annual Trends: Year-over-year comparison of delay times

- Weekly Patterns: Breakdown frequencies across different days of the week

- Company Performance: Rankings of bus companies by mechanical issues

- Cumulative Impact: Total accumulated delays by service provider

# Data Structure & Initial Checks
The data was obtained through a CSV file provided by the New York City Department of Education, with a total of 250K rows. The csv file with the data can be found [here](https://drive.google.com/file/d/1Bq89xxzxvSu-_GNwjIZGt4jgnpVDSymp/view?usp=sharing)

I have performed Data Cleaning and Exploratory Data Analysis using SQL. The SQL file with the code is attached in this repository. I then created a dashboard in Tableau to display the key insights visually.


# Executive Summary

Overview of Findings
- This analysis reveals significant challenges in service reliability and efficiency. Key findings include high delay frequencies, substantial accumulated delay times, and notable mechanical issues across bus companies.
- An interactive Tableau dashboard can e found [here](https://public.tableau.com/app/profile/carlos.s.nchez4177/viz/NYCSchoolBusServicePerformanceAnalysis/Dashboard)

![image](https://github.com/user-attachments/assets/4a93d9db-1386-47ce-83d6-333487f68891)
![image](https://github.com/user-attachments/assets/e051df29-5b7c-45d3-ab39-d20e72e11936)

Average Delay Time: 
- 37 minutes.
        
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

Key Insights & Recommendations:

- Traffic Management: Collaborate with city traffic department to optimize bus routes. Implement real-time traffic monitoring for dynamic route adjustments. Focus on Manhattan and Brooklyn for targeted improvements.

- Mechanical Reliability: Conduct a comprehensive audit of PRIDE TRANS, L&M and other high-incident companies. Implement stricter preventative maintenance schedules, especially before Mondays.

- Performance Incentives: Introduce a performance-based contract system for bus companies. Recognize and reward companies and drivers with the best on-time records.

Conclusion:

- While the NYC School Bus Service faces significant challenges, the data suggests areas where targeted interventions can lead to substantial improvements. By focusing on reducing delays, enhancing mechanical reliability, and optimizing routes, particularly in high-impact areas, the service can significantly enhance its performance and reliability for New York City's students and families.
