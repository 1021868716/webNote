# React

React是facebook开源的一个js函数库，结合周边生态（redux，react-router等）共同组成了一个MV*框架



**React特点**

- 声明式编码
- 组件化编码
- 高效的DOM diff算法，最小化页面的重绘
- 单向数据流（vue组件之间数据流动也是单向的）



下载react

在使用react开发网页时，会下载两个包，一个是react，一个是react-dom，其中react包是react的核心代码，react-dom则是React剥离出的涉及DOM操作的部分。

react的核心思想是虚拟DOM，react包含了生成虚拟DOM的函数react.createElement，及Component类。当我们自己封装组件时，就需要继承Component类，才能使用生命周期函数等。而react-dom包的核心功能就是把这些虚拟DOM渲染到文档中变成实际DOM。

```
npm i react react-dom
```



下载babel

```
npm i babel-preset-react -D
```





# 脚手架

下载脚手架create-react-app

create-react-app是官方提供的脚手架

此外还有umi等脚手架，可以快捷创建集合dva的项目

```
npm install -g create-react-app
```

创建项目

```
create-react-app projectname
```



启动项目

```
npm run start
```



项目打包

```
npm run build
```



暴露webpack配置

脚手架将webpack配置全部隐藏起来了，如果想要修改webpack配置需要通过eject命令将配置暴露出来

```
npm run eject
```







## 目录结构

- src目录

**index.js**  

入口js文件



**serviceWorker.js**

用于构建PWA应用，相当于移动端app

```
import * as serviceWorker from './serviceWorker';
```



**test文件**

自动化测试文件





- public目录

**manifest.json**

相当于把网页缓存在手机上使用，构建快捷图标等配置



**index.html**

单页面的html挂载模板







# index.js

index.js是react项目的入口文件



## ReactDOM.render

ReactDOM是一个第三方的核心模块，负责把组件挂载在dom上显示出来（更新UI）

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(组件,html模板上的dom元素)
```

组件将被挂在到对应的dom元素上并渲染



```jsx
// 引入React负责解析jsx语法，下面的<App/>标签就属于jsx语法
// 引入ReactDOM挂载组件在DOM上
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

// 将App组件挂载到文档中的root节点上
ReactDOM.render(<App/>,document.getElementById('root'));

/*
StrictMode 是一个用来突出显示应用程序中潜在问题的工具。与 Fragment 一样，StrictMode 不会渲染任何可见的 UI。它为其后代元素触发额外的检查和警告（开启严格模式检查组件的代码）。

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
*/
```





# StrictMode

StrictMode 是一个用来突出显示应用程序中潜在问题的工具。与 Fragment 一样，StrictMode 不会渲染任何可见的 UI。它为其后代元素触发额外的检查和警告。

类似于对组件进行严格模式的检测，使用StrictMode组件开启严格模式检测后所有被检测组件的render函数都会执行两次渲染防止渲染期间出现bug（无状态组件没有render函数所以只会执行函数本身一次）。

```react
import React from 'react';

function ExampleApplication() {
  return (
    <div>
      <Header />
      <React.StrictMode>
        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>
      <Footer />
    </div>
  );
}
```

在上述的示例中，*不*会对 `Header` 和 `Footer` 组件运行严格模式检查。但是，`ComponentOne` 和 `ComponentTwo` 以及它们的所有后代元素都将进行检查，并且其中普通组件的render函数都会执行两次（无状态组件没有render函数所以只会执行函数本身一次）。

`StrictMode` 目前有助于：

- 识别不安全的生命周期
- 关于使用过时字符串 ref API 的警告
- 关于使用废弃的 findDOMNode 方法的警告
- 检测意外的副作用
- 检测过时的 context API





# JSX

引入 React 解析jsx语法，并使用React.Component创建组件类，也可以单独引入Component负责创建组件，但因为Component属于React对象下，所以也可以不引入Component直接使用React.Component来继承组件。

React创建组件类时继承自Component类，最后将组件export default导出，需要使用的地方就可以引入这个组件了，并且组件类名必须大写开头，是为了区分html标签和React组件

```react
import React from 'react'
//import React, { Component } from 'react';

export default class Life extends Component {
  
}
```





## 语法

在js中写html标签并解析就叫 JSX 语法

**组件类内部的render函数中 return  (JSX) 书写JSX语法，JSX语法中必须有一个根标签包裹所有元素**

在js文件中引入React（`import React from 'react'`）就是为了解析JSX语法，JSX中可以使用JS语法，绑定变量，绑定事件等语法，但要加上{}包裹，不是加引号（vue是加引号，React是用一对大括号{}）

```react
import React from 'react'

export default class Life extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      name: 'wtw'
    }
  }
  render() {
    return (
      <div>
        <p>react生命周期: {this.state.name}</p>
        <button>onclick</button>
      </div>
    )
  }
}
```





### Fragment

JSX中用于包裹所有元素的根标签会被渲染到dom上，如果我们不想这个根标签被显示在dom树上，就可以从`'react'`引入一个`Fragment`占位符标签，用`Fragment`标签当做根标签包裹所有元素，占位符标签`Fragment`不会被渲染到dom上去，让代码看起来更简洁。

```jsx
import React, { Component, Fragment } from 'react';
...
<Fragment>
  <div>wtw</div>
  <div>hello,world</div>
</Fragment>

或者使用空标签
<></>
```

占位符组件和空标签同价



### 数据绑定

JSX中可以使用JS语法，但是要加上{}包裹，不用加引号，也不用{{}}

```jsx
<input value={this.state.inputValue} />
<div>{this.state.name}</div>
```



### 循环渲染

jsx中无法直接for循环

通过数组的map遍历，每次都返回一段jsx来实现循环渲染，和vue一样，每个item需要绑上key

```jsx
list = ['123', '456'] 
return (<ul>
{
  this.state.list.map((item, index) => {
    return (<li key={index}>{item}</li>)
  })
}
</ul>)
```





### 注释

```jsx
{/*多行注释*/}
{
  // 单行注释
}
```



### 引入样式

React中引入样式在文件的开头 `import 'cssurl'`，该页面的JSX即可使用，JSX中标签的class属性需要换为className，避免与继承class关键字相混淆



### 行内样式

`style={{key: value}}`，style内部存放一个样式对象，样式属性为key，字符串形式的值为value

```jsx
<div style={{backgroundColor:'red',color:'white',fontSize:40}}>看背景颜色和文字颜色</div>
```



### dangerouslySetInnerHTML

下面这段JSX表示根据state中的list数据来循环生成li

但是item不会识别html内容，例如item内容是`<h1>123</h1>`就会输出`<h1>123</h1>`，不会做转义

```jsx
list = ['<h1>123</h1>', '<h1>456</h1>'] 
return <ul>
{
  this.state.list.map((item, index) =>{
    return <li key={index}>{item}</li>
  })
}
</ul>
```



JSX中有将内容转义的属性`dangerouslySetInnerHTML`，危险的设置innerHTML，设置后会对内容进行转义产生效果，**但这样很容易被XSS攻击**

`{__html:item}`对象表示将`li`标签内容以item进行innerHtml解析填充

**设置了这个属性我们就不能再对`li`标签中的内容进行设置了，必须为空标签，将由html对item进行解析填充进里，否则报错**

```jsx
list = ['<h1>123</h1>', '<h1>456</h1>'] 

