# React-router

# 安装路由

单页应用的跳转应该使用路由，使用a标签跳转会重新加载html，页面会卡顿，需要加载更多资源

**安装react-router4.0**

```
npm install react-router-dom
```



常用组件

```
// 哈希路由
<HashRouter>

// H5路由　　
<BrowserRouter>

// hash路由
<HashRouter>

// 路由规则
<Route>

// 路由选项, 渲染第一个被location匹配到的并且作为子元素的<Route>或者<Redirect>
<Switch>

//  跳转按钮
<Link> || <NavLink>

// 自动跳转组件
<Redirect>
```



# BrowserRouter

**BrowserRouter组件**决定路由组件在哪渲染

Route组件代表了一个个组件元素，符合如果当前路径和path匹配就会被渲染在页面之上，exact属性表示必须完全匹配才渲染如果没有，`'/detail'`中也有'/'，会导致输入第二个时，第一个也会被渲染，component属性代表渲染哪个页面组件。所以一般path=”/"这个路由一般会加上exact，另外需要注意一点的是**嵌套路由不要加exact属性**。

`/detail/:blogid`  表示detail路径地址后面还需要一个logid参数，这种形式的路由上，被跳转到的组件的props中有一个match属性，match下有一个params对象属性，里面存放了冒号携带的参数

```jsx
import { BrowserRouter, Route } from 'react-router-dom'
import Home from './pages/home'
import Detail from './pages/detail'
import React from 'react';
import ReactDOM from 'react-dom';
/*访问那个路由渲染哪个路由组件*/
/*exact表示必须完全匹配才渲染，必须路由完全相等才能渲染，如果没有，'/detail'中也有'/'，会导致输入第二个时，第一个也会被渲染*/

function App() {
    return (
    	<BrowserRouter>  {/* route展示标签 */}
  			<div>
     			<Route path="/" exact component={Home}></Route>
     			{/* 等价于
     				<Route path="/" exact> <Home/> </Route>
     			*/}
     			<Route path="/detail/:blogid" component={Detail} ></Route>
			</div>
		</BrowserRouter>
    )
}

ReactDOM.render(<App />, document.querySelector('#root'));
```





# Link

组件中使用Link进行跳转

```react
import { Link } from 'react-router-dom'
...
<Link to={'/detail/' + props.blogId}></Link>
```



也可以使用js代码进行路由跳转

```react
import { useHistory } from 'react-router-dom'
function Trash() {
	let history = useHistory()
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

## Link携带数据

```jsx
<Link to={'/detail/' + props.blogId}></Link>
```

/detail/1

这种形式的路由上，被跳转到的组件的props中有一个match属性，match下有一个params对象属性，里面存放了冒号携带的参数



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





# 路由渲染

## Route

路由的基础渲染组件，根据Link跳转组件的to属性进行对应，需要展示的组件包裹在Route中，或者写进component属性，但当前url被Route的path属性命中后，对应组件就会渲染显示

在Router组件的子组件，其props会包含history，location，match属性

Route的path的命中是宽泛的，例如/about会同时命中/和/about，导致About组件和Home组件同时渲染，需要使用exact进行精准匹配

```jsx
<BrowserRouter>  
    <Route path={'/'} exact>
        <Home />
    </Route>
    <Route path={'/about'}>
        <About />
    </Route>
    <Route path={'/topics'}>
        <Topics />
    </Route>
</BrowserRouter>
```



## path

`path`属性可以使用通配符。

> ```jsx
> <Route path="/hello/:name">
> // name可以从this.props.match.params提取出来
> // 例如访问/hello/w，可以从渲染组件的this.props.match.params提取出来w
> 
> <Route path="/hello(/:name)">
> // 匹配 /hello
> // 匹配 /hello/michael
> // 匹配 /hello/ryan
> 
>     
> <Route path="/files/*">
> // *表示所有都匹配
> // 匹配 /files/ 
> // 匹配 /files/a
> // 匹配 /files/a/b
> 
> <Route path="/files/*.*">
> // 匹配 /files/hello.jpg
> // 匹配 /files/hello.html
> 
> 
> <Route path="/**/*.jpg">
> // 匹配 /files/hello.jpg
> // 匹配 /files/path/to/file.jpg
> ```

通配符的规则如下。

**（1）`:paramName`**

`:paramName`匹配URL的一个部分，直到遇到下一个`/`、`?`、`#`为止。这个路径参数可以通过`this.props.params.paramName`取出。

