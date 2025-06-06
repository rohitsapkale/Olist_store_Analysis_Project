# 🛍️ Olist E-commerce Analytics Project

A full-cycle data analytics project on Olist — a Brazilian online marketplace — to uncover business insights such as customer preferences, payment behavior, sales distribution, and product performance. This project demonstrates end-to-end analysis using **Excel**, **SQL**, **Power BI**, and **Tableau**.

---

## 🎯 Objectives

- Analyze order trends, payment methods, and delivery time metrics
- Identify top-performing product categories and sellers
- Uncover customer behavior by location and review scores
- Build dashboards and storyboards for business decision-making

---

## 📁 Project Structure

| File Name                    | Description                                     |
|-----------------------------|-------------------------------------------------|
| `Olist Excel Analysis.xlsx` | Excel-based cleaning, pivot reports, charts     |
| `olist_powerbi.pbix`        | Interactive dashboard with KPIs & slicers       |
| `olist_tableau.twbx`        | Storytelling dashboard with regional insights   |
| `olist_queries.sql`         | SQL code for extracting and shaping data        |
| `Olist_Presentation.pptx`   | Final summary deck for business presentation    |

---

## 🧠 Key Insights

- 🛒 Most sales come from the **Southeast region** of Brazil
- 🕐 **Electronics & fashion** categories dominate in order volume
- 🧾 **Credit card** is the most preferred payment method
- 📦 Longer delivery times correlate with lower review ratings
- 🧮 Built SQL views for top-selling products, delayed orders, and top-reviewed sellers

---

## 🧰 Tools & Technologies

- **SQL (MySQL)** – Data extraction, joins, views, KPIs  
- **Excel** – Data cleaning, pivot charts, conditional formatting  
- **Power BI** – Interactive visuals, category & region-wise analysis  
- **Tableau** – Dashboard storytelling and heatmaps  
- **PowerPoint** – Executive-level summary presentation

