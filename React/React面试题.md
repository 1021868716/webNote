# React面试题

# ajax放在哪个生命周期

同vue，放在dom挂载完后的componentDidMount上

# React性能优化

- 渲染列表时加key
- 自定义事件，dom事件及时销毁
- 合理使用异步组件
- 减少jsx函数bind this的次数，放在constructor中提前处理
- 合理使用SCU，PureComponent和memo
- 合理使用Immutable.js
- 使用SSR

# React和Vue的区别

相同点

- 都支持组件化
- 支持数据驱动视图（响应式）
- 都使用vdom操作DOM



不同点

- React使用JSX拥抱JS，Vue使用template拥抱HTML

- React是函数式编程，Vue是声明式编程

  React使用setState不可变值修改数据，Vue直接使用变量本身进行修改

- React是MV*（不自带双向绑定），Vue是MVVM（自带双向绑定）



# this.setState三大特性

this.setState三大特性

- 不可变值

- 可能是异步更新

- 更新可能被合并

  

## 不可变值

this.state需要使用this.setState()修改，this.state是一个不可变值（包括在this.setState()内），原因是会对shouldComponentUpdate中的比较产生影响，并且这样符合函数式编程，纯函数的编程思想

```javascript
this.state = {
  arr: [1,2,3],
  obj1:{},
  obj2:{}
}
this.setState({
  arr: this.state.arr.concat(4), // 追加
  // 对对象拓展的两种方式
  obj1: Object.assign({}, this.state.obj1, {a: 100}),
  obj2: {...this.state.obj2, a:100}
})
```

我们对数组操作不能使用push，pop，splice等方法，因为这些方法是在原本的数组上修改，而this.state是一个不可变值，只能使用返回新值的方法。

对对象拓展也需要使用Object.assign()或者es6拓展运算符，否则就违背了this.state不可变值。





## 可能是异步更新

setState本身无所谓异步还是同步，这和batchUpdate机制有关，看将setState放入异步队列还是同步队列中，所以可能是异步更新，亦可能是同步更新。



**同步代码（方法）中直接使用，处于batchUpdate机制中，setState是异步更新的**

```javascript
this.state = {
  count: 0
}
...
handleClick() {
  this.setState({
  	count: this.state.count + 1
	})
	console.log('count', this.state.count) // 0,说明此时更新操作是异步的
}
```

如果需要等更新后才执行的操作可以给setState加第二个参数回调函数，函数会在更新state后才去执行

```javascript
this.setState({
  count: this.state.count + 1
},() => {
  console.log('count', this.state.count) // 1,说明此时更新操作是同步的
})
```





**setTimeout回调中，setState不处于batchUpdate机制中，setState是同步更新的**

```javascript
setTimeout(()=>{
	this.setState({
  	count: this.state.count + 1
	})
	console.log('count', this.state.count) // 1,说明此时更新操作是同步的
},0
```





**在自定义的dom事件回调中，setState不处于batchUpdate机制中，setState是同步更新的**

自定义是指不是在jsx中直接绑定而是通过addEventListener这样的形式绑定，jsx中绑定相当于同步代码（方法）中直接使用

```javascript
handleClick = () => {
  this.setState({
	count: this.state.count + 1
  })
  console.log('count', this.state.count)
})
componentDidMount() {
	document.body.addEventListener('click', this.handleClick)
     // 1,说明此时更新操作是同步的
}
componentWillUnmount() {
    // 及时销毁自定义事件，避免内存泄漏
    document.body.removeEventListener('click', this.handleClick)
}
```





## 更新可能被合并

**setState传入对象时可能会合并**（类似于object.assgin），为了减少操作dom，也就是多次setState会合并成一次setState来更新

```
this.setState({
  a: 1
})
this.setState({
  b: 2
})

以上两次setState，react会进行合并成一次，等价下面
this.setState({
  a: 1,
  b: 2
})
```

如下，三次setState的对象合并了，但是合并之后count属性同名，只有一个count属性生效，所以最终结果只是+1而不是+3

