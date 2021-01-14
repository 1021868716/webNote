# Hooks

Hook钩子函数是React 16.8以后的新特性，可以在不编写class继承Component的情况下（在不适用类的情况下）使用state和其他React特性（生命周期等）。

例如Hook让无状态组件也具有了使用props， state，context，refs 以及生命周期等能力，无状态组件使用hooks后就为一个函数组件，也拥有了状态。





## 类组件的缺陷

- 逻辑复杂，难以复用



- 生命周期复杂，难以维护
  - 一些相关联的逻辑需要写到不同的生命周期中，不相关的逻辑却需要写到一个生命周期中，导致逻辑混乱



- this指向的困扰
  - 内联函数过度创建新句柄
  - 类成员函数不能保证this指向





## hooks优势

- 函数组件无this问题



- 自定义hook方便复用状态逻辑



- 副作用的关注点分离

副作用是渲染视图之外的作用（例如本地持久化缓存，绑定解绑事件等），以往的副作用需要写到生命周期中导致生命周期复杂，现在每一种副作用都单独写进一个hook，不必塞进一个生命周期，每个hook都只处理一个副作用，逻辑完全分离开。





# useState

useState是一个基础Hook

```javascript
const [state,setState] = useState(firstState)
// 生成一个userSeate Hook
```

userSeate用于生成一个state属性和改变这个属性的方法setState

```javascript
// 用setState修改state的值为newState,如果newState是一个函数，则会读取newState的返回值
setState(newState);
```

state声明一个新的count的state变量，初始值为firstState，firstState可以为一个函数并返回一个初始值，这个函数只会在组件第一次被渲染时运行一次，之后就不再运行，可以在这里执行一些只运行一次的函数节约性能，避免每次渲染都需要重新执行一次。

`setState()` 函数用于更新 state。它参数接收一个新的 state 值（可以接受一个函数）并将组件的一次重新渲染加入队列（异步的）。在后续的重新渲染中，useState不会将state重置为firstState，`useState` 的值将始终是上次更新的 state。**如果state为引用类型，setState(newState)必须新建一个新的对象，而不是在原本的对象上进行修改。**

每次使用setState更新state，如果state有变化组件会执行函数组件重新渲染，但是如果setState后state没有变化则不会执行函数组件重新渲染。

```react
import React,{ useState } from 'react'
// 导出一个无状态组件
export default function() {
  // 声明一个新的num的state变量，初始值为0
	const [num, setnum] = useState(0)
	return (
	<div>
		<h2>count：{num}</h2>
    <button onClick={()=>{setnum(num=>num+1)}}>change</button>
	</div>
	)
}
```



## 赋值方式

因为`seState`钩子提供的更新程序的状态更新是异步的，不会立即反映和更新，但会触发重新渲染，所以如果setState的新state值跟旧state值有关联，setState的参数必须采用回调函数的形式进行赋值

```javascript
setState(Math.random)   // 新值跟state没关系可以直接赋值
setState(state=>state+1) // 新值跟state有关需要使用回调函数的形式赋值
```

第一种setState方式相当于直接赋值

第二种setState方式相当于告诉react本次操作state的变化，让react根据变化自己计算state为何值，这种方式传递了更少的信息，因为它不再被当前的count值“污染”。它只是表达了一种递增行为





## 异步更新

为什么`seState`状态更新是异步的？

因为react会将多次seState合并为一次，减少dom操作和性能损耗

例如有一对父子组件，同时绑定了点击事件调用seState更新状态，点击子组件会冒泡到父组件。

那么这次组件更新的流程为

	1. 子组件触发click，子组件更新数据重渲染
 	2. 冒泡到父组件，父组件更新数据重渲染，但父组件的重渲染又会导致子组件的再次重渲染

以上流程子组件重渲染了两次，这是不必要的性能浪费，所以`seState`状态更新是异步的，会在组件内所有事件触发完成后再进行批量更新



```jsx
  const [count, setCounter] = useState(0);

  function increment() {
    setCounter(c => c + 1);
  }

  function handleClick() {
    increment();
    increment();
    increment();
  }
```

例如以上的更新，React 会将更新函数放入队列中，并在之后按顺序执行它们，最终 `count` 会被设置成 `3` 并作为一次重渲染的结果。





# useEffect

务必详细阅读：https://overreacted.io/zh-hans/a-complete-guide-to-useeffect/