<ul>
  {
    this.state.list.map((item, index) =>{
      return (
        <li key={index} dangerouslySetInnerHTML={{__html: item}}></li>
      )
     })
   }
</ul>
```



### label中的for

label标签中的`for`属性等于跳转input的id，可以跳转焦点，但是JSX中直接使用`for`会和js中的`for`循环产生歧义，所以JSX语法使用了`htmlFor`来代替label中的`for`属性

```jsx
<label htmlFor="input">todoList：</label>
<input id="input" />
```



### 条件判断

render函数中我们可以使用if来判断返回哪个jsx，但是在jsx中不能直接使用if，只能使用三元表达式来进行条件判断

```jsx
return <div>
{
  this.props.login
  ?<div className="RecommendInfo">{this.props.realname}</div>
  :<div className="RecommendInfo">尚未登陆</div>
}
</div>
```

也可以使用&&符合进行判断

```jsx
return (<div>
  {(record.status === 0 || record.status === 2) && <div>启用</div>}
</div>)
```



### 循环

jsx中只能写表达式，不能写多行语句所以无法使用for循环，所以最佳循环写法是使用map函数



## render

组件类内部有render生命周期函数，render函数返回  (JSX) ，用于构建组件的基本结构

```jsx
import React, { Component, Fragment } from 'react'

//  class TodoList extends React.Component
class TodoList extends Component {
  render() {
    return (
      <div>hello,world</div>
    )
  }
}

export default TodoList
```



## constructor

constructor函数默认传入一个参数props，并且需要调用super(props)继承父组件属性，然后在constructor函数中构建组件自己的属性。constructor函数的this.state属性是一个对象，用于保存组件的数据。state通过setState()修改。

可以不写constructor（js类会自动添加），一旦手动写了constructor，就必须在此函数中写super()，此时组件才有自己的this，在组件的全局中都可以使用this关键字，否则如果只是constructor 而不执行 super() 那么以后的this都是错的。

```react
class A extends React.Component {
  constructor(props) {
  	super(props)
    ...
  }
}
```

 **如果在custructor生命周期不使用 this.props或者props时候，custructor可以不传入props**。使用this.props的时候，constructor需要加入props参数，super需要super(props)，此时用props也行，用this.props也行，他俩都是一个东西。





# 事件

在React 16及之前的版本里，React会用 document.addEventListener()来绑定大部分的事件，所有事件都绑定在document上。

在React 17版本中， React不会再直接和document层面的事件机制绑定。它将会被绑定到React渲染的根Dom节点root上。React 17将会用rootNode.addEventListener()来进行绑定事件，并且弃用了事件池机制。

```javascript
const rootNode = document.getElementById('root');
ReactDOM.render(<App />, rootNode);
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ebf58acebcd34ec4bc2e422c01802350~tplv-k3u1fbpfcp-zoom-1.image)

## 绑定事件

JSX中使用`on事件={ this.方法 }`对事件进行绑定，事件首字母大写，绑定事件时如果需要使用event对象，event参数应该放在最后一个参数位置，而且这个event不是dom中的event，而是react模拟dom中的event生成的合成事件参数

```javascript
 this.handleInputChange(title, event)
// 已经绑定过this
```

**JSX中调用的方法内this默认指向undefined（因为类中默认严格模式）**，组件内中的方法直接写在class里即可，但是方法默认的this指向是undefined，直接绑定就会报错，有以下两个方法可以将this正确指向



- bind

需要在JSX中使用`this.方法.bind(this, 参数)`来使用这个方法，或者在注册事件时采用箭头函数的方式内部调用就无需使用bind

```jsx
handClick(num)  {
  console.log("click", num);
}
render() {
  const n = 10
  return (
    <button onClick={this.handClick.bind(this, n)}>click</button>
    {
      //或者使用箭头函数来注册事件
	  //<button onChange={() => { this.handleInputChange(n)}}>click</button>
    }
  )
}

```

也可以在constructor中提前使用bind绑定this，然后在jsx中也可以直接使用函数

```react
constructor() {
  super()
  this.handClick = this.handClick.bind(this)
}
handClick()  {
  console.log("click");
}
// 在constructor中提前改变了bind指向，render中就无需再bind了
render() {
  return (
  	<button onClick={this.handClick}>click</button>
  )
}
```

**但是如果已经在constructor使用bind为事件绑定过this，还想要传参**，则注册事件需要使用箭头函数，然后内部调用事件，且箭头函数不能写参数，否则会被当做e

```jsx
constructor() {
  super()
  this.handClick = this.handClick.bind(this)
}
handClick(num) {
  console.log("click", num);
}
render() {
  let n = 10
  return (
    // onClick={this.handClick(index)} // 直接加()添加参数是错误的，无法运行
    // 要借用箭头函数传参，但注意index不能当做箭头函数参数使用，直接使用，否则会被当做e
    <button onClick={() => {this.handClick(n)}}>
      click
    </button>
  );
}

```



- 箭头函数

  - 箭头函数定义事件

    ```react
    class App extends React.Component {
      handClick = () => {  // 箭头函数定义事件
        console.log("click");     
      };
      render() {
          return (<button onClick={this.handleClick}>click</button>) 
      };
    }
    ```

    

  - 箭头函数注册事件

    如果需要参数直接写进事件函数中，不用写在箭头函数中，否则会被当做e

    ```react
    class App extends React.Component {
      handClick(num) {
        // 普通定义事件
        console.log("click", num);
      }
      render() {
        let n = 10
    	return (
          <button onClick={ () => {this.handClick(n)} }>click</button>
        );
      }
    }
    ```

    

## SyntheticEvent

SyntheticEvent合成事件对象：我们在react中绑定的事件并不是原生的dom事件，而是react合成出来的。我们给元素绑定的事件全部绑定到了document上方便统一管理，而不是像原生js绑定到我们触发事件的元素上。

react事件处理函数中的e本质是SyntheticEvent（合成事件对象），不是dom原生的event，它模拟了原生event的全部能力（例如 `stopPropagation()` 和 `preventDefault()`），但他和浏览器和dom标准没有关系，全部是react自己控制的。

优点：

1.  抹平浏览器之间的兼容性差异
2. 可以自定义高级事件，例如React的onChange事件，它为表单元素定义了统一的值变动事件。
3. 抽象跨平台事件机制，使RN也可以使用这套机制，和VirtualDOM的意义差不多，VirtualDOM抽象了跨平台的渲染方式，那么对应的SyntheticEvent目的也是想提供一个抽象的跨平台事件机制。
4. 干预事件的分发。v16引入Fiber架构，React为了优化用户的交互体验，会干预事件的分发。不同类型的事件有不同的优先级，比如高优先级的事件可以中断渲染，让用户代码可以及时响应用户交互。



- event.nativeEvent：调取原生event

可以通过event.nativeEvent调出dom原生event。



- event.persist()：异步时保留事件引用

如果在react中想异步访问e（如在setTimeout内），应该在是处理事件时调用`event.persist()`，**这会从事件池中移除该合成函数并允许对该合成事件的引用被保留下来**，否则异步开始时，e可能已经变化了无法正确找到事件。从 v17 开始，`e.persist()` 将不再生效，因为 `SyntheticEvent` 不再放入事件池中。

```javascript
onChange = {
  e => {
    e.persist()
    setTimeout(() => {
  	  console.log(e.target.value)
    }, 200)
 }
}
```



## 事件池

