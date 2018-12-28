密码设置改成大小写字母加数字限制在50位一下：//密码 Admin123456
note
=================================================

重新拉取的时候记得把后台映射改到本地来测试
======================================================================

埋点
请求一个接口上送一个字段就可以

======================================================================

@connect
将 model 和 component 串联起来
@connect(({ production, monitor, project, price, loading }) => ({

project：这个参数就是[models/project.js]里面，[project/reducers/]的返回值。

  production,
  monitor,
  project,
  price,
  loadingDetail: loading.effects['project/fetchProjectDetail'],
  loadingPercent: loading.effects['project/fetchProjectDataPercent'],
  loadingProduction: loading.effects['production/fetchAggregationData'],
  loadingEnergy: loading.effects['production/fetchEnergyData'],
}))

=======================================================================

this.props.dispatch({
// type:找的是analyse.js下面的fetchAnalogyData方法

  type: 'analyse/fetchAnalogyData',
  params: {
    id: nextProps.monitor.currentMoni
    params: {
      date_type: this.state.date_type
      date: this.state.date,
      key: this.state.key,
      not_aggregate: this.state.not_a
    }
  }
})



=======================================================================

进度条使用react 的进度条

=======================================================================
react在循环的时候会报出 warning

在浏览器中打开后，没有警告，页面可以正常工作。

但是，这个key在传递数据的时候根本就没有用，设置了不是很多余吗？
有的人说：

 react的key关乎到react的dom-diff算法 react中对于dom的操作是根据生成的data-reactid进行绑定的，添加key可以保证dom结构的完整性，而不会根据react自己对dom标记的key进行重新分配 react每次决定重新渲染的时候，几乎完全是根据data-reactid来决定的，最重要的是这个机制

dom-diff是指： 所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实 DOM上。它可以极大提高网页的性能表现。


=======================================================================

我理解的权限验证
登录时设置token
在请求的时候验证一个token，token里面设置限制字段，如果没有这个字段就无法调取这个权限


=======================================================================

前端安全性问题


一、随着前端的快速发展，各种技术不断更新，但是前端的安全问题也值得我们重视，不要等到项目上线之后才去重视安全问题，到时候被黑客攻击的时候一切都太晚了。

二、本文将讲述前端的六大安全问题，是平常比较常见的安全问题，当然如果还有其他必要重要的安全问题大家可以帮忙补充：

1、XSS（Cross-Site Scripting）脚本攻击漏洞；

2、CSRF（Cross-sit request forgery）漏洞；

3、iframe安全隐患问题；

4、本地存储数据问题；

5、第三方依赖的安全性问题；

6.HTTPS加密传输数据；

下面将对这些问题进行分享说明。

三、XSS（Cross-Site Scripting）脚本攻击漏洞

    XSS是前端谈论最多的安全问题，是通过在你的输入文本当中或者这HTML标签当中插入js脚本进行攻击，比如会在你的a标签或者img标签之前插入一些脚本文件就能攻击到你的网站，所有在用HTML去切入到div的时候一定要注意，或者长串的字符串嵌入到a标签的时候。

解决办法：

1：如果要使用HTML进行转换内容的时候，写代码时改为innerText而不用innerHTML，或者把<script><iframe>等标签替换掉；

```
 var HtmlUtil = {
     /*1.用浏览器内部转换器实现html转码*/
     htmlEncode:function (html){
         //1.首先动态创建一个容器标签元素，如DIV
         var temp = document.createElement ("div");
         //2.然后将要转换的字符串设置为这个元素的innerText(ie支持)或者textContent(火狐，google支持)
         (temp.textContent != undefined ) ? (temp.textContent = html) : (temp.innerText = html);
         //3.最后返回这个元素的innerHTML，即得到经过HTML编码转换的字符串了
         var output = temp.innerHTML;
         temp = null;
         return output;
     },
     /*2.用浏览器内部转换器实现html解码*/
     htmlDecode:function (text){
         //1.首先动态创建一个容器标签元素，如DIV
         var temp = document.createElement("div");
         //2.然后将要转换的字符串设置为这个元素的innerHTML(ie，火狐，google都支持)
         temp.innerHTML = text;
        //3.最后返回这个元素的innerText(ie支持)或者textContent(火狐，google支持)，即得到经过HTML解码的字符串了。
        var output = temp.innerText || temp.textContent;
         temp = null;
        return output;
     }
 };
```
2.对一些切入标签的字符串进行转义：

