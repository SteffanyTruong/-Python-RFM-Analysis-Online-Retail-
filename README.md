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