在class编写组件时，我们可以直接在生命周期函数内之间操作dom，**但在函数组件主体内（指在 React 渲染阶段）改变 DOM、添加订阅、设置定时器、记录日志以及执行其他包含副作用的操作都是不被允许的，**因为这可能会产生莫名其妙的 bug 并破坏 UI 的一致性，所以提供了useEffect这个hook在函数组件中执行这些操作。

```JavaScript
useEffect(func[,arr]);
```

useEffect这个hook类似于容器组件的生命周期函数，并且可以添加一些副作用：如操作dom，数据请求，组件更新，绑定事件等常见的无需清除的操作，副作用常见的调用时机是在Mount之后（首次渲染后useEffect会执行），Update之后（数据更新导致组件重渲染后useEffect会执行），Unmount之前（如果参数函数返回一个回调函数会在数据更新或组件卸载导致组件销毁时运行一次）。可以把 `useEffect` Hook 看做 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合。不同逻辑我们可以写入多个useEffect，他们之间不会互相干扰。

**调用useEffect就是告诉react，在完成对dom的修改后运行副作用函数。**



`useEffect`会在页面挂载后执行一次参数函数，数据更新后执行一次参数函数。如果参数函数返回一个函数，回调函数会在组件销毁之前执行一次（组件销毁的原因有：state数据更新导致重渲染和组件卸载）

```jsx
// 因为监控的是空数组，所以参数函数只会在首次渲染时执行一次，等组件完全销毁时会去执行一次返回的函数
useEffect(()=> {
    ...
    return () => {
        ...
    };
 }, [])
```



但是与 `componentDidMount`、`componentDidUpdate` 这两个组件生命周期不同的是，**在浏览器完成布局与绘制之后，传给 `useEffect` 的函数会延迟调用。**这使得它适用于许多常见的副作用场景，比如设置订阅和事件处理等可以异步的情况，但是不应在useEffect函数中执行阻塞浏览器更新屏幕的操作。必须同步（不能被延迟）的任务不应该在这个hook中执行。

```react
import React , {useState , useEffect } from 'react'

function App (){
  const [count, setCount] = useState(0)
  
  useEffect(()=> {
    // 在一个useEffect中为dom元素绑定事件不能监控空数组，因为需要每次更新都执行绑定事件
    // 因为数据更新导致组件重渲染后，组件元素上的事件就跟着被销毁了，所以不能监控空数组，需要每次重新渲染需要绑定一次事件
    document.querySelector('#btn').addEventListener('click',() => {setCount(count+1)})
    return () => {
 		// 返回的回调函数会在组件销毁之前触发
      document.querySelector('#btn').removeEventListener('click',() => {setCount(count+1)})
    };
  })
  
  useEffect(()=> {
    // 函数组件主体中无法执行操作dom等操作，需要在useEffect这个hook中执行
    // 因为count是state，更新会导致组件卸载重渲染，所以会触发useEffect更新title
    document.title = count
  })
  
  return (
    <div>
      <button id="btn">btn</button>
  		<h1>count: {count}</h1>
    </div>
  )
}

export default App
```



## 依赖数组

有两个场景

1. 如果useEffect中修改了组件的state，则会导致组件重新挂载，组件重新挂载时又会执行useEffect，这样会造成无限循环

```jsx
// 死循环
// mount和unmount循环输出
function Example() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    setCount(count + 1)
    console.log('mount')
    return () => {
      console.log('unmount')
    }
  });
  return (
    <div>
      <p>You clicked {count} times</p>
    </div>
  );
}
```



 2.默认每次state中的数据更新了导致组件更新重新挂载了，但是effect中的参数函数并不用每次组件重渲染都执行

这个时候可以给useEffect添加第二个参数，第二个参数是一个数组，可以向这个数组传入数据，useEffect在每次组件重渲染时会监控数组中每一项数据，如果数据变化了才会执行参数函数，这次重渲染数组中的数据都没有变化则不执行参数函数

如果useEffect的参数函数再返回一个回调函数，回调函数会在组件销毁之前触发，**组件销毁的原因有：state数据更新导致重渲染和组件卸载。**

可以在第二个参数重监控一个空数组，这样参数函数只会在页面挂载时执行一次，参数函数返回的回调函数只会在组件卸载时执行一次，中途数据更新导致页面重渲染不会触发useEffect

