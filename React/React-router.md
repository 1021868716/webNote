# 安装路由

单页应用的跳转应该使用路由，使用a标签跳转会重新加载html，页面会卡顿，需要加载更多资源

**安装react-router4.0**

```
npm install react-router-dom
```

```
// 哈希路由
<HashRouter>

// H5路由　　
<BrowserRouter>

// hash路由
<HashRouter>


// 路由规则
<Route>

// 路由选项
<Switch>

//  跳转按钮
<Link> || <NavLink>

// 自动跳转组件
<Redirect>
```





# BrowserRouter

**BrowserRouter组件**决定路由组件在哪渲染

Route组件代表了一个个组件元素，符合如果当前路径和path匹配就会被渲染在页面之上，exact属性表示必须完全匹配才渲染如果没有，`'/detail'`中也有'/'，会导致输入第二个时，第一个也会被渲染，component属性代表渲染哪个页面组件。

`/detail/: blogid`  表示detail路径地址后面还需要一个logid参数，这种形式条抓的路由上，被跳转到的组件的props中有一个match属性，match下有一个params对象属性，里面存放了冒号携带的参数

```jsx
import { BrowserRouter, Route } from 'react-router-dom'
import Home from './pages/home'
import Detail from './pages/detail'

/*访问那个路由渲染哪个路由组件*/
/*exact表示必须完全匹配才渲染，必须路由完全相等才能渲染，如果没有，'/detail'中也有'/'，会导致输入第二个时，第一个也会被渲染*/
<BrowserRouter>  {/* route展示标签 */}
  <div>
     <Route path="/" exact component={Home}></Route>
     {/* 等价于
     		<Route path="/" exact> <Home/> </Route>
     */}
     <Route path="/detail/:blogid" exact component={Detail} ></Route>
	</div>
</BrowserRouter>
```





# Link

组件中使用Link进行跳转

```react
import { Link } from 'react-router-dom'
...
<Link to={'/detail/' + props.blogId}></Link>
```

当我们使用react-router时，当我们从A页面进入B页面的时候，由于共享了同一个history，导致浏览的位置也被记录下来的，这样A页面滑动到底部了，进入B页面也会停留在底部，显然不符合我们的浏览习惯。

也可以使用js代码进行跳转

```react
import { useHistory } from 'react-router-dom'
function Trash() {
	let history = use useHistory()
	function handleClick() {
	  history.push('/') // 使用js跳转路由
	}
	return (
		<div>
			<Button type='primary' onClick = {handleClick}>回到首页</Button>
		</div>
	)
}
```







# 动态路由

```jsx
<Link to={'/detail/' + props.blogId}></Link>
```

/detail/1

这种形式条抓的路由上，被跳转到的组件的props中有一个match属性，match下有一个params对象属性，里面存放了冒号携带的参数

```jsx
<Link to={'/detail?id=' + props.blogId}></Link>
```

/detail?id=1

这中写法Route组件的path应该改回'/detail'，这种写法是比较常见的

```jsx
<Route path="/detail" exact component={Detail} ></Route>
```

这种形式跳转路由，id参数在props的location对象，location对象下的search属性保存了?开始之后的字符串，需要手动解析这个字符串。



也可以利用to使用对象来传参,to对象传递，参数被放到this.props.history.location.query中

```jsx
<Link to={{pathname='/detail', query:{props.blogId}}}></Link>
```





# 自动渲染路由

```
import { Redirect } from 'react-touter-dom'
...
这个路由会渲染目标组件，普通路由Link用于点击后跳转
这个路由无需操作，直接就渲染目标组件
<Redirect to='/'/>
```





# 懒加载路由

利用Suspense，lazy

```jsx
import {BrowserRouter as Router, Route, Switch}
import React, { Supense, lazy } from 'react'
const Home = lazy(() => import('./routes/Home'))
const About = lazy(() => import('./routes/About'))
...
<BrowserRouter>
	<Supense fallback={<div>Loading</div>}
  	<Switch>
			<Route path="/" exact component={Home}></Route>
			<Route path="/detail/:blogid" exact component={Detail} ></Route>
		</Switch>
	</Supense>
</BrowserRouter>
```



