
# Data Pipeline from Kafka to Redshift and then to S3

## Overview

In this data pipeline, data is read from a Kafka topic, processed and stored into a Redshift schema and then finally into an S3 bucket.

## Tools and Technologies

- Apache Kafka for data ingestion
- Apache Spark for data processing
- Redshift for data storage
- Amazon S3 for data storage and retrieval
- Apache Airflow for batch job orchestration

## Steps

1. Ingest data from a Kafka topic into Spark
    - Use the Kafka Connect API or the Kafka Consumer API to read data from the Kafka topic
    - Use Spark Structured Streaming to process the data

2. Process data in Spark and store it into a Redshift schema
    - Use Spark SQL to process the data and write it into a Redshift schema
    - Use the Spark-Redshift library to write the data into Redshift

3. Schedule and orchestrate batch jobs using Apache Airflow
    - Use Airflow to schedule the Spark job that processes the data and stores it into Redshift
    - Create an Airflow DAG to define the tasks and their dependencies, and set the schedule interval (e.g., every 15 minutes)

4. Sink data from Redshift into S3
    - Use a Python script that uses the `psycopg2` and `boto3` libraries to read the data from Redshift and write it into an S3 bucket
    - Alternatively Spark can be used in batch mode to read from redshift and write to S3
    - Schedule the Python script using Airflow to run it as a separate task in the DAG



## Tips for the Interview

- Familiarize yourself with the tools and technologies used in the pipeline (Kafka, Spark, Redshift, S3, and Airflow)
- Know how to ingest data from a Kafka topic into Spark
- Know how to process data in Spark and store it into a Redshift schema
- Know how to schedule and orchestrate batch jobs using Apache Airflow
- Know how to sink data from Redshift into S3
- Be familiar with running SQL and Python jobs on Airflow
- Be able to explain the pipeline and the different steps involved in it.


# Reading Data from Kafka and Sinking into Redshift with PySpark

This code demonstrates how to read data from a Kafka topic and sink the data into Redshift using PySpark, while also flattening a JSON structure in the process. The code is written in Python and uses the PySpark library to perform the data processing and write the data to Redshift.

The code performs the following steps:

1. Initialize a Spark Session using `SparkSession.builder.appName("KafkaToRedshift").getOrCreate()`.
2. Define the schema for the incoming JSON data using the `StructType` and `StructField` classes from the PySpark `sql.types` module.
3. Read data from the specified Kafka topic using the `readStream` method of the Spark DataFrame, setting the format to `"kafka"`, and specifying the Kafka brokers and topic name in the options.
4. Extract the value from the Kafka message and parse it as a JSON using the `selectExpr` and `from_json` methods.
5. Flatten the JSON data into individual columns using the `explode` method.
6. Write the data to Redshift using the `writeStream` method, setting the format to `"jdbc"` and specifying the connection parameters, including the Redshift cluster endpoint, database name, table name, username, and password.
7. Start the Spark Stream using the `awaitAnyTermination` method.

Here is the full code:

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import from_json, explode
from pyspark.sql.types import StructType, StructField, StringType

# Initialize Spark Session
spark = SparkSession.builder.appName("KafkaToRedshift").getOrCreate()

# Define the schema for the incoming JSON data
json_schema = StructType([
    StructField("field1", StringType()),
    StructField("field2", StringType()),
    StructField("field3", StringType())
])

# Read data from Kafka
df = spark \
  .readStream \
  .format("kafka") \
  .option("kafka.bootstrap.servers", "kafka-broker-1:9092,kafka-broker-2:9092") \
  .option("subscribe", "topic_name") \
  .load()

# Extract the value from the Kafka message and parse it as a JSON
df = df.selectExpr("cast (value as string) as json_data") \
  .select(from_json("json_data", json_schema).alias("data"))

# Flatten the JSON data into individual columns
df = df.select("data.*") \
  .select(explode("field1").alias("field1_value"), "field2", "field3")

