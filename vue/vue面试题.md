# Vue面试题

# Vue特性

## computed和watch

单个监听watch、深度监听deep、多个监听computed

- computed有缓存，data不变不会重新计算

- watch监听

  在监听复杂数据类型的时候，不能像之前监听普通数据那样写，我们需要使用深度监听（deep），因为vue不监控数组和对象的改变，需要开启深度监听

  ```javascript
  data() {
  return {
  	name:'wtw',
  	info: {
  		city:'cd'
  	}
  }
  },
  watch: {
  	name(oldVal, val) {
  		console.log('oldVal:'+oldVal)
  		console.log('val:'+val)
  	},
  	info: {
  		// 引用类型必须深度监听都否则拿不到,用handler监听，deep设置true
  		handler(oldVal,val) {
  			console.log('oldVal:'+oldVal)
  			console.log('val:'+val)
  		},
  		deep: true //对象内部属性的监听，关键
  	}
  }
  ```

  

- 什么时候用watch，什么时候用computed

  - computed用来监控自己定义的变量，该变量不在data里面声明，直接在computed里面定义，然后就可以当做一个属性在页面上进行双向数据绑定展示出结果或者用作其他处理；
  - **computed比较适合对多个变量或者对象进行处理后返回一个结果值**，也就是数多个变量中的某一个值发生了变化则我们监控的这个值也就会发生变化，举例：购物车里面的商品列表和总金额之间的关系，只要商品列表里面的商品数量发生变化，或减少或增多或删除商品，总金额都应该发生变化。这里的这个总金额使用computed属性来进行计算是最好的选择
  - watch主要用于监控vue实例的变化，它监控的变量当然必须在data里面声明才可以，一次只能监控单个变量
  - watch一般用于监控路由、input输入框的值特殊处理等等，它比较适合的场景是一个数据影响多个数据



## :style和:class

v-bind绑定style属性和绑定class属性的区别

### :class

对象写法
`v-bind:class="{class:bool}" `
对象中键是样式名,值是布尔值,为真则存在该样式
格式` v-bind:class="{类名1:boolean,类名2:boolean...}"`
如果键值对过于复杂也可以保存为函数返回值进methods和computed中

数组写法
`v-bind:class="[变量1,变量2...]"`
数组写法也可以将样式名设置为变量名
但数组写法不如对象写法灵活

### :style

`:style="{key(属性名):value(属性值)}"`

key值需要驼峰来写



## v-if和v-show

v-show 通过css display控制一个元素是否在页面上显示，v-if 决定一个元素是否渲染（在dom中存在与否）

显示隐藏频率高时选择v-show，只有一次切换选择v-if



## display:none与visibility:hidden

css中

visibility:hidden 类似v-show决定是否显示

display:none 类似于v-if决定是否渲染



## v-for

v-for中的key要写，避免重复渲染，v-for和v-if不能一起使用（因为v-for优先级高，比v-if先执行）

**v-for遍历一个vue实例的data内的对象属性去渲染列表，如果打开网页页面生成列表后，在控制台或代码直接用点语法增加对象属性，已经渲染好的列表是不会直接增加列表项的**

因为vue不监控引用类型的变化

使用以下两个方法可以在列表渲染好后新增列表改变页面

- 重新绑定一个增加了属性的新对象
- 使用Vue.set()/VueObj.$set()增加对象的属性可以在页面上新增列表项

```javascript
const app = new Vue({
    el:'#app',
    data:{
       info:{
         name: 'wtw',
         age: '20',
         height: 170
         }
     }
   })
app.$set(app.info,"name","wy") 
```



## v-on

v-on绑定事件

如果绑定事件函数时加了()就获取不到默认事件参数e，需要手动传入一个`$event`默认事件参数对象

```html
<button @click="add($event)">+</button>
<!--加了括号，需要手动传入$event才能让函数获取默认事件参数对象e-->
<button @click="sub">-</button>
<!--没有括号，函数内直接可以直接传入e-->
```



### v-on的修饰符

修饰符可以实现一些事件功能，如阻止事件冒泡等

#### .stop 

调用event.stopPropagation()阻止事件冒泡
例  @click.stop="btn1"

#### .prevent

调用`event.preventDefault()`阻止浏览器默认事件(event事件默认参数)
例  `@click.prevent="btn1"`绑定事件，就会btn1阻止浏览器默认事件

#### .keyCode|.keyAlias

keyCode按键编码,keyAlias按键名称,用于监听某个的键盘按键点击
例如监听enter键  @keyup.enter="console.log("enter up")"

#### .native

自定义组件不能

监听组件标签的原生事件

#### .once

只触发一次回调
例 ` @click.once="btn1"`
这个按键只有第一次点击触发事件

#### .capture

添加了后缀的事件不遵守冒泡规则，遵守捕获规则，即冒泡的时候不触发，捕获的时候触发

​    捕获：内部到外部

​	冒泡：外部到内部





# scoped样式模块化

在*vue*组件中,在style标签上添加scoped属性,以表示它的样式作用于当下的*模块*,很好的实现了样式私有化的目的





# 组件通信

## 父子组件通信

