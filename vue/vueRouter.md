# VueRouter

# 路由

路由（routing）就是通过互联的网络把信息从源地址传输到目的地的活，路由决定数据包从来源到目的地的路径，转送将输入端的数据转移到合适的输出端，路由表本质是一个映射表，决定了数据包的流向



## 前端渲染与后端渲染

互联网早期采取后端渲染，所有网页在后端服务器进行渲染，用户拿到的是已经渲染好了的完整网页（后端路由：后端处理URL和页面之间的映射关系）。

后来随着Ajax出现，进入前后端分离阶段，后端只负责提供数据，不负责任何界面内容，用户浏览器从静态资源服务器拿到html，css，js代码在前端进行渲染。在执行js代码时如果通过ajax向api接口发送请求的时候，再通过提供api接口的服务器拿到大量数据再渲染到浏览器上，并且移动端也可以调用这套api，后端无需另行处理，互联网这时发展到前端渲染阶段，目前很多公司也是这样开发。

第三个阶段是单页面富应用阶段（SPA），SPA最主要的特点是在前后端分离的基础上加了一层前端路由，也就是前端来维护一套路由规则，SPA页面整个网页只有一个html页面一次性下载所有代码，在执行操作时通过前端路由跳转到相应的url渲染对应js代码和页面（前端路由：前端路由管理URL和页面之间的映射关系），前端路由的核心是改变URL，但是页面不进行整体刷新（Ajax是url不改变,页面实现部分刷新）。



## 单页应用与多页应用

多页应用每次页面跳转都会返回一个新html文件

优点：首屏时间快，SEO效果好（搜索引擎优化）

缺点：页面切换速度慢



单页应用页面跳转通过js渲染

优点：页面切换快，无需重新请求html

缺点：首屏时间慢，SEO优化差，需要通过服务器端渲染（SSR）解决这些问题



## URL的hash

改变url的hash页面不会整个刷新，类似于论坛的翻页就是改变了url的hash

```javascript
启动vue 本地服务器
进入 localhost:8080 主页
    1.改变url
    location.href=''
    页面会刷新并跳转到这个新url连接
```

```javascript
    2.改变url哈希
    改变url哈希页面不发生刷新
    
    location.hash='1'
    //http://localhost:8080/#/1
    url后面就会加上/#/1
    vue router就会不刷新页面只改变url的哈希后缀

    在html5中有一个history对象
    history对象pushState()方法或者replaceState()可以改变url的哈希
    两个方法最后一个参数就是改变url的哈希
    区别在于一个能返回一个不能返回

    -history.pushState()
    history对象在调用back()出栈返回上次入栈的url
    history对象在调用forward()入栈前进一次url
    也可以点击浏览器返回按键返回上次url的哈希
    这个方法类似一个栈结构存储hash，先入后出
    history.pushState({},'','adc')
    //http://localhost:8080/abc
    history.pushState({},'','home')
    //http://localhost:8080/home
    history.pushState({},'','about')
    //http://localhost:8080/about
    history.back()
    //http://localhost:8080/home#/
    history.forward()
    //http://localhost:8080/about#/
    history.go(-1)
    //http://localhost:8080/home#/
    //出栈一个，等同于history.back()
    history.go(1)
    //http://localhost:8080/about#/
    //入栈一个，等同于history.forward()
    
    -history.replaceState()
    replaceState()不保存hash，无法返回前进
    history.replaceState({},'','abc')
    //http://localhost:8080/about
    history.back()
    //报错
```



## 三大框架的router

目前前端流行的三大框架, 都有自己的路由实现:

```html
Angular的ngRouter
React的ReactRouter
Vue的vue-router

vue-router是Vue.js官方的路由插件，它和vue.js是深度集成的，
适合用于构建单页面应用。
其官方网站 https://router.vuejs.org/zh/
vue-router是基于路由和组件的。
路由用于设定访问路径, 将路径和组件映射起来。
在vue-router的单页面应用中, 页面的路径的改变就是组件的切换。
```





