# -plsql_window_functions_28959_Parfait
Course_name:Database Management with pl/sql
Student_id: 28959
Student_name: MUGISHA Jean Parfait
**Business problem**:
-> The company has several farms in different parts of the country that grow maize, beans, and onions, 
However the amount of crops that grow and the cost of inputs depends on the location and time of year. Though they are recorded sales data are not examined collectively. This makes it difficult for management to identify the most profitable crops and make informed production and sales decisions.
**Expected Outcome**
-> To identify crops output and seling price per farm, and to determine selling price per region and season . Providing information to help with crop choices  and season planning . 
**Tables in database**: Farms,crops,Production,Sales
ER Diagram 
**Join querie**
SELECT s.sale_id, s.sale_date,(s.quantity_sold * s.price_per_kg) AS total_revenue, p.production_id, p.quantity_harvested FROM Sales s INNER JOIN Production p ON s.production_id = p.production_id; -> This query Retrieve transactions with valid customers and products
2.	LEFT JOIN Identify customers who have never made a transaction
SELECT c.crop_id, c.crop_name, c.season FROM Crops c LEFT JOIN Production p ON c.crop_id = p.crop_id LEFT JOIN Sales s ON p.production_id = s.production_id WHERE s.sale_id IS NULL;
**Window function**
1.	Ranking Functions
SELECT c.crop_name, SUM(s.quantity_sold * s.price_per_kg) AS total_sales, RANK() OVER (ORDER BY SUM(s.quantity_sold * s.price_per_kg) DESC) AS sales_rank FROM Crops c JOIN Production p ON c.crop_id = p.crop_id JOIN Sales s ON p.production_id = s.production_id GROUP BY c.crop_name ORDER BY sales_rank;
2.	Aggregate Window Functions
SELECT SUM(quantity_sold * price_per_kg) AS total_sales FROM Sales;
**References**
LearnSQL.com
Coursera.com
SQL windows function
Machine learning Plus

