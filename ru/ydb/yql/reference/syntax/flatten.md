# FLATTEN

## FLATTEN BY {#flatten-by}

Преобразовывает строки исходной таблицы с помощью вертикального разворачивания [контейнеров](../types/containers.md) переменной длины (списков или словарей).

Например применив `FLATTEN BY` к левой колонке таблицы вида

|[a, b, c]|1|
| --- | --- |
|[d]|2|
|[]|3|

получается таблица:

|a|1|
| --- | --- |
|b|1|
|c|1|
|d|2|

Такое преобразование может быть удобным, когда по ячейкам из колонки-контейнера нужно посчитать какую-либо статистику (скажем, через [GROUP BY](group_by.md)) или когда в ячейках колонки-контейнера хранятся идентификаторы из другой таблицы, с которой нужно сделать [JOIN](join.md).

Особенности:

* Синтаксис `FLATTEN BY` аналогичен синтаксису `GROUP BY`. В `SELECT` ставится непосредственно после указания входных данных с помощью `FROM`, а если в запросе одновременно присутствует и `GROUP BY`, то перед ним.
* Тип колонки-результата зависит от типа исходной колонки:

| Тип контейнера | Тип результата | Комментарий |
| --- | --- | --- |
| `List<X>` | `X` | Тип ячейки списка |
| `Dict<X,Y>` | `Tuple<X,Y>` | Кортеж из двух элементов с парами «ключ—значение» |
| `Optional<X>` | `X` | Результат практически эквивалентен конструкции<br>`... WHERE foo IS NOT NULL ...`, с тем отличием,<br>что тип колонки `foo` будет изменен на `X`, что иногда удобно |

* По умолчанию колонка с результатом заменяет собой исходную. Если необходимо сохранить исходный контейнер, следует использовать конструкцию `... FLATTEN BY foo AS bar ...`. В результате исходный контейнер останется доступным под именем `foo`, а колонка, построенная из развернутых элементов контейнера будет доступна в `bar`.
* Есть возможность сгенерировать декартово произведение нескольких колонок-контейнеров. Для этого нужно использовать конструкцию `... FLATTEN BY (a, b, c) ...`. Скобки обязательны из-за конфликта в грамматике.
* Мы немного упростили первую реализацию этой функции за счет того, что не поддержали в ней произвольные выражения. Как и раньше было в `GROUP BY`, сейчас в `FLATTEN BY` можно использовать только имена колонок из входной таблицы, а если требуется применить эту операцию к результату какого-либо вычисления — придется делать подзапрос.
* Если в исходной колонке были вложенные контейнеры, например `List<Dict<X,Y>>`, то `FLATTEN BY` развернет только внешний уровень. Развернуть вложенные контейнеры полностью можно с помощью подзапросов.

{% note alert %}

`FLATTEN BY` интерпретирует [опциональные типы данных](../types/optional.md) как списки длины 0 или 1. Строки таблицы с `NULL` в колонке пропускаются, и её тип данных меняется на аналогичный неопциональный.<br><br>Имея в виду тот факт, что `FLATTEN BY` делает только одно преобразование за раз, на опциональных контейнерах (например, `Optional<List<String>>`) следует использовать или `FLATTEN LIST BY` или `FLATTEN OPTIONAL BY`.

{% endnote %}

Для уточнения вида контейнера, по которому необходимо произвести преобразование, можно использовать операции:

* `FLATTEN LIST BY`
* `FLATTEN DICT BY`
* `FLATTEN OPTIONAL BY`

Для колонки-контейнера с типом `Optional<List<T>>` операция `FLATTEN LIST BY` будет разворачивать список, интерперетируя NULL значение как пустой список.

Для колонки-контейнера с типом `Optional<Dict<T>>` операция `FLATTEN DICT BY` будет разворачивать словарь, интерперетируя NULL значение как пустой словарь.

Для того, чтобы в вышеупомянутых вариантах фильтровать сами NULL-значения без размножения, необходимо уточнить операцию до `FLATTEN OPTIONAL BY`.

В других системах аналоги `FLATTEN BY` в YQL называются следующим образом:

* PostgreSQL: `unnest`
* Hive: `LATERAL VIEW`
* MongoDB: `unwind`
* Google BigQuery: `FLATTEN`
* ClickHouse: `ARRAY JOIN / arrayJoin`
* Nile: `unfold`

**Примеры**

``` yql
SELECT
  t.item.0 AS key,
  t.item.1 AS value,
  t.dict_column AS original_dict,
  t.other_column AS other
FROM my_table AS t
FLATTEN DICT BY dict_column AS item;
```

``` yql
SELECT * FROM (
    SELECT
        AsList(1, 2, 3) AS a,
        AsList("x", "y", "z") AS b
) FLATTEN LIST BY (a, b);
```

## FLATTEN COLUMNS {#flatten-columns}

Преобразует таблицу, у которой все колонки должны являться структурами, в таблицу с колонками, соответствующими каждому элементу каждой структуры из исходных колонок.

Имена исходных колонок-структур не используются и исчезают из результата. Имена элементов структур не должны повторяться между исходными колонками.

**Примеры**

``` yql
SELECT x, y, z
FROM (
  SELECT
    AsStruct(
        1 AS x,
        "foo" AS y),
    AsStruct(
        false AS z)
) FLATTEN COLUMNS;
```