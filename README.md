# Data-Cleaning-in-SQL

## Data Cleaning, cleaning Maven Telecommunication Dataset 

*Querying database to validate the metadata in the data dictionary to ensure
      data completeness, accuracy, consistency, and overall data integrity.*
   
- Checking for duplicate records
   
```SQL
SELECT Customer_ID,
       COUNT(Customer_ID)
 FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
 GROUP BY Customer_ID
 HAVING COUNT(Customer_ID) > 1
 ```
 
 
 - validating we only have two genders type in the Gender column
   
```SQL
SELECT DISTINCT Gender
 FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data` 
 ```
 
 - validating data entries in the Married column
   
```SQL
SELECT DISTINCT Married
 FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data` 
 ```
 
 - validating data entries in the Contract column
   
```SQL
SELECT DISTINCT Contract
  FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
 ``` 
  
  - validating data entries in the Phone Service column
   
```SQL
SELECT DISTINCT Phone_Service
  FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
  ```
  
  - validating data entries in the Offer types column
   
```SQL
SELECT DISTINCT Offer
 FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data` 
 ```
 
 - validating data entries in the  Multiple Lines column
 
 *IS NULL is one of the distinct inputs in multiple line columns, need to update all NULL values to "No",
 since customers don't subscribe to phone service*
 
```SQL
SELECT DISTINCT Multiple_Lines
 FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data` 
 
UPDATE `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
SET Multiple_Lines = FALSE
  WHERE Multiple_Lines IS NULL
```
  
- checking data entries in the Internet_Type column

```SQL
SELECT DISTINCT Internet_Type
  FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
 ``` 
  
- checking data entries in the Payment_Method column

```SQL
SELECT DISTINCT Payment_Method
  FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
  ```
  
- checking data entries in the Customer_Status column
    
```SQL
SELECT DISTINCT Customer_Status
  FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
  ```
  
- checking data entries in THE Churn_Category column

```SQL
SELECT DISTINCT Churn_Category
  FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
  ```
  
  - checking data entries in the Average monthly GB download column
   
```SQL
SELECT Avg_Monthly_GB_Download
  FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data` AS telecomm_data
  WHERE Internet_Service IS FALSE
```

   *All average monthly GB downloads for customers that didn't subscribe to internet service
    return FALSE, according to the data dictionary should be 0*

 - Updating all NULL values in the Avg_Monthly_GB_Download to '0' 

```SQL
 UPDATE `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
 SET Avg_Monthly_GB_Download = 0
 WHERE Internet_Service IS FALSE
 ```
 
- Validating Total Revenue Calculation
 
*I calculated the total revenue with the formula in the data dictionary and compared it with
Total_Revenue column for accuracy*

- Creating a temporary table for self-calculated total_revenue

```SQL
WITH Total_Revenue_Check AS 
(
SELECT Customer_ID, Total_Revenue,
ROUND(Total_Charges + Total_Extra_Data_Charges + Total_Long_Distance_Charges - Total_Refunds,2)  AS Total_Revenue_2
```SQL
  FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data` ```
  GROUP BY Customer_ID, Total_Revenue, Total_Charges,Total_Extra_Data_Charges,Total_Long_Distance_Charges,Total_Refunds
)
SELECT churndata.Total_Revenue,
       Total_Revenue_Check.Total_Revenue_2
FROM Total_Revenue_Check
 INNER JOIN `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data` AS churndata
 ON Total_Revenue_Check.Customer_ID = churndata.Customer_ID
WHERE Total_Revenue_Check.Total_Revenue_2 != churndata.Total_Revenue
``` 
