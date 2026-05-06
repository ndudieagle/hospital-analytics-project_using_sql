# hospital-analytics-project_using_sql
This project analyzes hospital patient data to uncover insights into: - Readmission rates - Cost drivers - Patient risk levels - Operational efficiency  The goal is to support better decision-making in healthcare systems.


SELECT TOP (1000) [patient_id]
      ,[facility_id]
      ,[Mapped_Department]
      ,[diagnosis_description]
      ,[severity_of_illness]
      ,[risk_of_mortality]
      ,[length_of_stay]
      ,[total_costs]
      ,[room_charge]
      ,[medication_cost]
      ,[admission_type]
      ,[patient_disposition]
      ,[doctor_id]
      ,[doctor_specialty]
      ,[readmission_flag]
      ,[insurance_type]
      ,[payment_method]
      ,[city]
      ,[bed_type]
      ,[admission_date]
      ,[discharge_date]
  FROM [hospital data].[dbo].[healthcare_mhmc_dataset_v2$]


#Insight Which departments are causing repeated hospital visits (quality issue)
SELECT 
    Mapped_Department,
    COUNT(*) AS total_patients,
    SUM(CASE WHEN readmission_flag = 1 THEN 1 ELSE 0 END) AS readmissions,
    (SUM(CASE WHEN readmission_flag = 1 THEN 1 ELSE 0 END) * 100.0) / COUNT(*) AS readmission_rate
FROM [hospital data].[dbo].[healthcare_mhmc_dataset_v2$]
GROUP BY Mapped_Department
ORDER BY readmission_rate DESC;

## HighCost vs HighRisk Patients, Do high-risk patients actually cost more?
SELECT risk_of_mortality, 
AVG (total_costs) AS avg_cost,
COUNT(*) AS patient_count
FROM healthcare_mhmc_dataset_v2$
GROUP BY risk_of_mortality
ORDER BY avg_cost DESC;

## Length of Stay vs Cost (Efficiency Analysis)
SELECT length_of_stay,
AVG(total_costs) AS avg_cost,
COUNT (*) AS patients
FROM healthcare_mhmc_dataset_v2$
GROUP BY length_of_stay
ORDER BY length_of_stay DESC;

## Top 5 Doctors by Patient Volume

SELECT TOP 5
    doctor_id,
    COUNT(*) AS total_patients
FROM healthcare_mhmc_dataset_v2$
GROUP BY doctor_id
ORDER BY total_patients DESC;

## Cost Breakdown per Patient (Advanced),Where is hospital money going?
SELECT 
    patient_id,
    total_costs,
    room_charge,
    medication_cost,
    (room_charge * 100.0 / total_costs) AS room_percent,
    (medication_cost * 100.0 / total_costs) AS medication_percent
FROM healthcare_mhmc_dataset_v2$;

## Monthly Admission Trend,Detect peak hospital periods (seasonality)
SELECT 
    FORMAT(admission_date, 'yyyy-MM') AS month,
    COUNT(*) AS total_admissions
FROM healthcare_mhmc_dataset_v2$
GROUP BY FORMAT(admission_date, 'yyyy-MM')
ORDER BY month;

## Readmission by Admission Type,Emergency vs planned admissions impact
SELECT 
    admission_type,
    COUNT(*) AS total_cases,
    SUM(CASE WHEN readmission_flag = 1 THEN 1 ELSE 0 END) AS readmissions
FROM healthcare_mhmc_dataset_v2$
GROUP BY admission_type
ORDER BY readmissions DESC;

## Bed Type Utilization,ICU vs General bed demand
SELECT 
    bed_type,
    COUNT(*) AS usage_count
FROM healthcare_mhmc_dataset_v2$
GROUP BY bed_type
ORDER BY usage_count DESC;

## Insurance vs Self-Pay Revenue,Revenue drivers in hospital system
SELECT 
    insurance_type,
    SUM(total_costs) AS total_revenue,
    COUNT(*) AS patients
FROM healthcare_mhmc_dataset_v2$
GROUP BY insurance_type
ORDER BY total_revenue DESC;

## High-Risk + Long Stay Patients (Critical Cases)Patients needing special attention

SELECT *
FROM healthcare_mhmc_dataset_v2$
WHERE risk_of_mortality = 'High'
AND length_of_stay > 7;

## Rank doctors by cost handled. Which doctors generate most revenue
SELECT 
    doctor_id,
    SUM(total_costs) AS total_revenue,
    RANK() OVER (ORDER BY SUM(total_costs) DESC) AS rank_position
FROM healthcare_mhmc_dataset_v2$
GROUP BY doctor_id;

## Average Cost by City + Department,Geographic cost differences
SELECT 
    city,
    Mapped_Department,
    AVG(total_costs) AS avg_cost
FROM healthcare_mhmc_dataset_v2$
GROUP BY city, Mapped_Department
ORDER BY avg_cost DESC;


## 📊 Key Insights

- Departments with high readmission rates were identified, indicating potential quality issues.
- High-risk patients tend to incur higher treatment costs.
- Longer hospital stays significantly increase total costs.
- Emergency admissions show higher readmission likelihood.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              ## 🛠️ Tools Used
- SQL Server
- SQL (T-SQL)
- Power BI (for visualization)                                                                                                  ## 📂 Dataset Features
- Patient demographics
- Diagnosis and severity
- Cost breakdown (room, medication)
- Admission and discharge details                                                                                                                                                                                                                                                                                                                                                          
