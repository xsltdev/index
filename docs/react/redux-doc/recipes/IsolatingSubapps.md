# Изолирование субприложений Redux

Предcтавьте себе "большое" приложение (содержащееся в компоненте `<BigApp>`), 
которое включает в себя меньшие субприложения (содержащиеся в компонентах `<SubApp>`)

```js
import React, { Component } from 'react'
import SubApp from './subapp'

class BigApp extends Component {
  render() {
    return (
      <div>
        <SubApp />
        <SubApp />
        <SubApp />
      </div>
    )
  }
}
```

Эти компоненты `<SubApp>` будут полностью независимы. Они не будут разделять `состояние` или `экшены`, 
не будут видеть друг друга или взаимодействовать между собой.

Не стоит смешивать такой подход со стандартной композицией `редьюсеров` в Redux.
Для обычных веб-приложений лучше всего придерживаться композиции редьюсеров.
Попробуйте использовать подход субприложений для "концентраторов продуктов", дашбордов или enterprise-приложений, которые группируют несоизмеримые вещи в единый пакет приложения. 

Он также может быть полезен для больших команд, которые делят ответственность по продуктам или фичам.
Такие команды могут поставлять отдельные субприложения по отдельности или в рамках общей оболочки продукта.

Ниже представлен корневой компонент субприложения, соединённый с Redux. 
Как и любой другой компонент, он может рендерить другие компоненты, как соединённые с Redux, так и нет.
Обычно мы хотим отрендерить корневой компонент внутри `<Provider>`.

```js
class App extends Component { ... }
export default connect(mapStateToProps)(App)
```

При этом, нам не нужно вызывать `ReactDOM.render(<Provider><App /></Provider>)`,
если мы заинтересованы в сокрытии того, что наш компонент-субприложение использует Redux.
Возможно, мы хотим запустить множество таких субприложений внутри "большого" приложения,
и чтобы каждый из них был "чёрным ящиком", а Redux был бы всего лишь деталью их реализации.

Для того, чтобы спрятать Redux за React API, мы можем обернуть его в специальный компонент,
который инициализирует `стор` в конструкторе:

```js
import React, { Component } from 'react'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import reducer from './reducers'
import App from './App'

class SubApp extends Component {
  constructor(props) {
    super(props)
    this.store = createStore(reducer)
  }

  render() {
    return (
      <Provider store={this.store}>
        <App />
      </Provider>
    )
  }
}
```

Таким образом, каждый экземпляр субприложения будет полностью независимым.

Этот паттерн *не* рекомендуется для частей одного и того же приложения, которые имеют общие данные.
Тем не менее, он может быть полезен в случае, когда "большое" приложение не имеет доступа к внутренностям субприложений,
а мы бы хотели оставить факт использования ими Redux  деталью реализации.
В этом случае, каждый экземпляр компонента-субприложения будет иметь собственный `стора`, и они не будут "знать" друг о друге.
