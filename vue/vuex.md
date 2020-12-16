# Vuex

官方解释：Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。

它采用 集中式存储管理 应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

Vuex 也集成到 Vue 的官方调试工具 devtools extension，提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能。

其实可以简单看成可以把多个组件共享的变量保存进一个对象里，然后把这个对象放在顶层的Vue实例中，让其他组件可以使用。

我们自己封装一个对象放进Vue原型也能做到所有组件访问一个变量，但无法做到响应式（一处改，处处改）。Vuex就是为了提供这样一个在多个组件间共享状态的插件。

比如用户的登录状态（用json web token，用cookie不安全）、用户名称、头像、地理位置信息等等。比如商品的收藏、购物车中的物品等等。

这些状态信息，我们都可以放在统一的地方，对它进行保存和管理，而且它们还是响应式



## 组件中使用vuex

共享的数据放入Vuex.Store实例对象store的state属性下，所有组件访问属性通过 $store.state.属性名 来访问，$store是我们new的Vuex.Store实例对象store

修改不建议在组件中用 $store.state.属性名直接修改，因为这样浏览器插件Devtools无法监控是哪个组件最后修改了属性值，建议同步操作在Vuex.Store实例中mutations对属性值进行修改，异步操作在actions里对属性值进行修改（发送网络请求等），组件中需要自定义方法然后调用

例如mutations中添加increment和decrement两个处理数据方法
组件中想要调用就需要在组件中重新注册两个自己的方法引入

```javascript
this.$store.commit('mutations中的方法名')


methods:{
  addition(){
    this.$store.commit('increment')
  },
  subtraction(){
    this.$store.commit('decrement')
  }
}
```



## 初始化vuex

```javascript
vuex使用步骤
下载vuex插件
npm install vuex --save

再根目录下创建一个store（仓库）文件夹
在store内创建index.js引入并安装vuex插件
import Vue from 'vue';
import Vuex from 'vuex';
Vue.use(Vuex)

创建Vuex实例对象
const store = new Vuex.Store({
    state:{
        counter: 1000
        //其他组件可以用$store.state.counter调用查询
        //修改state建议用mutations/actions修改
        //这样可以用浏览器插件Devtools查询数据的修改人
        //方便调试和debug
    },
    mutations:{
        //添加各种数据处理方法
        //这里会自动获取state，不用手动传输
        //在组件中调用需要this.$store.commit()返回
        //处理同步修改数据
        increment(state){
            state.counter++
        },
        decrement(state){
            state.counter--
        },
        incrementCount(state,count){
            state.counter+=count
        }
    },
    actions:{
	//异步修改，在actions里对属性值进行操作（发送网络请求等）
    },
    getters:{
     
    },
    modules:{
        
    }
})
export default store
```



**引入store**

引入后使用`$store.state.数据`查询

使用`this.$store.commit('decrement',参数)`调用mutations中的方法

```javascript


//在main.js中导入并在vue实例中挂载
import Vue from 'vue'
import App from './App'
//引入vuex
import store from './store/index.js'
//把vuex定义成全局组件
Vue.prototype.$store = store
Vue.config.productionTip = false

App.mpType = 'app'

const app = new Vue({
    ...App,
//挂载
    store
})
app.$mount()


//在App.vue中访问
<template>
  <div id="app">
      <h2>{{message}}</h2>
      <h2>{{$store.state.counter}}</h2>
      <button @click="addition">+</button>
      <button @click="subtraction">-</button>
      <button @click="addCount(5)">+5</button>
  </div>
</template>
<script>
export default {
  	name: 'App',
  	components: {
    	HelloVuex
  	},
    data(){
      return {
          message:'我是app组件',
      }
	},   
    methods:{
        addition(){
            this.$store.commit('increment')
        },
        subtraction(){
            this.$store.commit('decrement')
        },
        addCount(count){
            this.$store.commit('incrementCount',count)
        }
    }
}
</script>

```



# devtools

