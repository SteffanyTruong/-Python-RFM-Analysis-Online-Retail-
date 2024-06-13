# -Python-RFM-Analysis-Online-Retail-
# I. Introduction
This python notebook explains how to perform RFM analysis from sales data. RFM is a method used for analyzing customer value. It is commonly used in database marketing and direct marketing and has received particular attention in retail and professional services industries.

RFM stands for the three dimensions:

- **R**ecency – Days since last purchase
- **F**requency – Total number of purchases
- **M**onetary Value – How much do a customer spend?

Customer purchases may be represented by a table with columns for the customer name, date of purchase and purchase value. In this company, RFM is to assign a score for each dimension on a scale from 1 to 5. The maximum score represents the preferred behavior and a formula could be used to calculate the three scores for each customer. 
Once each of the attributes has appropriate categories defined, segments are created from the intersection of the values.One well-known commercial approach uses five bins per attributes, which yields 125 segments. Companies may also decide to collapse certain subsegments, if the gradations appear too small to be useful. The resulting segments can be ordered from most valuable (lowest recency, highest frequency, and highest value) to least valuable (highest recency, lowest frequency, and lowest value). 

# II. RFM Analysis
`import pandas as pd  import numpy as np`  
`from datetime import datetime as dt`  
`import matplotlib.pyplot as plt`  
`import seaborn as sns`  
`import warnings`  
`warnings.filterwarnings ('ignore')`  
`import plotly.express as px` 
## 1. Data wrangling
*# Load the dataset and parse to DataFrame*    
`df=pd.ExcelFile("/content/drive/MyDrive/ecommerce retail.xlsx")`  
`Sales= df.parse('ecommerce retail')`  
`Segment= df.parse('Segmentation')`  
`Sales.info()`  
`Segment.info()`

*#Remove missing value*  
`Sales.dropna(inplace = True)`  
`print("Shape of the `ecommerce data`: {}".format(Sales.shape))`  

*# Remove negative values ( return order )*  
`Sales["InvoiceNo"] = Sales["InvoiceNo"].astype("str")`  
`Sales = Sales[~Sales["InvoiceNo"].str.contains("C", na = False)]`  

*# Convert to datetime.datetime*
`dt_datetime = pd.to_datetime(Sales['InvoiceDate'])`

## 2. RFM Analysis
### 2.1 Recency
`print("Sales: Min Date", Sales["InvoiceDate"].min(), "Max Date", Sales["InvoiceDate"].max())`  
*# Calculate recency*  
`recency = (dt(2011, 12, 9) - Sales.groupby("CustomerID").agg({"InvoiceDate":"max"})).rename(columns = {"InvoiceDate":"Recency"})`

*# Convert recency to days*  
`recency["Recency"] = recency["Recency"].apply(lambda x: x.days)`  

`recency.head()`

### 2.2 Frecency
*# Calculate frequency*
`freq = Sales.groupby("CustomerID").agg({"InvoiceDate":"nunique"}).rename(columns={"InvoiceDate": "Frequency"})`

`freg`

### 2.3 Monetary
*# Calculate monetary*  
`Sales["TotalPrice"] = Sales["Quantity"] * Sales["UnitPrice"]`  
`monetary = Sales.groupby("CustomerID").agg({"TotalPrice":"sum"}).rename(columns={"TotalPrice":"Monetary"})`  

`monetary.head()`

*# Create rfm table by concat three tables*  
`rfm = pd.concat([recency, freq, monetary],  axis=1).reset_index()`

`rfm.head()`

### 2.4 Assigning RFM score
*# Determining rfm quartile ( use 5 bins )*  
`rfm["RecencyScore"] = pd.qcut(rfm["Recency"], 5, labels = [5, 4 , 3, 2, 1])`  
`rfm["FrequencyScore"]= pd.qcut(rfm["Frequency"].rank(method="first"),5, labels=[1,2,3,4,5])`  
`rfm["MonetaryScore"] = pd.qcut(rfm['Monetary'], 5, labels = [1, 2, 3, 4, 5])`

`rfm.head()`

*# RFM Scores: Category*  
`rfm["RFM Score"] = (rfm['RecencyScore'].astype(str) +
                     rfm['FrequencyScore'].astype(str) +
                     rfm['MonetaryScore'].astype(str))`
                     
*# Convert each value in the 'RFM Score' column to an integer*  
`rfm['RFM Score'] = rfm['RFM Score'].astype(int)`  

`rfm.info()`

*# Convert comma-separated string to a list of rfm scores*  
`Segment['RFM Score'] = Segment['RFM Score'].str.split(',')`