# mode路由模式

SPA需要在不刷新页面的情况下做页面更新的能力，这就需要引入前端路由，实际上，前端路由是利用了浏览器的hash或history属性。history模式需要服务端支持，hash模式不需要服务端支持，所以一般选择hash模式。



**hash模式** ：`http://abc.com/#/a/1`

原理是触发hashChange事件

hash （url中#后面的部分）虽然出现在 URL 中，但不会被包含在 http 请求中，对后端完全没有影响，因此改变 hash 不会重新加载页面。当hash改变时，会触发hashChange事件，监听该事件，对页面进行更新。



**h5 history**模式 ：`http://abc.com/a/1`

History interface是浏览器历史记录栈提供的接口，通过back(), forward(), go()等方法，我们可以**读取浏览器历史记录栈的信息**，进行各种跳转操作。

vue-router history模式利用了 html5 history interface 中新增的 pushState() 和 replaceState() 方法。这两个方法应用于浏览器记录栈，在当前已有的 back、forward、go 基础之上，它们提供了对历史记录修改的功能（pushState将传入url直接压入历史记录栈，replaceState将传入url替换当前历史记录栈）。只是当它们执行修改时，虽然改变了当前的 URL ，但浏览器不会立即向后端发送请求。



**已经有 hash 模式了，而且 hash 能兼容到IE8， history 只能兼容到 IE10并且H5 history需要server端支持，为什么还要搞个 history 呢？**

首先，hash 本来是拿来做页面定位的，如果拿来做路由的话，原来的锚点功能就不能用了。其次，hash 的传参是基于 url 的，如果要传递复杂的数据，会有体积的限制，而 history 模式不仅可以在url里放参数，还可以将数据存放在一个特定的对象中。





# vue-router配置

安装vue router插件
`npm install vue-router --save`



第一步:安装配置vue-router并创建路由组件（Home和About）



第二步: 配置路由映射: 组件和路径映射关系

index.js中VueRouter实例对象的属性routes数组就是组件和url的映射关系，path代表url的哈希后缀，component是与之建立映射的组件



第三步: 使用路由: 通过`router-link`和`router-view`标签渲染组件和给组件占位

router-link用于设置跳转路由的按钮，最终被渲染成a标签

router-view用于显示路由跳转后渲染组件的位置



## 路由列表

path和component是路由的两个配置信息，当url变化时就会进入相应组件

```javascript
const routes = [
    {
    path:"/home"
    component:Home
    },{
    path:'/about',
    component:About 
    }
]
```



创建两个vue组件在component文件夹下并导出，两个组件为分别为Home.vue和About.vue

mode属性是配置采取哪种模式

```javascript
// router/index.js
import Vue from 'vue'
//使用Vue.use要导入Vue
import VueRouter from 'vue-router'
//引入vue-router插件
import Home from "../components/Home.vue";
import About from "../components/About.vue";
//引入需要的两个组件
Vue.use(VueRouter)
//必须通过Vue.use(插件)安装插件
const routes = [
//将routes抽离出来避免太多嵌套关系
//routes配置路由和组件直接的映射关系
    {
    path:"/home"
    component:Home
    },{
    path:'/about',
    component:About 
    }
]

//创建VueRouter对象
const router = new VueRouter({
  	mode: 'history', // 默认为hash模式路由，如果想使用history需要手动设置mode
    routes
})

//最后将router对象导出到Vue实例
export default router
```



在vue根实例中注入router

```javascript
//在main.js里引入router
import Vue from 'vue'
import App from './App'
import router from './router/index'

new Vue({
  el: '#app',
  router,
  //在vue根实例中注入router
  render: h => h(App)
})


3.使用vue router
<router-link to="path">和<router-view>
是vur router注册的两个组件
<router-link>最终被渲染成a标签
<router-view>用于占位
to="path"的组件被渲染到router-view标签位置上

例如
在App.vue组件中使用路由标签
<router-link to="/home">home</router-link>
<router-link to="/about">about</router-link>
//router-link会被渲染为a标签

<router-view></router-view>
//点击home或about对应的组件就会渲染到这里
//地址栏url地址栏会变，但页面不刷新其他页面其他内容也不改变
```