浏览器商店中下载vue官方编写的vuex监控插件，可以监控vuex中每一步数据的变化



# Vuex核心概念

```javascript
Vuex有几个比较核心的概念:
State
Getters
Mutation
Action
Module
```



# State 单一状态树

英文名称是Single Source of Truth，也可以翻译成单一数据源，vue建议Vuex.Store实例对象只有一个，所有需要管理的信息全部放进一个实例store中

getters里的函数也需要传入state



# Getters

类似于计算属性，用于数据的变化或者筛选数据

组件调用getters方式`$state.getters.powerCounter`

```javascript
//<h2>{{$state.getters.powerCounter}}</h2>
//使用getters
const store = new Vuex.Store({
    state:{
        counter: 1000
    },
    mutations:{
    
    },
    actions:{
        
    },
    getters:{
        powerCounter(state){
            return state.counter*state.counter
        }
        //使用时和计算属性一样不用加()
        //{{$state.getters.powerCounter}}
    },
    modules:{
        
    }
})
```



## mapGetters

可以将vuex中的getters映射到组件中直接当成一个计算属性来使用

我们可以在组件的计算属性中用展开符...将映射的mapGetters的getters参数提取出来

...mapGetters()的参数可以传入数组或者对象
传入数组时直接传入store中的getters当做计算属性使用，不能重新为getters命名
传入对象时可以为传入的getters当做value进行重命名，在组件调用时调用key即可
mapActions算一个函数所以需要在methods中注册

```
methods:{
...mapGetters(['gettersName'])
...mapGetters({gettersNewName:'gettersName'}) 
}
```

```javascript


例如我们在store中创建了两个getter
// store/getters.js
export default {
    cartLength(state) {
        return state.cartList.length
    },
    cartList(state){
        return state.cartList()
    }
}


//在组件的计算属性中可以通过...mapGetters提取出来
computed:{
    //...mapGetters(['cartLength','cartList'])
    ...mapGetters({
        length:'cartLength',
        list:'cartList'
    })
}

然后直接当做计算属性使用即可
<div slot="center">购物车({{length}})</div>
```



# Mutation 同步操作

Vuex的state状态更新的唯一方式提交Mutation

**Mutation只提交同步操作，异步操作在action中做**

组件调用Mutation方法---commit

`this.$store.commit('函数名',参数)`

```javascript
Mutation唯一的目的是修改state的状态，判断逻辑等过程放入action中做
Mutation包括两部分
事件类型(type)
回调函数(function),function第一个参数就是state


组件在使用mutation的函数改变state时必须先自己先创建函数
然后在函数内使用this.$store.commit('函数名',参数)提交
参数被成为是mutation的载荷（playload）,
如果需要传递多个参数Playload也可以是一个对象，对象以键值对保存参数

mutations:{
        //添加各种数据处理方法
        //这里会自动获取state，不用手动传输
        //在组件中调用需要this.$store.commit()提交
        increment(state){
            state.counter++
        },
        decrement(state){
            state.counter--
        },
        incrementCount(state,count){
        //count就是playload
            state.counter+=count
        }
        

调用组件的vue实例内添加methods方法提交Mutation更新状态
methods:{
        addition(){
            this.$store.commit('increment')
        },
        subtraction(){
            this.$store.commit('decrement')
        },
        addCount(count){
            this.$store.commit('incrementCount',count)
        }
}

```

## Mutation的提交风格

```javascript
1.$store.commit普通提交
this.$store.commit('incrementCount',count)


2.特殊的提交风格，提交对象
在组件method中直接提交一个对象
this.$store.commit({
    type: 'incrementCount',
    count: count
})

//在store的index.js中设置mutations
incrementCount(state,playload){
   //这种提交风格playload就会变为整个参数对象
   state.counter+=playload.count
}
```

## Mutation响应规则

Vuex的store中的state是响应式的, 当state中的数据发生改变时, Vue组件会自动更新。

state中的对象和数据都被加入响应式系统，每一个调用的地方对应一个watcher，一旦修改，就会通知每个watcher去监听的地方刷新数据

