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