事件池适用于仅适用于 React 16 及更早版本、React Native。Web 端的 React 17 不使用事件池。

合成事件对象池是 React 事件系统提供的一种性能优化方式。不同类型的合成事件的SyntheticEvent具有不同的对象池。

当对象池未满时，React创建新的事件对象派发给组件后将事件对象SyntheticEvent沉入事件对象池。当对象池装满后，React 从池中复用以前的事件对象，派发给组件避免继续创建新的事件对象节约性能。

这意味着React的事件对象是可以复用的，所以异步中要`e.persist()`保留本次事件引用，否则e就会被其他组件复用，异步开始执行时e已经不是原来的事件对象了。





# React.createElement()

在render函数中返回的JSX会先生成React.createElement()，React.createElement()是更底层的生成虚拟dom的方法，性能更高

ReactElement通过createElement创建，调用该方法需要传入三个参数：

- type (指代这个ReactElement的类型)
- config (ReactElement的属性)
- children (ReactElement的内容)

```react
render(){
  return (
    <div id="wrapper" a="1">
	  <span style={{color:'red'}}>a</span>b
    </div>
  )
}
```

```javascript
//render函数返回JSX等同于返回React.createElement(),后者更接近虚拟DOM性能更高
render() {
  return React.createElement(
    'div',
    { id:'wrapper' },
    a: '1',
    React.createElement(
      'span',
      {style={{color:'red'}}},
      'a'
	),
    'b'
  ) 
}

```







# CSS Module

首先在webpack中开启css-loader的options.modules: true 开启模块化

在react中使用模块化的方式，以对象来引入样式

```css
// GlobalSelectors.css文件
.container {
  border-width: 2px;
  border-style: solid;
  border-color: brown;
  padding: 0 20px;
  margin: 0 6px;
  max-width: 400px;
}
```

```jsx
//1.先引入对应模块的样式
import styles from './style.css';
 
// 2.使用 className={styles.container} 这种形式表示模块class名
// 而 className="text-left" 这种形式则表示全局下的选择器
 
export default class GlobalSelectors extends Component {
  render() {
    return (
      <div className={ styles.container }>  // 使用'./style.css'中的样式
        <p className="text-left">Global Selectors</p> // 使用全局样式
      </div>
    );
  }
}
 
```

css modules本身需要**css-loader**来配合，这可能会出现的缺点：

- 必须使用 **`camelCase`** 来命名 css class names
- 当引入到 **`className`** 中时必须要使用 **styles** 对象
- CSS modules 和 全局css类混合在一起会很难管理
- 引用没用定义的CSS modules不会出现警告



因此可以使用更多的社区方案来处理cssModules，例如styled-components







# this.state

React是一门数据驱动的框架，数据发生改变页面就会发生改变。

**this.state 用于保存组件的状态，并且this.state会和JSX中的数据进行双向绑定**

**this.state中的内容会响应的映射到调用的JSX中，并且如果this.state中的数据变化了，JSX中引用的数据也会跟着改变，**

但是这样写input的值固定为this.state中inputValue的hello，input一输入立马又会被改回hello，这跟Vue的v-model有很大不同

```react
import React, { Component, Fragment } from 'react'

class TodoList extends Component {
  constructor(props) {
    super(props)
    this.state  = {   // this.state 用于保存组件的数据
      inputValue: 'hello',
      list: []
      // 数据会映射到input上，但input无法输入，因为数据被固定为hello
    }
  };
    
  render() {
    return (
      <Fragment>
        <input value={ this.state.inputValue } />
		// 此时input完全无法输入，因为value一改变就会被改回绑定的inputValue值
      </Fragment>)
    };
}

export default TodoList
```

state的数据结构不要设计的太深，否则再shouldComponentUpdate中对比时很消耗性能



# this.setState()

三大特性

- 不可变值
- 可能是异步更新
- 可能会被合并



**`setState`是一个异步的方法，一个生命周期内所有的`setState`方法可能会合并操作用以减少虚拟dom比对次数**

**不能直接在方法中使用this.state的方法去改变数据，想要改变state中的数据，需要调用this.setState()方法**，this.setState()直接传入一个对象

```javascript
this.setState({
  inputValue: e.target.value,
  list: [...this.state, this.state.inputValue]
})
```

或者传入一个回调函数，这个回调函数内异步返回一个对象来修改数据，这样在返回对象之前还可以进行一些数据操作。

**但因为setState()会进行合并一起操作，在setState()内进行异步操作（如setTimeout，回调函数），会导致无法读取到对应事件函数内的信息，会直接报错数据无法读到。**

**如果在react中想setState()内异步访问事件属性（如setTimeout，回调函数），应该在是处理事件时调首先在setState()之前调用一次`event.persist()`，这会从事件池中移除该合成函数并允许对该合成事件的引用被保留下来。**

```javascript
e.persist()
// setState内使用了异步的回调函数，所以需要提前e.persist()保存引用
this.setState(() =>{
    inputValue= e.target.value
    list = [...this.state, this.state.inputValue]
    return {
     // 数据修改
      list: list 
    }
})
```

`this.setState`的回调可以传入一个prevState参数代替this.state，防止回调内不小心引用并改变了this.state造成混乱，prevState表示修改数据之前state的状态，这样可以更安全的修改数据

```react
const value  = e.target.value  // 提前手动缓存函数信息，也可以解决异步无法读取函数信息的问题
this.setState((prevState)=>{
  inputValue = value
  list = [...prevState, prevState.inputValue]
  return {
     // 返回数据修改
     list: list 
  }
})
```





## 操作数组

this.state需要使用this.setState()修改，this.state是一个不可变值（包括在this.setState()内），原因是会对shouldComponentUpdate中的比较产生影响，并且这样符合函数式编程，纯函数的编程思想

```javascript
this.state = {
  arr: [1,2,3],
  obj1:{},
  obj2:{}
}
this.setState({
  arr: this.state.arr.concat(4), // 追加
  // 对对象拓展的两种方式：需要使用Object.assign()或者es6拓展运算符，
  obj1: Object.assign({}, this.state.obj1, {a: 100}),
  obj2: {...this.state.obj2, a:100}
})
```

**我们对数组操作不能使用push，pop，splice等方法，因为这些方法是在原本的数组上修改，而this.state是一个不可变值，只能使用数组中返回新数组的方法，否则就违背了this.state不可变值特性。**





## 双向绑定

想要进行双向绑定，一般是监控JSX中标签的事件（例如onChange），在事件中传入e，通过e.target并获取这个标签，监控输入试图改变的value，然后用这个value来改变this.setState，这样标签内的值也改变了，用来实现了双向绑定的功能。







# 组件通信

### 父传子

this.props

父组件给子组件添加一个自定义属性，然后给自定义属性传入需要传递的属性，子组件构造函数中通过super(props)继承属性保存在props中，然后子组件通过`this.props`来调用

例如我们传入item变量，通过content属性挂载，index变量通过index属性挂载

```html
<TodoItem content={item} index={index}/> 
```

```react
<div onClick={ this.handleClick.bind(this) }>{this.props.content}</div>
...
handleClick() {
  console.log(this.props.index)
}
```



**直接在JSX中使用bind来绑定事件，性能会降低，**推荐使用constructor构造函数先将继承的事件进行私有化，然后在JSX中使用私有属性

