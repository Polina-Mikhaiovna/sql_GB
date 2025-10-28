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