```javascript
handleClick = () => {
	this.setState({
  		count: this.state.count + 1
	})
	this.setState({
  		count: this.state.count + 1
	})
	this.setState({
  		count: this.state.count + 1
	})
}

执行后，结果只加了1，而不是加3
```





**setState传入函数不会被合并，因为函数没法合并**

```javascript
handleClick = () => {
	this.setState((prevState, props)=>{
		return {
			count: prevState.count + 1
		}
	})
	this.setState((prevState, props)=>{
		return {
			count: prevState.count + 1
		}
	})
	this.setState((prevState, props)=>{
		return {
			count: prevState.count + 1
		}
	})
}

执行后，结果加了3
```





## setState主流程

setState处于batch update机制中就是异步更新，否则同步更新

```
this.setState(newState)
				|
newState存入pending队列
				|
判断是否处于batch update机制中
				|
Y：保存当前组件（以及相关子组件）于dirtyComponents列表中

N：遍历所有的dirtyComponents，
	 调用updateComponent，
	 更新pending state or props
```





# batchUpdate机制

setState有时是异步的（方法中使用），有时是同步的（setTimeout，自定义的DOM事件），有时是合并的（传入对象），有时不合并（传入函数）。

在React可以管理的入口函数（能命中batchUpdate机制的函数）开始执行之前，React会设置一个isBatchingUpdates变量，初始值为true，代表当前处于batchUpdate机制，当函数执行结束之后，isBatchingUpdates变量会设置为false，表示结束batchUpdate机制。

在React可以管理的入口函数中同步执行setstate时，发现isBatchingUpdates变量状态为true，React判断命中batchUpdate机制，会将setState将放入异步队列中，等当前函数执行完后才去执行setState。

而在setTimeout和自定义的dom事件中，需要等同步队列执行完后（执行完后isBatchingUpdates变量会变为false状态），才去执行宏任务队列中的回调函数。执行回调时，isBatchingUpdates已经变为false状态，而setTimeout和自定义的dom事件又不能命中batchUpdate机制，不能使isBatchingUpdates变为true，所以react判断不处于batchUpdate机制，执行到setState时立刻同步更新。



## 哪些能命中batchUpdate

能命中batchUpdate机制的函数开始执行之前会使isBatchingUpdates变量重置为true状态。

- 所有React可以管理的入口

- 生命周期（和生命周期中调用的函数）
- React中注册的事件



## 哪些不能命中batchUpdate

- 所有React无法管理的入口

- setTimeout setInterval等的回调函数（以及其中调用的函数）
- 自定义DOM事件回调函数（以及其中调用的函数）







# 组件通信

父子组件props和props传入的回调函数

嵌套组件使用中间组件层层传递props或者跨层传递context

非嵌套组件间使用自定义事件的方式或者利用二者共同父组件的 context 对象进行通信

状态管理：redux，mobx





# 无状态组件和class组件的区别

- 纯函数，输入props，输出jsx

- 没有实例，没有生命周期，没有state

- 不能拓展其他方法





# 非受控组件

用onChange配合setState和e.target.value实现双向绑定的表单组件叫受控组件。

非受控组件是通过ref，defaultValue/defaultChecked（设置初始值），然后手动操作DOM元素的方式来操作组件，意思是组件不受state控制，只是使用state设置初始值，之后只能通过ref的方式来操作dom元素。

```react
constructor() {
  this.state = {
    name: 'wtw'
  }
  this.nameInputRef = React.createRef() // 创建ref
}

render() {
  return <div>
		<input defaultValue={this.state.name} ref={this.nameInputRef} />
	</div>
}
```



非受控组件的使用场景

- 必须手动操作dom，setState实现不了
- 文件上传`<input type='file'/>`
- 某些富文本编辑器必须传入dom元素





# 异步组件

使用React.lazy和React.Suspense





# React性能优化

- shouldComponentUpdate
- PureComponent和memo
- 不可变值immutable.js







# 公共逻辑的抽离

- mixin    已被React废弃
- 高阶组件HOC
- Render Props



HOC和Render Props的区别

HOC：模式简单，但会增加组件层级

Render Props：代码简洁，学习成本更高，





## 高阶组件HOC