- props和$emit 

  `this.$emit('父组件事件',参数)`

  ​	

- $children/$parent /ref

  ```javascript
  //访问第一个子元素的showMessage()方法
  this.$children[0].showMessage();
  ```

  ```javascript
  //直接访问父组件的属性
  //this.$parent访问父组件
  console.log(this.$parent);
  console.log(this.$parent.name);
  //father name
  console.log(this.$root.message);
  ```

## 兄弟组件通信

- 绑定自定义事件触发事件总线 

  **事件总线 event bus**

  在两个组件中（vue实例外部）引入总线

  `Vue.prototype.bus = new Vue()`

  

  然后在两个组件的vue实例中分别绑定和触发总线事件

  **组件A绑定总线事件**

  `this.bus.$on('事件名',绑定事件函数) ` 

  在vue实例对象中mounted()中使用bus在总线上绑定自定义事件

  **组件B触发总线事件**

  `this.bus.$emit('事件名',参数)`  

  在其他组件中触发自定义事件，绑定总线事件的组件A就会接收到消息并触发该事件

  

  使用完后尽量在绑定总线事件的组件中销毁的生命周期（beforeDestory）中解绑总线事件避免内存泄漏

  ```javascript
  beforeDestory() {
  	event.$off('事件名', 事件处理函数)
  }
  ```



- vuex

- $attrs/$listeners
- provide/inject





# 生命周期

初始化 ：初始化vue的实例，模板还未挂载

beforeCreate() 

create() 



挂载：挂载vue到el绑定的dom上去

beforeMount()

mount() ：挂载模板，在此可以访问模板中的dom元素，或者ajax请求和异步任务，绑定总线事件



更新：数据更新

beforeUpdate()

Update()：



卸载：执行vueObj.$destroy销毁vue实例（销毁组件），收尾工作清除定时器，解绑总线事件等

beforeDestroy()

destroy() 



## 父子组件生命周期执行顺序

**一个生命周期由父组件开始，但只有全部子组件完成了这个生命周期，父组件才能说自己做完了**

**初始化**：父组件beforeCreated--->子组件beforeCreated--->子组件created--->父组件created



**挂载**：父组件beforeMounted--->子组件beforeMounted--->子组件mounted--->父组件mounted



**更新**：父组件beforeUpdate--->子组件beforeUpdate--->子组件update--->父组件update



**销毁**：父组件beforeDestroy--->子组件beforeDestroy--->子组件destroy--->父组件destroy





# vue高级特性

- 自定义v-model
- $nextTick
- slot
- 动态组件，异步组件
- keep-alive
- mixin

- refs

vue常见性能优化就是动态组件（is），异步组件（import()），keep-alive



## 异步合并渲染

为了尽量少的去操作dom，多次修改data导致dom修改时，会将修改dom的步骤合并到最后执行

```javascript
method() {
	addItem() {
		this.list.push('abc')
    this.list.push('123')
		const ulEle = this.$refs.ulbox
		console.log(ulEle.childNodes.length)
        this.list.push('789')
        // 三次数据修改会合并成一次去渲染dom
   }
}
```

异步合并渲染会导致在一次data改变后dom不会立刻改变，会等待这个函数执行完后才改变dom，但如果这个函数中有操作dom的步骤存在就会产生错误，因为dom还没渲染，操作dom的代码就已经开始执行了。

为了解决这个问题，需要使用$nextTick()将操作dom的代码延迟到dom渲染完后才执行。





## $nextTick()

`$nextTick(callback)`

vue是一个异步渲染的框架，为了尽量减少dom操作次数，所以在一个函数内当data改变后，dom不会立刻去渲染，他会将延迟等待多次修改data操作合并成一次再去执行，所以我们不能在data改变后在同一个函数内立刻去获取dom元素并操作dom元素，如果一定要操作dom需要使用$nextTick()

而$nextTick就是一个异步的方法，用于延迟执行一段代码，$nextTick(callback)的异步回调函数会在DOM渲染之后才被触发，以获取最新的DOM节点，所以我们在更新数据之后，如果需要拿到dom元素并操作，需要将操作dom部分的代码放入$nextTick的回调函数中，这样等dom渲染完后才会去执行其中操作dom的代码。

```javascript
method() {
	addItem() {
		this.list.push('abc')
        this.list.push('123')
		// 每次触发就往data里的list中加一项，与数据双向绑定的ul就会多一个li
		// 但数据改变dom不会立刻更新，就去操作ul输出有几个li，输出的是dom更新之前的li个数
		// const ulEle = this.$refs.ulbox
		// console.log(ulEle.childNodes.length)      
        //放进this.$nextTick的回调中就可以等dom渲染完后才执行这段代码
		this.$nextTick(() => {
		   const ulEle = this.$refs.ulbox
		   console.log(ulEle.childNodes.length)
		})
	}
}
```



## 缓存组件 keep-alive

keep-alive包裹的组件切换出去时不会触发销毁，会被缓存下来，状态会被保存，用于频繁切换又不需要重新缓存组件时使用。类似于让组件暂时隐藏。keep-alive是框架层面上的控制隐藏，keep-alive后控制state切换v-if的状态，不会去销毁组件（不会触发销毁的生命周期），而是类似于隐藏起来，类似v-show。