*# Tranform each element of the list to a row*
`Segment = Segment.explode('RFM Score').reset_index(drop=True)`

*# Convert each value in the 'RFM Score' column to an integer*
`Segment['RFM Score'] = Segment['RFM Score'].astype(int)  # Assuming 'RFM Score' contains integer-like strings`

### 2.5 Assigning customer segment
*# Merge rfm table with segment table*  
`rfm_segment = pd.merge(rfm,Segment, on='RFM Score',how='left')`  
`print(rfm_segment.head())`  
`rfm_segment.info()`
## 3. Summary Statistic
**How many customer in each segment?**  
`Stats = rfm_segment[["Segment", "CustomerID"]].groupby("Segment").nunique().rename(columns={"CustomerID": "Cust_Count"}).reset_index()`  
`Stats["Count_Share"] = Stats["Cust_Count"] / Stats["Cust_Count"].sum() * 100.00`  
`Stats = Stats.round({"Count_Share": 2})`  
`Stats`

**Calculate average recency ( days ) by segment customer** 
`rfmStats = rfm_segment.groupby("Segment")['Recency'].mean().reset_index()`   
`rfmStats = rfmStats.rename(columns = {'Recency':'Recency_mean'})` *# Rename the column after converting to DataFrame*  
`rfmStats = rfmStats.round({'Recency_mean':0})`  
`rfmStats`  
# III. Visualize
## Distribution of recency, frecency, monetary
*# Show distribution of each variable of the model*  
`colnames=['Recency','Frequency','Monetary']`      
`for col in colnames:`      
    `fig, ax = plt.subplots(figsize=(12,3))`    
    `sns.distplot(rfm_segment[col])`      
    `ax.set_title('Distribution of %s' %col)`      
  `plt.show()`  
## Average days since last orders by customer segment  
*# Seaborn barplot of average recency by customer segment*  
`sns.barplot(data=rfmStats,
            y='Segment',
            x='Recency_mean',
            palette='husl')`  
`plt.title('Average Recency (day)')`  
`plt.show()`
## Number of customer by number of orders   
*# Seaborn Countplot of Frequency*  
`sns.countplot(x=rfm_segment['Frequency'])`  
`plt.title('Number of customer by number of order')`  
`plt.ylabel('Number of customer')`  
`plt.show()`
## Build a treemap of customer segmentation
`treemap_data= rfm_segment.groupby("Segment").agg(Segment_count=("Segment", "count")).reset_index()`  
`fig = px.treemap(treemap_data, path=['Segment'], values='Segment_count', title='Treemap of customer segmentation')`  
`fig.show()`
# IV. Insights
1.The most important index of the 3 indicators that the SuperStore company needs to pay attention to is F, then R: because the rate of customers buying once and twice is very high. Very few customers make long-term purchases like 8-9 times or more. -> That shows that the customer retention rate at the company is still low or we need to dig in the categories analysis to figure out the deep problem.

2. The 2 big groups are champions (19.2% ) and hibernating customer (16% ), following by lost customer ( 11.2% ),loyal ( 9.93% ), at risk (9.7% ) and potential loyal ( 9.52% ). These groups are really need attentions. 
# IV. Recommendations

|Segment | Charateristics| Recommendation |
| :—– | :———- | :————– |
| Champions |	Bought most recently and most often, and spend the most| Reward them. They can be early adopters to new products. Suggest them "Refer a friend". No need price incentives|
| Loyal	| Buy most frequently and recently | Offer new product, early bird advantage to encourage them buy more often, cricticaly send the email of new produtcs |
| Potential Loyalist |	Bought recently and frequently, not spend much	| Offer them bigger set value to increase the amount they spend |
| New Customers	|Bought most recent |	Introduce loyal program |
| Promising |	Bought recenltly, big spenders, not buying frequently |	Agressively market the most expensive products, no need price incentives |
| Need Attention |	Buy recently and frequently, spend pretty big amount | Market the most expensive products |
| About To Sleep |	Haven't purchased lately, not spend much	|
| At Risk	Haven’t purchased for some time, but purchased frequently and spend the most|	Aggressive price incentives|
| Cannot Lose Them	| Haven’t purchased for some time, but purchased frequently and spend the most |	Aggressive price incentives |
|Hibernating customers	|They score 1-2 for recency, frequency, and monetary value. They made a purchase or two a long time ago and never returned.|	Don’t spend too much trying to re-acquire|
|Lost customers	|Last purchased long ago, purchased few, and spent little	|Don’t spend too much trying to re-acquire|
