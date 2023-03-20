# Задание

1. Удалите информацию о хобби, если студент начал им заниматься раньше, чем родился
2. Перевести всех студентов НЕ 4 курса на курс выше
3. Добавьте в таблицу хобби новое хобби с названием "Учёба"
4. У всех студентов, средний балл которых меньше 3.5 заменить все активные хобби на учебу
5. Удалите все завершенные хобби студентов
6. Добавьте всем студентам, которые занимаются хотя бы одним хобби 0.01 балл
7. Представление: для каждого курса вывести самое популярное хобби
8. Представление: для каждого курса посчитать количество студентов и количество отличников
9. Представление: вывести информацию о студентах, которые в данный момент продолжают заниматься каким-нибудь хобби более 5 лет
10. Создать обновляемое представление (для любой таблицы)


# View (представления)

Идея представления (View) состоит в следующем: определить запрос, который предполагается использовать достаточно часто, 
сохранить его в базе данных в виде объекта и разрешить пользователям обращаться к нему по имени, как к обычной таблице. 
Когда пользователь выбирает данные из представления, СУБД выполняет соответствующий запрос, организует результаты так, 
как определено в представлении, и выдает их пользователю. Для пользователя представление выглядит как таблица, 
из которой поступают данные. Однако на самом деле данные поступают через представление, из одного или нескольких других 
источников – исходных таблиц или других представлений.

Запрос, непосредственное выполнение которого можно заменить использованием представлений, может быть какой угодно сложности. 
В сущности, это и есть одно из назначений использования представлений – скрытие сложности запросов и структуры данных от пользователей.

### Создание представлений

Для создания представлений используется следующий синтаксис: 

```sql
    CREATE [OR REPLACE] VIEW имя_представления AS
    оператор SELECT
    [WITH CHECK OPTION];
```

Использование необязательного параметра OR REPLACE позволяет заменить уже существующее представление с таким же именем новым. Если существующего представления с таким именем нет, то этот параметр игнорируется.

Примеры создания представлений:

-- создание представления, содержащего поля таблицы student за исключением среднего балла и даты рождения студентов (скрытие данных)

```sql
CREATE OR REPLACE VIEW students_v1 AS
SELECT id, surname, name, n_group  FROM Students
ORDER BY surname ASC, n_group ASC;
```

Удаление представлений, как и всех других объектов базы данных, осуществляется при помощи оператора DROP по имени представления, например:

```sql
DROP VIEW students_v1;
```


### Операции выборки из представлений

Для пользователя выборка данных из представления ничем не отличается от выборки данных из таблицы. Иногда пользователи и не догадываются, пользуются они реальной таблицей или представлением. 
Выборка данных из представлений осуществляется так же, как и из таблиц – при помощи оператора SELECT, например:


```sql
SELECT * FROM students_v1
ORDER BY N_group;
```

В случае если при создании представления разработчик заложил какую-то сортировку, она будет использоваться как сортировка по умолчанию,
но если пользователь выбирая данные из представления укажет сортировку, то сортировка, реализованная внутри представления отменится
(в примере выше произойдет сортировка по номеру группы, хотя при создании представления была указана сортировка по фамилии)


### Обновляемые представления

Если к представлению можно применить операторы обновления (INSERT, UPDATE или DELETE), то представление является _обновляемым_ (updateble), иначе оно является _читаемым_ (read-only).

Ниже приведены критерии того, является ли представление обновляемым в SQL:

- оно базируется на одной таблице;
- оно должно включать первичный ключ таблицы;
- оно не должно включать полей, полученных в результате применения функций агрегирования;
- оно не может содержать спецификации DISTINCT;
- оно не должно использовать GROUP BY или HAVING;
- оно не должно использовать подзапросы;
- оно может быть определено на другом представлении, но это представление должно быть обновляемым;
- оно не может содержать константы, строки или выражения в списке выбираемых выходных полей;
- для INSERT оно должно включать поля из таблицы, которые имеют ограничения NOT NULL.

Для обновляемых представлений применимы любые операторы обновления, которые при выполнении по отношению к представлению транслируются на исходную таблицу, на основе которой было создано представление. Однако это правило на первый взгляд не согласуется с функцией представлений по защите данных, описанной ранее. Действительно, если представить, что при помощи представления были скрыты столбцы или строки, но пользователь знает об их существовании, он может выполнить операции обновления данных, затрагивающие скрытую в представлении информацию.

