## react－router－redux
- react－router－redux
 + 使用Redux去管理你的应用状态（state），使用React Router去管理路由。
   但是，这两个库不能协同工作。
   react-router-redux库可以协调这两个库。

 + 本库允许你使用React Router库中的api， 使用Redux库像平常一样去管理应用的状态state。 本库只是简单的加强了React Router库中history这个实例，以允许将history中接受到的变化反应到stae中去。
```
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider } from 'react-redux'
import { Router, hashHistory } from 'react-router'
import { syncHistoryWithStore } from 'react-router-redux'
// 利用react-router-redux提供的syncHistoryWithStore我们可以结合store同步导航事件
import routes from './router/routes.jsx'
import configureStore from './store'
import './static/style/base.css'

const store = configureStore();
const history = syncHistoryWithStore(hashHistory, store);
ReactDOM.render(
    <Provider store={store}>
        <Router history={history}>
            {routes}
        </Router>
    </Provider>,
    document.getElementById('app')
);
```

现在，你进行的所有页面导航和App导航，加强版的history会首先将新的路径通过Redux store传递，然后再通过React Router去更新组件树。

##怎样查看导航事件，比如进行导航事件的分析
只需要通过histroy.listen去监听加强版history即可。当store更新时，通过一个函数接受一个location参数。
```
const history = syncHistoryWithStore(browserHistory, store) history.listen(location => analyticsService.track(location.pathname))
```
对于系统中的其它事件，你可以通过Redux store中的middleware去查看任何dispatched到store里 action。


