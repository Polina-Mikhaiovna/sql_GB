##На какие цели берут кредит десять пенсионеров с наименьшим доходом.

**SELECT** product_type, income
**FROM** Clusters
**where** (sex = 'female' AND age >= 60) **OR**
(sex = 'male' **AND** age >= 65)
**ORDER BY** income **LIMIT** 10


##Вывести первых 10 человек с наибольшей заработной платой 

SELECT * FROM Clusters ORDER BY income DESC LIMIT 10

##Вывести первых 10 человек с наибольшей заработной платой 

SELECT * FROM Clusters ORDER BY income DESC LIMIT 10

##Вывести первых 10 человек с наибольшей разницей между доходом и запрашиваемым кредит.
То есть люди много зарабатывают и мало просят.

**SELECT** \*, income - credit_amount **AS** Diff **FROM** Clusters 
**ORDER BY** Diff **DESC LIMIT** 10


