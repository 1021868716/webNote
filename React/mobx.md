# mobx

`redux`：函数式编程风格

`mobx`：

- 响应式面向对象编程风格
- 集成度高无需引入大量第三方库
- 细粒度高渲染性能好（避免不必要的组件重渲染）

mobx的核心理念是：状态变化引起的副作用应该被自动触发

安装mobx

```
npm i mobx -s
```

安装React 绑定库: 

```
npm install mobx-react --save
```







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

直接对可观察数据进行修改会带来一定的副作用，例如频繁触发autorun和reaction，对性能不好，因此可以使用action对可观察数据进行修改，action的本质是将多次修改合并为一次，来减少副作用的触发。

action也可以通过函数和装饰器两种方式创建。

- action
- action.bound 
- runInAction






# mobx-react

mobx-react是React工具库，可以将react的组件的render方法转化为autorun，达到可观察数据更新视图的目的

mobx-react提供一个PropTypes包进行props校验，例如可观察数组不是普通的array而是observableObject

```js
import PropTypes form 'props-types'
import { PropTypes as mobxPropTypes} form 'mobx-react' // 避免重名所以取别名mobxPropTypes

class Bar extends Component {
    static propTypes = {
        queue: mobxPropTypes.observableObject
    }
}
```



## observer

`observer` 函数/装饰器可以用来将 React 组件转变成响应式组件。 它用 `mobx.autorun` 包装了组件的 render 函数以确保任何组件渲染中使用的数据变化时都可以强制刷新组件。 `observer` 是由单独的 `mobx-react` 包提供的。

可观察数据（可以是包含可观察数据的类）需要通过props属性的方式绑定挂载在react组件上，并且可以在propTypes中进行规定类型，然后使用observer装饰器（也可以使用observer函数）装饰react组件类，这样当绑定的可观察数据变化时，就会引起react组件重渲染。

```js
import {observer} from "mobx-react";

var timerData = observable({
    nums: 0
});

setInterval(() => {
    timerData.nums++;
}, 1000);

@observer class Timer extends React.Component {
    render() {
        return (<span>nums: { this.props.timerData.nums } </span> )
    }
};

ReactDOM.render(<Timer timerData={timerData} />, document.body);
```



上面的 `Timer` 组件还可以通过使用 `observer` 传递的无状态函数组件来编写:

```javascript
import {observer} from "mobx-react";

const Timer = observer(({ timerData }) =>
    <span>Seconds passed: { timerData.secondsPassed } </span>
);
Copy
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

