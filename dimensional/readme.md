
# Dimensional Model

## Fact Table: Sales (only contains ids and facts)

| Date       | Product_id | Client_id | Quantity | Price | Total Sales |
|------------|------------|-----------|----------|-------|------------|
| 1/1/2022   | 1          | 1         | 20       | 10    | 200        |
| 1/1/2022   | 2          | 1         | 10       | 50    | 500        |
| 1/2/2022   | 1          | 2         | 15       | 10    | 150        |

## Dimension Table: Product (contains details for the ids used in the fact)

| Product_id | Product_name | Category  | Color | Size |
|------------|--------------|-----------|-------|------|
| 1          | T-shirt      | Clothing  | Red   | M    |
| 2          | Jeans        | Clothing  | Blue  | 32   |

## Dimension Table: Client (contains details for the ids used in the fact)

| Client_id | Client_name  | Location |
|-----------|--------------|----------|
| 1         | John Doe     | New York |
| 2         | Jane Doe     | London   |


The Sales fact table contains the sales data for a store, including the date of the sale, the product sold, the quantity sold, and the price. The Product dimension table provides additional details about each product, such as its category, color, and size. This allows for more in-depth analysis of the sales data.

## Steps to Achieve a Dimensional Model

1. Identify the business requirements: The first step is to understand the business requirements and what information needs to be stored and analyzed.

2. Choose the facts and dimensions: Based on the business requirements, choose the facts and dimensions that are relevant to the analysis. The facts represent the measures or metrics that need to be analyzed, while the dimensions provide context to the facts.

3. Create the fact table: The fact table contains the facts or metrics that need to be analyzed, such as sales, quantity, or revenue. The fact table should include a foreign key for each dimension that provides context to the facts.

4. Create the dimension tables: The dimension tables contain descriptive information about the dimensions that provide context to the facts. Each dimension table should contain a primary key, which is used as a foreign key in the fact table to link the dimensions and facts together.

## Questions 
1. Can you explain what is dimensional modeling and why is it important?

Dimensional modeling is a data modeling technique used in data warehousing. It involves organizing data into fact tables and dimension tables to support business intelligence and analytics. Dimensional modeling is important because it provides a simplified and intuitive way to understand and analyze data, allowing for faster and more effective decision-making.

2. What are the steps involved in creating a dimensional model?

The steps involved in creating a dimensional model are:
- Identifying the business requirements
- Choosing the facts and dimensions
- Creating the fact table
- Creating the dimension tables
- Loading the data into the tables


3. Can you describe the difference between a fact table and a dimension table?

A fact table contains the metrics or measures that need to be analyzed, such as sales, quantity, or revenue. A dimension table contains descriptive information about a specific aspect of the data, such as time, product, or location, that provides context to the facts. The fact table contains foreign keys that link to the dimension tables to establish relationships between the facts and dimensions.

4. How do you choose the appropriate granularity for a fact table in a dimensional model?

The appropriate granularity for a fact table depends on the business requirements and the level of detail needed for analysis. The granularity should be specific enough to provide meaningful insights, but not so specific that the data becomes overwhelming. For example, if the goal is to analyze sales by product and time, the fact table might have a granularity of daily sales by product.

5. What is a data cube and how does it relate to dimensional modeling?

A data cube is a multi-dimensional representation of the data stored in fact and dimension tables. It is created by aggregating data from the fact and dimension tables and organizing it into a cube structure with dimensions representing the different aspects of the data. The data cube allows users to easily navigate and analyze the data from different perspectives by drilling down, slicing, and pivoting the data. Dimensional modeling and the creation of a data cube go hand in hand, as the dimensional model provides the structure and relationships necessary to create the data cube.

6. Can you explain the difference between a normalized and a dimensional model and when to use each one?

A normalized model is a traditional relational database design that minimizes data redundancy by normalizing the data into separate tables. A dimensional model, on the other hand, is a denormalized model that is optimized for fast query performance and easy data analysis. 

A normalized model is best suited for transactional systems where the focus is on data consistency and integrity. A dimensional model is best suited for data warehousing and business intelligence applications where the focus is on fast query performance and ease of data analysis.

7. How do you handle slowly changing dimensions in a dimensional model?

Slowly changing dimensions are dimensions that change over time, such as a product name or location. There are several methods for handling slowly changing dimensions, including:
- Type 1: Overwrite the dimension values with the new values
- Type 2: Create a new record in the dimension table for the new values
- Type 3: Add columns to the dimension table to store both the old and new values

The appropriate method depends on the specific business requirements and the amount of history that needs to be retained.
