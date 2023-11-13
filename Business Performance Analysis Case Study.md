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
 order by quarter_number
;
````