```react
constructor(props) {
  super(props)
  this.handleClick = this.handleClick.bind(this)
};

render() {
  return (
      // 绑定私有化事件
    <div onClick={ this.handleClick }> 
      {this.props.content} 
    </div>
  )
};

handleClick() {
  console.log(this.props.index)
};

```





### 子传父

父组件向子组件传入父组件的自定义事件，子组件可以将消息传入事件参数中，然后让父组件执行这个事件。

**为了实现子组件让父组件执行方法，所以需要父组件绑定事件时改变this指向自己，否则默认this执行子组件就找不到这个事件。**

父组件向子组件传入事件deleteItem

```jsx
<TodoItem content={item} index={index} deleteItem={ this.handleItemDelete.bind(this) }/>
```

子组件内通过触发这个事件并传入消息当参数，来让父组件通过执行`handleItemDelete`方法，并得到子组件的消息，注意除事件外的普通变量（例如下面的this.content）不要私有化有莫名其妙的bug

```react
class TodoItem extends React.Component {
  constructor(props) {
    super(props)
    this.handleClick = this.handleClick.bind(this)
    // this.content = this.props.content
  };

  render() {
    return (
      <div onClick={ this.handleClick }> 
        /*{this.content}私有化以后有bug*/
        {this.props.content}
      </div>
    )
  };

  handleClick() {
    // deleteItem也是父组件传入的数据，所以通过props调用
    // 这句代码等价于this.handleItemDelete(this.props.index)
    // 如果父组件未改变这个方法this指向自己，就等价于 子组件.handleItemDelete(this.props.index)
    // 但子组件中没有这个方法就会报错
    // 父组件改变了this的执行，所以就等价于 父组件.handleItemDelete(this.props.index)
    // 实现了子组件通知父组件执行方法
      
    // this.props.deleteItem(this.props.index)
    // ES6简写为
    const {index , deleteItem} = this.props
    deleteItem(index);
  }
}
```



### this.props.children

类似于vue的slot，父组件让子组件包裹的内容会传递到子组件的this.props.children属性中，子组件可以进行调用

```react
// 父组件中调用子组件
<child>abcd</child>
```

```react
// 子组件
<div>{this.props.children}</div>
```



### 单向数据流

父组件可以向子组件传递数据，但子组件无法修改这个数据

数据是自顶向下单向流动的，即从父组件到子组件。这条原则让组件之间的关系变得简单且可预测。

子组件只能将消息通过父组件传过来的事件，并且让子组件传递参数，父组件自己触发事件的方法对父组件进行操控

React不算mvvm而是mv*，虽然可以实现双向绑定，在React中实现双向绑定通过state属性，但如果将state绑定到视图中时，直接修改state属性是不可的，需要通过调用setState去触发更新视图，反过来视图如果要更新也需要监听视图变化 然后调用setState去同步state状态。标准MVVM应该属于给视图绑定数据后，操作数据立刻更新视图。









# 数据驱动

一旦改变state或者props内的数据，组件的render函数就会重新执行并渲染页面。页面是由render函数渲染出来的，所以数据变化页面就会跟着变化。

父组件的render函数执行时，内部的子组件也会被重新渲染，所以子组件的render函数也都会被重新执行一次



# prop-types

prop-types是用于检验react组件props的库

```
npm i prop-types
```

在组件中声明静态属性propTypes，但props属性与propTypes规定不符时就会报错

```react
import PropTypes from 'prop-types';
import React from 'react';

class Menu extends React.Component {
  static propTypes = {
    mockTemplate: PropTypes.any,
    curMockItem: PropTypes.object,
    getMockList: PropTypes.func,
    setCurMockItem: PropTypes.func
  }
	...
}
```



## PropTypes/defaultProps

PropTypes是一个插件（需要安装并引入）。PropTypes用于父组件传递给子组件的数据属性类型的校验,DefaultProps是用来给父组件没传值的属性赋默认值。类似vue自带的类型检测的type和default。

在组件类外调用  `类名.propTypes = { 属性:PropTypes.类型 }`  进行检测，一旦属性与类型不符就会出现警告。defaultProps用于没有传入时的设置默认值。

类型可以为：symbol，true，string，func，object，number



- oneOfType   类型多选一

`类名.propTypes = {属性:PropTypes.oneOfType(PropTypes.string,PropTypes.number)}`

表示可以为string或者number类型



- arrayType    限制数组item的类型

```
类名.propTypes = {属性:PropTypes.arrayType(PropTypes.string,PropTypes.number)}
```

表示接收一个数组，数组内容应该是数字或者字符串类型



```react
import React from 'react'
import PropTypes from 'prop-types'

class TodoItem extends React.Component {
...
}

TodoItem.propTypes = {
  content: PropTypes.string.isRequired
  deleteItem: PropTypes.func,
  index: PropTypes.number
}


TodoItem.defaultProps = {
  test:'helloworld'
}
export default TodoItem
```



### .isRequired

这个后缀跟在`PropTypes.类型.`后，表示对这个属性有一定限制

`.isRequired`后缀表示这个属性父组件必须传递给子组件不能省略，如果父组件没传，也没有DefaultProps默认值，就会触发警告。

```react
TodoItem.propTypes = {
  content: PropTypes.string.isRequired,  // 表示这个属性必须传入
  deleteItem: PropTypes.func,
  index: PropTypes.number
}
```





# 容器组件与UI组件

将render函数返回的UI拆分出来单独放在一个js文件里，变成一个单独的组件（UI组件）方便维护，原本的组件在render中返回这个子UI组件。UI组件中只放JSX，只放UI，不负责任何逻辑处理，UI组件可以用函数写成无状态组件，原本的组件存放数据与方法，叫容器组件。

**但需要将JSX中依赖的数据和方法传给子UI组件，需要引入的模块引入子UI组件**

```react
// TodoList.js
...
import TodoListUI from './TodoLisiUI'

// 没有逻辑处理的组件叫ui组件，写成函数形式就叫无状态组件
class TodoList extends React.Component {
...
  render() {
    return (
        // 需要将JSX中依赖的数据和方法传给子UI组件
        <TodoListUI 
        inputValue={this.state.inputValue}
        list={this.state.list}
        handleInputChange={this.handleInputChange}
        handleItemDelete={this.handleItemDelete}
        handleItemDelete={this.handleItemDelete}/>
    )
    )
  };
}

export default TodoList
```

```react
// TodoListUI.js
import React from 'react'
import 'antd/dist/antd.css'
import { Input, Button } from 'antd';

class TodoList extends React.Component {
  render() {
    return (
        render() {
    return (
      <div>
        <div style={{marginTop:'10px', marginLeft:'10px'}}>
          <Input
             value ={this.props.inputValue}
             placeholder="todo info"
             style={{width:'300px',marginRight:'10px'}} 
             onChange={this.props.handleInputChange}/>
          <Button 
            type="Primary"
            onClick={this.props.handleBtnClick}
            >put</Button>
          {
            this.props.list.map((item,index) => 
              <li 
                key={item}
                onClick={ () =>{this.props.handleItemDelete(index)}}>
              {item}</li>)
          }
        </div>
      </div>
    )
  }
    )
  }
}

export default TodoListUI
```





# 无状态组件（函数组件）

当一个组件（例如UI组件）只有render函数时，可以将它定义为一个无状态组件（函数），无状态组件写成一个函数的形式，**函数接收一个props参数（用于接受父组件在子组件上挂载的数据）**，这个函数返回JSX