```javascript
useEffect(()=>{
  console.log('1')
}) // 每次数据更新页面挂载页面销毁都会执行一次useEffect函数

useEffect(()=>{
  console.log('2')
},[count]) // 页面重渲染时只有count数据更新了才执行useEffect函数


useEffect(()=> {
  return () => {};
 }, [])
// 因为监控的是空数组，所以参数函数只会在首次渲染时执行一次，等组件完全销毁时会去执行一次返回的函数，数据更新不会重新执行useEffect
// 用于某些情况下节约性能
```



但是useEffect参数数组中数据的对比是进行浅比较，如果数组中对象只要指向不发生变化，那么就不会执行effect里面的函数

```jsx
function Example() {
  const [count, setCount] = useState({a: 12});
  useEffect(() => {
    console.log('effect');
      return () => {
        console.log('clean')
      }
  }, [count])

  function handleClick() {
    count.a++;
    setCount(count)
  }
// 点击按钮时发现屏幕显示的值不发生变化，而且effect里面的函数也没有执行，所以进行的是浅比较，这点类似于pureComponent

  return (
    <div>
      <p>You clicked {count.a} times</p>
      <button onClick={handleClick}>
      Click me
      </button>
    </div>
  );
}
```



### 正确使用依赖

如果在useEffect中使用异步引用并更改了组件的状态就不要依赖空数组，因为这会导致组件状态的错误，定时器的更新不会成功

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      console.log(count) // 定时器每隔一秒输出0
      setCount(count + 1); // 定时器永远在执行0+1
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return <h1>{count}</h1>; // 1
}
```

useEffect中设置了`[]`依赖，但是却对组件状态进行了更改，effect之后不会再重新运行，因为hooks的闭包特性它后面每一秒都会调用`setCount(0 + 1)` ，所以页面展示的count永远是1，之后每一秒定时器都在执行0+1



解决方案

1.正确的使用依赖，每次count都在变化，effect重复执行，所以定时器会被重复的创建清除，以此达到正确的效果

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1);
  }, 1000);
  return () => clearInterval(id);
}, [count]);

  return <h1>{count}</h1>; // count从0开始每秒递增
}
```



2.在setCount中使用函数进行更新，以认为它是在给React“发送指令”告知如何更新状态。

尽管effect只运行了一次，第一次渲染中的定时器回调函数可以完美地在每次触发的时候给React发送`c => c + 1`更新指令。它不再需要知道当前的`count`值。因为React已经知道了。

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return <h1>{count}</h1>; // count从0开始每秒递增
}
```





## 闭包

hooks依赖闭包，所以在异步中拿到的数据都是useEffect开始执行时拿到的组件状态（props和state），而不是异步开始时的组件状态

例如在react中class组件中使用定时器中会有问题

```jsx
class Example extends Component {
  state = {
    count: 0
  };
  componentDidMount() {
    setTimeout(() => {
      console.log(`You clicked ${this.state.count} times`);
    }, 3000);
  }
  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({
          count: this.state.count + 1
        })}>
          Click me
        </button>
      </div>
    )
  }
}

/*
连点五次
You clicked 0 times 
You clicked 5 times
You clicked 5 times
You clicked 5 times
You clicked 5 times
You clicked 5 times 
*/
```



解决方案1：class组件中使用闭包

```jsx
class Example extends Component {
  state = {
    count: 0
  };
  componentDidMount() {
    const count = this.state.count;
    setTimeout(() => {
      console.log(`You clicked ${count} times`);
    }, 3000);
  }
  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({
          count: this.state.count + 1
        })}>
          Click me
        </button>
      </div>
    )
  }
}
/*
连点五次
You clicked 0 times 
You clicked 1 times
You clicked 2 times
You clicked 3 times
You clicked 4 times
You clicked 5 times 
*/
```



解决方案2：使用hooks，hooks依赖闭包，所以会使用闭包保存每次执行时的props和state

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    setTimeout(() => {
      console.log(`You clicked ${count} times`);
    }, 3000);
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
/*
连点五次
You clicked 0 times 
You clicked 5 times
You clicked 5 times
You clicked 5 times
You clicked 5 times
You clicked 5 times 
*/
```



## useLayoutEffect

useLayoutEffect与useEffect用法完全一致，基本上90%的情况下，都应该用useEffect，因为 useEffect 不会阻塞浏览器重绘，而且平时业务中我们遇到的绝大多数场景都是时机不敏感的，比如取数、修改 dom、事件触发/监听…… 所推用 useEffect 来处理 side effects，性能上的表现会更好一些。

