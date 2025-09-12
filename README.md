# Financial-Statement-Analysis-SQL

## Introduction
This project focuses on analyzing my client’s bank transactions to uncover spending patterns, income sources, and overall financial health. Using SQL for exploratory analysis, I transformed raw transaction data into actionable insights, The project is a realworld project that contains client bank credit transactions includes key variables such as Date, Money_In, Money_Out, Category, To_From, Discription and Balance

## Key Metrics

•	What are the Total inflow (Credit) and outflow (Debit) transactions.

•	What are the largest credit and debit transactions. 

•	Whats are the transactions per month

•	What are the total amount and transaction count weekly.

•	Identifying recurring credits and debits


## Tools & Concepts Used

•	SQL

•	SQL Server

•	Data Exploration

•	Data Cleaning 

•	Aggregation


# Data Profilling
The dataset contains 493 rows and 7 columns of Financial transactional statement Analysis. It includes 6 categorical variables Date, Money_In, Money_Out, Category, To_From, Discription, Balance. 

The Bank statement was stored as PDF file from the bank then i was converted to as excel file which made it so unstructured and dirty so i performed some cleaning techniques before uploading it to mysql for exploratory analysis. 

This cleaned dataset now provides a reliable foundation for SQL-based analysis to explore monthly financial trends, inflows and outflows, recurring transactions and so on.

 ### Creating database
 ```
CREATE DATABASE IF NOT EXISTS account_analysis;
```

### Creating Table
```
CREATE TABLE IF NOT EXISTS transactions (
    Date DATE,
    Money_In DECIMAL(18 , 2 ),
    Money_Out DECIMAL(18 , 2 ),
    Category VARCHAR(100),
    To_From VARCHAR(150),
    Description VARCHAR(255),
    Balance DECIMAL(18 , 2 )
);
```

### Number of columns 
```
SELECT 
    COUNT(*) AS column_count
FROM
    INFORMATION_SCHEMA.COLUMNS
WHERE
    Table_Name = 'transactions';
```
| column_count |
|--------------|
| 7            |

### Number of rows
```
SELECT 
    COUNT(*) AS row_count
FROM
    transactions;
```
| row_count |
|--------------|
| 493          |

### Column and data types 
```
SELECT 
    column_name, data_type
FROM
    INFORMATION_SCHEMA.COLUMNS
WHERE
    TABLE_NAME = 'transactions';
```
| column_name       | data_type |
|-------------------|----------- |
| Date              | date       |
| Money_In          | decimal    |
| Money_Out         | decimal    |
| Category          | Varchar    |
| To_From           | varchar    |
| Discription       | varchar    |
| Balance           | varchar    |

### Top 10 of Total_Outflow (Debit transactions)
```
SELECT 
    To_From,
    SUM(Money_Out) AS Total_Spent
FROM transactions
GROUP BY To_From
ORDER BY Total_Spent DESC
LIMIT 10;
```
| To_From | Total_Spent |
|--------------|---------|
| OYEDOKUN KEHINDE |	638300.22 |
| POS TRANSFER |	141540.00 |
| MOYINOLUWA FAITH |	66210.00 |
| FATBUS JEKKY VENTURES |	60100.00 |
| MOROMOKE OYENUGA |	57970.00 |
| TAIWO SAMUEL	| 34730.00 |
| LOVETH EKWUTOSI EBE	| 25000.00 |
| OLUWAFERANMI EBENEZER	| 20000.00 |
| AIRTIME PURCHASE | 19304.00 |
| OLUBUNMI OLUBOWALE |	17150.00 |

### Top 10 Total_inflow (Credit transactions)
```
SELECT 
    To_From,
    SUM(Money_In) AS Total_Received
FROM transactions
GROUP BY To_From
ORDER BY Total_Received DESC
LIMIT 10;
```
| To_From | Total_Received |
|----------|----------------|
| ISREAL OLUWABUSUYI | 549918.00 |
| OYEDOKUN KEHINDE	| 514287.72 |
| OLUWAFOLAKEMI DEBORAH	| 80000.00 |
| PAYSTACK |	79050.00 |
| LEMONADE TECHNOLOGY |	78600.00 |
| PROALLY LIMITED |	68977.50 |
| ONYIYE EMMANUEL |	42900.00 |
| AMOS OLATUNDE |	35000.00 |
| PRECIOUS IFEOLUWA	| 27000.00 |
| DAAT INVESTMENT |	18000.00 |