HOC不是一个功能而是一种模式，类似于工厂模式(vue中也可以实现HOC)，表现形式是创建一个包含公共数据和逻辑的HOC函数，传入一个组件，函数将公共逻辑和传入的组件组合生成一个新组件返回。react-redux中的connect就是高阶组件。

高阶组件(HOC)应该是无副作用的纯函数，且不应该修改原组件,即原组件不能有变动

高阶组件(HOC)不关心你传递的数据(props)是什么，并且新生成组件不关心数据来源

高阶组件(HOC)接收到的 props 应该透传给被包装组件即直接将原组件prop传给包装组件

高阶组件完全可以添加、删除、修改 props

```react
const HOCFactor = (Component) => {
  class HOC extends React.Component {
      // 此处书写公共逻辑
  	render() {
  		return <Component {...this.props} />
  	}
  }
  return HOC
}

// 通过HOCFactor将旧组件封装出包含公共逻辑的新组件
const HOCCompontent1 = HOCFactor(component1)
const HOCCompontent2 = HOCFactor(component2)
```



```react
const withMouse = (Component) => {
  class withMouseComponent extends React.Component {
    constructor(props) {
      super(props)
      this.state = { x:0, y:0 }
   }
    
  handleMOuseMove = (event) => {
		this.setState({
      x: event.clientX,
      y: event.clientY
    })
  }
  
  render() {
    return <div onMouseMove={this.handleMOuseMove}>
    	<Component {...this.props} mouse={this.state}/>
    </div> 
   }
  }
}

// App是一个普通的无状态组件
const App = (props) => {
  const {x, y} = props.mouse
  return <div>
  	<h1> this mouse position is {x},{y} </h1>
  </div>
}

// 导出的是经过HOC函数处理生成的HOC高阶组件
export default withMouse(App)
```





## Render Props

Render Props的核心思想是通过一个函数将class组件的state作为props传递给纯函数组件，比HOC更高级更优雅。

公共组件中只定义公共的数据和方法，显示是根据父组件传入的函数组件进行显示。

```react
class Factory extneds React.Component {
	constructor() {
		this.state = {
			// state 即多个组件公共逻辑的数据
      a: 100,
      b: 200
		}
	}
	render() {
    // 执行传入的函数生成组件，公共逻辑this.state作为参数
	  return <div>
	  	{this.props.render(this.state)}
	  </div>
	}
}

const App = () => {
  // 调用公共逻辑组件Factory 
	<Factory render = {
      /* 传入一个render函数组件，我们希望获取Factor中的公共逻辑，并按照这个函数组件的方式显示*/
      (props) => <p>获取公共数据：{props.a} and {props.b}</p>
    }/>
}
```







# Redux

- state不可变值

- 单项数据流：只有reducer能改变store中state，组件只是dispatch(action)
- react-redux
- 异步action
- 中间件



## 基本概念

- store/state

- action

- reducer





# React-router

- 路由模式（同vue）

  hash，H5 history

- 路由配置（同vue）

  动态路由，懒加载(利用lazy和Suspense)







# 函数式编程

函数式编程是一种编程范式，概念较多

1.函数是一等公民

 2.只用表达式不用语句 

3.没有副作用(side effect)

 4.不修改状态 

5.引用透明



函数式编程要满足以下两点

- 纯函数：返回一个新值，没有副作用（不会偷偷修改其他值）
- **不可变值**







# vdom和diff

h函数返回vnode，然后使用patch函数更新到真实dom上



diff算法和vue基本相同

- 只比较同一层级，不跨级比较
- tag不相同则直接删掉重建，不再深度比较
- tag和key相同，则认为是相同节点，不再深度比较



vue和react的diff算法，都是忽略跨级比较，只做同级比较。vue diff时调动patch函数，参数是vnode和oldVnode，分别代表新旧节点。

1. vue比对节点，当节点元素类型相同，但是className不同，任务是不同类型元素，删除重建，而react会认为是同类型节点，只是修改节点属性

2. vue的列表比对，采用从两端到中间的比对方式，而react则采用从左到右依次比对的方式。当一个集合，只是把最后一个节点移动到了第一个，react会把前面的节点依次移动，而vue只会把最后一个节点移动到第一个。总体上，vue的对比方式更高效。