**（2）`()`**

`()`表示URL的这个部分是可选的。

**（3）`\*`**

`*`匹配任意字符，直到模式里面的下一个字符为止。匹配方式是非贪婪模式。

**（4） `\**`**

`**` 匹配任意字符，直到下一个`/`、`?`、`#`为止。匹配方式是贪婪模式。

`path`属性也可以使用相对路径（不以`/`开头），匹配时就会相对于父组件的路径，可以参考上一节的例子。嵌套路由如果想摆脱这个规则，可以使用绝对路由。





## Switch

渲染第一个被location匹配到的并且作为子元素的`<Route>`或者`<Redirect>`，Switch包裹和直接用一打Route区别在于：它仅仅只会渲染一个路径。不使用Switch包裹的情况，每一个被path匹配到的Route将都会被渲染。

```jsx
function App() {
    return 
    <BrowserRouter>
        <ul>
            <li>
                <Link to={'/'}>Home</Link>
            </li>
            <li>
                <Link to={'/about'}>About</Link>
            </li>
            <li>
                <Link to={'/topics'}>Topics</Link>
            </li>
        </ul>
        // 此时url为/about只会展示Home组件，所以需要path为/的Route加上exact属性
        <Switch>
            <Route path={'/'} exact>
                <Home />
            </Route>
            <Route path={'/about'}>
                <About />
            </Route>
            <Route path={'/topics'}>
                <Topics />
            </Route>
        </Switch>
    </BrowserRouter>
}

ReactDOM.render(<App />, document.querySelector('#root'));
```



## match对象

match是Router组件props中的匹配路径参数的对象，它有一个属性params，里面的内容就是路径参数，除常用的params属性外，它还有url、path、isExact属性。通过this.props.match.params.id获取了路径的匹配参数。

Match的获取方式： 
在Route component中，组件通过this.props.match获取。 
在Route render 和Route children中，通过传递一个参数的方式获取。





## 嵌套路由

很多时候路由要写进组件中，成为组件展示的一部分，这就是嵌套路由。

嵌套路由可以从props.match.url来拿到父路由的path

```jsx
class App extends Component {
  render() {
    return (
        <BrowserRouter>
          <Switch>
            <Route path="/beijing" component={Page} />
          </Switch>
        </BrowserRouter >
    );
  }
}

const Page = ({match}) => {
    return <div>
        <p>hello</p>
      	<Route path={`${match.url}/A`} component={...} />
      	<Route path={`${match.url}/B`} component={...} />
      	<Route path={`${match.url}/C`} component={...} />
    </div>
};
```



## exact

Route组件代表了一个个组件元素，符合如果当前路径和path匹配就会被渲染在页面之上，exact属性表示必须完全匹配才渲染如果没有，`'/detail'`中也有`'/'`，会导致输入`'/detail'`时，`/`也会命中，也会被渲染，所以一般path=”/"这个路由一般会加上exact。

```jsx
<Route path="/" exact component={Home}></Route>
<Route path="/detail/:blogid" component={Detail} ></Route>
```



另外需要注意一点的是**嵌套路由不要加exact属性**，因为外层的父路由已经被exact属性排除了，根本不会进内层进行匹配。

但想访问/detail/page1时，因为父路由加了exact，所以根本不会进去匹配子路由。

```jsx
<Route path="/detail" exact>
	<Route path='/detail/page1' component={...} ></Route>
</Route>
```









# 重定向Redirect

```
import { Redirect } from 'react-touter-dom'
...
这个路由会渲染目标组件，普通路由Link用于点击后跳转
这个路由无需操作，直接就渲染目标组件
<Redirect to='/'/>
```

`<Redirect>`组件用于路由的跳转，即用户访问一个路由，会自动跳转到另一个路由。可以做例如跳转登录。

例如访问/时自动跳转到login

```jsx
<Switch>
    <Redirect exact from="/" to='login'/>
    <Route path="/A" component={...}/>
    <Route path="/B" component={...}/>
</Switch>
```

也可以在判断中使用，做到跳转路由的效果

