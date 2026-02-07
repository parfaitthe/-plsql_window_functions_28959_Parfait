# Database Development with PL/SQL Assignment

**Student Name:** Mugisha Jean Parfait  
**ID:** 28959  
**Course:** Database Development with PL/SQL  

---

## 📌 Business Context

- **Company Type:** Farming Company  
- **Department:** Farm Operations  
- **Industry:** Agriculture  

The company operates several farms across different regions producing **maize, beans, and onions**. Crop output and input costs vary by **location** and **season**, but sales data is not analyzed collectively. This limits management’s ability to identify profitable crops and plan production effectively.

---

## 🎯 Problem Statement

Sales and production data exist but are not analyzed across farms, regions, and seasons. This makes it difficult to:
- Identify the most profitable crops
- Compare performance by farm and region
- Make informed seasonal production decisions

---

## ✅ Expected Outcomes

This project aims to:
- Identify **crop output and selling prices per farm**
- Determine **selling price patterns per region and season**
- Support **crop selection and seasonal planning decisions**

---

## 🎯 Measurable Goals Linked to Window Functions

| Goal | Window Function |
|------|----------------|
| Top 5 products per region or quarter | `RANK()` |
| Running monthly sales totals | `SUM() OVER()` |
| Month-over-month growth | `LAG()` / `LEAD()` |
| Crop revenue quartile segmentation | `NTILE(4)` |
| Average sales revenue over time | `AVG() OVER()` |

---

## 🗂️ Database Schema
![alt text](image.png)

### Tables
1. **Farms** (`farm_id`, `farm_name`, `location`)
2. **Crops** (`crop_id`, `crop_name`, `season`)
3. **Production** (`production_id`, `farm_id`, `crop_id`, `quantity_harvested`, `production_date`)
4. **Sales** (`sale_id`, `production_id`, `sale_date`, `quantity_sold`, `price_per_kg`)

### Relationships
- One Farm → Many Productions  
- One Crop → Many Productions  
- One Production → Many Sales  

---

## 🔗 Required JOIN Types

### 1️⃣ INNER JOIN – Valid transactions
```sql
SELECT s.sale_id, s.sale_date,
       (s.quantity_sold * s.price_per_kg) AS total_revenue,
       p.production_id, p.quantity_harvested
FROM Sales s
INNER JOIN Production p
ON s.production_id = p.production_id;
```
![alt text](image-1.png)
---

### 2️⃣ LEFT JOIN – Crops never sold
```sql
SELECT c.crop_id, c.crop_name, c.season
FROM Crops c
LEFT JOIN Production p ON c.crop_id = p.crop_id
LEFT JOIN Sales s ON p.production_id = s.production_id
WHERE s.sale_id IS NULL;
```
![alt text](image-2.png)
---

### 3️⃣ FULL OUTER JOIN – Crops with no sales activity
```sql
SELECT c.crop_id, c.crop_name, c.season, s.sale_id
FROM Crops c
FULL OUTER JOIN Production p ON c.crop_id = p.crop_id
FULL OUTER JOIN Sales s ON p.production_id = s.production_id
WHERE s.sale_id IS NULL;
```
![alt text](image-3.png)
---

### 4️⃣ FULL OUTER JOIN – Farms, crops, and sales comparison
```sql
SELECT f.farm_id, f.farm_name,
       c.crop_id, c.crop_name,
       s.sale_id
FROM Farms f
FULL OUTER JOIN Production p ON f.farm_id = p.farm_id
FULL OUTER JOIN Crops c ON p.crop_id = c.crop_id
FULL OUTER JOIN Sales s ON p.production_id = s.production_id;
```
![alt text](image-4.png)
---

### 5️⃣ SELF JOIN – Farms in the same region
```sql
SELECT f1.farm_id AS farm1_id,
       f1.farm_name AS farm1_name,
       f2.farm_id AS farm2_id,
       f2.farm_name AS farm2_name,
       f1.location
FROM Farms f1
JOIN Farms f2
ON f1.location = f2.location
AND f1.farm_id < f2.farm_id;
```
![alt text](image-5.png)
---

## 📊 Window Functions

### 1️⃣ Ranking – Top-selling crops
```sql
SELECT c.crop_name,
       SUM(s.quantity_sold * s.price_per_kg) AS total_sales,
       RANK() OVER (ORDER BY SUM(s.quantity_sold * s.price_per_kg) DESC) AS sales_rank
FROM Crops c
JOIN Production p ON c.crop_id = p.crop_id
JOIN Sales s ON p.production_id = s.production_id
GROUP BY c.crop_name
ORDER BY sales_rank;
```
![alt text](image-6.png)
---

### 2️⃣ Running totals
```sql
SELECT sale_date,
       SUM(quantity_sold * price_per_kg)
       OVER (ORDER BY sale_date
             ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM Sales;
```
![alt text](image-7.png)
---

### 3️⃣ Navigation – Next sale date
```sql
SELECT p.production_id,
       c.crop_name,
       s.sale_date AS current_sale,
       LEAD(s.sale_date)
       OVER (PARTITION BY p.production_id ORDER BY s.sale_date) AS next_sale
FROM Production p
JOIN Crops c ON p.crop_id = c.crop_id
JOIN Sales s ON p.production_id = s.production_id
ORDER BY p.production_id, s.sale_date;
```
![alt text](image-8.png)
---

### 4️⃣ Distribution – Revenue quartiles
```sql
SELECT c.crop_name,
       SUM(s.quantity_sold * s.price_per_kg) AS total_sales,
       NTILE(4) OVER (ORDER BY SUM(s.quantity_sold * s.price_per_kg) DESC) AS sales_quartile
FROM Crops c
JOIN Production p ON c.crop_id = p.crop_id
JOIN Sales s ON p.production_id = s.production_id
GROUP BY c.crop_name
ORDER BY sales_quartile, total_sales DESC;
```
![alt text](image-9.png)
---

### 5️⃣ Cumulative distribution
```sql
SELECT c.crop_name,
       SUM(s.quantity_sold * s.price_per_kg) AS total_sales,
       CUME_DIST() OVER (ORDER BY SUM(s.quantity_sold * s.price_per_kg) DESC) AS cumulative_dist
FROM Crops c
JOIN Production p ON c.crop_id = p.crop_id
JOIN Sales s ON p.production_id = s.production_id
GROUP BY c.crop_name
ORDER BY cumulative_dist;
```
![alt text](image-10.png)
---

## 📈 Business Value

- Identifies **profitable crops**
- Supports **regional and seasonal planning**
- Detects **unsold or low-performing crops**
- Improves **pricing and production decisions**

---

## 📚 References

- LearnSQL.com  
- Coursera.com  
- SQL Window Functions Documentation  
- Machine Learning Plus  

---

## ✅ Conclusion

This PL/SQL project demonstrates the effective use of **JOIN operations** and **window functions** to analyze agricultural production and sales data, supporting informed decision-making in farm management.

---

**Prepared by:** Mugisha Jean Parfait  
**Student ID:** 28959
