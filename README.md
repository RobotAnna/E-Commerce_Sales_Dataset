# Analysis
This is a demo project: from dataset to visualization, using DBT to create pipelines and a Postgres database as a data wareehouse.
The source dataset is the "E-Commerce Sales Dataset" from Kaggle.
https://www.kaggle.com/datasets/thedevastator/unlock-profits-with-e-commerce-sales-data/ 
There are 35 Kaggle projects where people clean this dataset using Python. I couldn't find any projects where the data was modelled &/or transformed using DBT.


## Environment Set-Up
Github - create an account and create new repository
VS Code - install locally
Python - install locally
DBT Core - install locally
Postgres - install locally, and using the PGAdmin tool, create a target database and schemas
Power BI Desktop - install locally


## Exploration
Explore the dataset

### Visual check, in Excel
This is a set of clothing orders from Amazon India.
Look for discrepancies and nulls, figure out what the data means, and brainstorm potential insights from the data.
<img src="/assets/Excel_visual_check.png" alt="Visual check in Excel"/>

### Power BI column preview
A quick check in Power BI Desktop (Column Preview) shows the data quality and distributions per column -- to define which cleaning is necessary. This step could also be done in Python..
<img src="/assets/PBI_Column_Preview.png" alt="Column check in PBI"/>

### stakeholder discussion
At this stage, I would contact business stakeholders to confirm definitions, and application managers to confirm discrepancies in the data.

Definitions
* SKU = Stock Keeping Unit. A number (usually eight alphanumeric digits) that retailers assign to products to keep track of stock levels.
* ASIN = Amazon Standard Identification Number. A 10-character alphanumeric unique identifier assigned by Amazon.com and its partners for product identification.
Several SKU's may belong to one ASIN; one ASIN has multiple offers.

Questions
* Courier status -- Why is Courier status sometimes empty?
* Courier status -- Why is there a mismatch between Courier status and Order status? (eg. Courier Status = Unshipped, Order Status = Shipped)
* Qty -- Why is Quantity sometimes 0? Does this represent an errorred order, or a returned order?
* Amount -- Are shipping costs included in this dataset, or does Amount only account for product retail price?


### Plan the data cleaning

Date
* Rename to Order Date
* Normalize date formats (2 formats)
Status
* Rename to Order status
* split into 2 columns: Status + sub-status
SKU
* derive feature Product Color
Courier Status
* If empty, then derive from Order Status
Currency
* If null: populate with INR. Assume only local orders.
Amount
* If null: populate with 0
Ship-City
* Convert to upper case
* Normalize some values with external list
Ship-State
* Convert to upper case
* Normalize with external list
Unnamed
* Remove column


### Brainstorm: Potential insights from this dataset
* Sales totals, slice by time (month), dice by sales channel, promotion, fulfilment method, shipment location.
* Are overall sales increasing/decreasing, per segment?
* Best performing product, style, size, color for a given target location, &/or for a given date range
* Average price of products
* Seasonality of products, eg. which product is sold in which month
* Best day/week/month for sales, for a given product/style/size/location
* Proportion of single-product orders to multiple-product orders
* Which product category is most ordered by businesses (B2B)? What's the average value (Amount) of these products?
* Which product is most often sold together, eg. set + kurta, top + set
* How frequently do orders get cancelled? % of orders per month. Dice by sales channel, shipment location, product, promotion
* Does the free shipping promotion increase orders?
* Proportion of rejected / non-rejected orders, and which factors may cause a rejected order. (Date, sales channel, promotion, fulfilment method, shipment location.)



## Design
This project will showcase implementation of basic DBT elements. The warehouse design is simplified so we can focus on DBT.
<img src="/assets/Design.This_demo.png" alt="Simplified warehouse design"/>
<!-- style="height: 100px; width:100px;"/>

1. STAGED
An ingestion framework already exists. The delivered data would already be loaded in the Staging area (partitioned by day) in a parquet file. Postgres accesses the staged data via a foreign table.
2. CLEANED
Cleaning (normalization, flitering, cleaning) is done. Features may be derived. Columns may be renamed. Data is kept in its original format. Data sources are kept separated.
3. TRANSFORMED
Data is transformed into facts and dimensions. Data sources are combined. Calculations &/or aggregations are created.
PURPOSELY OMITTED, to minimize complexity:
Historical records and slowly changing dimensions.
In a real warehouse each table has a load_date column. Typically the intermediate layer contains multiple records per item, with different load_dates -- the historical records. And unless there is a specific requirement to track the changes of an entity over time, the data mart / presentation layer contains only the current record for each entity.


A typical complete data warehouse would have more sections. Historical changes are handled, hashes may be included as indexes. Calculations and aggregations are done in separate layers.
<img src="/assets/Design.Typical_warehouse.png" alt="Typical full warehouse"/>

Typical naming convention for staged area:
* src/sal/year/month/day/amazon_orders.parquet
* src/ref/year/month/day/india_states.parquet
Parquet files, partitioned by year/month/day.

Cleaning area typically has data sources still separated (eg. by database schema).
Modelling area would have transformed data and calculations.
Presentation area would have aggregations and derived KPIs.


## Data model
To make a data model, first separate the data into logical entities.
<img src="/assets/separate_logical_entities.png" alt="Separate the logical entities"/>

Then draw relationships between the entities.
<img src="/assets/data_model.png" alt="Form relationships between the entites"/>
D_ORDER_ITEM is separated from F_ORDER, because there could be multiple items on one order.


## Test requirements
Xxxx.


## Implementation
Xxxx.


## Visualization
Xxxx.