# JSX编译原理

jsx类似于vue的template，不是html也不是js，都需要通过编译生成js代码来运行生成虚拟dom。

jsx本质就是React.createElemen()函数（h函数），执行h函数返回vnode，然后通过diff算法的patch函数渲染在真实dom上。

React.createElemen函数（h函数）第一个参数传入tag或者组件（根据首字母大小写区分），第二个参数传入tag的属性集合对象，第三个参数是传入child（多个子元素可以当成多余的参数传入，也可以组合成一个数组传入），child如果没有tag只是字符串直接传入字符串即可



babel中集成了jsx的编译工具，可以使用babel官网编译jsx

```react
const imgElem = <div id='div1'>
   <p>some text</p> 
   <img src={imgUrl}/>
</div>
```

编译后

```javascript
var imgElem = React.createElement("div",
  {
    id: "div1"
  },
  React.createElement("p", null, "some text"),
  React.createElement("img", {
    src: imgUrl
  })
);
```



**jsx加载组件**

```react
const App = <div>
	<Input submitTitle={onSubmitTitle}/>
  <List list={list}/>
</div>
```

编译后

```javascript
var App = React.createElement("div", null,
	React.createElement(Input, { // Input不是字符串而是一个变量，说明是一个组件而不是一个tag标签
  	submitTitle: onSubmitTitle
	}), 
	React.createElement(List, {
  	list: list
	})
);
```



**jsx绑定事件**

```react
const eventList = <p onClick ={this.clickHandler}>
	some text
</p>
```

编译后

```javascript
var eventList = React.createElement("p", {
  onClick: (void 0).clickHandler
}, "some text");
```





**jsx循环渲染列表**

```react
const listElem = <ul>
	{
  	this.state.list.map((item, index) => {
    	return <li key = {item.id}>
      	index: {index};
       	title: {item.title}
      </li>         
  	})
  }
</ul>
```

编译后

```javascript
var listElem = React.createElement("ul", null, 
(void 0).state.list.map(function (item, index) {
  return React.createElement("li", 
      		 {
   					 key: item.id
  				 }, "index: ", index, "; title: ", item.title // child只是字符串没有tag
         );
  })
);
```







# 合成事件机制

**react jsx中所有事件都挂载到document上**，并且事件中event不是原生的，是SyntheticEvent合成事件对象。这就是合成事件机制。

**合成事件机制原理**：给组件绑定了dom事件，事件触发后会冒泡到document上，document监听到事件触发进入事件合成阶段，React会将事件内容封装交给中间层SyntheticEvent（负责所有事件合成），然后使用统一的分发函数dispatchEvent将封装后的合成事件对象event派发到相应事件处理函数上。

SyntheticEvent模拟了原生dom事件event的全部能力，例如阻止默认行为`event.preventDefault()`，组织冒泡`event.stopPropagation()`，指向触发事件的元素`event.target`，指向绑定事件的元素`event.currentTarget`等。而且原生event也可以通过`event.nativeEvent`调用出来。



## 为什么要有合成事件机制

- 更好的兼容性和跨平台

  React自己实现了这种类dom的机制，这样跨到没有dom的平台上也能正常运行（例如React native）

- 挂载到ducument上，减少内存消耗，避免频繁解绑

  组件上没有绑定事件，销毁组件的时候就不用去解绑事件，减少了组件对dom事件的依赖。

- 方便事件的统一管理（例如事务机制）







# 组件渲染过程

- 组件初始渲染过程：

1）获取props和state

2）render() 生成vnode

3）patch(elem, vnode)



- 组件更新渲染过程：

1）setState(newState)

2）保存当前组件（以及相关子组件）于dirtyComponents列表中

3）render()遍历dirtyComponents列表生成newVnode，

4）patch(node, newVnode)





# Fiber

fiber：n. 纤维

React 框架内部的架构可以分为 3 层：