Для предотвращения описанной ситуации применяется параметр WITH CHECK OPTION, который может указываться при создании обновляемых представлений после оператора SELECT. При наличии этого параметра СУБД предотвращает операции обновления данных, если они нарушают условия горизонтальной или вертикальной фильтрации, определенные в предложении в операторе SELECT:

-- создание обновляемого представления с защитой от изменений скрытого столбца

```sql
CREATE OR REPLACE VIEW student_v2_updateable AS
SELECT id, surname, name, N_group FROM students_v1
WITH CHECK OPTION;
```

# DML

## INSERT

Запросы на добавление данных в таблицы выглядят следующим образом:

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```


В данном случае необходимо перечислить названия атрибутов, а также значение.

Если вы добавляете значения всех атрибутов таблицы, то можно не указывать названия атрибутов:

```sql
INSERT INTO table_name
VALUES (value1, value2, value3, ...);
```


Пример:

```sql
INSERT INTO student (id, name, surname)
VALUES (5, 'Иван', 'Иванов');
```

Пример выше включает и добавление первичного ключа, но в 99% случаев используется автоматическая генерация значения первичного ключа, 
поэтому в основном запросы добавления у вас будут выглядеть следующим образом

```sql
INSERT INTO student (name, surname, n_group, score)
VALUES ('Иван', 'Иванов', 1011, 4.25);
```

Практически всегда добавление в таблицу без перечисления столбцов невозможно.

Обратите внимание, что если это число, то можно не писать кавычки. Остальные типы данных пишутся в кавычках.

Также имеется возможность добавить данные в таблицу из другой таблицы

```sql
INSERT INTO table2
SELECT * FROM table1
WHERE condition;
```

В `Select` можно использовать что угодно, писать какие угодно условия. Главное - количество атрибутов в table2 должно совпадать с количеством атрибутов, которое возвращает `Select`

## UPDATE


`Update` запросы используются для изменения существующих данных в таблице.

**Будьте внимательны! Если вы не добавите `WHERE` в запрос на обновление данных, то обновятся все данные в таблице. Либо, если условия в `WHERE` написаны неправильно.**

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

Пример:

```sql
UPDATE student
SET score = 5.0
WHERE id = 3;
```

Если в запросе на обновление данных вам надо поменять только одну запись, то используйте первичный ключ.

Если вы напишите такой запрос:

```sql
UPDATE student
SET score = 5.0
WHERE name = 'Ivan';
```

То запрос поменяет атрибут `score` на "5" для всех Иванов в базе данных.

## DELETE


Запрос `Delete` удаляет существующие строки в таблице

```sql
DELETE
FROM table_name
WHERE condition;
```

**Как и с запросом на обновление, надо быть внимательным. Если вы не напишите `WHERE` или напишите его неправильно и под это условие подойдут, например, все строки в таблице, то они все будут удалены**

Если вы хотите удалить из таблицы всех студентов с заданными именем, то запрос будет выглядеть так:

```sql
DELETE
FROM student
WHERE name = 'Ivan';
```

Если вам надо удалить конкретного студента, то, опять же, необходимо использовать уникальные значения.

```sql
DELETE
FROM student
WHERE id = 3;
```

Если вам надо удалить все данные из таблицы, то:

```sql
DELETE FROM student;
```


## СКРИПТ



Ниже представлен скрипт, который создаёт такие же таблицы, с которыми мы работали ранее. 
Скрипт удаляет старые таблицы, создаёт новую и заполняет её данными. 
Это нужно для того, чтобы вы могли спокойно поэксперементировать с изменением и удалением данных. 

P.S. вы можете переименовать свои старые таблицы, если они назывались так же. Тогда запуск скрипта
создаст новые таблицы, для которых вы сможете безопасно писать запросы на удаление или обновление.

```sql
 BEGIN;
    
    DROP TABLE IF EXISTS student_hobby;
    DROP TABLE IF EXISTS student;
    DROP TABLE IF EXISTS hobby;
    
    CREATE TABLE student (
        id         SERIAL PRIMARY KEY,
        name       VARCHAR(255) NOT NULL,
        surname    VARCHAR(255) NOT NULL,
        n_group    INTEGER NOT NULL,
        score      NUMERIC(3,2),
        address    VARCHAR(3000),
        date_birth DATE
    );
    
    CREATE TABLE hobby (
        id    SERIAL PRIMARY KEY,
        name  VARCHAR(255) NOT NULL,
        risk  NUMERIC(4,2) NOT NULL
    );
    
    CREATE TABLE student_hobby (
        id          SERIAL PRIMARY KEY,
        student_id  INTEGER NOT NULL REFERENCES student(id),
        hobby_id    INTEGER NOT NULL REFERENCES hobby(id),
        date_start  TIMESTAMP NOT NULL,
        date_finish DATE
    );
    
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (1, 'Ivan', 'Ivanov', 2222, '09-09-1999', 4.02);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (2, 'Mixail', 'Mixajlov', 4032, '03-12-1997', 3.25);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (3, 'Viktoriya', 'Nikolaeva', 4011, '11-23-1994', 4.23);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (4, 'Nul', 'Nulyovyj', 2222, '04-05-1998', 4.23);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (5, 'Evgeniya', 'Sidorova', 2222, '04-05-1996', 3.59);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (6, 'Sergej', 'Ivancov', 3011, '12-24-1995', 3.85);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (7, 'Nikolaj', 'Borisov', 3011, '12-08-2000', 4.22);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (8, 'Viktoriya', 'Voroncov', 3011, '11-11-1999', 4.63);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (9, 'Marina', 'Kuznecov', 3011, '01-25-1998', 3.11);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (10, 'Dzhon', 'Uik', 3011, null, 3.45);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (11, 'Viktor', 'Ponedelnik', 3011, '11-23-1994', 3.98);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (12, 'Alisa', 'Vasilchenko', 2222, null, 2.98);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (13, 'Artyom', 'Ivan', 2222, '05-28-1999', 4.03);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (14, 'Sharlotta', 'Kalla', 2222, '05-23-1996', 4.67);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (15, 'Yuliya', 'Belorukova', 4011, '11-28-1997', 3.58);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (16, 'Tatyana', 'Akimova', 4011, '01-23-1995', 4.98);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (17, 'Ulyana', 'Kajsheva', 4011, '03-03-1998', 4.37);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (19, 'Nikita', 'Kryukov', 4011, '08-04-1999', 2.55);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (20, 'Ivan', 'Shapovalov', 4032, '04-29-2002', 2);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (21, 'Anastasiya', 'Ovsyannikova', 4032, '12-31-1998', 4.25);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (22, 'Lyudmila', 'Ivanova', 4032, '05-02-1993', 3.65);
    INSERT INTO student (id, name, surname, n_group, date_birth, score) VALUES (23, 'Valentina', 'Sidorova', 4032, null, 3.76);
    
    
    INSERT INTO hobby (id, risk, name) VALUES (2, 0.3, 'Tennis');
    INSERT INTO hobby (id, risk, name) VALUES (5, 0.4, 'Lyzhnye');
    INSERT INTO hobby (id, risk, name) VALUES (7, 0.2, 'Fextovanie');
    INSERT INTO hobby (id, risk, name) VALUES (1, 0.8, 'Futbol');
    INSERT INTO hobby (id, risk, name) VALUES (3, 0.5, 'Basketbol');
    INSERT INTO hobby (id, risk, name) VALUES (4, 0.4, 'Biatlon');
    INSERT INTO hobby (id, risk, name) VALUES (6, 0.6, 'Volejbol');
    INSERT INTO hobby (id, risk, name) VALUES (8, 0, 'Muzyka');
    
    
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (1, 2, 3, '03-15-2004', null);
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (2, 2, 5, '02-18-2009', null);
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (3, 3, 4, '11-12-1993', '12-11-2016');
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (4, 4, 5, '03-14-2004', '05-03-2006');
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (5, 5, 8, '06-18-2014', '08-09-2017');
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (6, 6, 7, '03-19-2018', '03-15-2017');
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (7, 7, 4, '04-07-2017', null);
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (8, 8, 2, '11-09-2018', null);
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (9, 8, 1, '02-28-2019', '03-02-2019');
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (10, 9, 4, '12-19-2009', '12-24-2009');
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (11, 9, 5, '06-18-2013', '09-25-2018');
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (12, 11, 6, '06-18-2014', null);
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (13, 12, 7, '01-23-1999', '04-14-2004');
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (14, 1, 1, '07-19-2017', null);
    INSERT INTO student_hobby (id, student_id, hobby_id, date_start, date_finish) VALUES (15, 16, 5, '02-13-2018', null);

 COMMIT;
```
