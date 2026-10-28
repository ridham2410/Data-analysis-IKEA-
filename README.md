# IKEA-Customer-Analytics-Dashboard

### Dashboard Link : _add your published Power BI Service link here_

> **Disclaimer :** "IKEA Gifting" is a fictional company name used for this educational project. All figures come from a public, anonymized dataset (Online Retail II, UCI Machine Learning Repository) and do not represent any real company.

## Problem Statement

This dashboard helps a UK based online gifting and home decor retailer understand the value of its customers. It shows who the most valuable customers are, which customers have stopped buying, and how customer value and churn change by month, quarter, year, country and customer segment.

Using RFM analysis (Recency, Frequency and Monetary value), every customer is placed in one of six segments, so that marketing and retention teams can spend their effort on the customers who matter most instead of treating everyone the same.

Since **Champions are only about 22 % of customers but generate about 68 % of the revenue**, the business depends heavily on a small group of customers, and these customers must be protected.

Also since **50.86 % of all customers have not bought in the last 90 days**, churn is a major risk, and the "At Risk" segment (churned customers who were once valuable) is the first group to win back.

### Steps followed

- Step 1 : Collect the data. The dataset is Online Retail II (December 2009 to December 2011), which has 1,067,371 raw transaction rows across two sheets (2009-2010 and 2010-2011).
- Step 2 : Clean the data in SQL (data types, mixed date formats, duplicate rows and rows without a customer ID) and combine both sheets into one table.
- Step 3 : Calculate the RFM values for every customer in Python.

  (a) Recency : number of days since the customer's last purchase

  (b) Frequency : number of purchases made by the customer

  (c) Monetary : total revenue from the customer

- Step 4 : Score each customer from 1 to 5 on each of R, F and M using quantiles (a higher score is better, and a recent purchase gets a higher recency score).

        rfm['R_Score'] = pd.qcut(rfm['Recency'], 5, labels=[5, 4, 3, 2, 1]).astype(int)
        rfm['F_Score'] = pd.qcut(rfm['Frequency'].rank(method='first'), 5, labels=[1, 2, 3, 4, 5]).astype(int)
        rfm['M_Score'] = pd.qcut(rfm['Monetary'], 5, labels=[1, 2, 3, 4, 5]).astype(int)

- Step 5 : Assign each customer to one of six segments based on the scores. In the final data the scores fall in the following ranges.

  | Segment | R score | F score | Meaning |
  |---|---|---|---|
  | Champions | 4 to 5 | 4 to 5 | bought recently, often and spend the most |
  | Loyal | 3 to 5 | 3 to 5 | regular buyers with good value |
  | At Risk | 1 to 2 | 3 to 5 | used to buy often, but have gone quiet |
  | New | 4 to 5 | 1 to 2 | recent first time buyers |
  | Needs Attention | 3 | 1 to 2 | average recency but low frequency |
  | Lost | 1 to 2 | 1 to 2 | no meaningful activity for a long time |

- Step 6 : Create a churn flag, `IsChurned`. A customer is marked as churned if there is no purchase in the last 90 days of the data (Recency of 91 days or more).
- Step 7 : Load the final table (`Sheet1`) into Power BI Desktop. It has 779,495 transaction rows and 19 columns : `Invoice`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `Price`, `Customer_ID`, `Country`, `SourceSheet`, `InvoiceMonth`, `Revenue`, `Recency`, `Frequency`, `Monetary`, `R_Score`, `F_Score`, `M_Score`, `Segment` and `IsChurned`.
- Step 8 : Date hierarchy (Year, Quarter, Month, Day) was used on `InvoiceDate`.
- Step 9 : A custom theme (**Scandi Blue Yellow**, blue and yellow colours) was imported from the View tab, and the IKEA logo and a page background image were added.
- Step 10 : Four DAX measures were created.

        Total Revenue = SUM('Sheet1'[Revenue])

        Total Customers = DISTINCTCOUNT('Sheet1'[Customer_ID])

        Churned % =
        DIVIDE(
            CALCULATE(DISTINCTCOUNT('Sheet1'[Customer_ID]), 'Sheet1'[IsChurned] = TRUE()),
            DISTINCTCOUNT('Sheet1'[Customer_ID]))

        Avg Customer Value = DIVIDE([Total Revenue], [Total Customers])

- Step 11 : **Page 1 (Business Overview)** was built with,

  (a) Four card visuals : Total Customers, Total Revenue, Churned % and Avg Customer Value

  (b) Slicers for Month, Quarter, Year, Country, Segment and Is Churned

  (c) Line charts : Total Customers by month, quarter and year

  (d) Column chart and pie chart : Total Customers by segment

  (e) Line charts : Total Revenue by month, quarter and year

  (f) Column chart and pie chart : Total Revenue by segment

  (g) Bookmarks and buttons for switching between the views and moving to the other page

- Step 12 : **Page 2 (RFM Analysis)** was built with,

  (a) The same six slicers

  (b) Table : Segment performance summary (customers, revenue and R, F and M scores for each segment)

  (c) Column chart : Avg Customer Value by segment

  (d) Column chart : Avg Customer Value by country

  (e) Table : the same summary for each country

  (f) Line charts : Churned % with Total Revenue by month and by quarter, and Churned % by year

  (g) Customer line charts and segment charts, so that every slicer filters the whole page

