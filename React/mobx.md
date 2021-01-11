# mobx

`redux`：函数式编程风格

`mobx`：

- 响应式面向对象编程风格
- 集成度高无需引入大量第三方库
- 细粒度高渲染性能好（避免不必要的组件重渲染）

mobx的核心理念是：状态变化引起的副作用应该被自动触发



**Redux vs Mobx**

那么具体到这两种模型，又有一些特定的优缺点呈现出来。

**Redux：**

数据流流动很自然，因为任何 dispatch 都会导致广播，需要依据对象引用是否变化来控制更新粒度。
如果充分利用时间回溯的特征，可以增强业务的可预测性与错误定位能力。
时间回溯代价很高，因为每次都要更新引用，除非增加代码复杂度，或使用 immutable。
时间回溯的另一个代价是 action 与 reducer 完全脱节，数据流过程需要自行脑补。原因是可回溯必然不能保证引用关系。
引入中间件，其实主要为了解决异步带来的副作用，业务逻辑或多或少参杂着 magic。
但是灵活利用中间件，可以通过约定完成许多复杂的工作。
对 typescript 支持困难。



**Mobx：**

数据流流动不自然，只有用到的数据才会引发绑定，局部精确更新，但免去了粒度控制烦恼。
没有时间回溯能力，因为数据只有一份引用。
自始至终一份引用，不需要 immutable，也没有复制对象的额外开销。
没有这样的烦恼，数据流动由函数调用一气呵成，便于调试。
业务开发不是脑力活，而是体力活，少一些 magic，多一些效率。
由于没有 magic，所以没有中间件机制，没法通过 magic 加快工作效率（这里 magic 是指 action 分发到 reducer 的过程）。
完美支持 typescript。

**到底如何选择**

从目前经验来看，前端数据流不太复杂的情况，使用 Mobx，因为更加清晰，也便于维护；如果大型项目建议使用redux。



安装mobx

```
npm i mobx -s
```

- mobx-react

安装React 绑定库: 

```
npm install mobx-react --save
```



- mobx-react-lite

mobx-react-lite，它是基于 `React 16.8` 和 `Hooks` 的 `MobX` 的轻量级React绑定。





# observable

observable是一种让数据的变化可以被观察的方法，传入需要观察的数据源，生成并返回一个可观察对象，之后对这个可观察对象的数据进行直接修改都能被mobx观测到（5.x版本使用了 Proxy 来实现 observable）。

常见的用于被观察的数据类型有：string，number，boolean，symbol，对象，数组

```js
import {observable} from 'mobx'
```





**引用类型**：

- es6 map则使用`observable.map()`，返回一个ObservableMap对象，该对象实现了map类型的所有功能

  ```js
  const map = observable.map({ key: "value"});
  map.set("key", "new value");
  console.log(map.get("key"))// new value
  ```

  

- 数组使用``observable()`，返回一个Proxy对象

  可以将数组转变为可观察的。 数组内部也是递归的，所以数组中的所有(未来的)值都会是可观察的。可通过`isObservableArray`这个api来检测可观察数组。

  ```js
  const list = observable([1, 2, 4]);
  list[2] = 3;
  console.log(Array.isArray(list))
  // 4.x版本是false，4.x版本提供isArrayLike函数判断数组，5.x版本是true
  console.log(list[2]) // 3
  ```

  

- 没有原型的对象使用``observable()`，返回一个Proxy对象，对象会被克隆并且所有的属性都会被转换成可观察的。

  ```js
  const a = observable({
      name: '123',
      age: 12
  });
  console.log(a.age)// 12
  ```
  


**基本数据类型**：

基本数据类型需要使用`observable.box()`进行监控，该方法会返回一个ObservableValue对象，并提供get()/set()方法来读写基本数据类型数值

```js
import {observable} from 'mobx'
let num = observable.box(20)
let string = observable.box('abc')
let bool = observable.box(true)
num.set(30)
console.log(num.get(), string.get(), bool.get())
// 30 "abc" true
```



## 装饰器语法

mobx为observable提供了一个装饰器语法，可以简单的实现监控类中的数据，修饰器调用会自动识别类型，无需再使用box，map等方法来生成可观察对象。用装饰器语法监控的基本类型无需使用get/set来进行读写。

`@observable` 等同于` @observable.deep`

```js
import {observable} from 'mobx'

class Store{
    @observable array = []
    @observable obj = {}
    @observable map = new Map()

    @observable string = 'abc'
    @observable number = 123
    @observable bool =false
}
```





# 对数据变化产生反应

提供四个api对可观察对象的数据变化产生反应

## computed

计算值函数，将其他可观察对象的数据组合起来，**并返回一个新的可观察对象**

