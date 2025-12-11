# Task:
Identify all products with a gross margin% below 30% in Q3 2025.
Create a dashboard showing Year-over-year GMP, revenue by Category, Product, and region
Provide clear, data-backed recommendations on which items require a price increase or discontinuation


## Dataset  SQL Server Management Studio Query code

-- Combining Order years
with all_orders as (
select
OrderID,
CustomerID,
ProductID,
OrderDate,
Quantity,
Revenue,
COGS
from Orders_2023

union all

select
OrderID,
CustomerID,
ProductID,
OrderDate,
Quantity,
Revenue,
COGS
from Orders_2024

union all

select
OrderID,
CustomerID,
ProductID,
OrderDate,
Quantity,
Revenue,
COGS
from Orders_2025)

-- Main dataset query
select 
a.OrderID,
a.CustomerID,
c.Region,
a.ProductID,
a.OrderDate,
dateadd(week,datediff(week,0,a.OrderDate),0) as WeekDate, --Creating Week Date variable
c.CustomerJoinDate,
a.Quantity,
a.Revenue,
case when a.Revenue is null then p.Price*a.Quantity else a.Revenue end as CleanedRevenue, -- Null Treatment
a.COGS,
a.Revenue-a.COGS as Profit,
p.ProductName,
p.ProductCategory,
p.Price,
p.Base_Cost
from all_orders a
left join customers c
on a.CustomerID =c.CustomerID 
left join products  p
on a.ProductID = p.ProductID 
where a.CustomerID is not null; -- dropping null values