ps：
当`<router-link>`对应的路由匹配成功时（被点击），会自动给点击的标签增加一个属性class='router-link-active'，也就是当这个标签被点击时会自动增加一个class属性，也可以给标签添加active-class="属性值"属性来对默认值进行修改





## 路由默认值

设置进入首页时router-view默认显示的组件

```javascript
const routes = [
    {
    	path:""
    	component:Home
    },
    {
    	path:"/home"
    	component:Home
    },{
    	path:'/about',
    	component:About 
    }
]
//这样做初始的url的哈希值不会变为/home,不推荐这样使用
```



## 重定向redirect

重定向是指url的哈希值一旦等于path值，就会跳转到重定向设置url哈希值

```javascript
const routes = [
    {
    	path:"/",
    	//path=''也可以
    	redirect:'/home'
    	//当url的hash等于空时立刻跳转到hash等于/home
    },
    {
    	path:"/home",
    	component:Home
    },{
    	path:'/about',
    	component:About 
    }
]
```



## hash模式切换history模式

```javascript
给VueRouter实例对象router加一个mode:'history'属性
history模式的url哈希值前面没有/#，不用history的hash值前都会有/#
就可以从哈希模式转换为history模式
history模式比哈希模式url哈希值更美观
哈希模式下使用history模式代码可能出现问题
所以推荐转变为history模式后再使用history.pushState等代码

const router= new VueRouter({
    routes,
    mode:'history'
    //给router对象加一个mode:'history'属性
})
```



## router-link组件

`<router-link>`除了to="path"属性还有一些其他属性

**tag属性**，tag可以指定`<router-link>`之后渲染成什么组件(默认是a标签),
比如下面的代码会被渲染成一个`<button>`元素, 而不是`<a>`

**replace属性**，replace不会留下history记录, 所以指定replace的情况下,
后退键返回不能返回到上一个页面中
路由默认是history.pushState(),可以通过浏览器或代码前进后退(出入栈)
如果不想用户通过浏览器实现返回前一个url哈希就可以添加replace属性
路由映射则会变为history.replaceState()模式
无法通过通过浏览器或代码实现返回前进

**active-class属性**， 当`<router-link>`对应的路由匹配成功时,
会自动给当前元素设置一个class='router-link-active'属性
也就是当这个标签被点击时会自动增加一个class属性

```html
<router-link to="/home" tag='button'>home</router-link>

<router-link to="/home" replace>home</router-link>

<router-link to="/home" active-class='active'>home</router-link>
<!--当标签被点击时添加的默认class属性值为'active'-->
```



也可以给router路由对象再添加一个**linkActiveClass属性**，这样所有被这个路由管理的router-link标签被点击时
都会增加一个class="active"属性

```javascript
const router= new VueRouter({
    routes,
    mode:'history',
    linkActiveClass:"active"
})
```





## 通过代码跳转路由

vue-router源码为每个组件加了个$router属性,通过$router可以控制路由
this.$router对象就是我们创建的VueRouter实例路由对象
所以可以用$router控制路由
`$router.push('path')`方法：带有history记录的跳转到path，将页面压栈

`$router.replace('path')`方法：不带有history记录的跳转到path

`history.pushState()`等history方法也能改变url的哈希

但这样做就跳过了映射url的路由，十分不安全，一定要使用$router属性通过路由跳转url

