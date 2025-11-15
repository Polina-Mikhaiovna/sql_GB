
## Рассчитайте среднее количество товаров, заказанных каждым покупателем

```
select distinct o.CustomerID ,
AVG(od.Quantity) over (partition by o.CustomerID) AS average_quantity
from Orders o
join OrderDetails od on o.OrderID = od.OrderID 
ORDER BY average_quantity DESC 
```

## Определите первую и последнюю даты заказа для каждого клиента

```
SELECT DISTINCT CustomerID,
MIN(OrderDate) over (PARTITION BY CustomerID ) AS first_order, 
MAX(OrderDate) over (PARTITION BY CustomerID ) AS last_order
FROM Orders
```

## Получите общее количество заказов для каждого клиента, а также имя и город клиента

```
SELECT DISTINCT c.CustomerName ,c.City ,
COUNT(o.OrderID) over (PARTITION BY o.CustomerID ) AS count_of_orders
FROM Orders o 
JOIN Customers c ON c.CustomerID = o.CustomerID 
ORDER by count_of_orders DESC 
```


## Ранжируйте сотрудников на основе общего количества обработанных ими заказов

```
select e.FirstName , e.LastName , COUNT(o.OrderID) AS Orders_count,
RANK() over (order by COUNT(o.OrderID) DESC) AS Rank
from Orders o
join Employees e on e.EmployeeID = o.EmployeeID 
GROUP by e.EmployeeID
```

## Определите среднюю цену товаров внутри каждой категории, рассматривая только категории, в которых более трех товаров

```
WITH avg_prise_in_category AS (SELECT CategoryID
, AVG(Price) OVER (PARTITION  CategoryID) AS average_prise
, Count(ProductID) OVER (PARTITION BY CategoryID) AS count
FROM Products)
SELECT * from avg_prise_in_category 
WHERE count > 3
GROUP BY CategoryID
```