注意无状态组件中不可以使用this，跟普通UI差别在于JSX中的this.props可以直接替换为props，无状态组件的性能较高，因为他就是一个函数，而UI组件是一个类，自带一些无用的生命周期函数，也不会生成组件的实例浪费性能。

```react
// TodoListUI.js
import React from 'react'
import 'antd/dist/antd.css'
import { Input, Button } from 'antd';

const TodoListUI = (props) => {
    return (
    	<div>
        <div style={{marginTop:'10px', marginLeft:'10px'}}>
          <Input
             value ={props.inputValue}
             placeholder="todo info"
             style={{width:'300px',marginRight:'10px'}} 
             onChange={props.handleInputChange}/>
          <Button 
            type="Primary"
            onClick={props.handleBtnClick}
            >put</Button>
          {
            props.list.map((item,index) => 
              <li 
               	key={item} 
				onClick={()=>{props.handleItemDelete(index)}}>
              {item}</li>)
          }
        </div>
      </div>
    )
}
```



# 函数组件/类组件的差异

- 类组件是一个类，要继承class
- 类组件可以使用this获取组件实例
- 类组件有状态和生命周期

但是两者最大的区别在于心智模型上：**函数组件捕获了（保留）渲染/执行时所用的值**，类似于函数的闭包效果。而类组件则是调用最新的属性值。两者区别是JS的特性造成的。

```react
// 定义一个类组件和一个函数组件
// 原本props.message为 'old message'
class ClassComponent extends React.Component {
  handClick = () => {
    setTimeout(() => {console.log(this.props.message)}, 3000)     
  };
  render() {
      return (<button onClick={this.handleClick}>click</button>) 
  };
}

function FuncComponent(props) {
  const handClick = () => {
    setTimeout(() => {console.log(props.message)}, 3000)     
  };
  return (<button onClick={handleClick}>click</button>)    
}

//在点击button后的定时器3s内修改props.message为 'new message'
//类组件最终会打印 'new message'
//函数组件最终会打印 'old message'
```



# 插槽

react中也可以实现类似vue中插槽的功能，子组件是会挂载到父组件的props.children属性上（多个组件会形成数组），并且在函数组件中，函数是可以传入一个对象参数的，这个对象就是props

```jsx
<a title='aaa'>
  <b></b>
</a>
// 此时就可以从a组件中可以通过props.title提取出aaa，也可以从props.children上提取出b组件实例
```



# ref

一般情况下不推荐使用ref操作dom，应该采用数据驱动，否则可能出各种各样的问题（例如setState是异步的，setState改变数据之后，ref可能获取的结果有延迟），组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟 DOM （virtual DOM）。只有当它插入文档以后，才会变成真实的 DOM， 如果需要从组件获取真实 DOM 的节点，就要用到官方提供的`ref`属性

`ref`用到react有状态组件时，ref引用的是组件的实例；所以可以通过子组件的`ref`可以访问到子组件实例的`props`、`state`、`refs`、实例方法(非继承而来的方法)等等。

**但是注意无状态组件是不会被实例化的**，所以在父组件中通过`ref`来获取无状态子组件时，其值为`null`



**可以通过React.createRef这个api来创建ref**，提取出真实dom节点的方式为`this.refObj.current`，并且在组件被卸载时,将current属性重置为null。

```react
constructor() {
  this.state = {
    name: 'wtw'
  }
  this.nameInputRef = React.createRef() // 创建ref
}

render() {
  return <div>
		<input defaultValue={this.state.name} ref={this.nameInputRef}
	</div>
}
...
 // 访问dom需要使用ref的current属性
 console.log(this.nameInputRef.current)
```



第二种创建ref的方式是将ref属性设置为一个回调函数，这也是官方强烈推荐的用法；

这个函数执行的时机为：

- `组件被挂载后`：回调函数被立即执行，回调函数的参数为该组件的具体实例。
- `组件被卸载或者原有的ref属性本身发生变化时`：回调也会被立即执行，此时回调函数参数为`null`，以确保内存不泄露。

回调函数的默认参数instance就是这个dom节点（`this.refObj.current`属性），当这个节点被渲染的时候就会执行一次回调

```react
render(){
  return(
    <input ref={this.refCb.bind(this)}/>
  )};
refCb(instance){
  this.input = instance // 创建一个指针指向input节点，这个指针挂载在组件上，这个组件内都能访问
  console.log(this.input)
}
```



ref也可以向vue一样设置为字符串，但未来react将不支持这种写法

```javascript
render(){
  return(
    <input ref="input"/>
  )}
...
提取dom：this.ref.input.current
```





# 生命周期

某一时刻组件自动调用的函数

生命周期：初始化--->更新--->卸载

## 初始化

组件初始化流程：1--->2--->3

0.     **constructor()**

构造函数阶段



1.	​	componentWillMount()   **v16.3后被废除**

**componentWillMount()  组件即将（还没有）被挂载到页面上的时候执行一次**

注：组件初始化前调用，以后组件更新不调用，**整个生命周期只调用一次**，此时可以修改state。



2. 	​	**render()**

render()函数返回jsx并挂载到dom上

state 或者 prop 改变，或者父组件的render函数触发时都会触发子组件的render函数重新渲染子组件

注：react最重要的步骤，**创建虚拟dom，进行diff算法，更新dom树都在此进行**。此时就不能更改state了。



3. ​	**componentDidMount()**

**这个组件被渲染完之后会调用一次**

注：组件首次渲染后调用，**只调用一次，在此请求AJAX数据**。



## 更新

**更新state**流程：2--->3--->4--->5



**更新props**流程：1--->2--->3--->4--->5

更新props的流程比更新state的流程多一个步骤



1.	​	componentWillReceiveProps(nextProps)    **v16.3后被废除**

组件接受新的props时调用，父组件的render函数重新执行了，子组件的这个生命周期函数就会执行

**如果是第一次被父组件渲染不会执行，之后父组件再传入props就会执行**

注：componentWillReceiveProps组件初始化时不调用，在组件接受新的props时调用。



2.	​	**shouldComponentUpdate(nextProps, nextState)**

组件更新之前自动执行，该函数要求返回一个bool值（默认返回true），返回false，组件不允许被更新，返回true，组件被允许更新

**注：shouldComponentUpdate是react性能优化非常重要的一环。**

组件接受新的state或者props时调用，我们可以设置在此对比前后两个props和state是否相同，如果相同则返回false阻止更新，因为相同的属性状态一定会生成相同的dom树，这样就不需要创造新的dom树和旧的dom树进行diff算法对比，节省大量性能，尤其是在dom结构复杂的时候

**利用shouldComponentUpdate进行性能优化**：因为父组件数据变化重新渲染会导致子组件也重新渲染，但是子组件可能并不需要重新渲染，子组件是可以复用的，我们就可以判断子组件的props和state是否变化，如果没有变化就不更新，变化才更新

shouldComponentUpdate() 两个参数分别为更新后的props和state

```javascript
shouldComponentUpdate(nextProps, nextState){
  if((nextProps.content !== this.props.content) || nextState.num !== this.state.num) {
     // 如果父组件传入的参数更新或者子组件数据产生变化，子组件才允许被更新
  	 return true
  }else{
  	 return false
  }
}
```



3.	​	componentWillUpdate(nextProps, nextState)   **v16.3后被废除**

在组件被更新之前，shouldComponentUpdate()返回true之后执行

