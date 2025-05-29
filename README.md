# Unit Economics
## 📜 Introduction
Context

You have been hired for a new job as a Data Analyst.

The company is called "TechStream Solutions", and the product is a Software as a Service (SaaS) platform named "Streamline Pro". This platform provides comprehensive project management and collaboration tools for businesses of all sizes.

TechStream Solutions has been operating for several years and has gathered significant data on their costs and revenues. They are now looking to analyze their unit economics to understand the profitability of Streamline Pro on a per-customer basis.

The datasets are in the shared folder on Google Drive:

https://drive.google.com/drive/folders/1qhOW9Y2orRXuzbX-kXEmuJ7TMQiRs2Uv?usp=drive_link

By performing these calculations, TechStream Solutions aims to:

- Identify the profitability of acquiring and retaining customers.
- Assess the efficiency of their marketing and sales strategies.
- Make informed decisions on scaling their operations and optimizing their resource allocation.
- This information will guide TechStream Solutions in refining their business strategies, ensuring sustainable growth, and maximizing profitability.
## Objective

 - Understanding the unit economics of Streamline Pro is crucial for evaluating its financial health and sustainability. This involves analysing key metrics such as Customer Acquisition Cost (CAC), Average Revenue Per User (ARPU), Cost of Goods Sold (COGS), Gross Margin, Customer Lifetime Value (LTV), and the LTV/CAC ratio.
- Your task is to calculate the unit economics for Streamline Pro for the month of March 2023. This will help us assess the profitability and efficiency of our customer acquisition strategies and operational expenses.

## Project workflow

### Read and filter data

```python
# Import libary
import pandas as pd
# Define a function to read data from a Google Sheet 
def read_data(file_id):
   df = pd.read_excel('https://docs.google.com/spreadsheets/d/'+ file_id + '/export?format=xlsx')
   return df
# Load datasets from Google Sheets using their file IDs
customer_lifespan = read_data(file_id = '1by8tPHwOnq3uKYK2E7sA9VBUYoPM4p1Rnrm_Ss9cyHI')
marketing_spendings = read_data(file_id='1AZOIThOV4P-0eYDge53ZwumVkfkHoYPWxst3k3Bv87c')
monthly_expenses = read_data(file_id='10OGbaywwMIqKgnPGy8VDvpBVtjyqln47iYa2lFhI9Mw')
payroll = read_data(file_id='1c_WihqTZCQvNgxzmd-OwhR9i5diwtfxXVLyMn8R-Lp4')
receipts_history = read_data(file_id='1qayqML1zCKdmtzutkcy9LWvE6xFRm6TGBEVkHHJKIuE')
```
```python
# Define a function to filter only the records from the most recent month in the dataset
def get_last_month(dataset,date_col):
  last_month = dataset[date_col].max().month
  cond = dataset[date_col].dt.month == last_month
  return dataset[cond]
# Apply the function to filter each dataset for the March 2023
marketing_spending_march = get_last_month(marketing_spendings,'date')
monthly_expenses_march = get_last_month(monthly_expenses,'month')
payroll_march = get_last_month(payroll,'month')
receipts_history_march = get_last_month(receipts_history,'date')
```

### Data overview

```python
# Show first 5 rows
marketing_spending_march.head()
```
|     | date                | channel      |   spending |
|----:|:--------------------|:-------------|-----------:|
| 236 | 2023-03-01 00:00:00 | Google Ads   |        449 |
| 237 | 2023-03-01 00:00:00 | Facebook Ads |        229 |
| 238 | 2023-03-01 00:00:00 | LinkedIn Ads |        835 |
| 239 | 2023-03-01 00:00:00 | Twitter Ads  |        986 |
| 240 | 2023-03-02 00:00:00 | Google Ads   |        912 |