# Write the data to Redshift
df.writeStream \
  .format("jdbc") \
  .option("url", "jdbc:redshift://redshift-cluster-endpoint:5439/dbname") \
  .option("dbtable", "table_name") \
  .option("user", "username") \
  .option("password", "password") \
  .option("checkpointLocation", "/tmp/
```


# PySpark Batch Job for Reading from Redshift and Sinking into Another Redshift Schema

This code demonstrates how to perform a batch data processing job using PySpark to read data from one Redshift schema and sink the processed data into another Redshift schema. The code is written in Python and uses the PySpark library to perform the data processing and write the data to Redshift.

The code performs the following steps:

1. Initialize a Spark Session using `SparkSession.builder.appName("RedshiftToRedshift").getOrCreate()`.
2. Read data from the source Redshift schema using the `read` method of the Spark DataFrame and specifying the JDBC connection parameters, including the Redshift cluster endpoint, database name, source table name, username, and password.
3. Perform data processing and transformations on the Spark DataFrame as needed.
4. Write the processed data to the target Redshift schema using the `write` method and specifying the JDBC connection parameters, including the Redshift cluster endpoint, database name, target table name, username, and password, as well as the write mode (e.g. `mode="overwrite"`).
5. Stop the Spark Session using the `stop` method.

Here is the full code:

```python
from pyspark.sql import SparkSession

# Initialize Spark Session
spark = SparkSession.builder.appName("RedshiftToRedshift").getOrCreate()

# Read data from source Redshift schema
df = spark.read \
  .format("jdbc") \
  .option("url", "jdbc:redshift://redshift-cluster-endpoint:5439/dbname") \
  .option("dbtable", "source_table_name") \
  .option("user", "username") \
  .option("password", "password") \
  .load()

# Perform data processing and transformations on the Spark DataFrame
# ...

# Write the processed data to target Redshift schema
df.write \
  .format("jdbc") \
  .option("url", "jdbc:redshift://redshift-cluster-endpoint:5439/dbname") \
  .option("dbtable", "target_table_name") \
  .option("user", "username") \
  .option("password", "password") \
  .mode("overwrite") \
  .save()

# Stop Spark Session
spark.stop()
```

# Schedule Redshift to Redshift Batch Job using Airflow DAG

This guide provides instructions for scheduling a Redshift to Redshift batch job using Airflow DAG.

## Prerequisites

- Airflow installation with the necessary dependencies, including the `psycopg2` library for connecting to Redshift.
- A Python script that implements the batch job and has been tested and validated.

## Steps

1. Create a new Python file in the Airflow `dags` directory and name it `redshift_to_redshift_batch_job.py`.
2. Copy the following code into the file:

```python
# Import necessary packages
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime, timedelta

# Default arguments for the DAG
default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2023, 2, 3),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5)
}

# Define the DAG
dag = DAG(
    'redshift_to_redshift_batch_job',
    default_args=default_args,
    schedule_interval=timedelta(minutes=15)
)