```vue
<keep-alive>
	<A v-if="state='A'"/>
	<B v-if="state='B'"/>
	<C v-if="state='C'"/>
</keep-alive>
```





# mvvm数据驱动视图

vue通过mvvm模型来实现数据驱动视图，react通过setState来实现数据驱动视图

  

View: DOM

​		|

ViewModel：Vue的数据操作（监听事件操作等）

​		|

Model：JavaScript Objects （vue中的data）



Vue中，我们直接操作的是Model层的数据，数据来驱动ViewModel层（Vue）去修改View层（dom）





# Vue响应式

**Vue2.x 实现数据双向绑定的原理：Object.defineProperty()**



vue实现数据双向绑定主要是：采用 **数据劫持结合发布者-订阅者模式** 的方式，通过 **Object.defineProperty()** 来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应监听回掉。当把一个普通 JavaScript 对象传给Vue 实例来作为它的 data 选项时，Vue 将遍历它的属性，用Object.defineProperty 将他们转为 getter/setter。用户看不到getter / setter ，但是在内部它们让vue追踪依赖，在属性被访问和修改时通知变化。



**要实现MVVM的双向绑定要实现以下几点**

1、实现一个数据监听器Observer，能够对数据对象的所有属性进行监听，如有变动可拿到最新值并通知订阅者

2、实现一个指令解析器Compile，对每个元素节点的指令进行扫描和解析，根据指令模板替换数据，以及绑定相应的更新函数

3、实现一个Watcher，作为连接Observer和Compile的桥梁，能够订阅并收到每个属性变动的通知，执行指令绑定的相应回调函数，从而更新视图

4、mvvm入口函数，整合以上三者



**vue3.x使用了Proxy来对数据进行监听**

Object.defineProperty缺点

- 不能监听数组变化，proxy可以原生支持监听数组
- 要配合Object.keys遍历对象必须遍历对象每一个属性
- 必须深层遍历嵌套的对象





# 数据劫持与发布订阅

数据驱动视图的第一步是数据劫持到data数据的更新，然后再发布订阅模式触发视图的更新。其中监控数据变化的的核心api是Object.defineProperty，这个api有一定的缺点，所以Vue3.0以后是改为使用Proxy来实现。但Proxy也有一些兼容性问题，例如兼容性不好且无法使用polyfill。



## Object.defineProperty

```
Object.defineProperty(obj,"属性名",属性值)
```

这个api可以给对象新增一个属性，并对属性的属性值和特征则进行修改，api的返回值为此对象

```javascript
const data = {}
const name = 'wtw'
Object.defineProperty(data, "name", {
	get: function () {
		console.log('getter')
		return name
	}
	set: function (newVal) {
		console.log('setter')
		name = newVal
	}
})
console.log(data.name) 
// getter
// wtw
data.name = 'lisi'
// setter
console.log(data.name) // lisi
```

光靠Object.defineProperty不能实现数据劫持，还需要解决几个问题

- 监听对象，监听数组
- 复杂对象怎么深度监听



## 数据劫持

```javascript
function updateView() { // 更新视图的函数
  console.log('视图更新')
  dep.notify() // 触发发布订阅模式的更新通知
}

// 对对象属性进行递归监听，并设置setter/getter
function defineReactive(target, key, value) {
  // 递归深度监听对象，如果传入的value是一个对象则继续observer
  observer(value)

  // 使用核心API进行数据劫持
  Object.defineProperty(target , key, {
   get() {
       return value
   },
   set(newValue) {
   if(newValue !== value) {
       // 设置值时执行一次深度监听，防止设置的值又是一个复杂对象，导致新对内部的属性监听不到
       observer(newValue)
       // 设置新值 ，vulue因为一直在闭包中，所以set之后再get的值为新的value值
       value = newValue
       // 触发视图更新
       updateView()
     } 
   }
 })
}

// 重新定义数组原型，用来监听数组操作
const oldArrayProperty = Array.prototype
// 创建新对象，原型指向oldArrayProperty，再扩展新方法不会影响原型
const arrProto = Object.create(oldArrayProperty);
// 监控使用以下方法操作数组
['push', 'pop', 'shift', 'unshift', 'splice'].forEach(methodName => {
  arrProto[methodName] = function () {
    updateView() // 更新视图
    oldArrayProperty[methodName].call(this, ...arguments) // 执行相应方法
  }
})

// 监听对象属性
function observer(target) {
  if(typeof target !== 'object' || target === null) {
    // 不是数组或者对象就不监听了
    return target
  }

  if(Array.isArray(target)) {
    // 如果是数组则修改原型用于监听某些操作
    target.__proto__ = arrProto
  }

  for(let key in target) {
    // 如果是对象则传入递归监听，并设置setter/getter
    defineReactive(target, key, target[key])
  }
}


// 准备数据
const data = {
  name: 'wtw',
  age: 20,
  info: {
    address: '北京'
  },
  nums: [10,20,30]
}

// 对data进行监听
observer(data)

//测试
data.name = 'world'
console.log('name:' + data.name)
data.age = 30
console.log('age: ' + data.age)
data.info.address = '上海' // 深度监听
console.log('info.address: ' + data.info.address)
data.nums.push(40) // 监听数组，数组监听需要重定义原型来实现
console.log('nums: ' + data.nums)

/*
视图更新
name:world

视图更新
age: 30

视图更新
info.address: 上海

视图更新
nums: 10,20,30,40
*/
```