- Virtual DOM 层：描述页面长什么样。
- Reconciler（调节器） 层（Stack Reconciler/Fiber Reconciler）：执行调用组件生命周期方法，进行 Diff 运算等。`Fiber` 之前的 Reconciler层（Stack Reconciler）是自顶向下的递归算法，遍历新数据生成新的Virtual DOM，通过 Diff 算法，找出需要更新的元素，放到更新队列中去。
- Renderer 层：根据不同的平台，渲染出相应的页面，比较常见的是 ReactDOM 和 ReactNative。根据所在的渲染环境，遍历更新队列，调用渲染宿主环境的 API, 将对应元素更新渲染。在浏览器中，就是更新对应的DOM元素，除浏览器外，渲染环境还可以是 Native、WebGL 等等。

在页面元素很多，且需要频繁刷新的场景下，React 15 会出现掉帧的现象。其根本原因，JS单线程且与ui渲染互斥，Reconciler层大量的计算任务占用主线程阻塞了浏览器的 UI 渲染。

 Stack Reconciler层的调度策略像函数调用栈一样，递归遍历所有的 Virtual DOM 节点，进行 Diff，是自顶向下的递归算法，一旦开始无法中断，要等整棵 Virtual DOM 树计算完成之后，才将任务出栈释放主线程。而浏览器中的渲染引擎是单线程的，除了网络操作，几乎所有的操作都在这个单线程上执行，此时如果主线程上用户交互、动画等周期性任务无法立即得到处理，影响体验。

当递归更新时间超过了16.67ms每帧，用户就会感觉到交互有卡顿的现象。（可以通过火焰图查看每次js执行时间是否超过了一帧16.67ms）

针对这一问题，React 团队从框架层面对 web 页面的运行机制做了优化，提出了Fiber架构。

从React 16开始，React推出了该内部实例树的新实现以及负责操作树的算法，被称为Fiber，主要功能是提供异步渲染（Async Mode）与时间分片（Time Slicing）。Fiber架构是除了React元素原有的Virtual DOM树之外，创建了一个用于保持状态的内部实例树(internal instances)（组件，DOM节点等），与之相对的是跟具体平台有关的public instance，也被称为Host instance 。为了加以区分，以前的三层架构中的 Reconciler层被命名为Stack Reconciler层，现在是Fiber Reconciler层。

Fiber Reconciler的调度策略类似操作系统中进程调度的时间片分片调度算法。把Diff中一个耗时长的任务分成很多小片（work），每一个小片的运行时间很短，虽然总时间依然很长，但是每个小任务执行完后都会给其他任务一个执行的机会，这样唯一的线程就不会被独占，让每个子任务都有机会轮转执行，而且重要的高权重任务（例如用户交互的输入等）可以优先执行，做完一段任务就把时间控制权交还给主线程，而不像之前长时间占用，从而实现对任务的暂停、恢复、复用灵活控制，这样主线程上的用户交互及动画可以快速响应，从而解决卡顿的问题。



## Fiber架构

Fiber的调度拆分的原理是`window.requestIdleCallback()`和Fiber Node，为了兼容所有平台，`facebook` 单独实现了其功能，作为一个独立的 npm 包react-schedule。

Fiber Node在 react 生成的 Virtual Dom 基础上增加的一层数据结构，主要是为了将递归遍历转变成循环遍历，配合 `requestIdleCallback` API, 实现任务拆分、中断与恢复。