```
var HtmlUtil = {
      /*1.用正则表达式实现html转码*/
      htmlEncodeByRegExp:function (str){
           var s = "";
           if(str.length == 0) return "";
           s = str.replace(/&/g,"&amp;");
           s = s.replace(/</g,"&lt;");
          s = s.replace(/>/g,"&gt;");
          s = s.replace(/ /g,"&nbsp;");
          s = s.replace(/\'/g,"&#39;");
          s = s.replace(/\"/g,"&quot;");
          return s;
    },
    /*2.用正则表达式实现html解码*/
    htmlDecodeByRegExp:function (str){
          var s = "";
          if(str.length == 0) return "";
          s = str.replace(/&amp;/g,"&");
          s = s.replace(/&lt;/g,"<");
          s = s.replace(/&gt;/g,">");
          s = s.replace(/&nbsp;/g," ");
          s = s.replace(/&#39;/g,"\'");
          s = s.replace(/&quot;/g,"\"");
          return s;
        }
    };
```
四、CSRF（Cross-sit request forgery）漏洞

CSRF也称为跨站请求伪造，其实就是对网站中的一些表单提交行为被黑客利用。比如你的网站登录的时候存到cookie的一些个人信息，当你访问黑客的网站有一段相同代码隐藏div，但你点击的时候就会导致你的网站被登出或者被登录，就是在对别的网站就行操作的时候会对你之前访问的网站发送请求。

解决办法：

1.增加token验证.因为cookie发送请求的时候会自动增加上，但是token却不会，这样就避免了攻击

2.Referer验证。页面来源的判断

五、iframe安全隐患问题

有时候前端页面为了显示别人的网站或者一些组件的时候，就用iframe来引入进来，比如嵌入一些广告等等。但是有些iframe安全性我们无法去评估测试，有时候会携带一些第三方的插件啊，或者嵌入了一下不安全的脚本啊，这些都是值得我们去考虑的。

解决办法：

1.使用安全的网站进行嵌入；

2.在iframe添加一个叫sandbox的属性，浏览器会对iframe内容进行严格的控制，详细了解可以看看相关的API接口文档。

六、本地存储数据问题

很多开发者为了方便，把一些个人信息不经加密直接存到本地或者cookie，这样是非常不安全的，黑客们可以很容易就拿到用户的信息，所有在放到cookie中的信息或者localStorage里的信息要进行加密，加密可以自己定义一些加密方法或者网上寻找一些加密的插件，或者用base64进行多次加密然后再多次解码，这样就比较安全了。

七、第三方依赖安全隐患

现如今的项目开发，很多都喜欢用别人写好的框架，为了方便快捷，很快的就搭建起项目，自己写的代码不到20%，过多的用第三方依赖或者插件，一方面会影响性能问题，另一方面第三方的依赖或者插件存在很多安全性问题，也会存在这样那样的漏洞，所以使用起来得谨慎。

解决办法：手动去检查那些依赖的安全性问题基本是不可能的，最好是利用一些自动化的工具进行扫描过后再用，比如NSP(Node Security Platform)，Snyk等等。

八、HTTPS加密传输数据

在浏览器对服务器访问或者请求的过程中，会经过很多的协议或者步骤，当其中的某一步被黑客拦截的时候，如果信息没有加密，就会很容易被盗取。所以接口请求以及网站部署等最好进行HTTPS加密，这样防止被人盗取数据。


=======================================================================
/* BasicLayout */

react-intl ：
为React 做国际化这个库提供了 React 组件和Api两种方式来格式化日期，数字和字符串等。


=====================================================================


navigator.language 和 navigator.browserLanguage;

首先查看一下 navigator 对象中的这几个与 language 相关的属性。

navigator 对象包含有关浏览器的信息。没有应用于 navigator 对象的公开标准，不过所有浏览器都支持该对象。但是其内部一些属性及其返回值在各浏览器并不统一。

language：返回当前的浏览器语言（来自 Mozilla Developer Center）
userLanguage：返回操作系统设定的自然语言（来自 MSDN）
browserLanguage：返回当前的浏览器语言（来自 MSDN）
systemLanguage：返回当前操作系统的缺省语言（来自 MSDN）
关于 navigator 对象的更多资料，请参见：MSDN、Mozilla Developer Center。

对于浏览器，Mozilla Developer Center 中的 language 属性与 MSDN 中的 browserLanguage 属性描述很像。


解决方案
可以使用下面的代码获取当前浏览器语言：

(navigator.language || navigator.browserLanguage).toLowerCase()


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

toLowerCase
toLowerCase() 方法用于把字符串转换为小写。

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


==========================================================================

Component还是PureComponent

Component和PureComponent有一个不同点
除了为你提供了一个具有浅比较的shouldComponentUpdate方法，PureComponent和Component基本上完全相同。当props或者state改变时，PureComponent将对props和state进行浅比较。另一方面，Component不会比较当前和下个状态的props和state。因此，每当shouldComponentUpdate被调用时，组件默认的会重新渲染。

浅比较101
当把之前和下一个的props和state作比较，浅比较将检查原始值是否有相同的值（例如：1 == 1或者ture==true）,数组和对象引用是否相同。