# Define the task using the PythonOperator
task = PythonOperator(
    task_id='redshift_to_redshift_batch_job',
    python_callable=run_batch_job, # the function that runs the batch job
    dag=dag
)
```

## Importing the Run Batch Job Code

The `run_batch_job` function that is called in the DAG needs to be defined in another module. To import this code, you need to add the following lines in the `redshift_to_redshift_batch_job.py` file, after the import statements:

```python
# Import the module that contains the run_batch_job function
import my_module
```

Replace my_module with the actual name of the module that contains the run_batch_job function.

## Placing the Code in the Airflow Repository
```
airflow/
|-- dags/
|   |-- redshift_to_redshift_batch_job.py  # the DAG definition file
|-- my_module/
|   |-- __init__.py
|   |-- run_batch_job.py  # the code for the run_batch_job function
```

Make sure to include the airflow directory in your Python path so that Airflow can find and execute the DAG and the run_batch_job code.



# Sort and Partition Keys in Redshift

Redshift is a columnar database, which means that data is stored in columns instead of rows. This design allows for faster querying, but also requires a careful consideration of how data is stored and organized.

Sort keys and partition keys are two techniques in Redshift that can help to optimize query performance.

## Sort Keys

Sort keys are used to store data in a specific order, which can make querying faster in certain cases. Redshift offers several types of sort keys, including:

- Compound sort keys: multiple columns that are used to sort data in a specific order
- Interleaved sort keys: data is sorted across multiple columns, instead of just one
- Extended sort keys: similar to interleaved sort keys, but allows for more fine-grained control over the sorting order

When designing a table in Redshift, you can choose to set one or more sort keys. This will determine the order in which data is stored on disk. When queries are executed, Redshift can use the sort key to quickly filter and sort data, which can make the queries run faster.

## Partition Keys

Partition keys are used to divide data into smaller pieces, called "partitions". This can make querying faster, because Redshift can skip over partitions that are not relevant to the query.

When designing a table in Redshift, you can choose to set one or more partition keys. This will determine how data is divided into partitions. When queries are executed, Redshift can use the partition key to filter and retrieve only the data that is needed, which can make the queries run faster.

## How to Use Sort and Partition Keys in Redshift

To use sort and partition keys in Redshift, you need to consider the following:

1. Determine the most frequently queried columns in your data
2. Decide which columns to use as sort keys
3. Decide which columns to use as partition keys
4. Create the table, specifying the sort and partition keys
5. Load data into the table
6. Monitor query performance and make adjustments as necessary

By carefully considering the design of your tables, you can use sort and partition keys to optimize query performance in Redshift.


# Procedure for creating or modifying Redshift objects using DDLs

1. Write the DDL code
    - The code can be written in any database client connected to Redshift, but it's recommended to store the code in a version control system such as Git.

2. Store the code in a YAML file
    - A YAML file is a human-readable data serialization format. In this case, it's used to store the DDL code.

3. Commit the YAML file to the repository
    - The YAML file can be committed to the repository, but it's not executed yet.

4. Deploy the YAML file to the production environment
    - The YAML file should be deployed to the production environment for further execution.

5. Execute the DDL code using a Jenkins job
    - A Jenkins job can be manually triggered to execute the DDL code stored in the YAML file.

# Example YAML file with Redshift DDL code
```
- ddl_code: |
    CREATE TABLE users (
      user_id INTEGER PRIMARY KEY,
      username VARCHAR(255),
      email VARCHAR(255)
    );