```html
<div id="app">
<!-- <router-link to="/home" tag='button'>home</router-link>-->
<!-- <router-link to="/about">about</router-link>-->
<!--原本通过router-link控制路由-->
<!--现在给按钮添加点击事件结合$router.push方法改变url哈希-->
<router-view></router-view>
<button @click="homeClick">Home</button>
<button @click="aboutClick">About</button>
</div>
<script>
export default {
  name: 'App',
    methods:{
        homeClick(){
            this.$router.push('/home')
            //对象的函数调用对象属性要加this
        },
        aboutClick(){
            this.$router.push('/about')
        }
    }
}
</script>
```





# 动态路由

某些情况下页面的path路径可能是不确定的，比如用户主页url后跟上ID就需要用到动态路由

```javascript
//配置路由映射时进行拼接
//sec/router/index.js
routes:[
    {
        path:'/user/:userID',
        //配置动态路由时变量前加：
        component: User
    }
]

//src/components/App.vue
//APP组件中data()返回了userID，使用拼接userID变量的方式拼接url
<router-link :to="'/user/'+userID"></router-link>
...
data(){
    return{
        userID:wtw
    }
}

//这样显示的path就为/user/wtw
```



# $route.params

$route.params获取正在活跃的路由

$router是整个路由实例对象，$route是正在活跃的路由

```javascript
如果想在User组件中使用App组件data()返回的userID
可以通过$route获取正在活跃的路由对象
params是$route的属性,用来储存数据的键值对(对象形式,{key:value})
params可以储存键值对在里面
而在index.js中配置路由映射path中用到的 :变量 就是再往里面加key
//path:'/user/:userID'就是key为userID
value值则由点击路由后触发路径的变化来决定具体的值
//App.vue中返回了wtw并让路径变化为/user/wtw，所以值为wtw
然后我们在组件中就可以使用$route.params.key来调用数据键值对


//src/components/User.vue
<template>
    <div>
    <h2>User</h2>
    <p>User主页</p>
    <h2>{{userID}}</h2>
    <h2>{{$route.params.userID}}</h2>
    </div>
</template>

<script>
    export default {
        name: "User",
        computed:{
            userID(){
                return this.$route.params.userID
            }
        }
    }
</script>
```



# 路由懒加载

打包后所有的业务代码会合成一个js文件，直接加载这个文件可能很大，导致用户界面出现短暂空白的情况，如果我们能把不同的路由对应的组件分割成不同的代码块，访问这些路由的时候才加载对应组件，这样更高效，将component属性设置为函数返回

懒加载：将每个路由对应的组件打包成一个个代码块，用到这个模块时再下载

```javascript
原本的直接加载配置路由的index.js
import Home from "../components/Home";
import About from "../components/About";
const routes = [
    {
        path:"/home",
        component:Home
    },{
        path:'/about',
        component:About
    }]

懒加载后修改为
const routes = [
    {
        path:"/home",
        component:()=>import "../components/Home"
    },{
        path:'/about',
        component:()=>import "../components/About"
    }]
    
或者（更推荐下面这种）

const Home = () => import("../components/Home");
const About= () => import("../components/About");
const routes = [
    {
        path:"/home",
        component:Home
    },{
        path:'/about',
        component:About
    }]

懒加载打包时就会多两个js文件，用到时才会下载
```





# 嵌套路由

嵌套路由就是路由里加子路由比如进入/home组件后再进入/home/news组件



步骤一：创建对应子组件，并在路由中映射配置对应子组件
创建两个组件news.vue和Message.vue

在index.js中配置
给home路由配置加一个属性children数组，在数组里面继续配置子路由
注意children中的子路由path开头不加/

```javascript
const HomeNews = ()=> import("../components/HomeNews");
const HomeMessage = ()=> import("../components/HomeMessage");
const routes=[{
        path:"/home",
        component:Home,
        children:[
        	{
        		path:"",
        		redirect:'news'
        	},
        	{
        		path:"news",
        		//path:'子路径哈希'
       	 		component:HomeNews
       	 	},{
        		path:"message" ,
       			component:HomeMessage
        	}
        ]
}]
```

步骤二：在router-view中显示子组件
在Home.vue中使用router-link和router-view
to必须to到完整的url  /父路由/子路由