```python
# Show first 5 rows
payroll_march.head()
```
|    | month               | department   | employee_name   | position          |   paid |
|---:|:--------------------|:-------------|:----------------|:------------------|-------:|
| 34 | 2023-03-01 00:00:00 | Sales        | John Doe        | Sales Manager     |   1500 |
| 35 | 2023-03-01 00:00:00 | Sales        | Jane Smith      | Sales Associate   |    600 |
| 36 | 2023-03-01 00:00:00 | Sales        | Jim Brown       | Sales Associate   |    700 |
| 37 | 2023-03-01 00:00:00 | Sales        | Laura Miller    | Sales Associate   |    800 |
| 38 | 2023-03-01 00:00:00 | Marketing    | Alice Johnson   | Marketing Manager |   1650 |

```python
# Show first 5 rows
monthly_expenses_march.head()
```
|    |   # | month               | category          | item                 |   amount |
|---:|----:|:--------------------|:------------------|:---------------------|---------:|
| 18 |  19 | 2023-03-01 00:00:00 | Server Costs      | AWS Hosting          |     8400 |
| 19 |  20 | 2023-03-01 00:00:00 | Server Costs      | Google Cloud Storage |     4400 |
| 20 |  21 | 2023-03-01 00:00:00 | Software Licenses | Atlassian Jira       |     1400 |
| 21 |  22 | 2023-03-01 00:00:00 | Software Licenses | Slack                |      900 |
| 22 |  23 | 2023-03-01 00:00:00 | Software Licenses | Salesforce           |     1700 |
```python
# Show first 5 rows
customer_lifespan.head()
```
|    |   Unnamed: 0 | start_date          | churn_date          |
|---:|-------------:|:--------------------|:--------------------|
|  0 |         1000 | 2021-11-15 00:00:00 | 2022-09-14 00:00:00 |
|  1 |         1001 | 2022-04-15 00:00:00 | 2023-02-16 00:00:00 |
|  2 |         1002 | 2022-10-30 00:00:00 | 2023-02-04 00:00:00 |
|  3 |         1003 | 2021-08-22 00:00:00 | 2023-02-07 00:00:00 |
|  4 |         1004 | 2021-08-23 00:00:00 | 2022-02-02 00:00:00 |
```python
# Show first 5 rows
receipts_history_march.head()
```
|     | date                |   customer_id |   receipt_amount |   new_customer |
|----:|:--------------------|--------------:|-----------------:|---------------:|
| 618 | 2023-03-01 00:00:00 |          1062 |              103 |              0 |
| 619 | 2023-03-01 00:00:00 |          2243 |              157 |              0 |
| 620 | 2023-03-01 00:00:00 |          1166 |              372 |              0 |
| 621 | 2023-03-01 00:00:00 |          2406 |              426 |              1 |
| 622 | 2023-03-01 00:00:00 |          2761 |               41 |              1 |
### CAC (Customer Acquisition Cost)

```python
# Calculate total spending on ads in March 2023
Ads_marketing_spending_march = (marketing_spending_march['spending'].sum())
# Calculate payroll cost for Sales and Marketing departments in March 2023
payroll_spending = payroll_march[payroll_march['department'].isin(['Sales','Marketing'])]['paid'].sum()
# Get the cost of Salesforce software in March 2023
software_cost = monthly_expenses_march[monthly_expenses_march['item'] == 'Salesforce']['amount'].sum()
```
```python
# Calculate total marketing-related spending in March 2023
total_marketing_spending_march = Ads_marketing_spending_march + payroll_spending + software_cost
```
```python
# Filter customers who started using the product in March 2023
new_customers = customer_lifespan[customer_lifespan['start_date'].dt.to_period('M') == '2023-03']
# Count the number of new customers in March 2023
num_new_customers = new_customers.shape[0]
```
```python
# Calculate CAC (Customer Acquisition Cost) = Total marketing spending / Number of new customers
CAC = int(total_marketing_spending_march/num_new_customers)
CAC
```


### ARPU (Average Revenue Per User)

