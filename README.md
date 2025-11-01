## На какие цели берут кредит десять пенсионеров с наименьшим доходом.

**SELECT** product_type, income
**FROM** Clusters
**where** (sex = 'female' AND age >= 60) **OR**
(sex = 'male' **AND** age >= 65)
**ORDER BY** income **LIMIT** 10


## Вывести первых 10 человек с наибольшей заработной платой 

**SELECT** \* **FROM** Clusters **ORDER BY** income **DESC LIMIT** 10


## Вывести первых 10 человек с наибольшей заработной платой 

**SELECT** \* **FROM** Clusters **ORDER BY** income **DESC LIMIT** 10


## Вывести первых 10 человек с наибольшей разницей между доходом и запрашиваемым кредит.
То есть люди много зарабатывают и мало просят.

**SELECT** \*, income - credit_amount **AS** Diff **FROM** Clusters 
**ORDER BY** Diff **DESC LIMIT** 10


## Получить список клиентов из 3 и 5 кластера с доходом больше 120000

**SELECT** \* **FROM** Clusters
**WHERE** cluster **IN** (3,5) **AND** income > 120000

или

**SELECT** \* **FROM** Clusters 
**WHERE** (cluster = 3 **OR** cluster = 5) **AND** income > 120000


## Выведите клиентов, у которых цель кредита заканчивается на ‘ces’

**SELECT** \* **FROM** Clusters
**WHERE** product_type  **LIKE** '%ces'


## Получите клиентов, у которых в цели кредита есть как минимум две
буквы n

**SELECT** \* **FROM** Clusters
**WHERE** product_type  **LIKE** '%n%n%'


## --Получите клиентов, у которых цель кредита менее 9 символов

**SELECT** \* **FROM** Clusters
**WHERE** product_type **NOT LIKE** '%_________%'


## Получите клиентов, у которых доход находится в пределах 20000 и 30000
(включительно)

**SELECT** \* **FROM* Clusters
**WHERE** income **BETWEEN** 20000 **and** 30000
**order by** income **desc**


## Получите новое поле из полей education, sex вида education(sex). Буквы привести к нижнему регистру

**SELECT LOWER**(education) || ' (' || sex || ')' **AS** 'education(sex)'
**FROM** Clusters


## Посчитайте, сколько денег в декабре месяце просили всего (суммарно) клиенты банка

**SELECT SUM**(credit_amount) **AS** Sum **FROM** Clusters
**WHERE** month = 12 **AND** is_client = 1 


## Определите, сколько уникальных стран представлено среди клиентов

SELECT COUNT (DISTINCT Country) FROM Customer


## Определите количество клиентов, которые проживают в Бразилии.

SELECT COUNT(*)
FROM Customer Where Country = 'Brazil'


## Посчитайте среднюю цену и общее количество товаров в категории с идентификатором 5.

SELECT AVG(Price), COUNT(*) AS total_products
FROM Products
WHERE CategoryID = 5


## Вычислите средний возраст сотрудников на дату 2024-01-01.

SELECT 
    AVG((julianday('2025-01-01') - julianday(BirthDate)) / 365.25) AS AverageAge
FROM Employee;

--julianday - это дни с момента начала юлианского периода (24 ноября 4714 до н.э.)


## Найдите заказы, сделанные в период с 16 января по 15 февраля 2024 года, и отсортируйте их по дате заказа.

select OrderID, OrderDate from Orders
where OrderDate between '2024-01-16' and '2024-02-15'
order by OrderDate


## Определите количество заказов, сделанных в ноябре 2023 года, используя начальную и конечную дату месяца.

select count(OrderDate) from Orders
where OrderDate between '2023-11-01' and '2023-11-30'


## Найдите количество заказов за январь 2024 года, используя оператор LIKE для фильтрации даты


select count(OrderDate) from Orders
where OrderDate LIKE '2024-01-%'


## Определите количество заказов за 2024 года, используя функцию STRFTIME для извлечения года.

select count(OrderDate) from Orders
where strftime('%Y', OrderDate) = '2024'
