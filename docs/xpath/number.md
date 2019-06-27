# number()

Функция **`number`** явным образом конвертирует свой аргумент в числовой тип. Если аргумент не указан, функции передается множество узлов, состоящее из единственного контекстного узла.

## Синтаксис

### XPath 1.0

```
number number( object? )
```

## Описание и примеры

Коротко напомним правила преобразования в числовой тип.

- Значения булевого типа преобразуются в `0` или `1` следующим образом: "ложь" преобразуется в `0`, "истина" в `1`.
- Строковое значение преобразуется в число, которое оно представляет.
- Множество узлов сначала преобразуется в строку, а затем, как строка в число. Фактически численным значением множества узлов является численное значение его первого узла.
- Объекты других типов преобразуются в число в соответствии с собственными правилами. Например, результирующий фрагмент дерева так же как и множество узлов сначала преобразуется к строке, а затем в численный формат.

### Примеры

```
number($to_be or not($to_be)) ? 1
```

Значение этого выражения будет `1`, поскольку `$to_be or not($to_be)` будет истинным вне зависимости от значения переменной `to_be`.

```
number(false()) ? 0
number('00015.0001000') ? 15.0001
number('.0001000') ? 0.0001
number('1.') ? 1
number('-.1') ? -0.1
number('-5') ? -5
```

## Ссылки

- [number()](https://developer.mozilla.org/en-US/docs/Web/XPath/Functions/number) на MDN