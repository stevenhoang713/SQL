# 🚙 Business Performance Analysis Case Study

## 📊 Business Outcomes  

### 1. What is the distribution of customers across states?

````sql
select state
     , count(customer_id) as total_customers
  from customer_t
 group by state
 order by total_customers desc;
````

**Answer:**

<img width="300" alt="image" src="https://github.com/stevenhoang713/SQL/assets/145725846/09164e03-8679-494e-bc0a-77db22d2073f.png">


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
 order by quarter_number;
````

**Answer:**


### 3. Are customers getting more dissatisfied over time?

````sql
with quarterly_feedback_summary as (
	select quarter_number
         , sum(case when customer_feedback = 'very good' then 1 else 0 end) as very_good
		 , sum(case when customer_feedback = 'good' then 1 else 0 end) as good
         , sum(case when customer_feedback = 'okay' then 1 else 0 end) as okay
         , sum(case when customer_feedback = 'bad' then 1 else 0 end) as bad
         , sum(case when customer_feedback = 'very bad' then 1 else 0 end) as very_bad
         , count(customer_feedback) as total_feedback
	  from order_t
  group by quarter_number
  order by quarter_number
           ) 
select quarter_number
	 , round((very_good/total_feedback),2) as very_good
	 , round((good/total_feedback),2) as good
     , round((okay/total_feedback),2) as okay
     , round((bad/total_feedback),2) as bad
     , round((very_bad/total_feedback),2) as very_bad
  from quarterly_feedback_summary
 group by quarter_number
 order by quarter_number;
````

**Answer:**


### 4. Which are the top 5 vehicle makers preferred by the customer?

````sql
select p.vehicle_maker
     , count(o.customer_id) as total_customers
  from product_t p 
 right join order_t o 
	on p.product_id = o.product_id
 group by p.vehicle_maker
 order by total_customers desc
 limit 5;
````

### 5. What is the most preferred vehicle make in each state?

````sql
select *
  from (
  select state
	   , vehicle_maker
	   , count(customer_id) as total_customers
	   , rank() over (partition by state order by count(customer_id) desc) as ranking
	from product_t
	join order_t using (product_id)
	join customer_t using (customer_id)
   group by state
		  , vehicle_maker
		 )   
	as _top_vehicle_makers
 where ranking = 1
 order by total_customers desc;
````

**Answer:**


### 6. What is the trend of number of orders by quarters?

````sql
select quarter_number
     , count(order_id) as total_orders
  from order_t
 group by quarter_number
 order by quarter_number;
````

**Answer:**


### 7. What is the quarter over quarter % change in revenue?

````sql
with qtr_rev_summary as (
            select quarter_number
                 , round(SUM(quantity * vehicle_price),2) as revenue
			  from order_t
          group by quarter_number
	      order by quarter_number 
				)
select quarter_number
     , revenue
     , lag(revenue) over (order by quarter_number) as prev_revenue
     , round((revenue - lag(revenue) over (order by quarter_number)) / 
       lag(revenue) over (order by quarter_number),2) * 100 as quarterly_change
  from qtr_rev_summary
 group by quarter_number;
````

**Answer:**


### 8. What is the trend of revenue and orders by quarters?

````sql
select quarter_number
     , round(sum(quantity*vehicle_price),0) as revenue
     , count(order_id) as total_orders
  from order_t
 group by quarter_number
 order by quarter_number;
````

**Answer:**


### 9. What is the average discount offered for different types of credit cards?

````sql
select c.credit_card_type
     , round(avg(o.discount),2) as average_discount
  from customer_t c 
  join order_t o 
    on c.customer_id = o.customer_id
 group by c.credit_card_type
 order by average_discount desc;
````

**Answer:**


### 10. What is the average time taken to ship the placed orders for each quarters?

````sql
select quarter_number
     , round(avg(datediff(ship_date, order_date)),0) as avg_shipping_time
  from order_t
 group by quarter_number
 order by quarter_number;
````

**Answer:**

