# -Python-RFM-Analysis-Online-Retail-
# I. Introduction
This python notebook explains how to perform RFM analysis from sales data. RFM is a method used for analyzing customer value. It is commonly used in database marketing and direct marketing and has received particular attention in retail and professional services industries.

RFM stands for the three dimensions:

- **R**ecency – Days since last purchase
- **F**requency – Total number of purchases
- **M**onetary Value – How much do a customer spend?

Customer purchases may be represented by a table with columns for the customer name, date of purchase and purchase value. In this company, RFM is to assign a score for each dimension on a scale from 1 to 5. The maximum score represents the preferred behavior and a formula could be used to calculate the three scores for each customer. 
Once each of the attributes has appropriate categories defined, segments are created from the intersection of the values.One well-known commercial approach uses five bins per attributes, which yields 125 segments. Companies may also decide to collapse certain subsegments, if the gradations appear too small to be useful. The resulting segments can be ordered from most valuable (lowest recency, highest frequency, and highest value) to least valuable (highest recency, lowest frequency, and lowest value). 

# II. Data Visualize with Python
## Distribution of recency, frecency, monetary
<img width="1048" alt="Screenshot 2024-06-14 at 15 22 02" src="https://github.com/SteffanyTruong/-Python-RFM-Analysis-Online-Retail-/assets/171953683/8e796070-3dfe-49d3-9f74-8d4496412b55">

<img width="1060" alt="Screenshot 2024-06-14 at 16 06 56" src="https://github.com/SteffanyTruong/-Python-RFM-Analysis-Online-Retail-/assets/171953683/91450c50-f040-4e35-adda-87df92c090b4">
## Number of Customer in each segment
<img width="410" alt="Screenshot 2024-07-29 at 15 19 18" src="https://github.com/user-attachments/assets/a365acb6-d95a-41a7-b36e-9ad4217b3885">  

## Average days since last orders by customer segment  
<img width="752" alt="Screenshot 2024-06-14 at 15 11 01" src="https://github.com/SteffanyTruong/-Python-RFM-Analysis-Online-Retail-/assets/171953683/1b644d2c-0860-4521-a6c1-7f4d92cc3ede">

## Number of customer by number of orders   
<img width="658" alt="Screenshot 2024-07-29 at 15 21 18" src="https://github.com/user-attachments/assets/db536fb8-48f1-4b12-adb7-ed1643cbe9d1">

## Treemap of customer segmentation
<img width="1234" alt="Screenshot 2024-06-14 at 16 09 18" src="https://github.com/SteffanyTruong/-Python-RFM-Analysis-Online-Retail-/assets/171953683/ccaa99d1-918b-4138-9b79-72e37e578e3b">

# III. Insights
1.The most important index of the 3 indicators that the SuperStore company needs to pay attention to is F, then R: because the rate of customers buying once and twice is very high. Very few customers make long-term purchases like 8-9 times or more. -> That shows that the customer retention rate at the company is still low or we need to dig in the categories analysis to figure out the deep problem.

2.The 2 big groups are champions (19.2% ) and hibernating customer (16% ), following by lost customer ( 11.2% ),loyal ( 9.93% ), at risk (9.7% ) and potential loyal ( 9.52% ). These groups are really need attentions. 
# IV. Recommendations
<img width="1104" alt="Screenshot 2024-07-29 at 14 32 39" src="https://github.com/user-attachments/assets/e6c5f8d2-c76f-47ff-94f0-4b070aa451af">

