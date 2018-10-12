#react-amap
- react-amap 的导入
react-amap 提供两种方式，一种是直接引用 CDN 脚本。

<script src="https://unpkg.com/react-amap@0.2.5/dist/react-amap.min.js"></script>
其中的 0.2.5 是版本号，如果有更新版本记得替换成最新版本号。引入脚本后，会在全局暴露一个 ReactAMAP 变量；然后通过这个变量引用你需要的组件即可。

var Map = ReactAMAP.map;
var Marker = ReactAMAP.marker;
当然更推荐的方式是用 ES6 的语法方式来使用，首先 npm 安装组件。

npm install —save react-amap
然后在需要的地方导入就行了。

import { Map, Marker } from 'react-amap';
或者按组件单个引用（如果你用 webpack 打包代码，会减少打包体积）。

import Map from 'react-amap/lib/map';
import Marker from 'react-amap/lib/marker';
react-amap 的使用
要使用 react-amap，首先我们需要在高德网站70申请一个开发者 Key，放在手边；待会会用到。

目前 react-amap 提供了 Map，Marker，Markers，Polygon，Polyline，InfoWindow，GroundImage 等 11 个常用地图组件：

Map 组件是最基本的，用于显示地图；其他的组件都要依赖它；
Marker 和 Markers 组件用于在地图上显示标记点；react-amap 在高德原生的标记点实例上，通过一层封装可以很方便的在地图上显示单个或者大批量的标记点；而且定义外观也极其方便；
Polyline 和 Polygon 组件分别用于在地图上绘制折线和多边形；
PolyEditor 用于给 Polygon 和 Polyline 组件加上编辑功能；
Circle 用于在地图上绘制一个圆形；
CircleEditor 用于给 Circle 提供编辑功能；
InfoWindow 用于在地图上显示信息窗体；在 react-amap 的封装下，可以直接以 JSX 语法来写窗体内容，非常方便；
GroundImage 用于在地图上的指定区域内显示一个地图。
本文主要介绍 Map、Marker 和 Markers 的使用；其他的组件用法可以参考 react-amap 的文档99。

一、用 Map 组件显示地图
Map 组件可以在某个 div 容器中显示一个地图，是 react-amap 中其他组件的基础。

Map 组件的使用
考虑最基础的用法，显示一个地图。

import { Map } from 'react-amap';
const MyApp = (<div style={{width: 800, height: 600}}>
  <Map amapkey={YOUR_AMAP_KEY} />
</div>)
ReactDOM.render(MyApp, document.getElementById('app'));
以上代码中的 YOUR_AMAP_KEY 就是我们申请的 Key；作为 Map 组件的 amapkey 属性传进去。
不过这样的话，如果要在多个地方使用地图，就要每次都传入；所以也可以预先将 Key 定义在全局变量 window.amapkey 变量上，这样不用再传入 Map 组件了。（后面的例子中，都省略了 Key；在实际项目使用时，可以自己在两种方式中选择。）

另外，Map 组件的父元素必须有宽度和高度。

Map 组件的属性
我们可以给 Map 传入属性，比如 zoom、center；这些详细的属性配置参考 Map 文档83。

const position = { longitude: 120, latitude: 32 }
// ...other code
<Map center={ position } zoom={5} />
Map 组件绑定事件
我们可以通过 events 属性给 Map 绑定事件，比如：

const mapEvents = {
  created: (mapInstance) => {
    console.log(mapInstance);
  },
  click: () => {
    console.log('You clicked map');
  },
}
// ...other code
<Map events={mapEvents} {...otherProps}/>
用户左键单击地图后，就会触发 click 事件；还有其他的事件可以参考高德地图官网关于 Map 可绑定事件30的说明。

注意例子中的 created 事件，并非高德提供，是 react-amap 提供的；在地图实例创建成功后调用，其中事件回调的第一个参数是高德的地图实例；这样即使有一些需求 react-amap 无法满足，我们可以在获取实例之后，自己根据高德官网的接口文档进行操作。react-amap 中的其他组件也可以按照这种方式来获得相应实例，方便我们进行扩展。

Map 组件的子组件
react-amap 中，其他的组件（如 Marker）都要作为 Map 的组件使用，毕竟先要有地图才能在地图上添加标记。

<Map {....mapProps}>
  <Marker {...markerProps } />
</Map>
不仅这样，Map 组件还接受其他任意组件作为子组件；通常用于在地图上放置一些自定义的元素；比如：

