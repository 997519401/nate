##react-router-transition

1. 官方方法（推荐）
https://github.com/reactjs/react-router/tree/master/examples/animations
```
//--js
import React from 'react';
import ReactCSSTransitionGroup from 'react-addons-css-transition-group';

class App extends React.Component {
  render() {
    return (
      <div className="app">
        <ReactCSSTransitionGroup
          component="div"
          className="transition-wrapper"
          transitionName="example"
          transitionEnterTimeout={500}
          transitionLeaveTimeout={500}>
          //注意这一部分很关键，不能直接使用{this.props.children}
          {React.cloneElement(this.props.children, {
            key: this.props.location.pathname
          })}
        </ReactCSSTransitionGroup>
      </div>
    );
  }
}

module.exports = App;

//--css
.example-enter {
  opacity: 0.01;
  transition: opacity .5s ease-in;
}

.example-enter.example-enter-active {
  opacity: 1;
}

.example-leave {
  opacity: 1;
  transition: opacity .5s ease-in;
}

.example-leave.example-leave-active {
  opacity: 0;
}

.transition-wrapper {
  height: 100%;
}
```
2. react-router-transition
https://github.com/maisano/react-router-transition

1. 安装
npm install --save react-router-transition
2. 使用
```
//--js
import React from 'react';
import RouteTransition from 'react-router-transition';

class App extends React.Component {
  render() {
    return (
      <div className="app">
        <RouteTransition
          pathname={this.props.location.pathname}
          atEnter={{ opacity: 0 }}
          atLeave={{ opacity: 0 }}
          atActive={{ opacity: 1 }}
          className="transition-wrapper">
          {this.props.children}
        </RouteTransition>
      </div>
    );
  }
}

module.exports = App;

//--css
html,
body,
#root,
.app,
.transition-wrapper,
.transition-wrapper>div {
  height: 100%;
}

//--生成的html结构
<div class='transition-wrapper'>
  <div style='opacity: 1'>
    <div class='app'>
      ...
    </div>
  </div>
</div>
```
3. 模块打包问题
目前npm上的react-router-transition模块有问题，package.json配置的入口文件为lib/react-router-transition.js，该文件为webpack编译打包后的文件，不能再次打包，所以实际使用时需要导入src/RouteTransition.js文件，结合webpack.config.js配置如下：
```
var node_modules_dir = path.join(__dirname, 'node_modules');

resolve: {
  extensions: ['', '.js', 'jsx'],
  alias: {
    'react-router-transition': path.resolve(node_modules_dir,'react-router-transition/src/RouteTransition.jsx')
  }
}
```


##组件header

###window.location.Reload()和window.location.href 区别
window.location.Reload()应该是刷新.【相当于 按页面刷新按钮】

(如果有数据提交的话，会提示是否提交的(是和否选项))

window.location.href=window.location.href;
是定向url提交数据

>他们最大区别是 是否有数据提交：

前者提交数据的刷新
后者没有提交的刷新


###首先介绍两个方法的语法：

reload 方法，该方法强迫浏览器刷新当前页面。
语法：location.reload([bForceGet])参数： bForceGet， 可选参数， 默认为 false，从客户端缓存里取当前页。 true, 则以GET 方式，从服务端取最新的页面, 相当于客户端点击 F5("刷新")

replace 方法，该方法通过指定URL替换当前缓存在历史里（客户端）的项目，因此当使用replace方法之后，你不能通过“前进”和“后退”来访问已经被替换的URL。
语法：location.replace(URL) 参数： URL

在实际应用的时候，重新刷新页面的时候，我们通常使用： location.reload() 或者是 history.go(0) 来做。因为这种做法就像是客户端点F5刷新页面，所以页面的method="post"的时候，会出现“网页过期”的提示。那是因为Session的安全保护机制。可以想到： 当调用 location.reload() 方法的时候， aspx页面此时在服务端内存里已经存在， 因此必定是 IsPostback 的。如果有这种应用： 我们需要重新加载该页面，也就是说我们期望页面能够在服务端重新被创建， 我们期望是 Not IsPostback 的。这里，location.replace() 就可以完成此任务。被replace的页面每次都在服务端重新生成。你可以这么写： location.replace(location.href)

=======================================================
<a onclick="javascript:window.location.href=window.location.href;">

<a onclick="javascript:window.location.reload();">

测试效果一样。表单没有提交。

<input type="submit" onclick="javascript:window.location.reload();" value="单击" id="btnVCode" />
<input type="submit" onclick="javascript:window.location.href=window.location.href;" value="单击" id="btnVCode" />

都提交数据



window.location.Reload()应该是刷新.(如果有数据提交的话，会提示是否提交的(是和否选项))
window.location.href=window.location.href; 是定向url提交数据

最好不要用location.reload()，而用location=location比较好，还有在模式窗口（showModalDialog和showModelessDialog）前者不能用。



reload参数有true和false，比较有意思?

避免重复提交：

    protected void Repeater1_ItemDataBound(object sender, RepeaterItemEventArgs e)
    {
        if (e.Item.ItemType == ListItemType.Item || e.Item.ItemType == ListItemType.AlternatingItem)
        {
            string nr = ((DataRowView)e.Item.DataItem).Row["GZZDS"].ToString();
            string id = ((DataRowView)e.Item.DataItem).Row["RZID"].ToString();
            string rid = ((DataRowView)e.Item.DataItem).Row["RWXH"].ToString();
            string link = "";

            if (nr == "")
            {
                link = "<a href='#' onclick=\"selectGuide2('BookDoc.aspx?type=2&Id=" + id + "&rid=" + rid + "&Rnd='+Math.random());location=location;\">选择指导书</a>";
            }
            else
            {
                string t = (ViewState["State"].ToString() == "Query" || ((DataRowView)e.Item.DataItem).Row["GZZT"].ToString() == "工作结束") ? "false" : "true";
                string path=((DataRowView)e.Item.DataItem).Row["GZZDSPath"].ToString();

                link = "<a href='#' onclick=\"EditWord('" + path + "'," + t + ")\">" + nr + "</a>";
            }

            ((Literal)e.Item.FindControl("Literal1")).Text = link;
        }
    }

 window.location.Reload()和window.location.href  window.location.Reload()应该是刷新.(如果有数据提交的话，会提示是否提交的(是和否选项))
window.location.href=window.location.href;
是定向url提交数据


是大的区别还是是否提交数据了



function refresh()
{
//刷新页面函数
//window.focus();刷新窗口
//document.execCommand("Refresh");刷新窗口
//self.location.reload();刷新当前窗口
parent.location.reload();刷新父窗口
//aaa.location.reload();弹出窗口刷新父窗口
}
使用window.location.replace() or window.location.href(), 来重新加载此页面不出现提示框