这就要求我们必须遵守一些Vuex对应的规则：**需要响应式更新的数据都需要提前在store实例对象中初始化**。

后面直接增加（例如state.info['city']='chengdu'）的属性并不会加入响应式系统。同理直接删除数据也做不到响应式（例如delete state.info.age）。

可以通过Vue.delete(obj,'key')响应式删除数据。，可以通过Vue.set()方法加入响应式加入数据。

所以当给state中的对象添加新属性时, 使用下面的方式:

方式一: 使用Vue.set(obj, 'key', value)。

方式二: 用新对象给旧对象重新赋值。



```javascript
const store = new Vuex.Store({
    state:{
        info:{
            name:'kobe',
            age:40,
            height:198
        }
    },
    mutations:{
        updateInfo(state){
            state.info.name='wtw',
            state.info['city']='chengdu'
            //给info直接加入新属性
            //但这个属性没有提前定义好
            //不会添加进响应式系统
            Vue.set(state.info,'city','chengdu')
            
            //Vue.set响应式增加的数据
            delete state.info.age
            //直接删除也做不到响应式数据
            Vue.delete(state.info,'age')
            //Vue.delete响应式删除数据
        }
    },
    actions:{
    },
    getters:{
    },
    modules:{
    }
})

```

## Mutations类型常量

当我们的项目增大时, Vuex管理的状态越来越多, 需要更新状态的情况越来越多, 那么意味着Mutation中的方法越来越多

方法过多, 使用者需要花费大量的经历去记住这些方法, 甚至是多个文件间来回切换, 查看方法名称, 甚至如果不是复制的时候, 可能还会出现写错的情况。

在各种Flux实现中, 一种很常见的方案就是使用常量替代Mutation事件的类型

我们可以将这些常量放在一个单独的文件中, 方便管理以及让整个app所有的事件类型一目了然。

可以创建一个文件: mutation-types.js, 并且在其中定义方法名常量。
定义常量时,  使用一个常量来作为函数的名称。

```javascript
官方推荐先把mutations定义的方法名在mutation-types.js中定义为常量
随后把mutations定义的和methods提交的方法名全部变为常量
将方法名定义为常量，方便以后修改
以后如果对方法名就行修改，只需要修改这个文件里给常量赋值的方法名
其他文件的方法名永远为这个常量的值，无需修改
而且可以防止有大量调用方法时写错方法名


在store文件夹下创建一个mutation-types.js
//store/mutation-types.js
//先定义常量
export const INCREMENT = 'increment'
export const DECREMENT = 'decrement'



//store/index.js
import {INCREMENT} from './mutation-types.js'
import {DECREMENT} from './mutation-types.js'
//导入常量
...
mutations:{
//将方法名设置为导入常量
        [INCREMENT](state){
            state.counter++
        },
        [DECREMENT](state){
            state.counter--
        }
}


//App.vue组件文件
import {INCREMENT} from '../store/mutation-types.js'
import {DECREMENT} from '../store/mutation-types.js'
//导入常量
...
methods:{
    addition(){
        this.$store.commit(INCREMENT)
        //提交变量名也改成常量
    },
    subtraction(){
        this.$store.commit(DECREMENT)
    }
}
```



# action 异步操作

Mutations中如果提交异步操作（网络请求等）数据更新，虽然界面会发生同步更新，但devtools将不能监控到数据的变化，这个工具将失去意义，所以如果需要异步操作必须放入action。

action里的函数传入的第一个参数context传入的参数就是store实例对象，context是上下文的意思。

如果需要修改$store.state也需要用mutations修改不能直接修改，在action中如果需要修改state的数据还是commit调用mutations的函数。

actions里的函数可以返回一个Promise,然后在调用的组件中来书写then提供代码复用性，action里的方法组件调用不是提交（commit）,而是this.$store.dispatch('方法名')。