注：组件初始化时不调用，只有在组件将要更新时才调用，此时可以修改state



4.	​	**render()**

组件更新

state (调用setState)或者 prop 改变，或者父组件的render函数触发时都会触发子组件的render函数重新渲染子组件

注：组件渲染，一个组件必须有render()生命周期函数，因为组件继承自Component，Component内置了处理render()以外的所有生命周期函数，所以必须手动添加render() 生命周期函数



5.	​	**componentDidUpdate()**

组件更新完成之后执行

注：组件初始化时不调用，组件更新完成后调用，此时可以获取dom节点。



## 卸载

卸载只涉及一个生命周期

**componentWillUnmount()**

把组件从页面上去除时执行

 注：组件将要卸载时调用，一些事件监听和定时器需要在此时清除。



## v16.3生命周期

**react v16.3以后删除以下三个生命周期**

componentWillMount
componentWillReceiveProps
componentWillUpdate



新增两个生命周期

**static getDerivedStateFromProps**

触发时间：在组件构建之后(虚拟dom之后，实际dom挂载之前) ，以及每次获取新的props之后。
每次接收新的props之后都会返回一个对象作为新的state，返回null则说明不需要更新state.
配合componentDidUpdate，可以覆盖componentWillReceiveProps的所有用法



**getSnapshotBeforeUpdate**

触发时间: update发生的时候，在render之后，在组件dom渲染之前。
返回一个值，作为componentDidUpdate的第三个参数。
配合componentDidUpdate, 可以覆盖componentWillUpdate的所有用法。





# React动画

## css过渡动画

transition 过渡

```react
import React, { Fragment } from 'react'
import './app.css'

class App extends React.Component{
  constructor(props) {
    super(props)
    this.state = {
      show: true
    }
    this.handleToogle = this.handleToogle.bind(this)
  };

  render(){
    return (
      <Fragment>
        <div className={ this.state.show ? 'show' : 'hide' }>hello,world</div>
        <button onClick={ this.handleToogle }>toggle</button>
      </Fragment>
    )
  };

  handleToogle(){
    this.setState(()=> {
    return {
            show: this.state.show ? false : true
           }
  })
  };
}

export default App
```

```css
.show {
  opacity: 1;
  transition: all 1s ease-in;
}

.hide {
  opacity: 0;
  transition: all 1s ease-in;
}
```

## css动画

animation + @keyframes

```css
.show {
  /*渲染出dom后立即执行动画show-item*/
  animation: show-item 1s ease-in forwards;
}

.hide {
  /* animation属性：绑定动画，设置动画时间与取消，是否保存最后一帧的动画css */
  animation: hide-item 1s ease-in forwards;
}

/* 
@keyframes 动画名{} 
动画执行完成后动画内的css效果不会保存下来会执行原本的css
绑定动画时加入forwards属性后可以将动画最后一帧的状态保存下来作为该元素的css
*/
@keyframes hide-item {
  0% {
    opacity: 1;
    color: red;
  }
  50% {
    opacity: 0.5;
    color: green;
  }
  100% {
    opacity: 0;
    color: blue;
  }
}

@keyframes show-item {
  0% {
    opacity: 0;
    color: red;
  }
  50% {
    opacity: 0.5;
    color: green;
  }
  100% {
    opacity: 1;
    color: blue;
  }
}
```



## react动画框架

react-transition-group 一个第三方的ReactJS动画框架

```
npm install react-transition-group --save
```

```javascript
import { CSSTransition, TransitionGroup } from 'react-transition-group'
// 引入动画框架的标签组件
```

用`<CSSTransition>`标签包裹需要动画的元素
`<CSSTransition>`可以绑定很多自带的钩子函数
如果需要给循环出来的dom标签加动画则需要在最外层循环外套一个`<TransitionGroup>`标签包裹，可以给每一个循环出来的子元素加动画

```react
<TransitionGroup>
{
this.state.list.map((item, index)=>{
	return (
		<CSSTransition 
    		in = { this.state.show }
    		timeout = { 1000 }
   			classNames = "fade"
            // 注意有s
    		unmountOnExit
    		// 动画结束后如果不显示，则不在文档上占位置
   		    onEntered = {(el)=>{el.style.color="blue"}}   
    		// 动画结束后调用钩子函数，参数el代表包裹的dom元素
    		appear = { true }
    		// 第一次渲染到页面上时也通过动画来入场而不是直接展示
    		// 但是第一次入场动画增加的class第一帧叫<name>-appear，第一帧后移除
    		// 第二帧到最后一帧叫<name>-appear-active然后class移除
    		// 动画完成之后就为这个dom元素本身的class样式
 		>
  		    <div>hello,world</div>
 		</CSSTransition>
	  )
  })
}
</TransitionGroup>
```

```css
/*入场动画具有三个.class*/
/*<name>是<CSSTransition> classNames属性名（注意有s）,一般为fade*/
.<name>-enter {} /*入场动画第一帧具有的class*/

.<name>-enter-active {} /*入场动画第二帧到最后一帧，最后一帧后消失*/

.<name>-enter-done {} /*入场动画执行完后拥有的css样式*/

/*出场动画也具有三个class*/
.<name>-exit {} /*出场动画第一帧具有的class*/

.<name>-exit-active {} /*出场动画第二帧到最后一帧，最后一帧后消失*/

.<name>-exit-done {} /*出场动画执行完后拥有的css样式*/
```



### 实现渐入渐出

```react
import React, { Fragment } from 'react'
import './app.css'
import { CSSTransition } from 'react-transition-group'

class App extends React.Component{
  constructor(props) {
    super(props)
    this.state = {
      show: true
    }
    this.handleToogle = this.handleToogle.bind(this)
  };

  render(){
    return (
      <Fragment>
        <CSSTransition 
          in = { this.state.show }
          timeout = { 1000 }
          classNames = "fade"
          >
          <div>hello,world</div>
        </CSSTransition>
        <button onClick = { this.handleToogle }>toggle</button>
      </Fragment>
    )
  };

  handleToogle(){
    this.setState(()=> {
    return {
            show: this.state.show ? false : true
           }
  })
  };
}

export default App
```

```css
.fade-enter {
  opacity: 0;
} /*动画第一帧具有的class*/

.fade-enter-active {
  opacity: 1;
  transition: opacity 1s ease-in;
} /*动画第二帧到最后一帧，最后一帧后消失*/

.fade-enter-done {
  opacity: 1;
} /*动画执行完后拥有的css样式*/

.fade-exit {
  opacity: 1;
} /*动画第一帧具有的class*/

.fade-exit-active {
  opacity: 0;
  transition: opacity 1s ease-in;
} /*动画第二帧到最后一帧，最后一帧后消失*/

.fade-exit-done {
  opacity: 0;
} /*动画执行完后拥有的css样式*/
```





# AntD

AntD是React的一个UI框架

```
npm install antd
```

在组件内引入antd.css，然后按需引入想使用的组件直接使用即可

```react
import React from 'react'
import 'antd/dist/antd.css'
import { Input, Button ,List} from 'antd';

const data = [
  'Racing car sprays burning fuel into crowd.',
  'Japanese princess to wed commoner.'
];


class TodoList extends React.Component {
  render() {
    return (
      <div>
        <div style={{marginTop:'10px', marginLeft:'10px'}}>
          <Input placeholder="todo info" style={{width:'300px',marginRight:'10px'}}/>
          <Button type="Primary">put</Button>
          <List
          style={{marginTop:"10px", width:"300px"}}
          bordered //列表是否有边框
          dataSource={data} // 绑定数据
          renderItem={item => ( // 展开item
            <List.Item>{item}</List.Item>
            )}
        />
        </div>
      </div>
    )
  }
}

export default TodoList
```