从不改变
您可能已经听说过，不要在props和state中改变对象和数组，如果你在你的父组件中改变对象，你的“pure”子组件不将更新。虽然值已经被改变，但是子组件比较的是之前props的引用是否相同，所以不会检测到不同。

因此，你可以通过使用es6的assign方法或者数组的扩展运算符或者使用第三方库，强制返回一个新的对象。

存在性能问题？
比较原始值值和对象引用是低耗时操作。如果你有一列子对象并且其中一个子对象更新，对它们的props和state进行检查要比重新渲染每一个子节点要快的多。

其它解决办法
不要在render的函数中绑定值
假设你有一个项目列表，每个项目都传递一个唯一的参数到父方法。为了绑定参数，你可能会这么做：

<CommentItem likeComment={() => this.likeComment(user.id)} />
这个问题会导致每次父组件render方法被调用时，一个新的函数被创建，已将其传入likeComment。这会有一个改变每个子组件props的副作用，它将会造成他们全部重新渲染，即使数据本身没有发生变化。

为了解决这个问题，只需要将父组件的原型方法的引用传递给子组件。子组件的likeComment属性将总是有相同的引用，这样就不会造成不必要的重新渲染。

<CommentItem likeComment={this.likeComment} userID={user.id} />
然后再子组件中创建一个引用了传入属性的类方法：

class CommentItem extends PureComponent {
  ...
  handleLike() {
    this.props.likeComment(this.props.userID)
  }
  ...
}
不要在render方法里派生数据
考虑一下你的配置组件将从一系列文章中展示用户最喜欢的十篇文章。

render() {
  const { posts } = this.props
  const topTen = posts.sort((a, b) => b.likes - a.likes).slice(0, 9)
  return //...
}
每次组件重新渲染时topTen都将有一个新的引用，即使posts没有改变并且派生数据也是相同的。这将造成列表不必要的重新渲染。

你可以通过缓存你的派生数据来解决这个问题。例如，设置派生数据在你的组件state中，仅当posts更新时它才更新。

componentWillMount() {
  this.setTopTenPosts(this.props.posts)
}
componentWillReceiveProps(nextProps) {
  if (this.props.posts !== nextProps.posts) {
    this.setTopTenPosts(nextProps)
  }
}
setTopTenPosts(posts) {
  this.setState({
    topTen: posts.sort((a, b) => b.likes - a.likes).slice(0, 9)
  })
}
如果你正在使用Redux，可以考虑使用reselect来创建"selectors"来组合和缓存派生数据。

==========================================================================


------------------------------------------------------------------------

 overview

react-intl多语言中<FormattedMessage />组件用法
因项目需要，需要根据不同地区当前用户选择的语言， 加载不同的语言文件从而实现国际化。

使用这个组件的时候，我们这么写：
```
<FormattedMessage
    id='app.greeting'
    description='say hello to Howard'
    defaultMessage='Hello, Howard!'
    />
```
- id指代的是这个字符串在locale配置文件中的属性名，description指的是对于这个位置替代的字符串的描述，便于维护代码，不写的话也不会影响输出的结果，当在locale配置文件中没有找到这个id的时候，输出的结果就是defaultMessage的值。

关于react-intl中的<FormattedMessage />组件用法：
首先创建需要翻译的国家语言的js或者json文件，比如：

en_CN.json:

```
{
  "i18n.hello": "Hello, this is i18n",
  "i18n.name": "my name is {name}"
}

```
zh_CN.json:

```
{
  "i18n.hello": "你好，这是i18n",
  "i18n.name": "我的名字是 {name}"
}

```
然后在需要翻译的组件中引入<FormattedMessage />
```
import {FormattedMessage} from 'react-intl';
```

基础用法：
以id属性的值为索引——索引到自定义的映射表：

<FormattedMessage id="i18n.hello" defaultMessage="Hello, this is i18n"/>
其中defaultMessage为id对应的属性值找不到时默认显示的语句。

动态传值:

<FormattedMessage id={i18n.name} values={{name: <b>{name}</b>}} />
在定义i18n.name的模板里用到了{name}，代表可以动态传值，这样可以通过<FormattedMessage />中的 values 属性传一个JSON对象来动态显示我们的内容了。这里要注意是values而不是value!!!!

=======================================================================================


-----------------------------------------------------
modle ：effect
put

用于触发 action 。

yield put({ type: 'todos/add', payload: 'Learn Dva' });

call

用于调用异步逻辑，支持 promise 。

const result = yield call(fetch, '/todos');

select

用于从 state 里获取数据。

const todos = yield select(state => state.todos);




modous pfuncin


============================================================

问题
overview 能耗占比分析 选择监控点是如果切换项目可能导致之前项目的id残留在库里，然后显示在选项里

同比分析里面的 选择年份 会重新渲染 不要放到render里
