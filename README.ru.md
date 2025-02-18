# SafeMySQL

[English](https://github.com/Impeck/safemysql/blob/master/README.md) | Русский

SafeMySQL - это класс PHP, разработанный для безопасной и удобной обработки запросов к MySQL. 

Форк [colshrapnel/safemysql](https://github.com/colshrapnel/safemysql).

Он выделяется несколькими ключевыми особенностями:

- **Безопасность:** Все динамические части запросов включаются в запрос с использованием заполнителей, повышая безопасность.
- **Удобство:** Он упрощает код приложения, уменьшая избыточность и следуя принципу DRY (Don't Repeat Yourself).

## Особенности

SafeMySQL предлагает три основных особенности, которые отличают его от стандартных библиотек:

1. **Заполнители с подсказками типов:** В отличие от традиционных библиотек, SafeMySQL использует заполнители с подсказками типов для всех элементов запроса.
2. **Упрощенное использование:** Это устраняет необходимость в повторном привязывании и извлечении данных благодаря набору вспомогательных методов.
3. **Частичный анализ заполнителей:** SafeMySQL позволяет анализировать заполнители не только во всем запросе, но и в любой его части, с помощью метода **parse()**, что делает выполнение сложных запросов так же простым и безопасным, как стандартные.

## Начало работы

Использование SafeMySQL просто. Вот ключевые шаги:

1. Всегда используйте заполнители для динамических данных в ваших запросах.
2. Помечайте каждый заполнитель типом данных, включая:
   - ?s ("строка"): для строк (включая `DATE`, `FLOAT` и `DECIMAL`).
   - ?i ("целое число"): для целых чисел.
   - ?n ("имя"): для идентификаторов (имен таблиц и полей).
   - ?a ("массив"): для сложных заполнителей, используемых с оператором `IN()` (заменяется строкой в формате 'a,'b,'c', без скобок).
   - ?u ("обновление"): для сложных заполнителей, используемых с оператором `SET` (заменяется строкой в формате `поле`='значение',`поле`='значение').
   - ?p ("разобранный"): специальный тип заполнителя для вставки предварительно разобранных выражений без дополнительной обработки для избегания двойного анализа.
3. Используйте вспомогательные методы для извлечения данных из запросов, включая:
   - `query($query, $param1, $param2, ...)`: Возвращает ресурс mysqli.
   - `getOne($query, $param1, $param2, ...)`: Возвращает скалярное значение.
   - `getRow($query, $param1, $param2, ...)`: Возвращает одномерный массив (строку).
   - `getCol($query, $param1, $param2, ...)`: Возвращает одномерный массив (столбец).
   - `getAll($query, $param1, $param2, ...)`: Возвращает двумерный массив (массив строк).
   - `getInd($key, $query, $par1, $par2, ...)`: Возвращает индексированный двумерный массив (массив строк).
   - `getIndCol($key, $query, $par1, $par2, ...)`: Возвращает одномерный массив (индексированный столбец), состоящий из пар ключ => значение.
4. Для сложных случаев полагайтесь на метод **parse()**.

### Пример использования

Вот несколько примеров использования SafeMySQL:

```php
$name = $db->getOne('SELECT name FROM table WHERE id = ?i', $_GET['id']);
$data = $db->getInd('id', 'SELECT * FROM ?n WHERE id IN (?a)', 'table', [1, 2]);
$data = $db->getAll("SELECT * FROM ?n WHERE mod=?s LIMIT ?i", $table, $mod, $limit);
```

Основная особенность SafeMySQL - это заполнители с подсказками типов. Этот подход расширяется за пределы простых скалярных данных, позволяя включать идентификаторы, массивы для оператора `IN`, а также массивы для запросов `INSERT` и `UPDATE`. Забудьте о сложностях при привязке имен полей или создании сложных запросов вручную.

Например, рассмотрим привязку имени поля без усилий:

```php
$field = $_POST['field'];
$value = $_POST['value'];
$sql   = "SELECT * FROM table WHERE ?n LIKE ?s";
$data  = $db->query($sql, $field, "%$value%");
```

Упрощение запросов, связанных с массивами для оператора `IN`:

```php
$array = [1, 2, 3];
$data  = $db->query("SELECT * FROM table WHERE id IN (?a)", $array);
```

Та же удобство распространяется и на сложные запросы, такие как `INSERT` и `UPDATE`.

SafeMySQL также предоставляет набор вспомогательных функций, которые делают вызовы к базе данных для повседневных задач быстрыми и простыми, сокращая их к одной или двум строкам кода.