## 自定义主题

antd是less写的，如果要自定义主题就需要修改less配置，所以需要安装less-loader并且修改webpack配置

脚手架默认隐藏了配置所以`npm run eject`需要暴露出来

```javascript
// 修改loader配置自定义主题
{
  test: /\.less$/,
  use: [{
        oader: 'style-loader',
     }, {
        loader: 'css-loader', 
     }, {
        loader: 'less-loader',
           options: {
             lessOptions: {
                modifyVars: {
                   'primary-color': '#FFB6C1', // 将antd的主题色改为粉色
                },
             javascriptEnabled: true,
           },
     },
  }],
},
```

自定义主题引入less文件而不是css文件

```
import 'antd/dist/antd.less'
```



## grid栅格

antd中有栅格系统用于布局

布局的栅格化系统，是基于行（row）和列（col）来定义信息区块的外部框架，以保证页面的每个区域能够稳健地排布起来。

- 通过 `row` 在水平方向建立一组 `column`（简写 col）
- 内容应当放置于 `col` 内，并且，只有 `col` 可以作为 `row` 的直接元素
- 栅格系统中的列是指 1 到 24 的值来表示其跨越的范围，在col标签的span属性设定这个元素占24格中的几格。例如，三个等宽的列可以使用 `<Col span={8} />` 来创建
- 如果一个 `row` 中的 `col` 总和超过 24，那么多余的 `col` 会作为一个整体另起一行排列

**antd栅格化系统基于 Flex 布局**，允许子元素在父节点内的水平对齐方式 - 居左、居中、居右、等宽排列、分散排列。子元素与子元素之间，支持顶部对齐、垂直居中对齐、底部对齐的方式。同时，支持使用 order 来定义元素的排列顺序。

布局是基于 24 栅格来定义每一个『盒子』的宽度，但不拘泥于栅格。

```react
import { Row, Col } from 'antd';
import 'antd/dist/antd.css'

ReactDOM.render(
  <>
    <Row>
      <Col span={24}>col</Col> <!--独占一行24格-->
    </Row>
    <Row>
    	<!--两个元素均分一行24格，每个12格-->
      <Col span={12}>col-12</Col>
      <Col span={12}>col-12</Col>
    </Row>
  </>,
  mountNode,
);
```





# Diff算法

diffrence 差异

React中的Diff算法是同层比对，两颗新旧虚拟dom组件树上，父组件与父组件比，子组件与子组件比，一层一层比对，一旦发现某层发现某个组件改变就立刻停止比对，这个组件这一层和这一层之下的子组件全部都会被替换。这样虽然可能会导致之后没有改变的子组件无法复用造成了性能的浪费，但这样算法简单，节点比对速度很快。

虚拟dom树上的key值就是用于比对两个节点是否应该进行比对，所以推荐虚拟dom循环时加上唯一的稳定的key值，可以key值相同直接进行比较。为什么不能使用数组下标index做key值，因为会导致key值不稳定，每次都会去对比dom，随时都在变动，key值就失去了意义了。





# 虚拟DOM

虚拟DOM是一个JS对象用于描述真实的DOM，虚拟DOM提高了VUE和React更新UI的性能，也为类似React Native这种跨端应用的实现提供了基础

```react
render(){
	return (
		<div id="wrapper" a="1">
			<span style="color:red">wtw</span>wy
		</div>
	)
  }		
```

```javascript
// render函数返回JSX后会转化为React.createElement()然后转化为虚拟dom
// h代表React.createElement()
h('div',{id:'wrapper'},a:'1',h('span',{style:{color:'red'}},'wtw'),'wy')
```

```javascript
//render函数创建出虚拟dom对象
{
    type : 'div',
    props : {id:"wrapper",a:'1'},
    children:[
        {type:'span',props:{color:'red',value:"wtw"}},
        {type:'',props:'',children:[],text:'wy'}
    ]
}
```

React会将初始的真实DOM转化为一个虚拟DOM，每当数据进行变化就会生成一个新的虚拟DOM，然后通过Diff算法对比原来的DOM，找到数据的部分后对原本的虚拟DOM进行了修改。JS对象之间的比对性能消耗极低，然后直接操作DOM对相应部分进行修改，达到提供更新UI性能的目的

React渲染页面步骤：JSX--->虚拟DOM--->真实DOM--->数据改变更新虚拟DOM--->更新部分真实DOM

1. 设置state数据
2. 生成JSX模板
3. 生成初始虚拟DOM
4. 用虚拟DOM结构生成真实DOM，完成首次页面渲染
5. 当state发生改变，需要更新UI
6. 数据+模板生成新的虚拟DOM
7. Diff算法比较初始虚拟DOM和新虚拟DOM，找出不同
8. 直接操作真实DOM中对应的地方进行修改





# Context

上下文，是react的新特性，Context提供了一种方式，让数据在组件树中传递不必一级一级手动传递。

导入react提供的api `cerateContext(defaultValue)`创建一个Context对象

Context对象两个可以自定义命名的组件：生产组件`<Provider>`和消费组件`<Consumer>`

生产组件必须是消费组件的祖先组件，一个Context的生产组件只能传递一条消息给消费组件，可以创建多个Context对象来产生多个生产组件，并且相互之间可以嵌套来传递多个消息，Context对象的生产组件不会渲染到dom上，所以不会有影响。

消费组件中不能渲染其他组件，消费组件使用一个函数处理传入的value值，并且函数必须返回jsx，函数的参数就是Provider组件传入的value值

`cerateContext(defaultValue)`中的defaultValue是默认参数，当Consumer向上找不到Provider时就会使用默认参数

`<Context.Consumer>`组件中不能直接渲染其他组件，需要使用一个函数处理传入的value值，函数的参数就是`<Context.Provider>`组件传入的value值



```jsx
import React, { createContext } from 'react';
import './App.css';

// defaultValue是默认参数，当Consumer向上找不到Provider时就会使用默认值
const DataContext = createContext(90)
const NameContext = createContext('abc')

class App extends React.Component {
  state = {
    data: 100,
    name: 'wtw'
  }
  render() {
    return (
      // <Context.Provider>组件传入value值 传递给<Context.Consumer>组件
      <DataContext.Provider value={this.state.data}>
        <NameContext.Provider value={this.state.name}>
          <button onClick={() => this.setState({data: this.state.data + 1})}>
            btn
          </button>
          <Son/>
        </NameContext.Provider>
      </DataContext.Provider>
    );
  }
}

// 儿子组件
class Son extends React.Component {
  render() {
    return (
      <Grandson/>
    )
  }
}

// 孙子组件
class Grandson extends React.Component {
  render() {
    return (
      // <Context.Consumer>组件中不能直接渲染其他组件，需要使用一个函数处理传入的value值，函数的参数就是<Context.Provider>组件传入的value值
      <DataContext.Consumer>
        {
          (data) => {
            return (
              <div>
                <h1>Provider's value: {data}</h1>
                <NameContext.Consumer>
                  {
                    (name) => {
                      return (
                        <h1>Provider's name: {name}</h1>
                      )
                    }
                  }
                </NameContext.Consumer>
              </div>
            ) 
          }
        }
      </DataContext.Consumer>
    )
  }
}

export default App;
```