```python
# Calculate the total revenue received in March 2023
total_revenue_march = receipts_history_march['receipt_amount'].sum()
```
```python
# Calculate ARPU (Average Revenue Per User) = Total revenue / Number of new customers
ARPU = int(total_revenue_march/num_new_customers)
ARPU
```


### COGS (Cost of Goods Sold)

```python
# Calculate infrastructure costs (e.g., hosting, storage, development tools)
introduction_cost = monthly_expenses_march[monthly_expenses_march['item'].isin(['AWS Hosting','Google Cloud Storage','Atlassian Jira'])]['amount'].sum()
# Calculate shared software costs (Slack, Zoom), allocating only 60% to production
software_cost = monthly_expenses_march[monthly_expenses_march['item'].isin(['Slack','Zoom'])]['amount'].sum() * 0.6
# Calculate salary costs for the Engineering department (considered part of production)
production_salary_cost = payroll_march[payroll_march['department'] == 'Engineering']['paid'].sum()
```
```python
# Calculate COGS (Cost of Goods Sold) = Infrastructure + Production-related software + Engineering salaries
COGS = int(introduction_cost + software_cost + production_salary_cost)
COGS
```


### Gross Margin

```python
# Calculate total revenue in March 2023
total_revenue = receipts_history_march['receipt_amount'].sum()  
```
```python
# Calculate Gross Margin = ((Total Revenue - COGS) / Total Revenue) * 100
# This tells us the percentage of revenue that remains after covering direct costs
GrossMargin = int((total_revenue - COGS)/total_revenue * 100)
GrossMargin
```

### LTV (Customer Lifetime Value)

```python
# Calculate customer lifespan in days (churn_date - start_date)
lifespan = (customer_lifespan['churn_date'] - customer_lifespan['start_date']).dt.days
# Calculate average lifespan per day
avg_lifespan_perday = lifespan.mean()
# Convert average lifespan from days to months
avg_lifespan_permonth = avg_lifespan_perday/30
```
```python
# Calculate Customer Lifetime Value (LTV)
# LTV = ARPU * Average Lifespan in Months * Gross Margin %
LTV = int(ARPU * avg_lifespan_permonth * GrossMargin/100)
 LTV
```

### LTV / CAC Ratio

```python
# Finally, calculate the LTV to CAC ratio
LTV_to_CAC = round(LTV / CAC, 2)
LTV_to_CAC
```

### Conclusion

| Index           | Values        |
| --------------- | ------------- |
| CAC             |  12,746       |
| ARPU            |  13,838       |
| COGS            |  20,264       |
| Gross Margin    | 75%           |
| LTV             |  102,138      |
| LTV / CAC Ratio | 8.01          |

- CAC is relatively high, but still very efficient when compared to the LTV. The company is investing significantly in customer acquisition.

- ARPU exceeds CAC: This is a great indicator — it means each customer starts generating profit in the short term.

- COGS is reasonable compared to revenue. After subtracting production costs, the profit margin remains strong.

- Gross Margin at 75% is impressive, showing Streamline Pro’s operations and delivery are highly efficient.

- LTV is very high: 102,138 shows each customer brings significant value throughout their lifecycle.

- LTV/CAC Ratio = 8.01 is excellent.
=> In general, a ratio above 3 is good, and above 5 is considered outstanding. This means your customer acquisition and retention strategies are working extremely well.

### Recommendation

- Scale marketing efforts strategically

=> With such a high LTV/CAC ratio, the company can confidently increase marketing investment, as long as conversion rates remain strong.

=> However, it's important to focus on the most effective acquisition channels to avoid waste.

- Leverage data to improve customer retention

=> Each customer is highly valuable — invest in onboarding, support, and relationship building.

- Consider offering premium features or higher-tier plans

=> This can boost ARPU without significantly increasing fixed costs, thereby raising LTV even further.

- Maintain strong gross margin

=> Keep production costs stable while revenue grows.

=> Explore automation or internal efficiency improvements to keep COGS low.




