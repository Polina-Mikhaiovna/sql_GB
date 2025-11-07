## Задание №1: Анализ влияния категорий продуктов на общий доход


Описание: Вам необходимо проверить, как различные категории продуктов влияют на общий доход (общую сумму заказов) в таблице OrderDetails.


Подсчитайте общее количество заказов (сумму количества) и
общий доход (сумму количества * цену)
для каждой категории продуктов.


Выведите результаты, включая:
● CategoryID
● Общее количество заказов (total_quantity)
● Общий доход (total_revenue)


Отсортируйте результаты по убыванию общего дохода (total_revenue).
Используйте таблицы Products, OrderDetails и Categories


SELECT
	p.CategoryID
	, c.CategoryName
	, COUNT(od.OrderDetailID)
	, SUM(od.Quantity) AS total_quantity
	, p.Price * SUM(od.Quantity) AS total_revenue
FROM
	Categories c
JOIN Products p ON	c.CategoryID = p.CategoryID
JOIN OrderDetails od ON	p.ProductID = od.ProductID
GROUP By
	p.CategoryID
ORDER BY
	total_revenue DESC


4	Dairy Products	100	2601	54621.0
8	Seafood     	67	1445	44795.0
1	Beverages   	93	2289	41202.0
3	Confections	    84	2110	36819.5
7	Produce	        33  715	    21450.0
5	Grains/Cereals	42	912	    19152.0
2	Condiments  	49	1383	13830.0*

## Задание №2: Анализ частоты заказа продуктов по категориям


Описание: Напишите запрос SQL для подсчета количества заказов продуктов по
каждой категории. Подсчитайте количество уникальных заказов (OrderID) для каждой
категории продуктов. Выведите результаты, включая:
● CategoryID
● Количество уникальных заказов (order_count)
Отсортируйте результаты по убыванию количества уникальных заказов
(order_count). Используйте таблицы Products, OrderDetails и Categories.


SELECT
	p.CategoryID
	, SUM(od.ProductID * od.Quantity) AS revenue_at_each_category
    , COUNT(DISTINCT od.OrderID) AS order_count
FROM
	OrderDetails od
JOIN Products p ON od.ProductID = p.ProductID
GROUP BY
	p.CategoryID
ORDER BY
	order_count DESC


1	Beverages	    84255	93
3	Confections	    78400	84
8	Seafood	        53184	67
6	Meat/Poultry	52192	50
2	Condiments	    56120	49
5	Grains/Cereals	45249	42
7	Produce	        29672	33*



## Задание №3: Вывод наиболее популярных продуктов по количеству заказов

Описание: Выведите список продуктов (название ProductName), отсортированных по
количеству заказов в порядке убывания. Подсчитайте общее количество заказов
(Quantity) для каждого продукта. Выведите результаты, включая:
● ProductName
● Общее количество заказов (total_quantity)
Отсортируйте результаты по убыванию общего количества заказов (total_quantity).
Используйте таблицы Products и OrderDetails.

SELECT
	od.ProductID
	, p.ProductName
	, SUM(od.Quantity) AS total_quantity
FROM
	Products p
JOIN OrderDetails od ON	p.ProductID = od.ProductID
GROUP BY
	p.ProductName
ORDER BY
	total_quantity DESC
LIMIT 10


31	Gorgonzola Telino	    458
60	Camembert Pierrot	    430
35	Steeleye Stout	        369
59	Raclette Courdavault	346
2	Chang	                341
16	Pavlova	                338
71	Fløtemysost	            336
17	Alice Mutton	        331
62	Tarte au sucre	        325
33	Geitost	                316