**缺点**

1）Object.defineProperty 深度监听的计算消耗大，需要一次性递归到最后一层对象，一次性计算量大。Proxy是分多次递归，用到这个属性时才去递归到那里。

2）数组的方法也没办法被直接监听，需要重写数组原型，对原型上的方法特殊处理才能监听一些数组操作。

3 ）直接`data.xxx = `增加属性或者`delete data.xxx`并不能被直接监听到，vue中需要使用Vue.set和Vue.delete来操作。



## 发布订阅模式

更新的第二步就是数据变化后Vue如何知道通知那些元素数据更新了需要更新界面

发布者订阅者设计模式

```javascript
class Dep{
    constructor(){
        this.subs=[]//订阅者数组
    },
    addSub(wather){ // 将订阅者加入订阅数组
        this.subs.push(wather)
    },
    notify(){ // 通知订阅者数据更新了，调用自己的update方法更新
        this.subs.forEach(item=>{
            item.update()
        })
    }
}

class Watcher{
    constructor(elementName){
        this.elementName = elementName
    }
    updata(){// 订阅者主动进行数据更新
        console.log(this.elementName +'组件要update')
        // 在这里更新组件的dom
    }
}

const dep = new Dep()

// 对三个元素分别进行订阅
const watcher1 = new Watcher('元素1')
dep.addSub(watcher1)
const watcher2 = new Watcher('元素2')
dep.addSub(watcher2)
const watcher3 = new Watcher('元素3')
dep.addSub(watcher3)
```

然后在数据劫持的更新视图函数里里调用dep的notify()方法

```javascript
function updateView() { // 更新视图的函数
  console.log('视图更新')
  dep.notify() // 通知组件数据更新
}
```





# virtual DOM

虚拟dom时将dom元素用js对象的形式表现出来方便进行diff算法比对

```html
<div id="wrapper" class="box">
  <span>wtw</span>
  wy
</div>
```

```javascript
// 以上html转化为vnode
{
  type : 'div',
  props : { id:"wrapper", className:'box' }, // class是关键字要使用className
  children:[{
    type:'span',
    children:[{
      type:'',
      props:'',
      children:[],
      text:'wtw
    }]
  },
  {
    type:'',
    props:'',
    children:[],
    text:'wy'
  }]
}
```



ps：snabbdom是一个简洁的虚拟dom库，vue参考它实现了虚拟dom和diff算法

SnabbDOM这种库的vnode对象最主要的结构如下：

一般text和children不兼容，文本会变成一个vnode放进children中

```javascript
{
	sel: string,元素选择器，就是tag
	data: any,元素属性 ，就是props
	children: Array,元素子节点 
	text: string,元素文本 
	elm: Element,页面上对应dom元素 
	key: string,元素唯一标记key值
}
```





# diff算法

diff即比对，diff算法的体现例如v-for中的key，diff算法会对两颗树（dom树）做比对。

但树diff的时间复杂度o(n的三次方)，分别遍历两颗树再排序一次，这样的高复杂度基本是不可用的，vue的diff算法经过优化时间复杂度变为o(n)，为了降低时间复杂度vue和react的diff算法都是深度优先遍历

**优化过程**

- 只比较同一层级，不进行跨级比较
- 新旧节点tag不相同直接删掉重建，不再深度比对
- 新旧节点 tag和key两者都相同，则直接认为是相同节点不再往下深度比较