```js
import { Redirect } from 'react-router';

 class App extends React.Component {
  render() {
    const {loginSuccess}=this.props;
    if(loginSuccess){
      return (<Redirect to="/home" />);
    }else{
      return(<Redirect to="/login" />)
    } 
  }
}
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



# srollToTop

当我们使用react-router时，当我们从A页面进入B页面的时候，由于共享了同一个history，导致浏览的位置也被记录下来的，这样A页面滑动到底部了，进入B页面也会停留在底部，显然不符合我们的浏览习惯。就需要定义一个使用scrollToTop组件，但url改变时自动回到顶部。

函数组件

```jsx
import { useEffect } from 'react';
import { useLocation } from 'react-router-dom';
export default function ScrollToTop() {
    const { pathname } = useLocation();
    console.log('pathname=>', pathname);
    useEffect(() => {
        window.scrollTo(0, 0);
    }, [ pathname ]);
    return null;
}
```

class组件

```jsx
import React from "react";
import { withRouter } from "react-router-dom";

class ScrollToTop extends React.Component {
  componentDidUpdate(prevProps) {
    if (
      this.props.location.pathname !== prevProps.location.pathname
    ) {
      window.scrollTo(0, 0);
    }
  }

  render() {
    return null;
  }
}

export default withRouter(ScrollToTop);
```

然后在应用程序的顶部渲染它，但是要把它放置路由器中：

```jsx
import React from 'react'
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import ScrollToTop from './ScrollToTop'

function App() {
    return (
    	<BrowserRouter>
        	<ScrollToTop/>
        	<h1>App</h1>
    	</BrowserRouter>
    )
    
}

ReactDOM.render(<App />, document.querySelector('#root'));
```







# Hooks

React Router附带了一些钩子，可让您访问路由器的状态并从组件内部执行导航。

## useHistory

useHistory钩子使您可以访问可用于导航的history实例。

```jsx
import { useHistory } from "react-router-dom";

function HomeButton() {
  let history = useHistory();

  function handleClick() {
    history.push("/home");
  }

  return (
    <button type="button" onClick={handleClick}>
      Go home
    </button>
  );
}
```



## useLocation

useLocation钩子返回代表当前URL的location对象。您可以像useState一样考虑它，只要URL更改，它就会返回一个新位置。
这可能非常有用，例如 在您希望每次加载新页面时都使用Web分析工具触发新的"page view"事件的情况下，如以下示例所示：

```jsx
import React from "react";
import ReactDOM from "react-dom";
import {
  BrowserRouter as Router,
  Switch,
  useLocation
} from "react-router-dom";

function usePageViews() {
  let location = useLocation();
  React.useEffect(() => {
    ga.send(["pageview", location.pathname]);
  }, [location]);
}

function App() {
  usePageViews();
  return <Switch>...</Switch>;
}

ReactDOM.render(
  <Router>
    <App />
  </Router>,
  node
);
```



## useParams

useParams返回URL参数的key/value的对象。 使用它来访问当前`<Route>`的match.params。

```jsx
import React from "react";
import ReactDOM from "react-dom";
import {
  BrowserRouter as Router,
  Switch,
  Route,
  useParams
} from "react-router-dom";

function BlogPost() {
  let { slug } = useParams();
  return <div>Now showing post {slug}</div>;
}

ReactDOM.render(
  <Router>
    <Switch>
      <Route exact path="/">
        <HomePage />
      </Route>
      <Route path="/blog/:slug">
        <BlogPost />
      </Route>
    </Switch>
  </Router>,
  node
);
```



## useRouteMatch

useRouteMatch钩子尝试以与`<Route>`相同的方式匹配当前URL。它主要用于在不实际渲染`<Route>`时访问匹配数据（一般用于Router组件return null时）。

不用useRouteMatch：

```jsx
import { Route } from "react-router-dom";

function BlogPost() {
  return (
    <Route
      path="/blog/:slug"
      render={({ match }) => {
        // 用match做你想做的一切...
        return null;
      }}
    />
  );
}
```

使用useRouteMatch：

```jsx
import { useRouteMatch } from "react-router-dom";

function BlogPost() {
  let match = useRouteMatch("/blog/:slug");
  // 用match做你想做的一切...
  return null;
}
```