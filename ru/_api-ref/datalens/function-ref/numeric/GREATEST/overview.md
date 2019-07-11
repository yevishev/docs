## GREATEST

_Математические функции_

#### Синтаксис


```
GREATEST( value_1, value_2, value_3 )
```

#### Описание
Возвращает наибольшее из значений.

См. также [LEAST](../../numeric/LEAST/overview.md).

В зависимости от указанного типа данных, возвращает:
- наибольшее число;
- последнюю строку в алфавитном порядке;
- позднюю дату;
- `TRUE` при выборе из `TRUE` и `FALSE` для логического типа.

**Типы аргументов:**
- `value_1` - `Любой`
- `value_2` - `Любой`
- `value_3` - `Любой`


**Возвращаемый тип**: Совпадает с типом аргументов (`value_1`, `value_2`, `value_3`)

> [!NOTE]
>
> Тип аргументов (`value_1`, `value_2`, `value_3`) должен совпадать.



#### Примеры

```
GREATEST(3.4, 2.6) = 3.4
```

```
GREATEST("3.4", "2.6") = "3.4"
```

```
GREATEST(#2019-01-02#, #2019-01-17#) = #2019-01-17#
```

```
GREATEST(#2019-01-02 04:03:02#, #2019-01-17 03:02:01#) = #2019-01-17 03:02:01#
```

```
GREATEST(TRUE, FALSE) = TRUE
```

```
GREATEST(34, 5, 7, 3, 99, 1, 2, 2, 56) = 99
```

```
GREATEST(5.6, 1.2, 7.8, 3.4) = 7.8
```

```
GREATEST(#2019-01-02#, #2019-01-17#, #2019-01-10#) = #2019-01-17#
```


#### Поддержка источников данных

`Материализованный датасет`, `ClickHouse 1.1`, `Microsoft SQL Server 2017 (14.0)`, `MySQL 5.6`, `PostgreSQL 9.3`