import { Button } from 'antd';
import { Map, Marker } from 'react-amap';

// ...other code

<Map>
  <Marker position={this.position} />
  <div className="customLayer" style={styleA}>
    <h4>A Custom Layer</h4>
    <p>Current Center Is: {this.state.center}</p>
  </div>
  <div className="customLayer" style={styleB}>
    <p> Another Custom Layer</p>
    <Button onClick={()=>{alert('You Clicked!')}}>An Ant Design Button</Button>
  </div>
</Map>
利用react-amap显示地图

二、用 Marker 组件在地图上显示标记点
react-amap 提供 Marker 组件可以方便得在地图上显示坐标点；而且你可以非常灵活的定义坐标点的外观。

Marker 组件定义坐标点的外观的方式有多种。

1. 高德地图默认的标记样式
不需要添加额外的属性，自动用高德地图默认的样式来绘制标记。

import { Map, Marker } from 'react-amap';
const position = { longitude: 120, latitude: 30 }
const MyApp = (<div style={{width: 800, height: 600}}>
  <Map>
    <Marker position={position} />
  </Map>
</div>)
2. 用 render 方法渲染外观
我们可以将一个函数作为 render 属性传入到 Marker 组件中，这个 render 函数返回一个 React 组件，可以渲染标记点的初始状态；（特别的：如果这个函数返回 false，则会采用高德默认的标记点外观。）

在后面如果需要随着用户交互使标记点外观发生改变，可以首先获取高德地图原生的 Marker 的实例；react-amap 在这个实例上挂载了一个 render 方法；通过这个 render 方法可以动态的改变标记点的外观。

import { Map, Marker } from 'react-amap';

const position = { longitude: 120, latitude: 30 };
const renderMarker = (extData) => {
  return <div style={styleA}>{extData.myLabel}</div>
}
const renderMarkerHover = (extData) => {
  return <div style={styleB}>{extData.myLabel}</div>
}
const markerEvents = {
  mouseover(e, marker){
    // 鼠标移入该标记时，使用 renderMarkerHover 渲染外观
    marker.render(renderMarkerHover);
  },
  mouseout(e, marker){
    // 鼠标移出该标记时，使用 renderMarker 渲染外观
    marker.render(renderMarker);
  }
}

const MyApp = (<div style={{width: 800, height: 600}}>
  <Map
    plugins={['ToolBar']}
    center={position}
    zoom={6}
  >
    <Marker
      position={position}
      { /* 这里 renderMarker 会渲染标记点的初始外观 */}
      render={renderMarker}
      events={markerEvents}
      extData={{ myLabel: 'A'}}
    />
  </Map>
</div>)


3. 以 JSX 语法直接写 Marker 的子组件作为外观
import { Map, Marker } from 'react-amap';
const position = { longitude: 120, latitude: 30 };

const MyApp = (<div style={{width: 800, height: 600}}>
  <Map
    plugins={['ToolBar']}
    center={{longitude: 121, latitude: 34}}
    zoom={6}
  >
    <Marker position={{longitude: 121, latitude: 35 }} >
      A
    </Marker>
    <Marker position={{longitude: 122, latitude: 35 }} >
      <div style={styleB}>B</div>
    </Marker>
    <Marker position={{longitude: 120, latitude: 34 }} >
      <div style={styleC}></div>
    </Marker>
    <Marker position={{longitude: 121, latitude: 34 }} >
      <div>A MARKER</div>
      <div>WITH A LOT OF TEXT IN</div>
      <div>OBVIOUSLY NOT LIKE A MARKER</div>
    </Marker>
  </Map>
</div>)


三、使用 Markers 组件显示多个坐标点
如果要显示多个坐标点，既可以采用多个 Marker 组件，也可以用一个 Markers 组件来实现。

react-amap 的 Markers 组件做了一个很实用的封装；当标记点在地图上过于集中导致很难分辨时，可以汇聚成一个聚合点，点击这个聚合点，所有的点会展开在一个弹窗中；要启用这个功能，只要在使用 Markers 组件时，传入 useCluster 属性并设置值为true 就可以了。



另外 Markers 组件也提供了与 Marker 的 render 方法一样的方式来渲染标记点的外观。关于 Markers 的详细使用案例，参考 react-amap 官网 Markers 组件文档90。

总结
高德地图原生提供的接口是纯 JavaScript 的，react-amap 把高德地图接口与 React 框架结合，使地图组件也可以按照 React 的方式来使用。
