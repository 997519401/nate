/* BasicLayout */

react-intl ：
为React 做国际化这个库提供了 React 组件和Api两种方式来格式化日期，数字和字符串等。

=====================================================================

#dva-loading 实践用法

- dva-loading 只是提供当前异步加载方法的状态（异步加载中状态为 true，异步加载完成状态为 false），对应加载样式由各自组件自己控制，如：Antd 中 Table 组件自身的 loading 属性。

- 作用
  该组件仅仅监听异步加载状态，这从它的调用方式就可以看出来 const isLoading = loading.effects['user/query']，其中 user/query 是 model 中的异步请求方法。

  loading 在异步请求发出那一刻会持续监听该异步请求方法的状态，在异步请求结束之前 isLoading 的值一直是 true，当此次异步请求结束时 isLoading 的值变成 false，同时 loading 对象停止监听。

- 配置
  dva 项目的 index.js 文件：

  ```
  import createLoading from 'dva-loading';

  const app = dva();

  app.use(createLoading());

  ```

  配置完成后，在任何一个 dva 的 routes 组件中就都会有一个 loading 对象，如果你对 dva 稍有了解的话，应该不难知道 它在哪。比如下面这行代码中的 loading 对象就是由于上面的配置。

```
loading: {
  global: false,
  models: {app: false},
  effects: {app: false}
}

```
 - loading 有三个方法，其中 loading.effects['user/query'] 为监听单一异步请求状态，当页面处于异步加载状态时该值为 true，当页面加载完成时，自动监听该值为 false。

 - 如果同时发出若干个异步请求，需求是当所有异步请求都响应才做下一步操作，可以使用 loading.global() 方法，该方法监听所有异步请求的状态。


- 怎么用

```
// src > models >user.js
export default {
  namespace: 'user',
  state: {
    userList: [],      // 存放用户列表
  },
  effects: {
    * query ({ payload = {} }, { call, put }) {
      // 获取用户列表，赋值给 userList
      // 使用 axios 或者 ajax 请求后台返回数据
      const result = axios.request('xxx/xxx');
      // 调用 reducers 中的 updateState 方法改变 state 中 userList 的值
      yield put({ type: 'updateState', payload: { userList: result.data });
    }
  },
  reducers: {
    updateState (state, { payload }) {
      return { ...state, ...payload };
    },
  },
}

```

```
// src > routes > user.js
import React from 'react';
import { connect } from 'dva';
import { Table } from 'antd';

const User = ({ dispatch, user, loading }) => {
  /**
    根据 loading.effects 对象判断当前异步加载是否完成，并将该值传递给 Table 组件的 loading 属性，
    Table 组件会自己控制加载样式。dva-loading 在这里的作用只是提供异步加载的状态，
    具体加载样式由对应组件自己提供。
  */
  const isLoading = loading.effects['user/query']
  const { userList } = user

  return (
    <Table
      dataSource={userList}
      loading={isLoading}
      rowKey={record => record.id}
    />
  );
}

export default connect(({ user, loading }) => ({ user, loading }))(User);
```

===============================================================

#dva-dynamic
- 其中有一个解决组件动态加载的问题dva/dynamic（工具类）方法
```
import dynamic from 'dva/dynamic'
{
              routeArr.map((item, key) => {
                return <Route key={key} exact path={item.path} component={dynamic({  //保证路由的唯一性 exact   key
                  app,
                  model: item.models,
                  component: item.component,
                })} />
              })

```

- 其中有三个参数
  app： dva实例
  models： 返回Promise数组的函数， Promise返回 dva model
  component : 返回Promise的函数 Promise返回ReactComponent


react-router(Switch)
 - Switch只会渲染一个组件，多个组件如果没有用Switch包着，有符合路径的就会加载


========================================================================

Path-to-RegExp模块
将路径字符串（如/ user /：name）转换为正则表达式。


========================================================================

some() 方法用于检测数组中的元素是否满足指定条件（函数提供）。

some() 方法会依次执行数组的每个元素：

如果有一个元素满足条件，则表达式返回true , 剩余的元素不会再执行检测。
如果没有满足条件的元素，则返回false。
注意： some() 不会对空数组进行检测。

注意： some() 不会改变原始数组。

========================================================================

react-document-title
因为react是单页应用，所以我们可能需要根据不同的路由改变文档的title;react-document-title组件可以实现:
提供了一种document.title在单页面应用程序中指定的声明方式。
这个组件也可以在服务器端使用。

========================================================================

