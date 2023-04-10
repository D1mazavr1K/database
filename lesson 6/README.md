# Задание

Необходимо спроектировать базу данных по текстовому описанию системы. Схема БД должна отвечать требованиям 3НФ

Индивидуальные задания [находятся в LMS](https://lms.uni-dubna.ru/mod/page/view.php?id=18867)

Выбирайте вариант, соответствующий вашему номеру в списке группы

# Нормальные формы

Нормальные формы или нормализация. Цель нормализации - избавиться от избыточности в отношениях.
Избыточность — это такое состояние, при котором в таблицах присутствуют лишние данные.

Существуют 6 нормальных форм, но рассматривать мы будем только первые 3, они же и обычно используются на практике.

## 1-ая нормальная форма

Отношение находится в первой нормальной форме (сокращённо 1НФ), если все его атрибуты атомарны, то есть если ни один из его атрибутов нельзя разделить на более простые атрибуты, которые соответствуют каким-то другим свойствам описываемой сущности.

Т.е. каждый атрибут отношения должен хранить одно-единственное значение и не являться ни множеством, ни списком.

На практике часто бывает очень сложно определить, когда атрибут является атомарным, а когда нет - все зависит от того, как он будет использоваться. Вполне может быть, что для разных пользователей потребуется разная степень дробности атрибутов. Здесь необходимо помнить, что объединять данные из базы легко, а дробить их после занесения информации - сложно.

## 2-ая нормальная форма

Схема отношения R находится во 2НФ относительно множества функциональных зависимостей F, если она находится в 1НФ и каждый неключевой атрибут полностью зависит от каждого ключа для R.

Можно дать его и более понятным языком: отношение находится во второй нормальной форме, если оно находится в первой нормальной форме и при этом все неключевые атрибуты зависят только от ключа целиком, а не какой-то его части.

| **Код поставщика** | **Город**  | **Статус города** | **Код товара** | **Количество** |
| ------------------ | ---------- | ----------------- | -------------- | -------------- |
| 1                  | Москва     | 20                | 1              | 300            |
| 1                  | Москва     | 20                | 2              | 400            |
| 1                  | Москва     | 20                | 3              | 100            |
| 2                  | Ярославль  | 10                | 4              | 200            |
| 3                  | Ставрополь | 30                | 5              | 300            |
| 3                  | Ставрополь | 30                | 6              | 400            |
| 4                  | Псков      | 15                | 7              | 100            |

Заранее известно, что в этом отношении содержатся следующие функциональные зависимости:  
{ {Код поставщика, Код товара} -> { Количество},  
{Код поставщика} -> {Город},  
{Код поставщика} -> {Статус},  
{Город} -> {Статус} }

Первичный ключ в отношении: {Код поставщика, Код товара}.

Очевидно, что отношение обладает избыточностью: оно описывает две сущности — поставку и поставщика. В связи с этим возникают следующие аномалии:

- Аномалия вставки. В отношение нельзя добавить информацию о поставщике, который ещё не поставил ни одного товара.
- Аномалия удаления. Если от поставщика была только одна поставка, то при удалении информации о ней будет удалена и вся информация о поставщике.
- Аномалия обновления. Если необходимо изменить какую-либо информацию о поставщике (например, поставщик переехал в другой город), то придётся изменять значения атрибутов во всех записях о поставках от него.

Физический смысл избыточности исходного отношения заключается в том, что оно описывает _не одну_ сущность, а _две_ — _поставку_ и _поставщика_.

## 3-ая нормальная форма

Схема отношения находится в третьей нормальной форме относительно множества функциональных зависимостей F, если она находится во второй нормальной форме и ни один из непервичных атрибутов в R не является транзитивно зависимым от ключа для R.

Иначе, чтобы привести отношение к третьей нормальной форме, необходимо устранить функциональные зависимости между неключевыми атрибутами отношения. То есть данные, хранимые в таблице, должны зависеть только от ключа.

### Примеры

[Популярная на хабре статья про нормализацию](https://habr.com/ru/post/254773/)

**1НФ:**

Не находится в 1 НФ:

| Фирма  | Модели |
| ------ | ------ |
| BMW    | M5,M1  |
| Nissan | GT-R   |

Чтобы находилась в 1НФ:

| Фирма  | Модели |
| ------ | ------ |
| BMW    | M5     |
| BMW    | M1     |
| Nissan | GT-R   |

**2НФ:**

Не находится в 2НФ:

| **Модель** | **Фирма** | **Цена** | **Скидка** |
| ---------- | --------- | -------- | ---------- |
| M5         | BMW       | 5500000  | 5%         |
| X5M        | BMW       | 6000000  | 5%         |
| M1         | BMW       | 2500000  | 5%         |
| GT-R       | Nissan    | 5000000  | 10%        |

Чтобы находилась во 2НФ:

| **Модель** | **Фирма** | **Цена** |
| ---------- | --------- | -------- |
| M5         | BMW       | 5500000  |
| X5M        | BMW       | 6000000  |
| M1         | BMW       | 2500000  |
| GT-R       | Nissan    | 5000000  |

| Фирма  | Скидка |
| ------ | ------ |
| BMW    | 5%     |
| Nissan | 10%    |

**3НФ:**

| Модель | Магазин    | Телефон  |
| ------ | ---------- | -------- |
| BMW    | Риал-авто  | 87-33-98 |
| Audi   | Риал-авто  | 87-33-98 |
| Nissan | Некст-Авто | 94-54-12 |

Модель – Магазин – Телефон.
Модель – Магазин, Магазин – Телефон.
Модель – Телефон – это транзитивная зависимость. А-Б Б-С =- А-С

Т.е. телефон зависит от магазина, который в свою очередь связан с моделью.

Поэтому таблицу необходимо разделить на 2:

| Модель | Магазин    |
| ------ | ---------- |
| BMW    | Риал-авто  |
| Audi   | Риал-авто  |
| Nissan | Некст-Авто |

| Магазин    | Телефон  |
| ---------- | -------- |
| Риал-авто  | 87-33-98 |
| Некст-Авто | 94-54-12 |

## Summary

По сути всё вышеперечисленное не надо знать досконально слово в слово. Необходимо понимать, что наша цель - избавиться от избыточности. Не хранить данные в одном атрибуте, если их необходимо потом парсить по частям. **Парсить полученные данные дороже и сложнее, чем сразу хранить их в удобном виде!**

Т.е. во 2 примере мы видим атрибут скидка, который зависит от магазина. Нет смысла его хранить в этой таблице, т.к. у нас появляется избыточность.

В 3 примере - телефон, он будет повторяться для каждой магазина, который встречается в этой таблицы более 1 раза. Аналогично - избыточность.

Иногда имеет место быть ситуация, когда приходится пожертвовать нормализацией в пользу каких-то других преимуществ. Это тоже может быть правильно, но вам нужно быть точно уверенным, что подобный шаг даст необходимый профит.