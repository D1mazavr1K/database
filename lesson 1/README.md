# Введение

[Учебный сервер](https://pgdb.uni-dubna.ru/)

СУБД - система управления базами данных.

Существует множество различных СУБД. Они делятся на разные модели:
Реляционная (relational), документоориентированная (document store), ключ-значение (key-value), поисковая (search engine), с широким значением столбца (wide column store), графовые (graph), временные ряды (time series).

Самые распространённые – это реляционные СУБД (РСУБД), такие как Oracle, MySQL, SQL Server, PostgreSQL.
РСУБД справляются с большинством задач, но есть и такие, где использование другой модели даст большое преимущество, например, в скорости или удобстве использования.

В этом семестре мы познакомимся только с реляционной моделью данных на примере СУБД `PostgreSQL`

# Задание 

1. Создать свою первую БД в соответствии со схемой ниже.
2. Наполнить БД данными
    1. Students - миниум 10 записей
    2. Hobby - минимум 5 записей
    3. Students_hoby - минимум 35 записией
2. Написать следующие запросы:
    1. Вывести имена и фамилии студентов, средний балл которых от 4 до 4.5
    2. Вывести всех студентов, отсортировать по убыванию номера группы и имени от а до я
    3. Вывести студентов, средний балл которых больше 4 и отсортировать по баллу от большего к меньшему
    4. Вывести название хобби и студентов, которые начали заниматься этим хобби между двумя заданными датами (выбрать самим) и студенты должны до сих пор заниматься хобби
    5. Вывести 3 хобби с максимальным риском
    
![img](./students_hobby.png)
