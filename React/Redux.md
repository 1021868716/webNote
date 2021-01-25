# Redux

![redux](http://www.ruanyifeng.com/blogimg/asset/2016/bg2016091802.jpg)

```
npm install redux
```

Redux 是 JavaScript 状态容器，提供可预测化的状态管理。

React是单项数据流且是函数式编程思想，React中Redux导出的store是唯一的，且只有store自己能改变自己的数据，reducer不能改变store，只能接收action并返回一个数据对象state，store拿到这个返回的state对象自己更新自己的数据

安装React绑定库和开发者工具

```
npm install --save react-redux
npm install --save-dev redux-devtools
```



src目录下建一个store文件夹，建立两个js文件reducer.js和index.js



# reducer.js

Redux保存的数据就存放在这个文件里，reducer.js负责保存Redux里的数据，而reducer.js导出的就是一个函数。

这个js文件导出一个函数，函数有两个参数state和action，这个函数用于接收原本的state数据和传来的action用于改变state中的数据，初始化时没有action，则返回初始的state（创建的数据对象defaultState）

其中state是代表了store中保存的数据，action是组件中传来行为，每次改变需要深拷贝生成一个新的state集合保存对象并返回给store，为什么不直接修改原来的state？因为要符合不可变值思想。

**reducer只能接收state，但不能修改state，所以需要深拷贝一份newState，对其进行修改，返回newState成为新的store数据。**

这个导出的函数reducer的返回的对象newState就是本次处理后的最新的数据集合state。

```javascript
// 初始数据格式defaultState
const defaultState = {
  inputValue: '',
  list: []
}

// reducer能接收原来的state和action，但不能修改state
// 需要深拷贝一份state进行修改，然后返回给store成为新的store数据
export default (state = defaultState, action) => {
  // state是redux存放的数据
  if(action.type === 'changeInputValue') {
    const newState = JSON.parse(JSON.stringify(state)) // 深拷贝state
    newState.inputValue = action.value
    return newState
  }
  // 初始化时没有匹配action，则返回原来的state（也就是defaultState）
  return state
}
```





# index.js

这个文件负责创建Redux暴露给外界组件调用数据的仓库对象store

首先从redux中导入创建暴露接口的createStore函数，然后从reducer.js中导入reducer函数

使用`const store = createStore(reducer);`创建出仓库接口对象store并导出

window.xxx是启用Chrome的插件`Redux DeVTools`监控redux

```javascript
import { createStore } from 'redux';
import  reducer  from './reducer'

const store = createStore(
    reducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
  ); 
//创建store读取保管的数据,后面的参数是启动Chrome的Redux DeVTools插件

export {store}
```





# 组件访问store

## getState()

- 获取数据  store.getState()

引入仓库对象store

`import { store } from './store/index.js'`

通过仓库对象的api获取到Redux中的数据`store.getState()`



## dispatch/subscribe

- 修改store数据： store.dispatch(action)

如果需要修改store内的数据，则通过行为对象action将修改数据的行为类型和新数据值diapatch给store

然后store将新的state值和行为类型对象转发给Reducers中导出的函数进行行为解释，导出函数会深拷贝一份state修改value并返回，成为store的新的数据



- 更新组件state：  store.subscribe(func)

但是store数据修改了，页面并不会直接更新，每次store数据修改就会自动触发`store.subscribe(func)`中的回调，通知组件数据已经修改了，我们在回调中使用setState进行组件的state数据修改

组件修改数据流程
1） 创建action对象

```js
const action  = {
  type: 'changeInputValue',
  value:  e.target.value
}
```

2） dispatch给store进行处理

```
store.dispatch(action)
```

3）store调用reducer.js导出的函数以state和action为参数进行数据修改

```react
import React from 'react'
import 'antd/dist/antd.css'
import { Input, Button } from 'antd';
import { store } from './store/index.js'

class TodoList extends React.Component {
  constructor(props) {
    super(props)
    // store.getState()访问store
    this.state = store.getState()
    this.handleInputChange = this.handleInputChange.bind(this)
    this.handleStoreChange = this.handleStoreChange.bind(this)
    this.handleBtnClick = this.handleBtnClick.bind(this)
    // 只要store数据改变，就调用handleStoreChange方法用于更新页面UI
    store.subscribe(this.handleStoreChange)
  };

  render() {
    return (
      <div>
        <div style={{marginTop:'10px', marginLeft:'10px'}}>
          <Input
             value ={this.state.inputValue}
             placeholder="todo info"
             style={{width:'300px',marginRight:'10px'}} 
             onChange={this.handleInputChange}/>
          <Button 
            type="Primary"
            onClick={this.handleBtnClick}
            >put</Button>
          {
            this.state.list.map((item,index) =>{
              return (
              <li key={item} onClick={this.handleItemDelete.bind(this,index)}>{item}</li>
              )
            })
          }
        </div>
      </div>
    )
  };

  // 根据不同的时间dispatch不同的action
  handleInputChange(e) {
    const action  = {
      type: 'changeInputValue',
      value:  e.target.value
    }
    store.dispatch(action)
  };

  handleItemDelete(index) {
    const action = {
      type: 'deleteTodoItem',
      index
    }
    store.dispatch(action)
  };
   
  handleBtnClick() {
    const action  = {
      type: 'addTodoItem'
    }
    store.dispatch(action)
  };

  handleStoreChange() {
    // store改变后更新界面
    this.setState(store.getState())
  };


}

export default TodoList
```





# actionTypes.js

在创建action时会输入大段字符串，容易写错，建议在一个单独的文件actionsType.js里提取成常量导出使用

```javascript
// actionTypes
export const ADD_TODO_ITEM = 'addTodoItem'
export const DELETE_TODO_ITEM = 'deleteTodoItem'
export const CHANGE_INPUT_VALUE = 'changeInputValue'
```

```javascript
//todoList.js
import {ADD_TODO_ITEM, DELETE_TODO_ITEM, CHANGE_INPUT_VALUE} from './store/actionsTypes'
...
const action = {
  type: DELETE_TODO_ITEM,
  index
}
```

```javascript
// store/reducer.js
import {ADD_TODO_ITEM, DELETE_TODO_ITEM, CHANGE_INPUT_VALUE} from './actionsTypes'
...
if(action.type === ADD_TODO_ITEM) 
```





# actionCreator.js

在组件中创建action会不好管理且不能复用，可以创建一个actionCreator.js文件专门管理action

```javascript
// store/actionCreator.js
import {ADD_TODO_ITEM, DELETE_TODO_ITEM, CHANGE_INPUT_VALUE} from './actionsTypes'
export const getInputChangeAction = (value)=>{
return {
     type:  CHANGE_INPUT_VALUE,
     value
   }
}

export const getAddItemAction = ()=>{
return {
     type:  ADD_TODO_ITEM,
   }
}
```

```javascript
// todoList.js
import { getInputChangeAction, getAddItemAction} from './store/actionCreator.js'
...
hanleInputChange(e) {
  const action = getInputChangeAction(e.target.value)
  store.dispatch(action)
}

handleBtnClick() {
  const action  = getAddItemAction
  store.dispatch(action)
};


```







# Redux中间件

*中间件*是介于应用系统和系统软件之间的一类软件

**在redux中中间件是指action和store之间的处理模块，redux中间件可以拦截action，然后内部进行处理，最后返回一个新的action给store修改数据**

以前将异步操作（ajax）放进生命周期函数中执行，拿到结果后dispath给store，但是当异步操作的代码量变多以后，生命周期函数会变得越来越复杂，组件会变得越来越冗余，使用中间件可以让异步修改store在中间件中执行，组件中不处理异步修改store的请求

中间件原理是，先把原本的action拦截下来，然后中间件对其进行改造，然后生成新的action在dispatch。



## Redux-thunk

redux-thunk（丧可），**是redux的一个中间件**，用于让action变成函数，dispatch也能识别并上传给store。

**chunk可以拦截函数类型的action，然后执行函数将中间件生成的action对象 dispath给store。**

普通的store.dispatch只能接受一个对象action当做参数（初始的函数action需要手动传入dispath当做参数），chunk中间件可以将action写成一种函数然后dispath，chunk拦截这个函数action并执行，最后返回一个自己生成的action后dispath给store修改数据

```
npm install --save redux-thunk
```

使用thunk，index.js需要引入chunk和一些兼容devTools的相关模块

使用中间件需要在index.js中从redux中引入applyMiddleware方法用于使用中间件，从redux-thunk中引入中间件模块thunk，REDUX_DEVTOOLS也算redux的一个中间件，想同时使用这两个中间件需要一些引入函数



thunk的index.js配置（从github官网上抄即可）

```javascript
import { createStore, applyMiddleware, compose} from 'redux';
import thunk from 'redux-thunk'
import  reducer  from './reducer'

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;

const enhancer = composeEnhancers(
  applyMiddleware(thunk)
);

const store = createStore(
    reducer,
    enhancer
); 

export {store}
```



组件中使用chunk中间件，可以将action定义为一个异步的函数AsyncAction

```javascript
// 使用chunk，将action定义为异步的函数
componentDidMount() {
let AsyncAction = (dispatch)=>{
  axios.get('/todolist.json').then((res)=>{
     const data = res.data;
     const action = {
    		type: 'initListAction',
    		value: data
 		 };
     // 这里的dispath是拿到结果后dispath store改变数据
     dispatch(action)
   })
  }
  // AsyncAction异步函数，让chunk执行这个异步函数
  store.dispatch(AsyncAction)
}
```







## Redux-saga

Redux-saga也是一个用于store异步处理的中间件

```
npm install --save redux-saga
```

使用saga后，saga可以对特定的action进行拦截，然后新建一个saga.js自己处理这个action。拿到结果后再put给store处理。

组件需要store异步修改时，可以将异步请求分离到saga.js中进行处理，例如组件发送一个正常的action，saga.js拦截这个action，然后内部调用函数进行异步请求，然后和thunk类似，saga会生成一个拿到结果的action，最后会put这个结果action给store进行数据修改



使用saga的index.js配置（github上抄）

```javascript
import { createStore, applyMiddleware, compose} from 'redux';
import  reducer  from './reducer'

// import thunk from 'redux-thunk' // 引入thunk

import createSagaMiddleware from 'redux-saga' //引入saga
import mySagas from './sagas'

const sagaMiddleware = createSagaMiddleware() // 创建saga中间件
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;

const enhancer = composeEnhancers(
  // applyMiddleware(thunk) //安装thunk
  applyMiddleware(sagaMiddleware) // 安装saga
);

const store = createStore(reducer,enhancer); 
sagaMiddleware.run(mySagas) // 运行mySagas函数，拦截action

export { store }
```



新建saga.js拦截特定的action

```javascript
import { takeEvery, put } from 'redux-saga/effects'
// takeEvery，put是saga提供的方法，分别用于拦截组件发来的action和向store发送action
import axios from 'axios'

async function getInitList() {
  const res = await axios.get('/todolist.json')
  const action = {
    type: 'initListAction',
    value: res
  };
  await put(action)
}

async function mySaga(){
  // 接收到type为getInitList的action就执行getInitList方法进行异步请求
  await takeEvery('getInitList', getInitList)
}

export default mySaga;
```



在组件中，无需特意使用异步（异步已经分离给saga.js了），只需要发送正常的action即可

```javascript
const action  = {
  type: 'getInitList'
}
store.dispatch(action)
```



### 辅助函数

#### **takeEvery**

例如：每次点击 “1秒后加1” 按钮时，我们发起一个 `incrementAsync` 的 action。

首先我们创建一个将执行异步 action 的任务：

```javascript
import { delay, put } from 'redux-saga/effects'

function* incrementAsync() {
    // 延迟1s
    yield delay(1000)
	// 发起action
    yield put({
        type: 'increment'
    }) 
}
```

拦截type为increment的action并执行回调

```javascript
import { takeEvery } from 'redux-saga'

function* watchIncrementAsync() {
    yield takeEvery('incrementAsync', incrementAsync)
}
```



#### **takeLatest**

在上面的例子中，`takeEvery` 是每次发起“incrementAsync” action的时候都会执行。如果我们只想得到最新那个请求的响应，我们可以使用 `takeLatest` 辅助函数

```javascript
import { takeLatest } from 'redux-saga'

function* watchIncrementAsync() {
    yield takeLatest('incrementAsync', incrementAsync)
}
```



### Effect Creators

`redux-saga`框架提供了很多创建effect的函数，下面是开发中最常用的几种：

- take(pattern)
- put(action)
- fork(fn, ...args)

#### **take(pattern)**

`take`函数可以理解为监听未来的action，它创建了一个命令对象，告诉`middleware`等待一个特定的`action`，直到一个与pattern匹配的`action`被发起，才会继续执行下面的语句。

#### **put(action)**

`put`函数是用来发送action的 effect，可以简单的把它理解成为Redux框架中的`dispatch`函数，当`put`一个action后，reducer中就会计算新的state并返回。

#### **fork(fn, ...args)**

`fork` 函数是用来调用其他函数的，但是fork函数是非阻塞函数，也就是说，程序执行完 `yield fork(fn， args)` 这一行代码后，会立即接着执行下一行代码语句。





# React-Redux

React-Redux是一个第三方库，可以在react中更好地适应redux

```
npm install --save react-redux
```



## Provider

React-Redux自带了一个核心组件Provider，Provider连接一个store后，他的所有子组件都有能力通过引入connect方法与store建立映射关系，将需要使用store数据的组件放入Provider中使用。

首先src/index.js中引入react-redux，使用核心组件Provider连接store，然后需要使用store的组件作为Provider的子组件

```react
import React from 'react';
import ReactDOM from 'react-dom';
import TodoList from './TodoList.js';
import { Provider } from 'react-redux'
import store from './store'

const APP = (
  <Provider store={ store }>
    <TodoList/>
  </Provider>
)

ReactDOM.render(APP, document.getElementById('root'));
```



## connect

connect本质是一个高阶组件（HOC），在组件中引入react-redux中的connect方法建立store与组件的映射关系，connect有两个参数：mapStateToProps和mapDispatchToProps，都需要传入函数，并且两个函数都需要返回对象，第一个函数的对象时建立store中数据与组件props数据的关系，第二个函数返回的对象是提供修改store中数据的方法。

第一个参数函数参数是state（sotre中的），用于建立组件与store之间的映射关系，store的数据会映射到组件的props上，组件内可以通过props进行调用可以访问关联过的store数据，连接后组件使用store中数据的方式变为使用 this.props.属性。

第二个参数函数参数是dispatch，用于建立组件与store的dispath方法之间的映射，修改方法也映射到了props上，调用方法dispatch action来修改store，组件可以使用第二个参数函数返回的对象中的方法修改store中的数据



导出组件之前使用connect建立映射关系，然后把高阶组件再导出出去

```react
import React from 'react'
import { connect } from 'react-redux'

class TodoList extends React.Component {
  render() {
    return (
      <div>
        <div>
          <input value = {this.props.inputValue} onChange={this.props.changeInputValue}/>
          <button>put</button>
        </div>
      </div>
    )
  }
}

const mapStateToProps = (state) => {
  return {
    inputValue: state.inputValue
  }
}

const mapDispatchToProps = (dispath) => {
  return {
    changeInputValue(e) {
      const action = {
        type: 'changeInputValue',
        inputValue: e.target.value
      }
      dispath(action)
    }
  }
}
// 导出组件之前使用connect建立映射关系
export default connect(mapStateToProps, mapDispatchToProps)(TodoList)
```





## combineReducers拆分reducer

当reducer含有太多代码时可以将在每个组件内生成一个reducer.js，自己管理自己的action，store内的reducer再通过引入redux的combineReducers方法整合这些小的reducer.js

```javascript
import { combineReducers } from 'redux'
import headerReducer from '@/common/header/store/reducer'

export default combineReducers({
  header: headerReducer
})
```

react-redux插件中建立映射变为（多了一层关系）

```javascript
const mapStateToProps= (state) => {
  return {
    focused: state.header.focused
  }
}
```

**即使拆分了reducer最后也会合并，所以一个组件发送action，其他组件的reducer也都能接收到action。**





## 性能优化

使用connect连接了组件之后，一旦store数据更新，所有连接了的组件的component都会重新渲染，拉低了性能，可以在shouldComponentupdate这个生命周期函数中做代码优化，判断只有和组件相关的数据发生改变时才去执行render函数重新渲染，否则就return false来拒绝虚拟dom的比对，不重新渲染。

每个组件都去写生命周期函数优化会比较麻烦，react提供了一个新组件PureComponent，替代原来的从react中引入的Component，PureComponent是纯组件，这个组件内部实现了一个shouldComponentupdate浅比对，无需我们自己手动比对。