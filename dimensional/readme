
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
