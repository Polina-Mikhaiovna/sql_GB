
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


## Рассчитайте процент от общего объема (выручки) продаж каждого продукта в своей категории

```
select p.CategoryID, od.ProductID, p.ProductName,
sum(quantity), p.Price , sum(Quantity*p.Price) AS profit_of_product,
sum(Quantity*p.Price) / sum(sum(Quantity*p.Price)) over (partition by CategoryID) *100 AS percentage_of_sales
from OrderDetails od 
JOIN Products p ON p.ProductID = od.ProductID
GROUP by p.CategoryID ,p.ProductID
```

## Для каждого заказа сделайте новую колонку в которой определите общий объем продаж за каждый месяц, учитывая все годы

```
SELECT DISTINCT od.OrderID, STRFTIME("%Y", o.OrderDate) AS YEAR, STRFTIME("%m", o.OrderDate) AS MONTH,
SUM(p.Price * od.Quantity) over (partition BY STRFTIME("%m/%Y", o.OrderDate) ORDER BY STRFTIME("%Y", o.OrderDate)) AS month_cash
From Orders o 
JOIN OrderDetails od ON o.OrderID = od.OrderID 
join Products p ON p.ProductID = od.ProductID 
Order by Year, Month
```

## Рассчитайте промежуточную сумму заказанных количеств для каждого продукта

```
SELECT OrderID
, ProductID
, Quantity 
, SUM(Quantity) OVER (PARTITION BY ProductID order by ProductID) AS SumQantity
FROM OrderDetails
```

## Рассчитайте разницу в общем объеме продаж за каждый день по сравнению с предыдущим днем

```
SELECT OrderDate,
SUM(Quantity * Price) AS DailySales,
SUM(Quantity * Price) - LAG(SUM(Quantity * Price)) OVER (ORDER BY OrderDate) AS SalesDifference
FROM Orders o
JOIN OrderDetails od ON o.OrderID = od.OrderID
JOIN Products p ON od.ProductID = p.ProductID
GROUP BY OrderDate
```

## Рассчитайте среднюю стоимость заказа для каждого сотрудника, учитывая только заказы после 01-01-2023


```
SELECT DISTINCT EmployeeID
, AVG(SumOrder) OVER (PARTITION BY EmployeeID) AS AvgOrderValue
FROM (
	SELECT o.EmployeeID,  o.OrderID, 
	SUM(od.Quantity * p.Price) over (PARTITION BY o.OrderID ORDER BY o.EmployeeID) SumOrder
	FROM Orders o 
	JOIN OrderDetails od ON o.OrderID = od.OrderID 
	JOIN Products p ON od.ProductID = p.ProductID 
)
```

## Ранжируйте продукты в каждой категории на основе их средней цены (AvgPrice). Используйте таблицы OrderDetails и Products

```
SELECT
p.CategoryID,
p.ProductID,
p.ProductName,
p.Price,
RANK() OVER (PARTITION BY CategoryID ORDER BY p.Price DESC) AS ProductRank
FROM Products p
JOIN OrderDetails od ON p.ProductID = od.ProductID
GROUP BY p.CategoryID, p.ProductID, p.ProductName 
```

## Ранжируйте продукты (по ProductRank) в каждой категории на основе их общего объема продаж (TotalSales)


```
With TotalSales AS (
select c.CategoryID
, c.CategoryName
, od.ProductID 
, SUM(p.Price * od.Quantity ) over (partition by p.ProductID) AS SalesByCategory
from Categories c 
join Products p ON c.CategoryID = p.CategoryID 
join OrderDetails od ON p.ProductID = od.ProductID 
GROUP BY p.CategoryID, p.ProductID, p.ProductName 
)
SELECT *,
RANK() over (partition by CategoryID order by SalesByCategory DESC) AS ProductRank
FROM TotalSales 
```

## Рассчитайте среднюю сумму кредита (AvgCreditAmount) для каждого кластера в каждом месяце и сравните её с максимальной суммой кредита (MaxCreditAmount) за тот же месяц.

```
with MaxCredit AS (
SELECT
"month", credit_amount, cluster
, MAX(credit_amount) over (partition by "month") AS MaxCreditAmount
FROM Clusters
)
select "month", cluster
, AVG(credit_amount) AS AvgCreditAmount
, MaxCreditAmount
from MaxCredit 
GROUP BY "month", cluster
```



## Сопоставьте совокупную сумму сумм кредита (CumulativeSum) для каждого кластера, упорядоченную по месяцам, и сумму кредита в порядке возрастания. Определите CumulativeSum в первой строке результатов запроса

```
with Month_credit_CNT AS (
select "month" , cluster
, SUM(credit_amount) over (partition by cluster order by "month" ) AS Month_credit
from Clusters
Group by "month" , cluster )
SELECT *
, sum(Month_credit) over (partition by cluster)
from Month_credit_CNT 
Group by "month" , cluster 
```


## Создайте таблицу с разницей (Difference) между суммой кредита и предыдущей суммой кредита по месяцам для каждого кластера


```
-- Рассчитываем сумму кредита и сумму кредита в предыдущем месяце
WITH CreditWithPrevious AS (
SELECT month, cluster, credit_amount,
LAG(credit_amount) OVER (PARTITION BY cluster ORDER BY "month") AS PreviousCreditAmount
FROM Clusters
)
-- Вычисляем разницу между текущей и предыдущей суммой кредита
SELECT
month, cluster, credit_amount, PreviousCreditAmount,
COALESCE(credit_amount - PreviousCreditAmount, 0) AS Difference
FROM CreditWithPrevious;
```