![img](https://pic1.zhimg.com/80/v2-cf341c21c60e826195fd7c03869c061c_720w.jpg)



Stack Reconciler 运作的过程（patch）是不能被打断的，必须一条道走到黑。而 Fiber Reconciler 每执行一段时间，都会将控制权交回给浏览器，patch的过程可以分段执行，React 中使用Fiber后，patch的过程可以拆分为两个阶段：

- reconciliation阶段（n.  调解;和解）：执行diff算法得出需要更新的节点，纯js计算，第一阶段生命周期等。

  此阶段如果不拆分可能会出现性能问题：js单线程，并且和DOM渲染共用一个线程，当组件足够复杂时组件更新时计算和渲染压力大，如果此时再有DOM操作需要（动画，鼠标拖拽）可能出现卡顿

  Reactreconciliation阶段可以进行任务拆分（commit阶段任务无法拆分），拆分成了一个个子片段的小任务。DOM需要渲染时先暂停reconciliation阶段正在进行的js计算，剩余的子片段的任务将在空闲时恢复运行。例如检查组件的state个props，进行diff比对，调用生命周期，更新引用等。所有这些活动在fiber架构中统称为“work”。不同work具有不同权重，高权重的work优先执行。（Fiber更类似一种协程的实现，优先级调度属于多线程）

  为了达到这种效果，就需要有一个调度程序 (Scheduler) 来进行work权重分配。work的权重优先级有六种：

  - synchronous，与之前的Stack Reconciler操作一样，同步执行
- task，在nextTick之前执行
  - animation，动画，下一帧之前执行
  - high，高优先级，在不久的将来立即执行
  - low，低优先级，稍微延迟执行也没关系
  - offscreen，下一次render时或scroll时才执行
  
  优先级高的任务（如键盘输入）可以打断优先级低的任务（如Diff）的执行，从而更快的生效。reconciliation 阶段可被打断的特性，让优先级更高的work先执行，从框架层面大大降低了页面掉帧的概率。

  

- commit 阶段：处理 effect list （更新 DOM 树、调用第二阶段生命周期函数以及更新 ref 等内部状态，最新版本的react源码中已经移除了effect list），最后将diff结果渲染DOM，此阶段不可拆分



相应的，Fiber中类组件的生命周期也会对应拆分为两个阶段（以render为界限）

- reconciliation阶段：这个阶段在计算前后 dom 树的差异，这个阶段可被打断，所以移除了三个可能因为打断而导致错误执行的生命周期，新增了两个替代他们的生命周期

  ~~componentWillMount~~

  ~~componentWillReceiveProps~~

  shouldComponentUpdate

  ~~ComponentWillUpdate~~

- render

- commit阶段：这个阶段将把更新渲染到页面上，这个阶段不可被打断

  componentDidMount

  componentDidUpdate

  componentWillUnmount

由于 reconciliation 的阶段会被打断，可能会导致 commit 前的这些生命周期函数多次执行。react 官方目前已经把 componentWillMount、componentWillReceiveProps 和 componetWillUpdate 标记为 unsafe，并使用新的生命周期函数 getDerivedStateFromProps 和 getSnapshotBeforeUpdate 进行替换。





## requestIdleCallback()

requestIdleCallback 是浏览器提供的一个 api，可以让浏览器在空闲的时候执行回调，在回调参数中可以获取到当前帧剩余的时间，fiber 利用了这个参数，判断当前剩下的时间是否足够继续执行任务，如果足够则继续执行，否则暂停任务，并调用 requestIdleCallback 通知浏览器空闲的时候继续执行当前的任务。

```
var handle = window.requestIdleCallback(callback[, options])
```

`window.requestIdleCallback()`方法将在浏览器的空闲时段内调用的函数排队。这使开发者能够在主事件循环上执行后台和低优先级工作，而不会影响延迟关键事件，如动画和输入响应。函数一般会按先进先调用的顺序执行，然而，如果回调函数指定了执行超时时间`timeout`，则有可能为了在超时前执行函数而打乱执行顺序。你可以在空闲回调函数中调用`requestIdleCallback()`，以便在下一次通过事件循环之前调度另一个回调。

requestIdleCallback 就能够充分利用帧与帧之间的空闲时间来执行 JS，可以根据 callback 传入的 dealine 判断当前是否还有空闲时间（timeRemaining）用于执行。由于浏览器可能始终处于繁忙的状态，导致 callback 一直无法执行，它还能够设置超时时间（timeout），一旦超过时间（didTimeout）能使任务被强制执行。

参数：

- callback

一个在事件循环空闲时即将被调用的函数的引用。函数会接收到一个名为 [`IdleDeadline`](https://developer.mozilla.org/zh-CN/docs/Web/API/IdleDeadline) 的参数，这个参数可以获取当前空闲时间以及回调是否在超时时间前已经执行的状态。

- `options` 可选

  包括可选的配置参数。具有如下属性：

  - `timeout`：如果指定了timeout并具有一个正值，并且尚未通过超时毫秒数调用回调，那么回调会在下一次空闲时期被强制执行，尽管这样很可能会对性能造成负面影响。



## Fiber Node

从流程图上看到会有 `Fiber Node` 节点，这个是在 react 生成的 Virtual Dom 基础上增加的一层数据结构，主要是为了将递归遍历转变成循环遍历，配合 `requestIdleCallback` API, 实现任务拆分、中断与恢复。

为了实现循环遍历，Fiber Node上比vnode携带了更多的信息， 其数据结构如下所示：

```typescript
export type Fiber = {
  tag: TypeOfWork,
  key: null | string,
  type: any,

  return: Fiber | null,
  child: Fiber | null,
  sibling: Fiber | null,

  effectTag: TypeOfSideEffect,
  nextEffect: Fiber | null,
  firstEffect: Fiber | null,
  lastEffect: Fiber | null,

  alternate: Fiber | null,
  stateNode: any,
  ...
}

Fiber = {
 tag // 标记任务的进度
 return // 父节点
 child // 子节点
 sibling // 兄弟节点
 alternate // 变化记录
 .....
};
```

 fiber 基于链表结构，拥有一个个指针，指向它的父节点子节点和兄弟节点，在 diff 的过程中，依照节点连接的关系进行遍历。

Stack 是 tree 的结构，沿着树状层级结构向下处理。Fiber 则是依照 return、child 及 sibling 的顺序来针对该 ReactElement 做处理。





## Fiber Tree

每一个 Fiber Node 节点与 Virtual Dom 一一对应，所有 Fiber Node 连接起来形成 Fiber tree, 是个链表树结构，如下图所示：

![img](https://pic4.zhimg.com/80/v2-a825372d761879bd1639016e6db93947_720w.jpg)

Fiber Reconciler 在阶段一进行 Diff 计算的时候，会生成一棵 Fiber 树。这棵树是在 Virtual DOM 树的基础上增加额外的信息来生成Fiber Node组成的，**Fiber树本质来说是一个单链表**。

Fiber 树在首次渲染的时候会一次过生成。在后续需要 Diff 的时候，会根据已有树和最新 Virtual DOM 的信息，生成一棵新的树。这颗新树每生成一个新的节点，都会将控制权交回给主线程，去检查有没有优先级更高的任务需要执行。如果没有，则继续构建树的过程：

如果过程中有优先级更高的任务需要进行，则 Fiber Reconciler 会丢弃正在生成的树，在空闲的时候再重新执行一遍。

在构造 Fiber 树的过程中，Fiber Reconciler层会将需要更新的节点信息保存在Effect List当中（effect n.结果；影响），在commit阶段执行的时候根据Effect List批量更新相应的节点。最新版本的react源码中移除了effect list。





# Concurrent Mode

concurrent：并存的; 同时发生的;adj.

Concurrent Mode 只是fiber异步渲染（Async Mode）的重新定义，来凸显出 React 在不同优先级上的执行能力，与其它的异步渲染方式进行区分。

Fiber Reconciler 就是 Concurrent 的雏形。Concurrent能使 React 在长时间渲染的场景下依旧保持良好的交互性，能优先执行高优先级变更，不会使页面处于卡顿或无响应状态，从而提升应用的用户体验。





# transaction事务机制

transaction就是执行batchUpdate这样的内部设置的事务机制

何为事务？

> 根据维基百科的解释: 提供独立可靠的恢复机制，保证出错时数据的一致性，并且不同事务之间互相独立。

事务一般在数据库中使用的比较多，能保证出错的时候进行rollbakc恢复。在React源码中作者给出了事务的一张明细图能够帮助较好的理解，就是在执行目标代码之前，之后可以添加很多自己的逻辑，将目标代码经过自身的perform的封装可以在执行前后添加一组或者多组逻辑方法

React内部的事务分为三个阶段initialize, method以及close阶段，会在开始和结束时候分别遍历transactionWrapper内部的所有初始化方法和close方法。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191017192319914.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzQ3NjE0MQ==,size_16,color_FFFFFF,t_70)