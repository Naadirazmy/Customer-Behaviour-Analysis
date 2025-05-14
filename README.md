# Ecommerce-Customer-Behaviour-Analysis

## Table of Content
-
-
-

### Project Overview
---

This data analysis project aims to provide insights into the historical purchase data for a retail store to uncover customer shopping patterns across channels. By analyzing various aspect of the purchase data, we seek to identify high-value customers by conducting RFM Analysis, evaluate product and sales trends, and highlight opportunities to improve subscription management and boost online sales.

### Data Sources 

- Sourced from a real, globally recognized omnichannel retailer (brand name anonymized for confidentiality).
- Contains 500 transaction records spanning 2020 to 2024, covering both in-store and online sales channels.

### Tools
- Excel
- Google Cloud Platform
- SQL
- Looker Studio

### Data Cleaning/Preparation

In the initial data preparation phase, we performed the following tasks:
1. Data loading and inspection
2. Handling duplicates and missing values
3. Grouping certain variables
4. Data cleaning and formatting

### Exploratory Data Analysis
The EDA phase focused on uncovering insights from historical purchase data by addressing key business questions, including:
- Which product is the best-seller?
- Which product has the highest return rate?
- Which product is most frequently associated with discounts?
- Which sales channel performs best?
- What are the RFM (Recency, Frequency, Monetary) value scores for each customer?

### Data Analysis
Key code snippets used in this project:

1. Identifying duplicates

```sql
SELECT StockCode, COUNT(*) AS occurrence_count, 
FROM `assignment-1-final-443605.transaction_data.transaction`
GROUP BY StockCode
HAVING COUNT(*) > 1
ORDER BY occurrence_count DESC;
```

2. Identifying returned items and calculating return rates 
```sql

#identify returned items
CASE WHEN ReturnStatus = 'Returned' THEN Quantity ELSE 0 END) AS total_returned

#return rate
total_returned / total_sold AS return_rate
```

3. Calculate how long it has been since a customer’s most recent purchase based on months (recency score)

```sql
DATE_DIFF(CURRENT_DATE(),MAX(InvoiceDate),MONTH) AS recency_score
```
4. Segment customers into quantiles (1–5) based on their RFM scores and assign meaningful labels to each group.
```sql

#segment different quantiles
NTILE(5) OVER (ORDER BY recency_score DESC) AS recency_quintiles, 
NTILE(5) OVER (ORDER BY frequency_score DESC) AS frequency_quintiles,
NTILE(5) OVER (ORDER BY monetary_score DESC) AS monetary_quintiles

#grouping customers based on quantiles
CONCAT (recency_quintiles,frequency_quintiles, monetary_quintiles) AS RFM_cell,
CASE #segment customers
  WHEN recency_quintiles IN (5) AND frequency_quintiles IN (4,5) AND monetary_quintiles IN (3,4,5) THEN 'VIPs'
  WHEN recency_quintiles IN (3,4) AND frequency_quintiles IN (2,3) AND monetary_quintiles IN (4,5) THEN 'Silent VIPs'
  WHEN recency_quintiles IN (5) AND frequency_quintiles IN (1,2,3) AND monetary_quintiles IN (4,5) THEN 'Promising Newbies'
  WHEN recency_quintiles IN (1,2) AND frequency_quintiles IN (1,2) AND monetary_quintiles IN (3,4,5) THEN 'Drifting Customers' ELSE 'Dormant Customers'
  END AS rfm_group
```
5. Filter and select products sold through the in-store sales channel

```sql
SELECT Description, SUM(Quantity) AS total_sold
FROM `assignment-1-final-443605.transaction_data.transaction`
WHERE SalesChannel='In-store'
GROUP BY Description
ORDER BY total_sold DESC;
```

### Results/Findings
1. **Bulk Buying Behavior**: On average, customers purchase more than 20 items per transaction across all products, indicating a strong bulk-buying trend.
2. **Online Channel Performance**: Online sales significantly outperform in-store sales, generating $334K compared to $269K, highlighting the strength of the digital channel in revenue contribution.
3. **Notebook Discount Sensitivity**: Notebooks receive the highest average transaction-level discount at 30%, yet remain one of the lowest-selling products. This suggests there is interest in the product, but the price may be a barrier to purchase.
4. **High Returns on Top-Selling Online Items**: Wall clocks and office chairs are the top-selling products online but show an elevated return rate of 22%, suggesting a possible mismatch between customer expectations and product experience. This underlines the need for improved product detail, images, or quality assurance in the online store.
5. **Customer Retention Challenge**: RFM analysis reveals that 78% of customers are dormant and 10% are drifting, indicating a critical need for re-engagement strategies, such as personalized offers, win-back campaigns, or loyalty programs.

## Recommendations
- Reactivation and Retention Campaigns for Inactive and Dormant Customers
- Optimize Online Sales Channel with Targeted Promotions for High Value Customers
- Enhance the online sales channel by improving user experience and providing clearer, more detailed product information to ensure customer expectations align with the actual product upon delivery

## Limitations
- Duplicate Stock Codes: Repeated product codes create ambiguity when analyzing specific products, affecting the accuracy of calculations related to sales performance and customer interest.
- Duplicate User IDs: The presence of duplicate customer identifiers compromises the reliability of customer-level analysis, such as RFM scoring and purchase behavior tracking.

## Reference
1. [Business Report](https://drive.google.com/file/d/10lH3q07kpEWSdL3VvsxR29en_GOl2Qqj/view?usp=drive_link)
2. [SQL Query](https://docs.google.com/document/d/1fLBpubdZlqBzh4BLcnPkSoa8u3Da7yLr/edit?usp=drive_link&ouid=105505531963600591665&rtpof=true&sd=true)

## Visualisation