computed函数的参数为一个无参数的函数，该参数函数内可以调用可观察对象的数据。computed会在根据所观察的变量**发生变化**的时候触发参数函数并更新生成的可观察对象。

computed也可以作为装饰器来使用，被装饰的参数会自动转化为computed生成的可观察对象。

```js
import {observable, computed} from 'mobx'

class Store{
    @observable array = []
    @observable obj = {}
    @observable map = new Map()

    @observable string = 'abc'
    @observable number = 123
    @observable bool = true

    @computed get mixed() {
        return store.string + '/' + store.number
    }
}

var store = new Store()
var value = computed(function() {
    return store.string + '/' + store.number
})

console.log(value.get()) // abc/123
console.log(store.mixed) // abc/123

store.string = 'world'
console.log(value.get()) // world/123
console.log(store.mixed) // world/123
```

## autorun

自动运行，autorun函数的第一个参数是一个函数，参数函数中可以引用可观察对象的数据，autorun参数函数会在初始化阶段时会先执行一次，来获悉该函数内有哪些可观察数据被引用了。

之后这些可观察数据发生改变时autorun也会自动运行。如果有一个函数应该自动运行，但不会产生一个新的值，请使用`autorun`， 其余情况都应该使用 `computed`。 

autorun返回自身的清理函数，运行该清理函数后autorun不会再运行。

```js
import {observable, autorun} from 'mobx'

class Store {
    @observable array = []
    @observable obj = {}
    @observable map = new Map()

    @observable string = 'abc'
    @observable number = 123
    @observable bool =true
}

var store = new Store()

autorun(() => {
    console.log(store.string + '/' + store.number)
}) // abc/123
// 每次修改autorun中的可观察数据都会自动执行autorun
store.string = 'world' // world/123
store.number = 456	// world/456
```



## when

when函数接收两个函数参数，在初始化阶段第一个参数函数会先执行一次，来获悉该函数内有哪些可观察数据被引用了。

第一个参数函数需要返回一个bool值，根据该bool判断是否执行第二个参数函数并且保证该参数函数最多执行一次。第一个参数函数必须根据可观察数据来计算bool值，不能根据普通变量计算，否则第二个函数不会执行。

```js
when(() => store.bool, () => {
    console.log('in')
})
```



## reaction

when函数接收两个函数参数，在初始化阶段第一个参数函数会先执行一次，来获悉该函数内有哪些可观察数据被引用了。

之后在第一个参数函数中的可观察数据被修改时会自动执行第二个函数，第二个函数会将第一个函数的返回值作为参数来执行。



# action

action类似redux中的action，是store中用来改变state的方法，原本mobx允许直接对可观察数据进行修改，但直接对可观察数据进行修改会带来一定的副作用，例如频繁触发autorun和reaction，对性能不好，因此可以使用action对可观察数据进行修改，action的本质是将多次修改合并为一次，来减少副作用的触发。

redux强制规定所有state改变通过action来执行，Mobx并不强制所有state的改变必须通过action来改变。

action也可以通过函数和装饰器两种方式创建。

- action
- action.bound 
- runInAction



## action.bound

`action` 装饰器/函数遵循 javascript 中标准的绑定规则。 但是，`action.bound` 可以用来自动地将动作绑定到目标对象。 注意，与 `action` 不同的是，`@action.bound` 不需要一个name参数，名称将始终基于动作绑定的属性。

action.bound不要和箭头函数一起使用；箭头函数已经是绑定过的并且不能重新绑定。

```jsx
class Ticker {
    @observable tick = 0
    @action.bound
    increment() {
        this.tick++ // this永远都是正确的不会指偏
    }
}

const ticker = new Ticker()
setInterval(ticker.increment, 1000)
```




# mobx-react

mobx-react是React工具库，可以将react的组件的render方法转化为autorun，达到可观察数据更新视图的目的

mobx-react提供一个PropTypes包进行props校验，例如可观察数组不是普通的array而是observableObject

```jsx
import PropTypes form 'props-types'
import { PropTypes as mobxPropTypes} form 'mobx-react' // 避免重名所以取别名mobxPropTypes

class Bar extends Component {
    static propTypes = {
        queue: mobxPropTypes.observableObject
    }
}
```



## Provider

mobx-react中Provider组件（用于包裹根组件）和inject组件（用于注入需要使用store的组件）通过context将store注入并使得任何层级的子组件可以访问到store。

每个store都将以props的形式挂载到Procider组件上，在子组件中通过inject来接收

```jsx
import React from 'react'
import {render} from 'react-dom'
import { Provider } from 'mobx-react';
impore store1 from '../store/store1.js'
impore store2 from '../store/store2.js'

const App = (props) => {
  return (
    <Provider store1 = {store1} store2 = {store2}>
      <div className="App">
        <CounterClass></CounterClass>
        <CounterFunction></CounterFunction>
      </div>
    </Provider>
  );
}
```