```html
<router-link to="/home/news">news</router-link>
<router-link to="/home/message">message</router-link>
<router-view></router-view>
```





# vue-router传递消息

利用v-bind和router-link的to给user组件传递userID就是传递消息的一种

URL的组成：协议://主机:端口/路径?查询
scheme://host:port/path?query#fragment
协议http或者https
端口生产环境默认80，开发环境默认8080
查询就是query

传递消息主要有两种类型：params和query
params的类型:
配置路由格式: /router/:id
传递的方式: 在path后面跟上对应的值
传递后形成的路径: /router/123, /router/abc

```
params是$route的属性,
用来储存数据的键值对(对象形式,{key:value})
params可以储存键值对在里面
而在index.js中配置路由映射path中用到的 :变量 就是再往里面加key
<!--/path:'/user/:userID'就是key为userID-->
value值则由点击路由后触发路径的变化来决定具体的值
<!--App.vue中返回了wtw并让路径变化为/user/wtw，所以值为wtw-->
然后我们在组件中就可以使用$route.params.key来调用数据键值对
```

```html
query的类型:
配置路由格式: /router, 也就是普通配置
传递的方式: 对象中使用query的键值对作为传递方式
传递后形成的query路径

创建一个profile组件
在index.js中配置路由
在App.vue中调用路由
<router-link :to="{path:'/profile',query:{name:'wtw',age:22}}">
    Profile
</router-link>
打开后url变为 http://localhost:8080/profile?name=wtw&age=22

也可以在vue实例里传递,将router-link变为button标签，添加事件传递
<button @click='profileClick'></button>
在vue实例中添加计算属性利用$router.push()跳转路由并传递消息
$router.push()也能传入一个对象用于跳转路由和传递消息
$router.push({
    path:'',
    query:{
    
    }})


method(){
    profileClick(){
        this.$router.push({
        <!--$router.push路由跳转方法-->
        path:'/profile',
        query:{
            name:'wtw',
            age:22
        }
        })
    }
}



在profile组件中可以用活跃路由的query属性
$route.query取出传递的消息
<h2>{{$route.query.age}}</h2><!--22-->
<h2>{{$route.query.name}}</h2><!--wtw-->
```



## $route和$router的区别

$router调用的是我们创建的VueRouter实例全局路由对象，可以通过$router跳转路由<br>
$route是调用当前正在活跃的路由组件对象，可以通过$route传递消息





# 导航守卫

导航守卫是监听路由从哪里跳转到哪里了

## 生命周期函数

```javascript
vue的生命周期函数，运行到该周期自动回调函数
created()    组件创建时回调
mounted()    template挂载到dom上时回调
updated()    数据发生更新时回调
destroyed()  销毁时发生回调


需求：当路由跳转后网页title要变为组件名
在About.vue中添加created函数
created(){
    document.title='about'
}
当我们点击about让About组件创建时网页标题就会变为about
但路由组件变多了一个个修改太费时间了，这时可以添加全局导航守卫
```



## 全局导航守卫

**beforeEach,afterEach也被称为全局守卫**

在index.js中,先给route添加配置，每个路由都添加一个mate属性存储title属性
meta属性，元数据，描述数据的数据
例如

```javascript
{
    path:'/about',
    component:About,
    mate:{
        title:'about'
    }
}
```

------

然后在index.js中添加router.beforeEach()导航守卫函数

导航守卫函数是一个前置回调钩子

当从一个路由跳转到另外一个路由前就会执行该函数

router是我们创建的VueRouter实例对象

router有一个beforeEach是导航守卫,需要传入一个函数

这个函数有三个参数to，from，next（无需修改直接使用）

必须传入并执行next()用于路由跳转，不然路由会失效

参数的意思是路由从from跳转到to最后执行next是路由保持运行

