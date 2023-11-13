# 🚙 Business Performance Analysis Case Study

## 📊 Business Outcomes  

### 1. What is the distribution of customers across states?

````sql
select state
     , count(customer_id) as total_customers
  from customer_t
 group by state
 order by total_customers desc
;
````

### 2. What is the average rating in each quarter?

````sql
with rated_orders as (
       select order_id
	        , quarter_number
	        , case when customer_feedback like 'very bad' then 1
				   when customer_feedback like 'bad' then 2
				   when customer_feedback like 'okay' then 3
				   when customer_feedback like 'good' then 4 
		           else 5
		       end as rating
		 from order_t
		      )
select quarter_number
     , round(avg(rating),2) as avg_rating
  from rated_orders
 group by quarter_number
 order by quarter_number
;
````