```

## Dimensional Model

The program uses a dimensional model to store sales data. The data is organized into two tables:

### Fact Table: Sales (only contains ids and facts)

| Date | Product_id | Quantity | Price | Total Sales |
|------|---------|----------|-------|------------|
| 1/1/2022 | T-shirt | 20 | 10 | 200 |
| 1/1/2022 | Jeans | 10 | 50 | 500 |
| 1/2/2022 | T-shirt | 15 | 10 | 150 |

### Dimension Table: Product (constains deatails for the ids used in the fact)

| Product_id | Category | Color | Size |
|---------|----------|-------|------|
| T-shirt | Clothing | Red | M |
| Jeans | Clothing | Blue | 32 |

The Sales fact table contains the sales data for a store, including the date of the sale, the product sold, the quantity sold, and the price. The Product dimension table provides additional details about each product, such as its category, color, and size. This allows for more in-depth analysis of the sales data.

## Steps to Achieve a Dimensional Model

1. Identify the business requirements: The first step is to understand the business requirements and what information needs to be stored and analyzed.

2. Choose the facts and dimensions: Based on the business requirements, choose the facts and dimensions that are relevant to the analysis. The facts represent the measures or metrics that need to be analyzed, while the dimensions provide context to the facts.

3. Create the fact table: The fact table contains the facts or metrics that need to be analyzed, such as sales, quantity, or revenue. The fact table should include a foreign key for each dimension that provides context to the facts.

4. Create the dimension tables: The dimension tables contain descriptive information about the dimensions that provide context to the facts. Each dimension table should contain a primary key, which is used as a foreign key in the fact table to link the dimensions and facts together.


# Questions for interviewrs 

## Data Pipeline and Technologies 
1. Can you describe your current data pipeline and the technologies you use to process and store data?
- As a candidate with a background in using Apache Spark, Redshift, and Airflow, I might answer that my current data pipeline involves ingesting data from a Kafka topic, processing it using Spark, and storing it in Redshift. Then I process the data in another Redshift schema, and finally sink it into S3 for long-term storage. 

## Schedule and Orchestrate Batch Processing Jobs
2. How do you schedule and orchestrate your batch processing jobs?
- I use Apache Airflow for scheduling and orchestration of my batch processing jobs. Airflow allows me to define, schedule, and monitor all the tasks in my data pipeline in a single place.

## Data Ingestion and Integration with External Sources
3. How do you handle data ingestion and integration with external sources?
- I use Apache Spark to ingest data from external sources like a Kafka topic. Spark provides a scalable and efficient way to process large amounts of data in real-time.

## Processing and Storing Large Datasets
4. Can you walk me through your process for processing and storing large datasets?
- To process and store large datasets, I use Apache Spark for data processing and Redshift for data storage. Spark allows me to efficiently process large amounts of data, and Redshift provides a scalable and cost-effective solution for storing and querying large amounts of data. Before storing the data in Redshift, I make sure to properly format and clean the data, using Spark if necessary.

# Projects

## Experience with Apache Flink POC

In my current project, we were using Apache Spark for data streaming from Apache Kafka into Amazon Redshift. However, we wanted to evaluate alternative options, especially for stateful aggregations. One of the alternatives we evaluated was Apache Flink.

I was responsible for setting up the POC to test Flink's capabilities. Our devops team set up a Flink cluster on a Kubernetes environment. The streaming data source was Apache Kafka, and the goal was to perform stateful aggregations on the incoming data and store the results in Redshift.

I worked closely with the devops team to ensure that the cluster was properly set up and configured. I also researched and tested various techniques for performing stateful aggregations in Flink, as well as integrating with Redshift.

Unfortunately, we ultimately decided not to use Flink due to a lack of documentation and maturity of the Pyflink API. Despite this, the POC provided valuable insights and experience in evaluating alternative technologies, and working with a cross-functional team.

# Jenkins for Continuous Integration and Deployment

In our data processing pipeline, we use Jenkins as our tool for Continuous Integration and Deployment (CICD). This allows us to automate the build and deployment process, ensuring that the code we are working on is constantly tested and integrated into the production environment. 

Here's a simple example of how Jenkins can be used in our pipeline:

1. A developer pushes a new feature or bug fix to the code repository (e.g. Github).
2. Jenkins automatically detects the new code and triggers a build.
3. The build process runs tests on the code to check if it's functioning correctly.
4. If the tests pass, Jenkins automatically deploys the new code to the production environment.
5. If the tests fail, the deployment is halted and the developer is notified of the failure.

By using Jenkins, we can ensure that the code we're working on is constantly tested and integrated into the production environment, reducing the risk of downtime or issues caused by untested code. Additionally, this also helps us to catch and fix bugs earlier in the development process, before they become bigger problems.

# Comparison of On-Premise Technologies with AWS Technologies

Below is a list of some popular on-premise technologies and their equivalent services in AWS:

### Redshift
- On-premise: Redshift is used as a data warehouse for storing large amounts of data.
- AWS equivalent: Amazon Redshift, with the same capabilities as Redshift on-premise, can be used as a data warehouse.

### Apache Spark
- On-premise: Apache Spark is used for big data processing.
- AWS equivalent: AWS Glue, which is a serverless ETL service, can be used instead of Apache Spark. However, it is important to note that Glue is focused on ETL and may not have the same level of processing capabilities as Spark.

### Jenkins
- On-premise: Jenkins is used for continuous integration and continuous deployment (CI/CD).
- AWS equivalent: AWS CodePipeline, a continuous delivery service, can be used instead of Jenkins for CI/CD.

### Airflow
- On-premise: Airflow is used for scheduling and orchestration of batch jobs.
- AWS equivalent: AWS Glue, which provides similar functionality for scheduling and orchestration of batch jobs, can be used instead of Airflow.

### AWS Lambda
- On-premise: There is no equivalent technology to AWS Lambda.
- AWS equivalent: AWS Lambda is a serverless compute service that runs your code in response to events and automatically manages the underlying infrastructure.

In conclusion, while the on-premise technologies mentioned above can be replaced by AWS services, it is important to carefully evaluate the requirements of each project and choose the appropriate service that meets those needs.


# Kubernetes and Docker 

In my previous role, I was not responsible for setting up the Kubernetes clusters, but I was able to utilize `kubectl` to monitor and manage the pods in the cluster. 

My main use case for `kubectl` was to debug and restart pods if necessary. I also used it to get an overall view of the state of the cluster and the health of individual pods.

While I did not set up the cluster myself, I have a good understanding of the basics of Kubernetes and how it operates. I have experience with creating and managing pods, and I am familiar with the different components of a Kubernetes cluster such as nodes, services, and deployment objects.

In terms of Docker, I have experience with creating and deploying Docker containers, as well as managing and scaling them in a production environment.

Overall, I am confident in my ability to utilize Kubernetes and Docker to support the deployment and management of applications in a production environment.

# My Use of Kubernetes and Docker

Kubernetes and Docker are two technologies that are commonly used for container orchestration and deployment. Although I haven't set up the cluster myself, I have gained experience working with both technologies in a monitoring and debugging role.

## Monitoring with Kubectl

Kubectl is the command line interface for Kubernetes, and it allows you to manage and monitor the state of your cluster and its components. I have used kubectl to check the status of pods, and to get detailed information about the state of containers and their resources.

For example, to check the status of all pods in a namespace, I would run the following command:

```
kubectl get pods -n mynamespace
```

This would return a list of all pods in the `mynamespace` namespace, along with their current state (running, pending, failed, etc.).

## Debugging with Kubectl

In addition to monitoring, I have also used kubectl to debug issues with pods and containers. For example, if a pod is failing, I can use the `kubectl describe` command to get detailed information about the issue, including any error messages or log outputs.

For example, to get detailed information about a failing pod, I would run the following command:

```
kubectl describe pod mypod -n mynamespace