```javascript
router.beforeEach((to,from,next)=>{
    doucument.title= to.mached[0].meta.title
    //mached[0]是指永远取到嵌套路由的第一层
    next()
    //调用next才能跳转路由下一步，不写next整个路由都会失效
})
```



后置回调钩子
当从一个路由跳转到另外一个路由后就会执行该函数

```javascript
router.afterEach((to,from)=>{
//afterEach无需调用next
console.log('-----')
})
```





## 独享守卫与组件内守卫

我们可以直接在路由配置上定义路由独享守卫beforeEnter
参数与router.beforeEach一致

```javascript
beforeEnter
{
    path:'/about',
    component:About,
    mate:{
        title:'about'
    },
    beforeEnter: ((to,from,next)=>{
        consloe.log('about beforeEnter')
    })
}

组件内守卫（了解）
创建在组件内的守卫
beforeRouteEnter，beforeRouteUpdate，beforeRouteLeave
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，
    //在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。
    //而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```





# keep-alive

keep-alive 是 Vue 内置的一个组件，可以使被包含的组件保留状态，避免重新渲染，被包裹的router-view调用的路由组件都不会被重新销毁创建。

它们有两个非常重要的属性:
include - 字符串或正则表达，只有匹配的组件会被缓存
exclude - 字符串或正则表达式，任何匹配的组件都不会被缓存

include/exclude='componentName' 匹配的是组件的name属性,用逗号间隔

router-view 也是一个组件，如果直接被包在 keep-alive 里面，
所有路径匹配到的视图组件都会被缓存



keep-alive提供的回调生命周期函数，以下只有在keep-alive包裹的router-view内有效
activated()     页面活跃时回调
deactivated()   页面不活跃时回调
beforeRouteLeave()  路由跳转之前回调



案例：在Home组件中点击message后切换其他组件，当再次切换回Home时，子组件router-view还是显示message（重定向的是news）

```javascript
//在Home.vue的data()返回中加入两个keep-alive函数
activated(){
    this.$router.push(this.path)
},
beforeRouteLeave(to,from,next){
    this.path=this.$route.path
    next()
}


//在App.vue加入keep-alive缓存组件状态
<keep-alive exclude='Profile,User'>
//不缓存Profile,User，他们切换出去就会销毁，进来会被创建
    <router-view/>
</keep-alive>
```





# 路由原理

## hash模式原理

url中#后面的部分叫hash部分，hash的变化会触发网页跳转，即可以浏览器前进后退，但是hash变化不会刷新页面，这是SPA的必须的特点，并且hash永远不会提交到服务端

hash变化可能有三种原因：js修改，手动修改，浏览器前进后退

hash的变化会触发`window.onhashchange`事件，并且可以传入e获取新旧url，也可以通过location对象获取当前url的hash值

vue-router根据hash变化渲染对应组件，这个过程不会导致页面的刷新

```javascript
window.onhashchange = (event) => {
  console.log('old url: ' + event.oldURL)
  console.log('new url: ' + event.newURL)
  console.log('hash: ' + location.hash)
}
```



## H5 history模式原理

H5 history模式需要服务端配合，但H5 history模式有在切换路由时可以携带一些数据，并且可以做一些SEO优化的优点

history模式跳转路由跟hash一样也不会刷新页面（但实现方法不同），H5 history来实现路由有两个关键api

- history.pushState()   		使用这个api修改url，浏览器不会刷新页面
- window.onpopstate事件  监听浏览器前进后退而引起的url改变

```javascript
// 页面初次加载，获取path
document.addEventListener('DOMContentLoaded', () => {
    console.log('load: ' + location.pathname )
})

// 打开一个新路由
document.getElementById('btn1').addEventListener('click', {
    const state = { name: 'page1' } // state是我们切换路由携带的数据
    console.log('切换路由至page1')
	history.pushState(state, '', 'page1') // 使用这个api修改url，浏览器不会刷新页面
})

// 监听浏览器前进后退
window.onpopstate = (event) => {
    console.log('onpopstate', event.state, location.pathname)
}
```



