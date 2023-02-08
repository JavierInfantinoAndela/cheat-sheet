Here is a complete documentation on processing the last 15 minutes of sales data from a Redshift table and loading the processed data into another Redshift table using PySpark and SparkSQL:

Requirements
A Redshift cluster with the sales data table
PySpark and SparkSQL installed on a machine that has access to the Redshift cluster
The Redshift JDBC driver installed
A Redshift table for storing the processed data
Preparation
Create the Redshift table for storing the processed data. Here's an example of the SQL command to create the table:
sql
Copy code
CREATE TABLE processed_sales (
    timestamp timestamp,
    product varchar(255),
    total_sales double precision
);
Load the sales data into the Redshift table.
Code
Here's the code for processing the last 15 minutes of sales data from the Redshift table using PySpark and SparkSQL:

python
Copy code
# Import the necessary modules
from pyspark.sql import SparkSession
from pyspark.sql.functions import from_unixtime, unix_timestamp

# Create a SparkSession
spark = SparkSession.builder.appName("ProcessSalesData").getOrCreate()

# Read the sales data from Redshift into a PySpark DataFrame
df = spark.read \
    .format("jdbc") \
    .option("url", "jdbc:redshift://<redshift_cluster_endpoint>:5439/<database_name>") \
    .option("dbtable", "sales") \
    .option("user", "<redshift_user>") \
    .option("password", "<redshift_password>") \
    .load()

# Filter the data for the last 15 minutes
df = df.filter(df.timestamp >= from_unixtime(unix_timestamp() - 15 * 60))

# Use SparkSQL to aggregate the data
aggregated_df = df.createOrReplaceTempView("sales")
aggregated_df = spark.sql("""
    SELECT 
        timestamp, 
        product, 
        SUM(sales) as total_sales 
    FROM sales 
    GROUP BY 
        timestamp, 
        product
""")

# Write the processed data back to Redshift
aggregated_df.write \
    .format("jdbc") \
    .option("url", "jdbc:redshift://<redshift_cluster_endpoint>:5439/<database_name>") \
    .option("dbtable", "processed_sales") \
    .option("user", "<redshift_user>") \
    .option("password", "<redshift_password>") \
    .mode("overwrite") \
    .save()

# Stop the SparkSession
spark.stop()
Explanation
The necessary modules are imported.
A SparkSession is created.
The sales data is read from Redshift into a PySpark DataFrame. The JDBC URL, Redshift user, and password are specified in the option function.
The data is filtered for the last 15 minutes using the from_unixtime and unix_timestamp functions.
SparkSQL is used to aggregate the