## observer/inject

observer函数/装饰器可以用来将 React 组件转变成响应式组件。 它用 `mobx.autorun` 包装了组件的 render 函数以确保任何组件渲染中使用的数据变化时都可以强制刷新组件。 `observer` 是由单独的 `mobx-react` 包提供的。

可观察数据（可以是包含可观察数据的类）需要通过props属性的方式绑定挂载在react组件上，并且可以在propTypes中进行规定类型，然后使用observer装饰器（也可以使用observer函数）装饰react组件类，这样当绑定的可观察数据变化时，就会引起react组件重渲染。

inject装饰器用于将指定的store注入组件，组件通过props.store的方式拿到可观察数据。

类组件中使用装饰器语法

```jsx
import {inject, observer} from 'mobx-react';
@inject('store1', 'store2')
@observer
class Timer extends React.Component {...};
export default Timer
```

上面的 `Timer` 组件还可以通过使用 `observer` 传递的无状态函数组件来编写:

```jsx
import {inject, observer} from 'mobx-react';

const Timer = observer(({ timerData }) =>{...});
export default inject('store1', 'store2')(observer(Timer));          
```





## 使用流程

1. 创建store

```jsx
// store.js
import {observable, action} from 'mobx';

class Store1 {
  @observable
  count = 0;

  @action
  handleCount() {
    this.count += 1;
  }
}

class Store2 {
  @observable
  num = 0;

  @action
  handleNum() {
    this.num += 1;
  }
}

export default {
    new Store1(),
    new Store2()
}
```



2. 根组件通过`Provider`注入store

```jsx
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider } from 'mobx-react';
impore stores from '../store/store.js'

const App = (props) => {
  return (
    <div className="App">
      ...
    </div>
  );
}

ReactDOM.render(
  <Provider {...stores}>
    <App/>
  </Provider>,
  document.body
);
```



**组件通过props.store来访问挂载的store**

3. 类组件（class）访问  `store1`和`store2`

```jsx
import React, { Component } from 'react';
import {inject, observer} from 'mobx-react';
import {toJS} from 'mobx'; // 查看store中原始JavaScript数据结构

@inject('store1', 'store2')
@observer
class CounterClass extends Component {
  handleClick1 = () => {
    this.props.store1.handleCount();
  };
  handleClick2 = () => {
    this.props.store2.handleNum();
  };
  render() {
    console.log(toJS(this.props.store1));
    console.log(toJS(this.props.store2));
    return (
      <div>
        count: {this.props.store1.count}
        <button onClick={this.handleClick1}>add one</button>
        num: {this.props.store2.num}
        <button onClick={this.handleClick2}>add one</button>
      </div>
    )
  }
}

export default CounterClass;
```



4. 函数组件使用 `store1`和`store2`

```jsx
import React from 'react'
import {inject, observer} from 'mobx-react';

const CounterFunction = (props) => {
  return (
    <div>
      <p>count: {props.store1.count} </p>
      <button onClick={() => props.store1.handleCount()}>Add</button>
      <p>num: {props.store2.num} </p>
      <button onClick={() => props.store2.handlenum()}>Add</button>
    </div>
  );
}

export default inject('store1', 'store2')(observer(CounterFunction));
```









# 工具函数

## 检测类型

- isObservable

  检测是否为可观察数据（任意类型）

  

- `isBoxedObservable`

  检测是否为可观察基础类型

```
var num = observable.box(1)
console.log(isBoxedObservable(num)) // true
```



- `isObservableObject|Array|Map`

  检测是否为可观察对象|数组|Map

```js
var arr = observable(['1'])
console.log(isObservableObject(arr)) // false
console.log(isObservableArray(arr)) // true

var obj = observable({
    name: 'a'
})
console.log(isObservableObject(obj)) // false
```



- `isArrayLike`

用法: `isArrayLike(thing)`。如果给定的 thing 是 javascript 数组或者 observable (MobX的)数组的话，返回 true。 这个方法更简便。 注意，observable 数组可以通过 `.slice()` 转变成 javascript 数组。



- `isAction`

`isAction(func)`。如果给定函数是用 `action` 方法包裹的或者是用 `@action` 装饰的话，返回 true。



- `isComputed`

 `isComputed(thing)` 。如果给定的 thing 是计算值返回 true 。





## toJS

将一个observable对象转换为 javascript 结构

```js
var obj = mobx.observable({
    x: 1
});

var clone = mobx.toJS(obj);

console.log(mobx.isObservableObject(obj)); // true
console.log(mobx.isObservableObject(clone)); // false
```



## trace



## spy