```


This would return information about the pod, including its status, events, and logs. This information can be useful for identifying the root cause of a failure and determining the appropriate course of action to resolve the issue.

## Restarting Pods

Finally, I have used kubectl to restart pods when necessary. For example, if a pod is failing and a simple restart is likely to resolve the issue, I can use the `kubectl delete pod` command to delete the pod and have it automatically recreated by the cluster.

For example, to restart a pod, I would run the following command:

```
kubectl delete pod mypod -n mynamespace

```


This would delete the `mypod` pod in the `mynamespace` namespace, and the cluster would automatically recreate it based on the defined pod configuration.

## Conclusion

In conclusion, my experience working with kubectl and Kubernetes has been focused on monitoring, debugging, and restarting pods. While I have not set up the cluster myself, I have gained a good understanding of how to use kubectl to manage and monitor a Kubernetes cluster.





-----
-----

# Behavioural questions

 **Question: Tell me about a time when you were faced with a challenging situation. How did you solve it? Talk about a query taking too long because of bad sort keys**

**Answer**: A challenging situation I faced as a data engineer was when I was working on a customer success company, and I encountered a query that was taking too long to run. This was due to the fact that the sort keys were not properly configured for the data.

To solve this issue, I followed these steps:

1. I analyzed the data and identified which columns were frequently used in the queries.
2. I set the sort key for the frequently used columns to optimize the query performance.
3. I tested the query again to see if the performance improved.

By doing this, the query performance was significantly improved, and the customer was able to receive their data in a timely manner. This was a great solution as it not only solved the immediate problem, but it also ensured that future queries would run more efficiently as well.


**Example of a mistake made at work:**

## **Question:** Give me an example of a time you made a mistake at work. 

**Answer:** One instance that comes to mind was when I was working on a regular ETL script. I was asked by the business team to manually insert data into a Redshift database as a backfill. I thought it would be a quick and simple task, but I soon realized that it was a dangerous practice. I had made a mistake in manually entering the data and it took me a while to find and fix it.

After finding the mistake, I brought it to the attention of my boss and proposed a solution to avoid this kind of situation in the future. I suggested that we commit all DDL statements into a YAML file, which could be reviewed and approved in a pull request. After approval, the code could be run with Jenkins to prevent dangerous manual insertion of data into the database.

Q: Tell me about a time when you went above and beyond your duties for a job or task.

A: Sure, I'd be happy to. When I was working as a data engineer, I was faced with a legacy schema that was becoming increasingly difficult to work with. The schema was flat and lacked proper relationships between tables, which made it challenging to run efficient queries and extract insights from the data.

In order to meet the business needs, I proposed to my boss to refactor the schema into a dimensional model. The dimensional model would have a star schema structure, with a central fact table and dimension tables that captured different aspects of the data, such as time, location, and product.

The key benefits of the dimensional model were that it would make it easier to understand the relationships between the data, would allow us to run more efficient queries, and would provide a foundation for better data visualization. After reviewing my proposal, my boss approved the refactor and I was able to successfully implement the new schema. The result was a more streamlined and efficient way of working with our data, which improved our ability to deliver insights to the business.

## Have you ever corrected one of your superiors when they were wrong? How did you approach the situation?

One time, my superior suggested to include some dimensions as fields on a fact table in a data warehousing project. However, I knew this approach would not be optimal for querying the data. I approached the situation by first acknowledging my superior's idea and then presenting the drawbacks of this approach. I explained that including dimensions as fields on a fact table would result in data redundancy and would make it difficult to update or delete data.

I then presented a alternative approach of having a separate dimension table that could be joined with the fact table, which would ensure the data remains normalized and would make querying and updating data much easier. I also provided examples and data to support my argument and showed how the dimensional model would lead to better performance and scalability in the long run.

My superior was open to my suggestion and ultimately agreed with my proposal. This change resulted in a more efficient and scalable data warehousing solution that was easier to maintain.

## **Question:** Sometimes employers put too much on their employees’ plates. Was there a time when you were overwhelmed with work? How did you handle the situation?

**Answer:**

1. Prioritizing tasks: Assess the urgency and importance of the tasks at hand, and prioritize them accordingly.

2. Asking for help: If necessary, reach out to colleagues or superiors for assistance in completing the work.

3. Time management: Utilize time management techniques such as setting daily goals, breaking down tasks into smaller parts, and focusing on one task at a time.

4. Taking breaks: Taking short breaks throughout the day can help to clear the mind and increase focus and productivity.

5. Effective communication: Clear and open communication with employers about workload expectations and the need for additional support can help to manage workloads and prevent burnout.

In the end, it's important to maintain a healthy work-life balance and take care of oneself, both physically and mentally.


## **Question**: How do you deal with coworkers that don’t cooperate or can’t contribute enough?

**Answer**:

When faced with coworkers who are not cooperative or cannot contribute enough, I try to approach the situation with empathy and open communication. I believe that it's important to understand their perspective and any challenges they may be facing, and to find ways to work together to achieve our common goals.

First, I try to have a one-on-one conversation with the coworker to express my concerns and to understand their point of view. I then collaborate with them to find solutions to any problems and to set clear expectations for their role in our team.

If the issue persists, I bring it to the attention of our manager and work with them to find a solution. I believe in finding a solution that benefits both the team and the individual in question.

Ultimately, I believe in maintaining a positive and productive work environment and fostering strong relationships with my coworkers. By working together and finding ways to support one another, we can overcome any challenges and achieve our goals as a team.


## Is there a situation you think you could’ve handled better or differently?

Yes, there was a time when I was working on a data integration project. One of the key stakeholders was not cooperating and not providing the necessary information on time. This caused a delay in the project timeline and put additional pressure on my team.

Looking back, I realized that I could have communicated the importance of timely responses more clearly to the stakeholder and established a follow-up plan for any outstanding information. Additionally, I could have also involved a project manager earlier to help resolve the issue.

In the future, I would handle similar situations by being proactive in my communication and escalation to ensure that everyone is aligned and working towards the common goal.