I discovered that the date format was wrong, decided to rebuild date as DD-MM-YY and stores it correctly as YYYY-MM-DD

### Updating Date format
```
UPDATE transactions
SET Date = STR_TO_DATE(
             CONCAT('20', LPAD(DAY(Date),2,'0'), '-', LPAD(MONTH(Date),2,'0'), '-', RIGHT(CAST(YEAR(Date) AS CHAR),2)),
             '%Y-%m-%d'
          );
```

### Top 5 Largest Credit Transactions
```
SELECT 
    To_From AS Person,
    Date,
    Money_In AS Credit_Amount
FROM transactions
WHERE Money_In > 0
ORDER BY Money_In DESC
LIMIT 5;
```

| Person	| Date	| Credit_Amount |
|----------|---------|-------------|
| OYEDOKUN KEHINDE	| 2025-05-14	| 79930.00 |
| PAYSTACK	| 2025-05-24	| 79050.00 |
| LEMONADE TECHNOLOGY	| 2025-06-25	| 78600.00 |
| PROALLY LIMITED	| 2025-05-21	| 60000.00 |
| OLUWAFOLAKEMI DEBORAH	| 2025-01-14	| 50000.00 |


### Top 5 Largest Debit Transactions
```
SELECT 
    To_From AS Person,
    Date,
    Money_Out AS Debit_Amount
FROM transactions
WHERE Money_Out > 0
ORDER BY Money_Out DESC
LIMIT 3;
```
| Person	| Date	| Debit_Amount |
|---------|---------|-------------|
| OYEDOKUN KEHINDE |	2025-04-03 |	50000.00 |
| POS TRANSFER |	2025-02-17 |	50000.00 |
| FATBUS JEKKY VENTURES	| 2025-05-16 |	37050.00 |

### Get the top 10 people you’ve transacted with, Shows both how much they credited you (Money_In) and how much you sent them (Money_Out)
```
SELECT 
    To_From AS Person,
    SUM(Money_In) AS Total_Credited,
    SUM(Money_Out) AS Total_Sent
FROM transactions
GROUP BY To_From
ORDER BY Total_Credited DESC, Total_Sent DESC
LIMIT 10;
```
| Person	| Total_Credited	|Total_Sent |
|----------|-----------------|------------|
| ISREAL OLUWABUSUYI	| 549918.00	| 14000.00 |
| OYEDOKUN KEHINDE |	514287.72 |	638300.22 |
| OLUWAFOLAKEMI DEBORAH |	80000.00	| 0.00 |
| PAYSTACK	| 79050.00	| 0.00 |
| LEMONADE TECHNOLOGY |	78600.00 |	0.00 |
| PROALLY LIMITED	| 68977.50	| 0.00 |
| ONYIYE EMMANUEL	| 42900.00	| 7000.00 |
| AMOS OLATUNDE	| 35000.00	| 0.00 |
| PRECIOUS IFEOLUWA	| 27000.00 |	0.00 |
| DAAT INVESTMENT	| 18000.00	| 0.00 |

### Find people who had a positive net impact (you received more than you sent)
```
SELECT 
    To_From AS Person,
    SUM(Money_In) AS Total_Credited,
    SUM(Money_Out) AS Total_Sent,
    (SUM(Money_In) - SUM(Money_Out)) AS Positive_Impact
FROM transactions
GROUP BY To_From
HAVING Positive_Impact > 0
ORDER BY Positive_Impact DESC;
```

### Find people who had a negative net impact (you sent them more than you received)
```
SELECT 
    To_From AS Person,
    SUM(Money_In) AS Total_Credited,
    SUM(Money_Out) AS Total_Sent,
    (SUM(Money_In) - SUM(Money_Out)) AS Negative_Impact
FROM transactions
GROUP BY To_From
HAVING Negative_Impact < 0
ORDER BY Negative_Impact ASC;
```

