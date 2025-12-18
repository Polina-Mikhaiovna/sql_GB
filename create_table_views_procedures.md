# Примеры создания таблиц, представлений и процедур

## Создайте таблицу с именем «OrderStatus» со столбцами OrderStatusID, OrderID (INT), Status (VARCHAR)

```
CREATE TABLE order_status (
order_status_id INT NOT NULL PRIMARY KEY,
order_id INT,
status VARCHAR(50)
)
```


## Вставьте образец данных в поле «OrderStatus»

```
INSERT INTO order_status 
VALUES (1, 101,'Shipped')
```

## Обновите параметр 'status' у идентификатора заказа 101 на 'Delivered'

```
UPDATE order_status SET status = 'Delivered'
WHERE  order_id = 101
```

## Создайте представление с именем «DeliveredOrders» которое отображает OrderID и OrderDate для заказов со статусом 'Delivered'

```
CREATE VIEW view_delivered_orders AS
SELECT * FROM order_status
WHERE status = 'Delivered';

--обращаемся к представлению как к таблице
SELECT * FROM view_delivered_orders vdo 
```

## Создайте представление с именем «DeliveredOrders», которое отображает OrderID и OrderDate для заказов со статусом 'Delivered'

```
CREATE VIEW view_delivered_orders AS
SELECT os.order_id, o.orderDate, os.status FROM order_status os
join Orders o ON os.order_id = o.OrderID
WHERE status = 'Delivered';
```

```
DROP VIEW view_delivered_orders
```


### Пример без динамического SQL

```
CREATE PROCEDURE UpdateOrderStatus
@pOrderID INT, @pStatus VARCHAR(50)
as
UPDATE order_status
SET status = @pStatus
WHERE order_id = @pOrderID
```


###  С динамическим SQL

Переменные хранимой процедуры (такие как @ORDERID и @STATUS) существуют только в контексте самой процедуры.

Но динамический SQL — это отдельный контекст, выполняемый через sp_executesql.

Внутри динамического SQL нет доступа к переменным хранимой процедуры.
По сути:

есть переменные процедуры, например @STATUS

и есть переменные динамического SQL, которые вы должны объявить вручную

Это два разных набора переменных, и SQL Server их не смешивает.


```
CREATE PROCEDURE UpdateOrderStatus
    @ORDERID INT,
    @STATUS VARCHAR(50)
AS
BEGIN
    DECLARE @query NVARCHAR(1000);

    SET @query = N'UPDATE order_status 
                   SET status = @STATUS 
                   WHERE order_id = @ORDERID';
    EXEC sp_executesql 
        @query,
        N'@STATUS VARCHAR(50), @ORDERID INT',
        @STATUS = @STATUS,
        @ORDERID = @ORDERID;
END;
```


# HOME WORK

## Создайте таблицу EmployeeDetails для хранения информации о сотрудниках. 

Таблица должна содержать следующие столбцы:

● EmployeeID (INTEGER, PRIMARY KEY)

● EmployeeName (TEXT)

● Position (TEXT)

● HireDate (DATE)

● Salary (NUMERIC)

После создания таблицы добавьте в неё три записи с произвольными данными о
сотрудниках.


```
--создать

CREATE TABLE employee_details(
employee_id int PRIMARY KEY NOT NULL,
employee_name varchar(200),
position varchar(50),
hire_date datetime,
salary int
)

--заполнить
INSERT INTO employee_details (employee_id, employee_name, position,
hire_date, salary)
VALUES
(1, 'John Smith', 'Sales Manager','2022-01-15', 55000)
, (2, 'Jane Doe', 'Marketing Specialist','2021-06-30', 48000)
, (3, 'Emily Johnson', 'Software Engineer', '2023-03-22', 65000);
```


## Создайте представление HighValueOrders для отображения всех заказов,сумма которых превышает 1000. 

В представлении должны быть следующие столбцы:

● OrderID (идентификатор заказа),

● OrderDate (дата заказа),

● TotalAmount (общая сумма заказа, вычисленная как сумма всех Quantity *
Price).

```
CREATE VIEW high_value_orders AS
SELECT o.OrderID, o.OrderDate, ed.employee_name
, SUM(od.Quantity * p.Price) AS total_amount
FROM employee_details ed
JOIN Orders o ON o.EmployeeID = ed.employee_id
JOIN OrderDetails od ON od.OrderID = o.OrderID
JOIN Products p ON p.ProductID = od.ProductID
GROUP BY o.OrderID, o.OrderDate, ed.employee_name
HAVING SUM(od.Quantity * p.Price) > 1000;
```



## Удалите все записи из таблицы EmployeeDetails, где Salary меньшe 50000. Затем удалите таблицу EmployeeDetails из базы данных.


```
-- удалить записи
DELETE FROM master.dbo.employee_details
WHERE salary > 50000

-- удалить таблицу диалект MS SQL
IF object_id('employee_details') IS not NULL
BEGIN
	DROP TABLE employee_details
	END;
```

## Создайте хранимую процедуру GetProductSales с одним параметром ProductID.
Эта процедура должна возвращать список всех заказов, в которых
участвует продукт с заданным ProductID, включая следующие столбцы:

● OrderID (идентификатор заказа),

● OrderDate (дата заказа),

● CustomerID (идентификатор клиента).

```
-- создание процедуры
CREATE PROCEDURE get_product_sales @PRODUCT_ID INT
AS
BEGIN
	SELECT p.ProductID, o.OrderID, o.OrderDate, c.CustomerID FROM Products p
	JOIN OrderDetails od ON od.ProductID = p.ProductID
	JOIN Orders o ON o.OrderID = od.OrderID
	JOIN Customers c ON c.CustomerID = o.CustomerID
	WHERE p.ProductID = @PRODUCT_ID 
END

-- вызов процедуры
get_product_sales @PRODUCT_ID = 11
```