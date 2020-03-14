# Business_Queries

---Query 1:What are the most popular sales channels being used?
--Starting with a CTE to keep multiple sub queries in a cleaner space.
with channel_sales_num as (
select 
(select count(*) from sh.sales
 where channel_id = 3) as Direct_sales,
 ( select count(*) from sh.sales
 where channel_id = 9) as tele,
 (select count(*) from sh.sales
 where channel_id = 5) as catalog,
 (select count(*) from sh.sales
 where channel_id = 4) as internet,
 (select count(*) from sh.sales
 where channel_id = 2) as partners
from sh.sales
where rownum = 1
)
--Calling the CTE to view results on channel numbers and how many sales go through each.
select * from channel_sales_num
-------------------------------------------------------------------------------------
---Query 2:Whats the average order total from all orders?
--Finding the average without using the avg function.
select round(sum(order_total)/count(order_id),2) avg_order_total from oe.orders
--or
--Using the avg function.
select round(avg(order_total),2) avg_order_total from oe.orders

------------------------------------------------------------------------------------
--Query 3:When looking to determine raises what employees have been working here the longest to the current date?
---CTEs that are built to deal with the time problem 
with times as(
---Pulling the start dates and current date into an available variable
select employee_id, to_date(hire_date, 'dd-mon-yy') d1,
to_date('14-MAR-20', 'dd-mon-yy') d2
from hr.employees
),

tim as
---Getting the accurate year from the 'time' CTE
(select employee_id, trunc( months_between( d2, d1 )/12) years_working from times
)

select e.employee_id,first_name,last_name,t.years_working,job_id
from hr.employees e left join tim t on e.employee_id = t.employee_id
order by t.years_working desc