```javascript


const store = new Vuex.Store({
    state:{
        info:{
            name:'kobe',
            age:40,
            height:198
        }
    },
    mutations:{
        updateInfo(state,playload){
         state.info.name=playload
            // setTimeout(()=>{
            //     state.info.name='wtw'
            // },1000)
            //mutations不能设置异步操作
            //否则devtools无法监控数据变化
        }
    },
    actions:{
        aUpdateInfo(context,playload){
        //异步操作放入actions
            //context传入的参数就是store对象
            setTimeout(()=>{
                context.commit('updateInfo',playload)
            },1000)
        }
    },
    getters:{
    },
    modules:{
    }
})

在App.vue中使用

methods:{
    updateInfo(){
        this.$store.dispatch('aUpdateInfo','wtw')
        //使用actions不是commit而是dispatch
    }
}


actions可以结合Promise
actions:{
        aUpdateInfo(context,playload){
            return new Promise((resolve,reject)=>{
                setTimeout(()=>{
                context.commit('updateInfo',playload)
                resolve('成功提交')
                },1000)
            })
            //promise的then写到了组件的dispatch函数后
        }
    }
    
App.vue中
methods:{
    updateInfo(){
        this.$store.dispatch('aUpdateInfo','wtw').then(res=>{
    //actions中aUpdateInfo返回了一个new Promise，
    //所以可以链式调用then
        console.log(res)
        //打印 成功提交
        })
    }
}

```



### mapActions

如果想要调用vuex的actions里的函数要通过dispatch

如果我们像映射mapGetters一样映射mapActions的话就无须dispatch

mapActions算一个函数所以需要在methods中注册

```javascript
methods:{
    ...mapActions(['actionsName'])
    ...mapActions({
        actionsNewName:'actionsName'
    })
}


//在组件中导入mapActions
import {mapActions} from 'vuex'
//在调用之前先注册要映射的actions
methods:{
...mapActions(['addCart']),
addToCart(){
    //使用dispatch调用
    // this.$store.dispatch('addCart',product).then(res=>{
    //     console.log(res)
    // })
    
    //使用mapActions就可以直接用actionsName调用使用
    this.addCart(product).then(res=>{
         console.log(res)
     })
}}

```



# modules

modules是模块的意思，可以用于抽离代码。

Vue使用单一状态树,那么也意味着很多状态都会交给Vuex来管理。当应用变得非常复杂时,store对象就有可能变得相当臃肿。

为了解决这个问题, Vuex允许我们将store分割成模块(Module), 而每个模块拥有自己的state、mutations、actions、getters等。



modules里的模块最后都会放入state
所以使用state时 $store.state.模块.属性 就行了

提交mutations时还是普通提交,他会先去store对象中寻找，没有再去模块找
this.$store.commit('方法名',playload)

getters和mutations一样还是普通使用
{{$store.getters.函数名}}

actions的context不再是store对象，而是自己这个模块
只能提交进这个模块的mutations

```javascript

const moduleA={
    state:{
        name:'wtw'
            },
    mutations:{
        updateName(state,playload){
            state.name = playload
        }
    },
    actions:{
        aUpdataName(context){
            setTimeout(()=>{
                context.commit('updataName','wangwu')
            },1000)
        }
    },
    getters:{
        fullname(state){
            return state.name+'123'
        }
    } 
}

const store = new Vuex.Store({
    state:{
    },
    mutations:{
    },
    actions:{
    },
    getters:{
    },
    modules:{
        a:moduleA
    }
})


组件中调用时
//查询state
{{$store.state.a.name}}//$store.state.模块.属性

//getters还是照常使用
{{$store.getters.fullname}}

//mutations
methods(){
//提交方式不变
    updataName(){
       this.$store.commit('updateName','wy') 
    },
    asyncUpdataName(){
        this.$store.dispatch('aUpdataName')
    }
}


```



# vuex的分离

```javascript
state
mutations
getters
modules

除了state每个都建议单独抽离出来一个js文件，在index.js中导入
创建一个modules文件夹，每个模块一个js文件装进这个文件夹
```