---
## Raw Data File Link
(https://drive.google.com/drive/folders/1eNlv9k7ic8AuYnIiDzCXy3TwK-J24hMD?usp=drive_link)

## Olist_store_Analysis_project File Link
https://drive.google.com/drive/folders/1GTHaSpXwe41DLxmVO9vRNRrafGs4uXNu?usp=sharing


## 📸 Screenshots

### 🧾 Excel Dashboard  
![Screenshot 2025-06-06 224738](https://github.com/user-attachments/assets/6822c3ba-42b7-475f-898b-5148dc147d95)
![Screenshot 2025-06-06 224649](https://github.com/user-attachments/assets/b402c052-d991-47de-ad2c-9767cc4528d7)

### 📊 Power BI Dashboard  
![Screenshot 2025-06-06 225334](https://github.com/user-attachments/assets/ac85a72f-0909-432b-ade0-2b7b7c2948aa)
![Screenshot 2025-06-06 225258](https://github.com/user-attachments/assets/15de6967-58bc-46c9-8f65-4a7b9c707e71)
![Screenshot 2025-06-06 225315](https://github.com/user-attachments/assets/c52c0bc8-d07d-4222-892a-6f2fbc9c126c)



### 📈 Tableau Story  

![Screenshot 2025-06-06 225929](https://github.com/user-attachments/assets/912f1430-6c98-4f17-87ed-f6ea0919dea3)
![Screenshot 2025-06-06 225911](https://github.com/user-attachments/assets/1e7e849c-2d04-43d9-a134-d2126ac6f42a)



---

## 🚀 How to Use

1. Clone the repository to your local machine  
2. Run SQL scripts in your MySQL Workbench or database tool  
3. Explore Excel workbook for initial trends and pivot summaries  
4. Open `.pbix` or `.twbx` files for advanced dashboarding  
5. Review the PowerPoint for business-level takeaways

---

## 📌 Sample SQL Snippet

create database olist;
use olist;
-- 1). WEEKDAY VS WEEKEND PAYMENT STATISTICS:

select * from olist.olist_orders_dataset;
select * from olist.olist_order_payments_dataset;

   select
   kpi1.day_end,
   concat(round(kpi1.total_payments/(select sum(payment_value) from olist_order_payments_dataset)*100,2)
   ,"%" )as percentage_values
   
   from
   (select ord.day_end ,sum(pmt.payment_value) as total_payments
   from olist_order_payments_dataset as pmt
   join
   (select distinct order_id,
   case
   when weekday(order_purchase_timestamp) in (5,6) then "weekend"
   else "weekday"
   end as Day_end
   from olist_orders_dataset) as ord
on ord.order_id = pmt.order_id
group by ord.day_end) as kpi1;

/*_______________________Total Orders: Weekday vs. Weekend_____________________*/
SELECT 
    CASE WHEN DAYOFWEEK(order_purchase_timestamp) IN (1,7) THEN 'Weekend' ELSE 'Weekday' END AS DayType,
    COUNT(*) AS TotalOrders
FROM 
    olist_orders_dataset
GROUP BY 
    DayType;
/*__________________Average Payment Value: Weekday vs. Weekend:________________________*/
SELECT 
    CASE WHEN DAYOFWEEK(order_purchase_timestamp) IN (1,7) THEN 'Weekend' ELSE 'Weekday' END AS DayType,
    round(AVG(payment_value),2) AS AveragePaymentValue
FROM 
    olist_orders_dataset o
JOIN 
    olist_order_payments_dataset p ON o.order_id = p.order_id
GROUP BY 
    DayType;

/*________________________________________________________/*/
-- 2).Number of Orders with review score 5 and payment type as credit card
SELECT 
    COUNT(olist_order_payments_dataset.order_id) AS Order_Count
FROM 
    olist_order_payments_dataset 
INNER JOIN 
    olist_order_reviews_dataset ON olist_order_payments_dataset.order_id = olist_order_reviews_dataset.order_id
WHERE 
    olist_order_reviews_dataset.Review_score = 5
    AND olist_order_payments_dataset.Payment_type = 'credit_card';
    


-- 3) Average number of days taken for order_delivered_customer_date for pet_shop

select
prod.product_category_name,
round(avg(datediff(ord.order_delivered_customer_date, ord.order_purchase_timestamp)),0) as Avg_delivery_days
from olist_orders_dataset ord
join
(SELECT product_id, Order_id, product_category_name
from olist_products_dataset
join olist_order_items_dataset using(product_id)) as prod
on ord.order_id = prod.order_id
where prod.product_category_name = "Pet_shop"
group by prod.product_category_name ;

/*____________________________________________________________*/
-- 4) Average price and payment values from customers of sao paulo city

SELECT 
    ROUND(AVG(order_items.price), 0) AS Average_Price,
    ROUND(AVG(order_payments.payment_value), 0) AS Average_Payment_Value
FROM 
    olist_orders_dataset AS orders
JOIN 
    olist_order_items_dataset AS order_items ON orders.order_id = order_items.order_id
JOIN 
    olist_order_payments_dataset AS order_payments ON orders.order_id = order_payments.order_id
JOIN 
    olist_customers_dataset AS customers ON orders.customer_id = customers.customer_id
WHERE 
    customers.customer_city = 'São Paulo';
    
/*_______________________________________________________________*/

-- 5) Relationship between shipping days (order_delivered_customer_date - order_purchase_timestamp) Vs review scores.

SELECT 
     r.review_score, 
    round(avg(DATEDIFF(o.order_delivered_customer_date, o.order_purchase_timestamp)),0) AS avg_shipping_days
FROM 
    olist_order_payments_dataset AS p
JOIN 
    olist_order_reviews_dataset AS r
    ON p.order_id = r.order_id
JOIN
    olist_orders_dataset AS o
    ON p.order_id = o.order_id
WHERE 
    o.order_delivered_customer_date IS NOT NULL 
    AND o.order_purchase_timestamp IS NOT NULL
GROUP BY r.review_score
order by r.review_score;


-- 6) year wise payment values
SELECT ROUND(SUM(payment_value), 0) AS payment_value,
       YEAR(order_purchase_timestamp) AS order_year
FROM Olist_orders_dataset
INNER JOIN Olist_order_payments_dataset ON Olist_order_payments_dataset.order_id = Olist_orders_dataset.order_id
GROUP BY YEAR(order_purchase_timestamp)
ORDER BY payment_value ASC;



-- 7) Top 5 state orders 
select seller_state , count(seller_id) as Total_seller from olist_sellers_dataset
group by seller_state
order by total_seller desc
limit 5 ;

-- 8) Bottom 5 state orders
select seller_state , count(seller_id) as Total_seller from olist_sellers_dataset
group by seller_state
order by total_seller asc
limit 5 ;