而useLayoutEffect会阻止浏览器重绘，会等参数函数完全执行完毕后才会让浏览器继续绘制页面，目的是当useEffect里面的操作需要处理DOM，并且会改变页面的样式时，如果参数函数的计算时间过长可能会出现出现闪屏问题。





# useContext

这个hook必须和createContext配合使用，用于两个父子组件间的通信，createContext()出一个父组件，子组件能通过useContext这个hook获取父组件传递过来的消息

```
const value = useContext(Context对象);
```

useContext接收一个 context 对象（`React.createContext` 创建的 context 对象，**useContext的参数必须是 context 对象**）并获取该 context 的当前值。当前的 context 值由上层组件中距离当前组件最近对应的Provider的 `value` prop 决定。

当组件上层最近的 `<Provider>` 更新时，该 Hook 会触发重渲染，并使用最新传递给 `Context` provider 的 context `value` 值。即使祖先使用 `React.memo`或 `shouldComponentUpdate`，也会在组件本身使用 `useContext` 时重新渲染。

```react
import React,{ useState, useContext, createContext} from 'react'

//创建Context对象
const FatherContext = createContext(0);

function App() {
  const [count,setCount] = useState(1)
  return (
  <div>
    <FatherContext.Provider value={count}>
      <ChildContext></ChildContext>
    </FatherContext.Provider>
    <button onClick={setCount(count+1)}>count + 1</button>
  </div>
  )
}

// 创建一个容器组件，并使用useContext定义变量接受Provider传递的数据
function ChildContext (){
  let count = useContext(FatherContext)
  return(
    <h3>子组件拿到的值为: {count}</h3>
  )
}

export default App
```





# useMemo

memo用来优化无状态组件渲染性能，当组件的props没变时就不去重新渲染无状态组件

**useMemo是定义一个函数（不是组件，而是一个有返回值的函数）是否重新执行，使用useMemo方法 避免无用方法的调用。**

useMemo在函数初始化挂载时执行一次，之后数据更新导致的组件卸载重渲染不会重新执行useMemo，但第二个参数数组内数据变化就会立刻去执行参数函数然后重渲染组件

useMemo缓存了（返回）参数函数的返回值，useMemo会根据第二个参数数组arr中的值是否变化而决定是否执行函数参数

```
useMemo(func,arr)
```



不使用useMemo，当我们点击父组件的按钮的时候，子组件的name和children都会发生变化。
不管我们是改变name或者content的值，父组件重新渲染都会导致子组件跟着重新渲染，changeName的方法都会被调用。我们本来只想修改content的值，但是由于name并没有发生变化，所以无需执行对应的changeName方法。但是发现他却执行了。 这是不是就意味着性能的损耗，做了无用功。

```jsx
import React , { useState , useMemo } from 'react'

function App() {
  const [name, setName] = useState('名称')
  const [content,setContent] = useState('内容')
  return (
      <>
        <button onClick={() => setName(new Date().getTime())}>name</button>
        <button onClick={() => setContent(new Date().getTime())}>content</button>
        <child name={name} content={content}></child>
      </>
  )
}

function child(props) {
  function changeName(name) {
    console.log('name change')
    return 'name为' + name
  }
  let getName =  changeName(props.name)
  let getcontent =  props.content
  return (
      <>
        <div>getName：{getName}</div>
        <div>getcontent：{getcontent}</div>
      </>
  )
}

export default App
```



使用useMemo进行性能优化

改变content值的按钮，发现changeName 并没有被调用。但是点击改变name值按钮的时候，changeName被调用了。

```jsx
function child(props) {
  function changeName(name) {
    console.log('name change')
    return 'name为' + name
  }
	// useMemo缓存参数函数返回的值
  let getName =  useMemo(()=>changeName(props.name),[props.name])
  let getcontent =  props.content
  return (
      <>
        <div>getName：{getName}</div>
        <div>getcontent：{getcontent}</div>
      </>
  )
}
```





# useCallback

useCallback作用和useMemo类似，是useMemo的函数参数再返回一个函数的语法糖

```
useMemo( () => fn ) 等价于 useCallback(fn)
```

```jsx
let getName =  useMemo(()=>{ return changeName(props.name) },[props.name])
等价于
let getName =  useCallback(changeName(props.name), [props.name])
```

但是跟useMemo有一点不同时，参数数组没变，useCallback依然会去执行函数，但是返回值会被抛弃不缓存，也就是执行函数，但是返回值被抛弃了，useCallback依旧缓存上次数组变化后执行的缓存结果。