- Step 13 : The report was then published to Power BI Service.

# Snapshot of Dashboard (Power BI Service)

<img width="1917" height="1015" alt="Image" src="https://github.com/user-attachments/assets/ef892a79-f6f2-45b8-98dc-302becd7ec21" />

# Report Snapshot (Power BI DESKTOP)

<img width="1917" height="1015" alt="Image" src="https://github.com/user-attachments/assets/a317ea0b-af5d-409b-94b8-c7f898184751" />

<img width="1917" height="1015" alt="Image" src="https://github.com/user-attachments/assets/5cb34202-cbc4-46ac-b95a-4768bf7d31fd" />

# Insights

A two page report was created on Power BI Desktop & it was then published to Power BI Service.

Following inferences can be drawn from the dashboard. All figures are calculated from the data in the report (01 Dec 2009 to 09 Dec 2011).

### [1] Headline Numbers

   Total customers = 5,881

   Total revenue = 17,374,804 (about 17.4 M)

   Average customer value = 2,954

   Churned customers = 2,991 (50.86 %)

   Number of countries = 41

           thus, more than half of the customers have stopped buying. The median customer spent only 866, so a few big customers pull the average up (the top 20 % of customers give about 77 % of revenue).

### [2] Customer Segments

   | Segment | Customers | % of customers | Revenue | % of revenue | Avg customer value | Churned |
   |---|---|---|---|---|---|---|
   | Champions | 1,289 | 21.92 % | 11,830,781 | 68.09 % | 9,178 | 0 % |
   | Loyal | 1,416 | 24.08 % | 2,705,479 | 15.57 % | 1,911 | 31.36 % |
   | At Risk | 823 | 13.99 % | 1,588,014 | 9.14 % | 1,930 | 100 % |
   | Lost | 1,529 | 26.00 % | 655,951 | 3.78 % | 429 | 100 % |
   | New | 442 | 7.52 % | 392,022 | 2.26 % | 887 | 0 % |
   | Needs Attention | 382 | 6.50 % | 202,557 | 1.17 % | 530 | 51.05 % |

           thus, maximum revenue comes from Champions, whereas Lost customers are the biggest group by number (26 %) but give less than 4 % of the revenue.

### [3] Churn

   Churned customers by segment : At Risk = 823, Lost = 1,529, Loyal = 444 and Needs Attention = 195 (2,991 in total).

   Champions and New customers have no churn, as they all bought in the last 90 days.

           thus, At Risk customers are the most important group to win back : their average value (1,930) is almost the same as Loyal customers and they bring in 1.59 M in revenue.

### [4] Revenue and Customers over Time

   | Year | Revenue | Customers |
   |---|---|---|
   | 2009 (December only) | 683,504 | 955 |
   | 2010 | 8,374,496 | 4,233 |
   | 2011 (till 9 December) | 8,316,804 | 4,220 |

   | Quarter | Revenue | Customers |
   |---|---|---|
   | Q1 | 3,365,608 | 2,747 |
   | Q2 | 3,631,962 | 3,039 |
   | Q3 | 4,214,679 | 3,160 |
   | Q4 | 6,162,555 | 4,101 |

   Highest month = November (2,322,666)

   Second highest month = October (2,068,754)

   Lowest month = February (950,644)

           thus, revenue and customers are the highest in Q4, which is expected for a gifting business, and revenue in 2010 and 2011 is almost the same.

### [5] Countries

   Revenue from the United Kingdom = 14,389,235 (82.82 %), from 5,353 customers (91 % of customers)

   Next countries by revenue : EIRE (3.55 %), Netherlands (3.19 %), Germany (2.45 %) and France (2.01 %)

   Average customer value is very high in a few countries with very few customers :

   | Country | Customers | Avg customer value |
   |---|---|---|
   | EIRE | 5 | 123,314 |
   | Singapore | 1 | 25,317 |
   | Netherlands | 22 | 25,184 |
   | Australia | 15 | 11,286 |

           thus, the business depends on the UK market, and the high value overseas customers look like wholesale buyers.

### [6] Suggested Actions by Segment

   | Segment | Suggested action |
   |---|---|
   | Champions | Protect them with early access, loyalty rewards and personal service |
   | Loyal | Encourage more frequent and larger orders, and watch the 31 % who have churned |
   | At Risk | Run win back campaigns first, as they are the highest value churned group |
   | New | Send a follow up offer so that they make a second purchase |
   | Needs Attention | Use low cost reminders and seasonal offers to raise purchase frequency |
   | Lost | Low priority, use only low cost email |

### [7] Things to Keep in Mind

1. **Churned % over time is not a period churn rate.** `IsChurned` is a fixed flag for each customer, measured at the end of the data (9 Dec 2011). The Churned % by month, quarter or year shows what share of the customers who bought in that period are churned today, so recent periods will always look better.
2. **Average R, F and M scores in the summary table are averaged over transaction rows, not over customers**, so customers with many transactions count more. For example, Champions show 4.70, 4.88 and 4.84, whereas the per customer averages are 4.57, 4.69 and 4.65. Averaging the score once per customer fixes this.
3. **Wholesale and retail customers are not separated**, so countries with very few customers (EIRE, Singapore) show a very high average customer value.
4. **2009 and 2011 are partial years** (2009 has only December and 2011 ends on 9 December), so year to year comparisons should be read with care.
