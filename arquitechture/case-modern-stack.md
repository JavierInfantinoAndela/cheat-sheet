## Data Engineering Case Study

### Requirements

We are a large retail business with thousands of stores worldwide. We need to analyze our sales data to optimize inventory management and sales strategy. We have data for each store's sales, inventory, and customer behavior, and we need to integrate this data and perform analytics on it.

### First Answer

We recommend using the Modern Data Stack with the following components:

- Data Integration: AWS Glue
- Data Warehousing: Amazon Redshift
- Data Visualization and Analytics: Tableau

Why use: Provides a cloud-based, serverless, and scalable data stack that enables easy and fast data integration, warehousing, and analytics.

How it works:
- Data from each store is collected and stored in S3 using AWS Glue for ETL processing and integration
- Data is then moved to Redshift, where it is stored in a columnar format that is optimized for analytics queries
- Tableau is used to visualize the data and perform analytics