![diff](https://images2018.cnblogs.com/blog/998023/201805/998023-20180519212357826-1474719173.png)



## h函数

diff算法的入口是h函数，h函数接收1-3个参数，并`return vnode(sel，data，children，text，undefined)`，也就是返回vnode()函数生成的vnode

```js
h(string)
h(string, vnodeData)
h(string, vnodeChildren)
h(string, vnodeData, vnodeChildren) 
{
	...
	return return vnode(sel，data，children，text，undefined)
}
```



## vnode函数

vnode()函数接收五个参数sel，data，children，text，elm，然后vnode()函数会返回一个对象，也就是vnode虚拟node对象，vnode对象包含以下属性

```js
vnode(sel，data，children，text，elm) {
 ...
 return {
 	sel,
 	data,
 	children,
 	text,
 	elm,
 	key // 组件的唯一标记key
 }
}
```



## patch函数

patch可以传入两个参数，第一个参数可以是vnode元素，也可以是真实node元素，第二个参数为一个vnode

```js
patch(oldVnode,vnode) // 第一个参数可以为vnode，也可以为真实node
{
    let i, ele, parent
    ...
	if(!isVnode(oldNode)) // 判断是vnode还是node
	{
		// 如果不是vnode，就创建一个空的vnode，并关联到这个dom元素，但是vnode是空的
		// emptyNodeAt方法接收一个dom元素，调用vnode()方法，返回一个vnode对象
		oldVnode = emptyNodeAt(oldVnode)
	}
    
    // sameVnode函数根据key值和sel值，判断这个两个vnode是不是相同的vnode，两个属性同时相等返回true
    if(sameVnode(oldVnode, vnode)) {
        // 两个vnode相同的话，使用patchVnode进行深度对比
        patchVnode(oldVnode, vnode, insertedVnodeQueue)
    } else {
        // 不是同一个vnode，根据diff算法优化，就直接删掉销毁重建这个元素
        ele = oldVnode.elm
        parent= appi.parentNode(elm)
        // 用新的vnode重建一个元素
        createElm(vnode, insertedVnodeQueue)
    }
}
```



## patchVnode函数

对比两个vnode并进行更新

```js
patchVnode(oldVnode, vnode, insertedVnodeQueue) {
	// 设置vnode.elem，新的vnode需要知道oldVnode在页面哪个元素上，然后覆盖
	// 首先要将在页面上那个元素提取出来
	const elem = vnode.elm = oldVnode.elm!
    
    // 旧children
    let oldCh = oldVnode.children as VNode[]
    // 新children
    let ch = vnode.children as VNode[]
    
    if(oldVnode === vnode) return // 如果两者相等直接返回，但新旧vnode基本上不可能相等
    
    // 以下是比对vnode直接的text，chidren的差异并调用api更新
    // isUndef是判断是否是undefined
    if(isUndef(vnode.text)) { 
        // 判断新vnode的text是不是undefined
        // 由于text和children一般不共存，有text就没有children，所以可以判断vnode是否含有children 
        
        if(ifDef(oldCh)&&isDef(ch)) { 
            // 进入这里说明，vnode text为undefined（说明不是文本节点），且新旧vnode都有children
            if(oldCh !== ch) {
                // 新旧vnode都有children，则需要对比两个children
                // updateChildren是diff算法最繁琐的函数
                updateChildren(elm, oldCh, ch, insertedVnodeQueue)
            }
     
        } else if(isDef(ch)) {
            // 旧children无（说明旧text有），新children有（说明新text无）
            // 更新第一步：清空text
            if(isDef(oldVnode.text)) {
                api.setTextContent(elm, '')
            }
            // 更新第二步：添加children
            addVnodes(elm, null, ch, 0, ch.length-1, insertedVnodeQueue)
            
        }else if(idDef(oldCh)) {
            // 旧children有，新children无
            // 移除所有旧children内的所有dom元素和text元素
            removeVnodes(elm, oldCh, 0, ch.length-1)
            
        } else if(isDef(oldVnode.text)) {
            // 旧text有，但新text为undefined
            // 更新：清空text
            api.setTextContext(elm, '')
        }

    } else if(oldVnode.text !== vnode.text) {
        // text不是undefined，说明是新vnode文本节点（说明vnode的children没有值）且新旧text不同
     
        // 更新第一步：先移除旧children，理由是新的vnode的children没有值
        if(isDef(oldCh)) {
            removeVnodes(elm, oldCh, 0, oldCh.length-1)
        }
        // 更新第二步：设置新text，理由是新旧text不同
        api.setTextContext(elm, vnode.text)
    }
}
```



## updateChildren函数

updateChildren函数定义了oldCh和newCh两个列表，并使用oldStartIndex和oldEndIndex两个指针指向oldCh的头和尾，用newStartIndex和newEndIndex两个指针指向newCh的头和尾。

然后执行循环，startIndex累加，endIndex累减，当oldCh和newCh两个列表的头尾指针都相撞时结束循环

```js
// 以下是snabbdom的对比方式，vue3.0中已经更改了对比方式，react中也不一样
while(oldStartIndex <= oldEndIndex && newStartIndex <= newEndIndex) {
 ... 
 // 循环体内分别进行对比
 } else if(sameVnode(oldStartVnode, newStartVnode)) {
 	// start和start对比
    // 调用patchVnode对两个结点进行对比
    patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue)
    // 指针移动
    oldStartVnode = oldCh[++oldStartIndex]
    newStartVnode = newCh[++newStartIndex]
     
 } else if(sameVnode(oldEndVnode, newEndVnode)) {
    // end和end对比
    patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue)
    oldEndVnode = oldCh[--oldEndIndex]
    newEndVnode = newCh[--newEndIndex]
     
 } else if(sameVnode(oldStartVnode, newEndVnode)) {
    // start和end对比
    patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue)
    oldStartVnode = oldCh[++oldStartIndex]
    newEndVnode = newCh[--newEndIndex]
     
 } else if(sameVnode(oldEndVnode, newStartVnode)) {
    // end和start对比
     patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue)
     oldEndVnode = oldCh[--oldEndIndex]
     newStartVnode = newCh[++newStartIndex]
     
 } else {
  	// 以上都没命中
    // 先循环比对所有结点的key
     if(key值对应上) {
         if(sel值相等) {
             // key相等sel相等，直接patchVnode比对节点，比对后直接移动该节点即可，无需销毁重建的过程
             patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue)
         } else {
             // key相等，sel不相等，当做新节点，new element然后在对应位置直接插入
         }
     } else{
         // key没有对应上，说明这个节点是新节点
         // new element然后在对应位置直接插入
     }
 }
}
```





# 模板编译原理

模板时vue开发中最常用的部分，模板类似html但不是html，它有指令，插值，表达式。html只是标记语言，只有js这样的图灵完备语言才能执行判断和循环等操作。

因此模板肯定是将类html代码转化为了某种js代码来完成这些操作。这样的过程叫模板编译。模板编译为render函数，执行render函数返回vnode，基于vnode在执行patch和diff。



## with沙箱

JavaScript有with语法，用于制造沙箱环境（常见的沙箱还要Proxy）

```javascript
const obj = { a: 100, b: 200 }
console.log(obj.a, obj.b) // 100 200
console.log(obj.c) // undefined
```

```javascript
const obj = { a: 100, b: 200 }
with(obj) {
  console.log(a, b) // 100 200
  console.log(c) // 报错
}
```

with的作用是将`with(obj){}`作用域内的自由变量（例如变量a，原本是obj.a）当做obj属性来查找，如果obj中找不到对应匹配的属性不会返回undefined而是报错。with语法要慎用，他打破了原本的作用域规则，代码阅读性变差



## vue-template-compiler

vue-template-compiler模块可用于将 Vue 2.0 模板预编译为渲染函数（template => ast => render）**，以避免运行时编译开销和 CSP 限制。**大都数场景下，与 vue-loader一起使用，只有在编写具有非常特定需求的构建工具时，才需要单独使用它。

`vue-template-compiler` 的代码是**从 vue 源码中抽离的**！对比一下 `vue-template-compiler` 和 vue 关于编译的 API。发现对于 compile 等函数是一致，只是 `vue-template-compiler` 开放的参数和方法更多。因此，**vue 和 vue-template-compiler 的版本必须一致（同一份源码）！**



我们可以使用它将模板语法编译为js代码

```
npm i vue-template-compiler --save
```

```javascript
const compiler = require('vue-template-compiler')
const template = `<p>{{message}}</p>`

// 编译
const res = compiler.compile(template)
console.log(res.render)
// with(this){return _c('p',[_v(_s(message))])}
```

打印结果with中的this是指vue实例（new Vue()）,使用with语法后内部代码的自由变量都会去查找this的属性。例如message就是vue实例中的message变量

**vue源码中缩写**

`_c`是createElement函数（也就是h函数）的缩写

`_v`是createTextVNode函数的缩写

`_s`是toString的缩写

`_l`是renderList的缩写，用于渲染列表

```
_c('p',[_v(_s(message))])
翻译过来就是
createElement('P', createTextVNode(toString(message)))
```



## v-bind

v-bind语法绑定属性则属性值编译为一个变量`"src":imgUrl`，而不是一个字符串

class属性因为是一个关键字所以编译为staticClass（虚拟dom中叫className）

```javascript
const compiler = require('vue-template-compiler')
const template = `
	<div id="div1" class="container">
		<img :src="imgUrl"/>
	</div>
`
// 编译
const res = compiler.compile(template)
console.log(res.render)
// with(this){return _c('div',{staticClass:"container",attrs:{"id":"div1"}},[_c('img',{attrs:{"src":imgUrl}})])}
```



## v-if/v-else

v-if、v-else则是根据判断条件编译为三元表达式

```javascript
const compiler = require('vue-template-compiler')
const template = `
  <div>
    <p v-if="show">1</p>
    <p v-else>2</p>
  </div>
`
// 编译
const res = compiler.compile(template)
console.log(res.render)
// with(this){return _c('div',[(show)?_c('p',[_v("1")]):_c('p',[_v("2")])])}
```



## v-for

v-for是根据`_l`，renderList函数来循环遍历list并渲染元素

```javascript
const compiler = require('vue-template-compiler')
const template = `
	<div>
    	<div v-for='(item,index) in list' :key='item.id'>{{item.title}}</div>
	</div>
`
// 编译
const res = compiler.compile(template)
// with(this){return _c('div',_l((list),function(item,index){return _c('div',{key:item.id},[_v(_s(item.title))])}),0)}
console.log(res.render)
```



## 事件

```javascript
const compiler = require('vue-template-compiler')
const template = `
    <div @click='handleClick'>btn</div>
`
// 编译
const res = compiler.compile(template)
console.log(res.render)
// with(this){return _c('div',{on:{"click":handleClick}},[_v("btn")])}
```



## v-model

v-model主要是v-bind绑定value=name属性，来让input显示name的内容，依靠input事件，然后执行name = $event.target.value来更新name属性

```javascript
const compiler = require('vue-template-compiler')
const template = `
	<input type='text' v-mode='name'></input>
`
// 编译
const res = compiler.compile(template)
console.log(res.render)
// with(this){return _c('input',{directives:[{name:"mode",rawName:"v-mode",value:(name),expression:"name"}],attrs:{"type":"text"}})}
```





# render函数

使用render函数代替template，可以省略模板编译为ast的过程，提高性能，直接使用对应的render函数生成虚拟dom。

```
template --> AST语法树 --> render --> virtual dom --> 真实dom(UI)
```

```javascript
const cpn = {
    template:'<div>{{message}}</div>'
    data(){
        return {
            message:'我是组件cpn'
        }
    }
}
//注册一个组件
new Vue({
  el: '#app',
  //普通注册组件
  // components: { App },
  // template: '<App/>'

  // 使用render函数注册组件
  render: function(createElement){
      return createElement(cpn) //传入组件对象
    }
})
```





# 组件渲染/更新流程

一个组件渲染页面，修改data触发更新（数据驱动视图）的流程

- 初次渲染流程

  1.  解析模板为ast（vue-loader在开发环境打包时就完成了解析模板，客户端直接拿到的就是render函数）

  2.  触发响应式，监听data的getter/setter，只给和视图有关的data绑定getter/setter，因为setter操作会引起更新流程执行render重绘视图，和视图无关的数据不必更新视图浪费性能
  3.  执行render函数，生成虚拟dom树并patch渲染成真实dom



- 更新流程
  1.  修改data，触发setter
  2.   重新执行render函数，生成新的虚拟dom树
  3.   diff算法比对两个树，patch(vnode, newVnode)，进行更新



- 组件异步渲染与$nextTick()

  vue是一个异步渲染的框架，为了尽量减少dom操作次数，所以在一个函数内当data改变后，dom不会立刻去渲染，他会将延迟等待多次修改data操作合并成一次再去执行，所以我们不能在data改变后在同一个函数内立刻去获取dom元素并操作dom元素，如果一定要操作dom需要使用$nextTick()，他会延迟我们写的处理方法的执行，等待dom更新完成再去执行。





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





# 为何data是一个函数

为了方便复用，因为写的vue组件相当于一个class类，每次用相当于创建一个实例，如果直接写成一个对象，相当于一个静态成员变量，所有的实例vue组件共享了。所以写成一个函数，每次初始化组件都返回一个新对象，这样所有实例都有自己单独的data。



# ajax放在哪个生命周期

ajax放进mounted，js单线程，ajax是异步获取数据会放在最后，所以放在mounted之前没用，但需要注意的是服务端渲染时不支持mounted，需要放到created中。



# 何时使用异步组件

加载大组件和路由异步加载的时候



# 何时使用keep-alive

缓存组件不需要重复渲染，优化性能，例如tap页切换



# 何时使用beforeDestory

解绑自定义事件event.$off

清除定时器

解除自定义的dom事件，例如window.scroll等



# vue常见性能优化

- 合理使用v-show/v-if
- 合理使用computed
- v-for加key，以及避免和v-if一起使用，因为v-for优先级更高，每次都会先计算v-for再去v-if造成性能浪费
- 自定义事件，dom事件及时销毁
- 合理使用异步组件
- 合理使用keep-alive
- data层级不要太深，因为Object.defineProperty()数据劫持时会一次性遍历到最后一层，可能造成性能过低
- 使用vue-loader在开发环境做模板编译（预编译）
- webpack层面的优化
- 前端通过性能优化，例如图片懒加载
- 使用SSR





# vue3

- 全部使用ts重写
- 性能提升，代码量减少
- 会调整部分api
- 使用Proxy重写响应式



# Proxy

proxy除了get，set还可以删除，并且可以监听到新增属性（无需调用Vue.set了）

proxy代理拦截器中getter/setter的参数：target就是传入的对象data，key就是属性名（数组是下标），val是设置的新值，receiver是new出来的代理拦截器实例proxyData。

代理一个对象后，对对象的操作就变为对代理器进行操作。

Reflect是配合Proxy实现监听的api，通过Reflect的get，set，deleteProperty方法帮助proxy进行拦截，Reflect方法参数和proxy的参数一样。其中Proxy用来代理，Reflect用来反射。

```javascript
const data = {
	name: 'wtw',
	age: 18
}

const proxyData = new Proxy(data, {
	get(target, key, receiver) {
    	const result = Reflect.get(target, key, receiver)
        console.log('get: ', key)
        return result // 返回结果
    },
    set(target, key, val, receiver) {
        const result = Reflect.set(target, key, val, receiver)
        console.log('set: ', key,val)
        return result // 返回布尔值，是否set成功
    },
    deleteProperty(target, key) { // 拦截delete关键字
    	const result = Reflect.deleteProperty(target, key)
        console.log('delete property', key)
        return result // 返回布尔值，是否delete成功
	}
})

// 使用proxy后，应该对代理器进行操作。
console.log(proxyData.age) 
// get: age
// 18
proxyData.age = 30
// set: age 30
delete proxyData.age
// delete property age
```



## 监听数组

**并且proxy可以原生实现监听数组**

```javascript
const data = ['a', 'b', 'c']

const proxyData = new Proxy(data, {
	get(target, key, receiver) {
    	const result = Reflect.get(target, key, receiver)
        console.log('get: ', key)
        return result // 返回结果
    },
    set(target, key, val, receiver) {
        const result = Reflect.set(target, key, val, receiver)
        console.log('set: ', key, val)
        return result // 返回布尔值，是否set成功
    },
    deleteProperty(target, key) { // 拦截delete关键字
    	const result = Reflect.deleteProperty(target, key)
        console.log('delete property', key)
        return result // 返回布尔值，是否delete成功
	}
})

proxyData.push('d')
/*
get:  push
get:  length
set:  3 d
set:  length 4
*/
```

调用push方法时，会调用getter，先访问数组的push和length两个属性，然后调用setter，将下标为3的值赋值为d，再调用setter，将数组的length属性+1



**但我们使用数组原型属性push没必要执行getter中的副作用（但是还是要执行返回，只是我们附加的副作用没必要执行），最后也没必要再set一次length，所以对数组可以进行一些处理，使用原型属性不唤醒getter，最后也不用在set一次length**

`Reflect.ownKeys()`这个api可以只遍历出在对象和数组本身的属性（类似于in关键字），忽略原型上的属性，并集合成一个数组，`Object.getOwnPropertyNames(obj)`也能实现同样的效果

```
Reflect.ownKeys([10,20,30])
// ['0','1','2','length']
数组中只有下标和length是自己的，其他属性在原型上

Reflect.ownKeys({a: 1, b: 2})
// ['a', 'b']
```



可以使用这个api来改造getter，然后在setter中判断，如果修改前后值相同就不执行副作用，直接返回true

```javascript
const data = ['a', 'b', 'c']

const proxyData = new Proxy(data, {
	get(target, key, receiver) {
        // 只处理对象或者数组本身的属性
        const ownKeys = Reflect.ownKeys(target)
        if(ownKeys.includes(key)) {
            console.log('get: ', key) // 副作用
        }
    	const result = Reflect.get(target, key, receiver)
        return result // 返回结果
    },
    set(target, key, val, receiver) {
        // 前后属性值相同直接返回true
        const oldVal= target[key]
        if(oldVal === val) return
        
        const result = Reflect.set(target, key, val, receiver)
        console.log('set: ', key, val)
        return result // 返回布尔值，是否set成功
    },
    deleteProperty(target, key) { // 拦截delete关键字
    	const result = Reflect.deleteProperty(target, key)
        console.log('delete property', key)
        return result // 返回布尔值，是否delete成功
	}
})

proxyData.push('d')
/*
get:  length
set:  3 d
*/
```

**这样设置对监听对象也有效**



## Reflect的作用

- 和Proxy作用一一对应，api和参数完全相同
- 标准化，规范化，函数式（函数式编程，减少关键字），例如deleteProperty中如果没有Reflect.deleteProperty我们就需要手动使用delete关键字来删除属性。
- 提供工具函数，例如`Reflect.ownKeys()`





# Proxy实现响应式

还需要解决两个问题

- 如何深度监听

  在getter返回时递归循环深度监听

- 如何判断一个属性时新增的还是原有的

  在setter中Reflect.ownKeys()判断一下

```javascript
// 创建响应式
function reactive(target = {}) {
	if(typeof target !== 'object' || target == null) {
			// 不是对象或数组则直接返回
			return target
	}
	
	// proxy代理配置
	const proxyConf = {
			get(target, key, receiver) {
				// 只处理对象或者数组本身的属性
				const ownKeys = Reflect.ownKeys(target)
				if(ownKeys.includes(key)) {
						console.log('get: ', key) // 副作用
				}
					
			const result = Reflect.get(target, key, receiver)
				return reactive(result) // 循环深度监听
		},
		 set(target, key, val, receiver) {
				// 前后属性值相同直接返回true
				const oldVal= target[key]
				if(oldVal === val) return
				
					// 判断时新增属性还是修改属性
					const ownKeys = Reflect.ownKeys(target)
					if(ownKeys.includes(key)) {
							console.log('已有的属性进行修改', key)
					} else {
						console.log('新增属性', key)
					}
					
				const result = Reflect.set(target, key, val, receiver)
				console.log('set: ', key, val)
				return result // 返回布尔值，是否set成功
		},
		deleteProperty(target, key) { // 拦截delete关键字
			const result = Reflect.deleteProperty(target, key)
				console.log('delete property', key)
				return result // 返回布尔值，是否delete成功
	}
	}
	
	// 生成proxy代理
	const observed = new Proxy(target, proxyConf)
	return observed
}


// 准备数据
const data = {
name: 'wtw',
age: 20,
info: {
	address: '北京'
},
nums: [10,20,30]
}

const proxyData = reactive(data)
console.log(proxyData.info.address)
/* 实现深度监听
get:  info
get:  address
北京
*/
```



Proxy不是一次性监听完对象所有层级，只有使用到该层的时候才去递归监听到该层，而Object.defineProperty则是初始时就一次性递归到最深以层，因此性能更高





# Proxy优缺点

- 深度监听性能更好

- 可监听 新增和删除操作
- 可原生实现监听数组

但是也有一些缺点

- Proxy无法兼容全部浏览器，无法polyfill