enquire.js。
在CSS中使用Media Query已经变得家常便饭，但如果需要JavaScript参与到这一过程中，则不是非常容易了。好在我们现在有了enquire.js。

API:

　　enquire.register(mediaQuery, handler).

　　　　mediaQuery: 字符串，需要响应的媒体。

　　　　handler: 函数或对象，

　　enquire.unregister(mediaQuery[, handler])

　　　　mediaQuery: 字符串，需要注销响应的媒体。

　　　　handler: 函数或对象，可不写，一旦写了，只有这个handler被注销

　　handler Object:

　　　　destory: 当handler是unregister状态时响应。

　　　　deferSetup: 布尔值。默认false,设置为true时，推迟执行setup里的回调，直到第一次match时才执行。

　　　　setup: 函数。

　　　　　　一般情况下，如果当match时需要请求数据，改变UI。变成unmatch不需要显示UI,再变回match,又要再一次请求数据,显示UI,请求数据就会有很多次。setup的存在就是让数据只需要请求一次，将数据与UI的显示分离开来。即在setup中请求数据,在match中显示UI,setup的回调是只会执行一次的。

　　　　match: 函数。媒体匹配时的回调。

　　　　unmatch: 函数。媒体不匹配时的回调。　　　

主要使用的方法为 register。

```
enquire.register('screen and (max-width: 45em)', {
  // 以下为使用的五种选择

// 当媒体查询与上述匹配时，即当窗口宽度小于45em时的响应
  match: function() {},
// 当窗口从匹配调整到不匹配以后的响应
  unmatch: function() {},
// 只会响应一次，when the handler is registered?
  setup: function() {},
// 默认为false,一旦被设置为true,defers execution of the setup function until the first time the media query is matched
deferSetup: true, destroy: function() {},
// triggered when handler is unregistered. Place cleanup code here
destroy: function() {},
});
```

inquire允许在register中有多个处理程序，
```
// 可以把各种回调放在一个数组中
enquire.register('screen and min-width: 45em', [
  { match: function() { console.log('handler 1 matched'); } },
  { match: function() { console.log('handler 2 matched'); } }
]);

// 或者用以下的方式
const query = 'screen and (min-width: 45em)';
enquire.register(query, function() { console.log('handler 3 matched'); });
enquire.register(query, function() { console.log('handler 4 matched'); });
...
```

当存在很多种不同的屏幕宽度，每种宽度需要不同的布局时，
```
enquire
.register('screen and (max-width: 50em)', function() {
         // handler 1 matched
    });
.register('screen and (max-width: 40em)', function() {
        // handler 2 matched
    });
```

取消注册处理程序，
　　enquire提供了unregister方法去取消register，
```
const query1 = 'screen and (min-width: 40em)';
const query2 = 'screen and (min-width: 5oem)';
const handler1 = {
  match: function() {},
  destory: function() { console.log('handler 1 destoryed'); },
};
const handler2 = {
  match: function() {},
  unmatch: function() { console.log('handler 2 unmatched'); }
};

enquire.register(query1, handler1);
enquire.unregister(query1); // 'handler 1 destoryed'

enquire.register(query2, handler2);
enquire.unregister(query2, handler2); // 'handler 2 unmatched'
```

当unregister只有一个参数query时，匹配该query的handler将会全部被注销，即是说不再会有关于该query的任何操作和响应。

　　当unregister有两个参数query和handler时，注销指定query的指定handler。

　　当handler内有destory时执行destory内的回调，没有destory时，执行unmatch。

在项目中，通过match和unmatch去改变组件的状态，以控制组件是否显示或以怎样的形式显示来达到响应式布局。


========================================================================

getRoutes
获取完整的路线列表。

========================================================================

import { getMenuData } from '../common/menu';
格式化路由

===============================================================

JavaScript使用URLSearchParams获取查询字符串的值（query string）
基本用法

let params = new URLSearchParams(location.search.slice(1));
直接使用location.search需要把开始的问号"?"去掉。例子里使用slice(1)来去掉“?”。

使用URL构造

除了直接使用URLSearchParams构造查询参数外，也可以使用URL来构造

let params = (new URL(location)).searchParams;
获取/添加参数

构造出URLSearchParams后，可以使用

get(key)：获取参数值
set(key,value)：设置参数值
append(key,value)：添加参数值
polyfill

并不是所有的浏览器都支持URLSearchParams API，为了在多个浏览器支持，可以添加URLSearchParams polyfill 。



-----------------------------------------------------