### Transactions Count per Month
```
SELECT 
    DATE_FORMAT(Date, '%Y-%m') AS Month,
    COUNT(*) AS Transaction_Count
FROM transactions
GROUP BY DATE_FORMAT(Date, '%Y-%m')
ORDER BY Month;
```
| Month |	Transaction_Count |
|--------|-------------------|
| 2025-01 |	54 |
| 2025-02 |	52 |
| 2025-03 |	45 |
| 2025-04 |	66 |
| 2025-05 |	92 |
| 2025-06 |	145 |
| 2025-07 |	31 |
| 2025-08	| 8 |

### Transaction Volume vs Value (Monthly)
```
SELECT 
    DATE_FORMAT(Date, '%Y-%m') AS Month,
    COUNT(*) AS Transaction_Count,
    SUM(Money_In) AS Total_Credited,
    SUM(Money_Out) AS Total_Sent,
    (SUM(Money_In) - SUM(Money_Out)) AS Net_Impact,
    ROUND(SUM(Money_In) / NULLIF(COUNT(*),0), 2) AS Avg_Credit_Per_Transaction,
    ROUND(SUM(Money_Out) / NULLIF(COUNT(*),0), 2) AS Avg_Debit_Per_Transaction
FROM transactions
GROUP BY DATE_FORMAT(Date, '%Y-%m')
ORDER BY Month;
```

### The total amount spent and transaction count done in a week
```
SELECT 
    DAYNAME(Date) AS DayOfWeek,
    COUNT(*) AS Transaction_Count,
    SUM(Money_In) AS Total_Credited,
    SUM(Money_Out) AS Total_Sent
FROM transactions
GROUP BY DayOfWeek
ORDER BY Transaction_Count DESC;
```
| DayOfWeek |	Transaction_Count |	Total_Credited | Total_Sent |
|------------|-------------------|---------------|-------------|
| Wednesday |	126	| 510537.50	| 340400.50 |
| Thursday	| 74	| 133192.00 |	292740.00 |
| Tuesday	| 71	| 223810.00	| 141947.72 |
| Friday	| 69	| 429910.00	| 147140.00 |
| Monday |	63	| 42717.72	| 224962.00 |
| Saturday |	57	| 162166.00 |	106394.00 |
| Sunday	| 33	| 88000.00 |	58190.00 |

### Monthly Credits, Debits & Net Impact
```
SELECT 
    DATE_FORMAT(Date, '%Y-%m') AS Month,
    SUM(Money_In) AS Total_Credited,
    SUM(Money_Out) AS Total_Sent,
    (SUM(Money_In) - SUM(Money_Out)) AS Net_Impact
FROM transactions
GROUP BY DATE_FORMAT(Date, '%Y-%m')
ORDER BY Month;
```
| Month	| Total_Credited	| Total_Sent	| Net_Impact |
|--------|-----------------|-------------|------------|
| 2025-01	| 176378.00	| 112760.00	| 63618.00 |
| 2025-02	| 317842.00 |	229521.00	| 88321.00 |
| 2025-03	| 153820.00	| 106360.00	| 47460.00 |
| 2025-04 |	146342.00 |	146702.00	| -360.00 |
| 2025-05	| 313487.72	| 286747.72	| 26740.00 |
| 2025-06	| 310443.50	| 372743.50	 | -62300.00 |
| 2025-07	| 165510.00	| 41310.00	| 124200.00 |
| 2025-08	| 6510.00	| 15630.00	| -9120.00 |

### Identify recurring credits (repeated Money_In from the same person with the same amount)
```
SELECT 
    To_From AS Person,
    Money_In AS Amount,
    COUNT(*) AS Frequency,
    MIN(Date) AS First_Date,
    MAX(Date) AS Last_Date
FROM transactions
WHERE Money_In > 0
GROUP BY To_From, Money_In
HAVING COUNT(*) > 1
ORDER BY Frequency DESC, Amount DESC;
```
### Identify recurring debits (repeated Money_Out to the same person with the same amount)
```
SELECT 
    To_From AS Person,
    Money_Out AS Amount,
    COUNT(*) AS Frequency,
    MIN(Date) AS First_Date,
    MAX(Date) AS Last_Date
FROM transactions
WHERE Money_Out > 0
GROUP BY To_From, Money_Out
HAVING COUNT(*) > 1
ORDER BY Frequency DESC, Amount DESC;
```



