# Retail Sales Analysis with PL/SQL Window Functions  

**Course**: Database Development with PL/SQL (INSY 8311)  
**Instructor**: Eric Maniraguha  
**Student**: [Your Full Name]  
**Repository**: plsql-window-functions-[lastname]-[firstname]  

---

## 📌 Problem Definition  

**Business Context**  
Our project focuses on a mid-sized **retail company** operating across multiple regions in Rwanda. The company sells a mix of **beverages, clothing, and electronics**. Each customer belongs to a specific region, and every purchase is tracked in the transactions table.  

**Data Challenge**  
The business has plenty of sales data but struggles to extract actionable insights. Managers cannot easily identify **which products sell best per region and quarter**, nor can they track **growth trends** or **customer value segmentation**.  

**Expected Outcome**  
By applying PL/SQL window functions, we aim to:  
- Reveal **top-selling products** in each region per quarter.  
- Track **running totals and growth** month to month.  
- Classify customers into **spending quartiles**.  
- Show a **3-month moving average** to smooth out fluctuations.  

The insights will support **inventory planning, marketing campaigns, and customer loyalty programs**.  

---

## 🎯 Success Criteria  

The project demonstrates five specific goals using window functions:  

1. **Top 5 products per region and quarter** → `RANK()`  
2. **Running monthly sales totals** → `SUM() OVER()`  
3. **Month-over-month growth %** → `LAG()` / `LEAD()`  
4. **Customer quartiles (spending segmentation)** → `NTILE(4)`  
5. **3-month moving average of sales** → `AVG() OVER()`  

---

## 🗂 Database Schema  

We designed three main tables with foreign key relationships:  

### Tables  

**customers**  
- `customer_id (PK)`  
- `name`  
- `region`  

**products**  
- `product_id (PK)`  
- `name`  
- `category`  

**transactions**  
- `transaction_id (PK)`  
- `customer_id (FK)`  
- `product_id (FK)`  
- `sale_date`  
- `amount`  

**Example rows**  
- `(1001, John Doe, Kigali)` in customers  
- `(2001, Coffee Beans, Beverages)` in products  
- `(3001, 1001, 2001, 2024-01-15, 25000)` in transactions  

### ER Diagram  
📷 *[Insert ER diagram image here]*  

This diagram shows:  
- One-to-many between **customers → transactions**  
- One-to-many between **products → transactions**  

---

## 🧮 Window Functions Implementation  

For each category, we provide the SQL query, the screenshot of results, and interpretation.  

---

<img width="959" height="346" alt="Image" src="https://github.com/user-attachments/assets/49c9b835-bce7-4375-adab-3c6c1e1896ae" />

### 1. Ranking – Top 5 Products by Region and Quarter  

```sql
SELECT region, product_id, 
       RANK() OVER (PARTITION BY region, TO_CHAR(sale_date,'Q') 
                    ORDER BY SUM(amount) DESC) AS product_rank,
       SUM(amount) AS total_sales
FROM transactions t
JOIN customers c ON t.customer_id = c.customer_id
GROUP BY region, product_id, TO_CHAR(sale_date,'Q');