## 配合useEffect

useCallback也常用于解决useEffect中`React Hook useEffect has missing dependencies`警告，也就是useEffect中要求调用的函数必须在内部或者依赖中定义。

```react
const funcA = async(value: string) => {
  const jobs = await searchChildJob(value)
  setChildJobs(jobs)
}

const funcB = async(value: string) => {
  const jobs = await searchChildJob(value)
  setDependencyExecuteJobs(jobs)
}

useEffect(() => {
  funcA('')
  funcB('')
}, [props]);

// 警告： React Hook useEffect has missing dependencies: 'funcA' and 'funcB'. Either include them or remove the dependency array
```

这是因为useEffect中要求调用的函数必须在内部/依赖中定义，但是函数可能其他地方也要使用不能再effect中定义。直接写进依赖中也有问题，因为每次组件渲染都会重新定义这两个函数导致effect的重新执行。

这时就可以使用useCallback解决，然后就可以在effect的依赖中添加这两个函数解决警告。

```react
const funcA = useCallback(async(value: string) => {
  const jobs = await searchChildJob(value)
  setChildJobs(jobs)
}, [searchChildJob, setChildJobs])

const funcB = useCallback(async(value: string) => {
  const jobs = await searchChildJob(value)
  setDependencyExecuteJobs(jobs)
}, [searchChildJob, setDependencyExecuteJobs])

useEffect(() => {
  funcA('')
  funcB('')
}, [props, funcA, funcB]);
```





# useRef

ref是用来获取组件或者dom元素的，useRef可以在函数组件中使用ref的特性

useRef创建的 ref 对象在组件的整个生命周期内保持不变，被绑定了ref的dom无论如何变化，refObj.current始终指向这个dom元素或者变量

 useRef有两种作用：

- 获取子代类组件（函数组件无法被useRef获取）或者dom元素的句柄



- 函数组件跨渲染周期之间数据的存储。

  函数组件每次useState数据更新都会导致组件销毁重渲染一次，函数组件主体内的变量都会被重新声明一次，可能会造成句柄丢失问题，（类似类组件每次state/props更新都会重新执行render函数重渲染更新ui，但属性成员更新不会去更新ui）。

  而用useRef保存数据相当于类组件中constructor之外赋值的普通属性成员，普通属性成员的变化不会去更新页面，也不会让组件重渲染。所以函数组件中useState的赋值触发重渲染后普通定义的变量将会重置，但useRef保存的数据不会发生改变还是原来保存的数据（跨渲染周期保存数据），而且对useRef保存的数据的修改也不会引起组件的重渲染。

  ```jsx
  import React  from 'react'
  class App extends React.Component {
    a = 5
    render() {
      console.log('render')
      return (
        <div>
          {/*类组件属性成员的变化不会去更新ui，所以不会引起组件重渲染*/}
          <button onClick={()=>{ this.a  = this.a + 1; }}>btn</button>
          <h1>{this.a}</h1> {/*始终显示5，属性成员变化ui不会更新*/}
        </div>
      )
    }
  }
  export default App 
  ```

  

## 绑定dom元素

```
const refObj = useRef();
refObj.current // 输出dom元素
```

`useRef` 返回一个可变的 ref 对象，通过标签上的ref属性手动绑定。将ref对象绑定在dom元素或子组件上（在标签上添加属性`ref = {refobj}`）。返回的 ref 对象在组件的整个生命周期内保持不变，被绑定了ref的dom无论如何变化，refObj.current指针始终指向这个dom元素



```react
import React , { useState , useRef } from 'react'

function App() {
  const [name, setName] = useState(1)
  
  const childRef = useRef() // 创建ref对象
  
  const btnClick = () => {
    setName(Math.random())
    console.log(childRef.current) // 输出dom元素 
  }
  return (
      <>
        <button onClick={ btnClick }>name</button>
        <Child ref={childRef} name={name}></Child> // 将ref对象绑定在dom元素或子组件上
      </>
  )
}

class Child extends React.Component {
  render() {
    return (
      <>
        <div>
          <h1>name: {this.props.name}</h1>
        </div>
      </>
    )
  }
}

export default App 
```



## 共享数据

下面代码中直接let变量，然后将变量指向定时器，发现点击btn，页面上time到10后不会停止，`console.log(time)`一直输出0，控制台的clear timer在页面上time到10之后还会一直输出，说明副作用有效但是定时器没有被清除。