Context可能会提高组件之间的耦合，让组件变得不纯粹，尽量不要大规模的使用





# ContextType

ContextType是一个类静态成员

```
static contextType = Context对象
```

使用Context会让组件变得不纯粹。所以这决定了Context一般不会大规模的使用。所以一般在一个组件中使用一个Context就好。只有一个Context时可以使用语法糖ContextType。可以省略Consumer组件与内部函数的复杂写法。

定义一个静态变量contextType等于Context组件开启语法糖，然后在调用数据的render函数中定义接受变量等于`this.context`，`this.context`是react内部定义的变量，用于传递生产组件传递的value。

```jsx
class Grandson extends React.Component {
  static contextType = DataContext
  render() {
    const data = this.context // 他会去寻找全局唯一的context，并将value赋值给data
    return (
      <h1>Provider's value: {data}</h1>
    )
  }
}
```





# lazy()/Supense异步组件

懒加载组件函数lazy()，一般配合Supense组件联合使用

将导入组件的行为进行封装，将组件单独拆分成一个文件来导入完成懒加载，因为使用lazy可能会产生一个空档（很短一闪而过），需要使用Supense组件的fallback属性返回jsx来指定空档时的显示内容，Supense包裹异步导入的组件。

lazy是设置webpack的import()，将导入组件单独拆分成一个chunk，单独打包成一个文件来导入。

```jsx
import React, { lazy, Suspense } from 'react';

// 使用webpack魔法注释为分离出来的chunk命名，about会单独打包成一个文件来导入
const About = lazy(() => import(/*webpackChunkName: 'about'*/'./about.jsx'))
class App extends React.Component {
  render() {
    return (
      <div>
        {/*使用Supense组件的fallback属性返回jsx来指定懒加载空档时的显示内容*/}
        <Suspense fallback={<div>Loading</div>}>
          <About></About>
        </Suspense>
      </div>
    )
  }
}

export default App;
```





# PureComponent

优化渲染性能

父组件render函数执行或者子组件的state和props改变都会导致子组件的render函数执行渲染，很多时候子组件往往不需要重新渲染，一般使用shouldComponentUpdate生命周期来优化子组件的渲染性能。

react还提供了一个PureComponent来创建组件（PureComponent和Component都是react中导出的用于创建组件的。）使用PureComponent创建的子组件，组件内部可以简单的识别基本类型的变化，判断子组件是否需要重新渲染。

和生命周期相比PureComponent具有局限，PureComponent使用浅比较（深比较耗费更多性能，因此state数据结构尽量设计浅一点），相当于自己写了一个shouldComponentUpdate钩子函数处理，对props和state进行浅比较。

所谓浅比较就是之比较内部第一层的各个属性的值是否相同，像对象和数组这种数据类型,如果只改变内部的元素,不会造成重新渲染。浅比较用的是函数shallowEqual（浅比较）

```jsx
import React from 'react';

class Foo extends React.PureComponent {
  // 使用PureComponent代替shouldComponentUpdate判断基本类型的变化
  render() {
    console.log('foo render')
    return(
      <div>foo</div>
    )
  }
}

class App extends React.Component {
  state = {
    count: 0
  }
  render() {
    console.log('App render')
    return (
      <div>
        <h1>count: {this.state.count}</h1>
        <button onClick={() => {this.setState({ count: this.state.count + 1 })}}>
          button
        </button>
        <Foo/>
      </div>
    )
  }
}

export default App;
```

PureComponent相较于Component区别就是,对props和state默认进行判断来确定是否渲染,从而减少无效渲染次数. 大部分情况下直接用PureComponent比较好可以提高性能,但是如果遇到需要频繁修改值重新渲染的组件,用Component比较好,因为PureComponent频繁的判断也会影响性能.





# memo

无状态组件是一个函数，无法继承PureComponent来实现简单的渲染优化，react提供了memo函数来给无状态组件提供简单的渲染性能优化

`memo`和`PureComponent`的作用是一样的，不同的是`memo`是生成无状态组件，浅比较的是`Props`（函数组件没有state），`PureComponent`用来生成class组件,比较`Props`和`state` 

React.memo第一个参数就是函数组件。

React.memo可以传入第二个参数，props比较函数，用来自定义比较逻辑，如果不传PureComponent会使用默认的浅比较比较props，函数的参数为prevProps和nextProps。

```jsx
import React, { memo } from 'react';

class App extends React.Component {
  state = {
    count: 0
  }
  render() {
    console.log('App render')
    return (
      <div>
        <h1>count: {this.state.count}</h1>
        <button onClick={() => {this.setState({ count: this.state.count + 1 })}}>button</button>
        <Foo/>
      </div>
    )
  }
}

const Foo = memo(function () {
  console.log('foo')
    return(
      <div>foo</div>
    )
})

export default App;
```





# 浅比较函数

手撕shallowEqual函数

```javascript
// 拿出原型链上的方法
const hasOwn = Object.prototype.hasOwnProperty

// 这个函数实际上是Object.is()的polyfill
function is(x, y) {
  if (x === y) {
    // 处理为+0 != -0的情况
    return x !== 0 || y !== 0 || 1 / x === 1 / y
  } else {
    // 处理 NaN === NaN的情况
    return x !== x && y !== y
  }
}

export default function shallowEqual(objA, objB) {
  // 首先使用is对基本数据类型比较
  if (is(objA, objB)) return true
  
  // 由于Obejct.is()可以对基本数据类型做一个精确的比较， 所以如果不等
  // 只有一种情况是误判的，那就是object,所以在判断两个对象都不是object，就可以返回false了
  if (typeof objA !== 'object' || objA === null ||
      typeof objB !== 'object' || objB === null) {
    return false
  }

  // 过滤掉基本数据类型之后，就是对对象的比较了
  // 首先拿出key数组，对key的长度进行对比

  const keysA = Object.keys(objA)
  const keysB = Object.keys(objB)

  // 长度不等直接返回false
  if (keysA.length !== keysB.length) return false
  // key相等的情况下，在去循环比较
  for (let i = 0; i < keysA.length; i++) {
  // key值相等的时候
  // 借用原型链上真正的 hasOwnProperty 方法，判断ObjB里面是否有A的key的key值
  // 属性的顺序不影响结果也就是{name:'daisy', age:'24'} 跟{age:'24'，name:'daisy' }是一样的
  // 最后，对对象的value进行一个基本数据类型的比较，返回结果
    if (!hasOwn.call(objB, keysA[i]) ||
        !is(objA[keysA[i]], objB[keysA[i]])) {
      return false
    }
  }

  return true
}
```





# immutable.js

- immutable       彻底拥抱不可变值

state不可变值的有一个方案时每次都彻底深拷贝，但这样性能很低。immutable基于共享数据（比深拷贝快）。

但是因为api和原生js有较大不同。



# 结合TS

tsx防抖

```tsx
const debounce = (fn: Function, ms = 300)=> {
  let timeoutId: any;
  return function (value: string, e?: React.ChangeEvent) {
  	if(e) e.persist();
  	clearTimeout(timeoutId);
  	timeoutId = setTimeout(() => {
    	fn(value)
  	}, ms)
  }
}
```

