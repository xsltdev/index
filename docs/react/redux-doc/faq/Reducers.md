# Redux FAQ: Редьюсеры

## Содержание

- [Как мне передавать состояние(state) между двумя редьюсерами? Должен ли я использовать combineReducers?](#reducers-share-state)
- [Должен ли я использовать оператор switch для обработки экшенов (actions)?](#reducers-use-switch)



## Редьюсеры

<a id="reducers-share-state"></a>
### Как мне передавать состояние(state) между двумя редьюсерами? Должен ли я использовать `combineReducers`?

Рекомендуемая структура Redux-стора — это разделение объекта состояния на несколько “частей” или “областей” по ключу, и предоставление отдельной функции-редьюсера для управления каждой частью данных. Это похоже на то, как стандартная Flux структура имеет несколько независимых сторов, а Redux предоставляет утилиту [`комбинация редьюсеров (combineReducers)`](/docs/api/combineReducers.md) для упрощения реализации этого подхода. Однако, важно заметить, что `комбинация редьюсеров` *необязательна* — это просто функция для совместного использования имеющихся редьюсеров (по одному на каждую часть состояния) с простыми JavaScript-объектами для данных.

У многих пользователей возникает необходимость реализовать обмен данными между двумя редьюсерами, но `комбинация редьюсеров` не позволяет им сделать это. Существует несколько подходов для решения этой задачи:

* Если редьюсеру нужны данные из другой части состояния, то дерево состояния требуется переорганизовать так, чтобы один редьюсер охватывал больше данных.
* Вам может понадобиться написать некоторые стандартные функции для обработки некоторых из этих экшенов. Это требует обязательной замены `комбинации редьюсеров` на Вашу собственную функцию-редьюсер верхнего порядка. Вы также можете использовать такие утилиты, как [reduce-reducers](https://github.com/acdlite/reduce-reducers), для запуска `комбинации редьюсеров` для обработки большинства экшенов, но также можно запустить более специализированный редьюсер для конкретных экшенов, который скрещивает части состояния.
* [Асинхронные генераторы экшенов](/docs/advanced/AsyncActions.md#async-action-creators), такие как [redux-thunk](https://github.com/gaearon/redux-thunk), имеют доступ ко всему состоянию через `getState()`. Генератор экшенов может извлечь дополнительные данные из состояния и передать их в экшен, таким образом, каждый редьюсер имеет достаточно информации для обновления своей части состояния.

В общем, помните, что редьюсеры — это всего лишь функции. Вы можете организовать их и разделить по своему усмотрению, и вам следует разбить их на более мелкие переиспользуемые функции (“композиция редьюсеров”). Пока Вы это делаете, Вы можете передавать в нестандартном третьем аргументе необходимые для вычисления следующего состояния дополнительные данные от родительского редьюсера к дочернему. Только Вам надо убедиться, что соблюдается главное правило редьюсеров: `(state, action) => newState`, и состояние не изменяется напрямую.

#### Дополнительная информация

**Документация**
- [API: combineReducers](/docs/api/combineReducers.md)
- [Рецепты: Структурирование редьюсеров](/docs/recipes/StructuringReducers.md)

**Обсуждения**
- [#601: A concern on combineReducers, when an action is related to multiple reducers](https://github.com/reactjs/redux/issues/601)
- [#1400: Is passing top-level state object to branch reducer an anti-pattern?](https://github.com/reactjs/redux/issues/1400)
- [Stack Overflow: Accessing other parts of the state when using combined reducers?](http://stackoverflow.com/questions/34333979/accessing-other-parts-of-the-state-when-using-combined-reducers)
- [Stack Overflow: Reducing an entire subtree with redux combineReducers](http://stackoverflow.com/questions/34427851/reducing-an-entire-subtree-with-redux-combinereducers)
- [Sharing State Between Redux Reducers](https://invalidpatent.wordpress.com/2016/02/18/sharing-state-between-redux-reducers/)


<a id="reducers-use-switch"></a>
### Должен ли я использовать оператор `switch` для обработки экшенов (actions)

Нет, вы можете использовать любой подход, с помощью которого вы бы хотели реагировать на экшенов в редьюсере. Оператор `switch` является наиболее распространенным подходом, но так же отлично подойдет и оператор `if`, таблица соответствия функций или создание функции, которая абстрагирует все это. На самом деле, пока Redux требует, чтобы объект экшенов содержал поле `type`, логике вашего редьюсера даже не придется полагаться на то, чтобы управлять экшеном.
Тем не менее, стандартный подход, безусловно, с помощью инструкции `switch`  или таблицы поиска, основанной на `type`.


#### Дополнительная информация

**Документация**
- [Рецепты: Упрощение шаблона](/docs/recipes/ReducingBoilerplate.md)
- [Рецепты: Структурирование редьюсеров - Разделение логики редьюсера](/docs/recipes/reducers/SplittingReducerLogic.md)

**Обсуждения**
- [#883: take away the huge switch block](https://github.com/reactjs/redux/issues/883)
- [#1167: Reducer without switch](https://github.com/reactjs/redux/issues/1167)