这是因为每次数据更新App会重渲染，timer变量就会被重新let一次，重新let后句柄timer就不再指向定时器了，`clearInterval(timer)`中的timer每次都是最新let的句柄，而句柄timer已经不指向定时器了，在内存中没有句柄指向定时器，所以无法清除。

```jsx
import React , { useRef , useState ,useEffect } from 'react'

function App() {
  console.log('app run')
  const [time, setTime] = useState(0)
  let timer
  useEffect(()=>{
    // 副作用启动定时器
    timer = setInterval(()=>{
      console.log(time)
      setTime(time => time+1)
    },1000)
  },[])

  useEffect(()=>{
    // 副作用清除定时器
    if(time >= 10) {
      console.log('clear timer')
      clearInterval(timer)
    } 
  })// 没有监控数组，所以每次time更新页面重渲染就会执行一次判断

  return (
      <>
        <h1>time: {time}</h1>
      </>
  )
}
```



使用useRef可以解决这个问题，`console.log(time)`依然会打印0，但是页面上time到10后会成功停止

因为将定时器句柄设置为refObj的current属性下，利用current属性作为指针始终指向不变的特点，所以就能成功抓取并清除掉这个定时器

```jsx
import React , { useRef , useState ,useEffect } from 'react'

function App() {
  console.log('app run')
  const [time, setTime] = useState(0)
  let timer = useRef()

  useEffect(()=>{
    // 副作用启动定时器
    timer.current = setInterval(()=>{
      console.log(time)
      setTime(time => time+1)
    },1000)
  },[])

  useEffect(()=>{
    // 副作用清除定时器
    if(time >= 10) {
      console.log('clear timer')
      clearInterval(timer.current)
    } 
  })// 没有监控数组，所以每次time更新页面重渲染就会执行一次判断

  return (
    <>
      <h1>apptime: {time}</h1>
    </>
  )
}
```

并且useRef可以传入一个初始值（不支持传入回调函数）来保存数据

```
const r = useRef('abc')
```

更改r不会触发组件重渲染，重渲染函数组件也不会让r的值重置，可以用来在不同渲染周期保存数据





# useReducer



# 自定义hook

自定义hook是将相关联的逻辑集成到一个hook中

例如上面的time定时器，将逻辑写进函数组件中不方便复用，且如果还有其他逻辑容易混在一起，就可以将定时器逻辑单独抽离出来，自定义一个useTime hook，复用程度和解耦都提高了。

自定义hook函数需要接收默认值传给普通hook，并返回普通hook的返回数组让外部访问。

```jsx
import React , {useRef, useState ,useEffect } from 'react'

function useTime(defaultTime) {
  const [time , setTime] = useState(0)
  const it = useRef()
  
  useEffect(() => {
    it.current = setInterval(() => {
      setTime(time => time + 1)
    },1000)
  }, [])
  
  useEffect(() => {
    if(time >= 10) {
      clearInterval(it.current)
    }
  })

  return [time, setTime];
}


function App() {
  const [time, setTime] = useTime(0)
  return (
    <>
      <h1>apptime: {time}</h1>
    </>
  )
}


export default App 
```





# hooks使用约定

- 所有hooks包括自定义的都应该以use开头



- 只能在函数最外层调用 hook，不要在循环、条件判断或者嵌套函数中调用hook。

  hook函数可能依赖调用顺序， 这样react才能在不同的渲染周期中将hook按调用顺序串联起来，如果不在顶层调用，很有可能在渲染周期中让hook调用顺序发生混乱



- 只能在react的函数组件和自定义hook函数中调用hook，类组件和普通函数中无法使用hook






# hook使用技巧

- 生命周期通过useEffect映射到hook中



- 类成员变量通过useRef映射到hook中

  成员变量可以在不同周期中记录数据，且更改成员变量不触发更改ui导致重渲染

  

- 获取历史props和state

​      通过useRef创建保存，这样组件更新也不会影响保存的值



- 如何强制更新一个函数组件

  创建一个不参与渲染的useState，手动更新他的值实现强制更新函数组件

  虽然创建的state不参与渲染，但是更新它依然会触发组件的重渲染

  ```jsx
  function Counter() {
  	const [updater, setupdater] = useState(0)
  	function update() {
  	  setupdater(updater => updater + 1)
  	}
  }
  ```

  