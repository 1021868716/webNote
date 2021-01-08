# Vue

vue渲染流程：

template --> AST语法树 --> render --> virtual dom --> 真实dom(UI)

![vue运行机制](https://img2018.cnblogs.com/blog/1449188/201906/1449188-20190630234249553-1929355900.png)



## 引入Vue

1. CDN引入
2. 下载引入
3. NPM引入





# MVVM

**MVVM的思想是我们不用直接去操纵dom，使用先绑定dom与数据，然后以修改数据的方式更改dom的内容**



View: DOM

Model: 抽离出来的Obj

ViewModel: 创建的Vue对象实例

- ViewModel通过Data Binding让obj中的数据实时的在DOM中显示
- ViewModel通过DOM Listener来监听DOM事件，并通过methods中的操作来改变obj中的数据

![vue-mvvm](http://f2ex.cn/wp-content/uploads/2017/07/what-is-vue-content1.png)



## MVVM与MVP的不同

MVVM用ViewModel层代替了P层，我们不需要关注ViewModel层是怎么操纵dom的，这是vue内置的。我们关注的重点变为了Model层和View层。

MVP设计模式是面向dom进行开发，重点是怎么去修改dom，MVVM是面向数据进行开发，极大减少了dom操作的代码。



## MVC/MVP

![](https://img-blog.csdn.net/20180322204213875)

- MVC

MVC 模式代表 Model-View-Controller（模型-视图-控制器） 模式。这种模式用于应用程序的分层开发。View层是界面，Model层是业务逻辑，Controller层用来调度View层和Model层。

MVC和MVP区别在于代码逻辑有没有写在View中的，有就是MVC，没有就是MVP，MVC的核心是在View层，在View层可能会进行任何操作（例如JSP等，View层和逻辑代码是耦合的）。



- MVP

MVP是MVC的改进，View层有了分离，逻辑放在P层，View只关注页面展示，不会写逻辑代码。

例如jQuery就属于MVP设计模式，，都是直接在dom上进行修改数据，然后dom树更新展示页面。

Model层，模型层，请求和控制数据的一层，比如AJAX

Presenter层，控制器，逻辑层，我们写的代码就是这一层，MVP设计模式中我们先在P层操作M层请求数据，然后用大量代码操作View层更新dom，更新视图。

View层，视图层，dom就是这一层













# 虚拟dom

vue在浏览器渲染真实dom之前创建虚拟dom然后再进行渲染：

**vue创建**  -->  **虚拟dompatch**-->  浏览器dom

如果有相同元素会直接把虚拟dom之前创建的同名元素复用替换过去，复用时，元素属性互斥部分会修改，不互斥部分会直接复制，可以给元素加入key属性，如果key属性值相同会进行复用，key不同会重新创建元素，不复用。

```html
<div id="add">
  <span v-if="isUser">用户账户:
    <input type="text" id="username" key="username">
  </span>
  <span v-else>用户邮箱:
    <input type="text" id="email" key="email">
  </span>
  <button @click="isUser=!isUser;">切换</button>
</div> 
    
<!--不加key属性，在username内输入值再点切换，值会被复制在email内-->
    
<script src="../vue.js"></script>
<script>
  const add = new Vue({
    el:'#add',
    data:{
      isUser: true
    },
    methods:{}
  })
</script>
```





# Vue实例对象

Vue实例会接管dom操作，vue实例会对接管的dom元素进行分析和更改。

Vue是声明式编程，必须创建Vue对象并挂载在相应元素，Vue数据是响应式的，会监听数据变化实时更新虚拟dom并来实时更新dom树

```javascript
const app = new Vue(options)
```

创建Vue实例时传入了一个对象options，options具有以下常用属性

​	el：类型是string | HTMLElement，作用是挂载元素，决定管理那个DOM

​	data：类型是Object | Function，作用是Vue实例对应的数据对象

​	methods：Function，作用是为Vue添加方法

​	computed：Function，添加计算属性方法，使用时当成属性使用，无需加()

​	filters：Function，过滤器，让数据以正确的方式显示

​	components: 注册局部组件

​	watch: 绑定监听的数据变化后的回调

​	生命周期函数: 作用是运行到该生命周期后再执行该函数






# Vue的生命周期

```javascript
  new Vue()
	  |
      |
阶段一：初始化
  beforeCreate()
      |
      | ---> 实现数据代理，进行双向绑定等
      |
  created() ---> 可以访问data数据
      |       注意created()阶段拿不到dom中的元素
      |       在挂载el之后的mounted()阶段才能拿到dom元素
      |
      | ---> 在内存中编译模板
      |
  beforeMount()
      |
      | ---> 挂载el
      |
  mounted() --->在此可以访问模板中的标签对象（dom元素），或者执行初始异步任务,ajax请求和异步任务。
      |    	  SSR时不支持mounted，这部分代码需要放到created中。
      |
      | ---> 数据发生更新 this.xxx = xxx，进入更新阶段
      |
阶段二：更新
  beforeUpdate()
      |
      | ---> 更新界面
      |
  Updated()
      |
      | --->  执行vueObj.$destroy销毁vue组件，进入死亡阶段
      |
阶段三：死亡
  beforeDestroy() ---> 收尾工作清除定时器，解绑总线事件等
      |
      |
      |
  destroyed()
```





# Vue基本语法

## 插值操作Mustache

Mustache(胡须，胡子)，语法双大括号内可以为data的key也可以为表达式，Mustache语法使用在Vue标签内容里。Mustache 语法不能作用在 HTML attribute（自定义属性） 上，自定义属性应该使用 `v-bind` 指令添加。

```html
<div id="add">
  <h2>hello,{{message}}</h2>
  <!-- hello,Vue -->
  <h2>{{firstName+' '+lastName}}</h2>
  <!-- w tw -->
  <h2>{{count*2}}</h2>
  <!-- 200 -->
</div>
<script src="../vue.js"></script>
<script>
  const add = new Vue({
    //el属性：将Vue对象挂载在元素#app上
    el:'#add',
    //data属性：设置挂载元素的属性
    data:{
      message:"Vue",
      firstName:"w",
      lastName:"tw",
      count:100
    }
  })
</script>
```



## 外部访问vue实例

`vueObj.$data`，外部通过这个vue的实例属性来访问vue实例的data数据

```html
<div id="app">{{content}}</div>
<script src="vue.js"></script>
<script>
  var app = new Vue({
    el:"#app",
    data:{
      content:"hello,world"
    }
  })
        
  setTimeout(function(){
    app.$data.content = "wtw"
  },2000)
  //因为data数据双向绑定，所以两秒后页面会发生更改
</script>
```



## 销毁vue实例

```
vueobj.$destroy()
```

这个api是销毁一个vue实例





# 双向绑定

Vue数据是响应式的，会监听数据变化更新虚拟dom来实时更新dom，但通过点操作增加对象属性，delete删除对象属性或者下标直接操作数组都不会被vue监控。

vue对数组方法的进行了重写，实现了数组方法的响应式操作，以下数组的方法来修改数据会被vue监控到

- 直接改变数组引用，指向新数组

- .push()  添加元素到数组最后

- .pop()   删除数组最后一个元素

- .shift() 删除数组第一个元素

- .unshift() 在数组最前面添加元素

- .splice() 

- .sort() 数组排序

- .reverse() 数组反转

- Vue.set()/VueObj.$set()，Vue内部实现的用于修改数组/对象的方法，用于解决增删对象属性和下标操作数组vue监控不到的问题：`Vue.set(数组/对象, 数组索引/对象属性名, 修改的值)`

   `Vue.set(this.letters, 2, 'g');`



**非响应式操作**：**通过下标操作数组是不会双向绑定的**，数据变化但界面不变化，因为vue不监听这种数据变化
通过下标修改数组 
如在浏览器调试栏中输入  `app.letters[0]='wtw'`，数组数据会发生变化但是界面不会出现变化

```html
<div id="app">
<li v-for="(letter,index) in letters" :key="item">
  {{index+1}}.{{letter}}
</li>
<button @click="btnclick">button</button>
</div>
<script src="../vue.js"></script>
<script>
const app = new Vue({
  el:'#app',
  data:{
    letters:["A","B","C","D"]
  },
  methods:{
    btnclick(){
      this.letters.pop();
      this.letters.shift();
      this.letters.push('F','G','H');
      this.letters.unshift('1','2','3');
      this.letters.splice(2,0,"F");
      this.letters[0]="wang";//非响应式操作
      Vue.set(this.letters,2,'vue.set');
    }
  }
})
app.letters[0]='tai';
</script>        
```





# Vue.set()/delete()

如果在实例创建之后添加新的属性到已经监控的data对象上，它不会触发视图更新。改变vue实例中引用类型的属性值，这样的修改会被vue监控到。

`Vue.set(obj,"属性名",属性值)`

`VueObj.$set(obj,"属性名",属性值)`



想要删除data中一个对象的属性，不能直接使用delete而是使用Vue.delete()

```
Vue.delete(obj,"属性名")
VueObj.$delete(obj,"属性名")
```





## 解决v-for不更新列表

**v-for遍历一个vue实例的data内的对象属性去渲染列表，如果打开网页页面生成列表后，在控制台或代码直接用点语法增加对象属性，已经渲染好的列表是不会直接增加列表项的**

使用以下两个方法可以在列表渲染好后新增列表改变页面

- 重新绑定一个增加了属性的新对象
- 使用Vue.set()/VueObj.$set()增加对象的属性可以在页面上新增列表项

```html
   <div id="app">
        <ul>
        <li v-for="(value,key) in info">{{value}}--{{key}}</li>
        </ul>
    </div>
    <!--
	页面打开渲染列表，此时name已经改为wy
    <li>wy--name</li>
    <li>20--age</li>
    <li>170--height</li>
    -->
    
    <script src="../vue.js"></script>
    <script>
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
    </script>
```





# vue指令

**指令添加到HTML的元素标签行内的属性上，让元素进行指定操作**



## ref引用

在vue中往往不需要我们操作dom，但很多时候，只使用数据的双向绑定不能解决问题，还是需要操作dom节点的时候，可以给元素标签添加一个ref属性，在vue实例中可以通过`this.$refs.`调取所有引用了的dom。

例如给一个普通HTML标签加上了`ref="abc"`属性，在vue实例中需要这个dom节点时，这个元素dom节点就可以通过`this.$refs.abc`表示。

给vue组件标签加上ref属性，使用`this.$refs.`调用时都会显示组件的JS引用对象（组件对象），而不是简单的dom节点，可以通过引用对象获取这个组件中的数据。

例如`this.$refs.abc.number`可以直接获取ref="abc"的组件中data内number的值。

在添加了v-for的标签上加ref，最后获取到的是一个数组，要通过`this.$refs.ref[index]`访问具体对象。



## key值

vue在操作dom的时候会尽量去复用当前dom树上已经存在的dom，被复用的时候这个dom元素内部的数据不会被清除（例如input里的value）

但有的时候我们不想这个元素被复用，就可以给元素加一个唯一key值属性，vue遍历到这个dom元素具有key值后发现复用元素会导致key值不一样的话，就不会去尝试复用这个元素了

v-if和v-else的两个input的key值不一样，vue不会尝试去复用

```html
<div id="app">
	<div v-if="show">
		用户名：<input key="username"/>
	</div>
    <div v-else>
		密码：<input key="password"/>
	</div>
    <!--如果不加这两个key，点击切换时上一个input会被复用，输入框的内容会被保留-->
    <button @click="isShow()">切换</button>
</div>
<script src="../vue.js"></script>
<script>
    const app = new Vue({
        el:'#app',
        data:{
            show:false
        },
        methods:{
            isShow(){
                this.show = !this.show
            }
        }
    })
</script>
```

在使用v-for循环渲染列表时，可以给每个列表通过 `:key`加上唯一key值提高性能，但key值不建议使用index，应该使用后端传过来的列表项的唯一id值，这样可以提高性能



## v-for指令

v-for主要用于批量列表渲染数组或对象,v-for循环产生的组件必须有唯一且不变的key属性，用于diff算法的updateChildren阶段，目的是在修改(插入，删除)v-for创建的元素时不需要修改的元素会直接复用，不进行比对，提高性能

### 遍历数组

    v-for="item in/of items" 遍历数组进行操作
    in和of的效果一模一样
    可以用 of 替代 in 作为分隔符，因为它更接近 JavaScript 迭代器的语法：for-of
    v-for="(item,index) in items" 可以得到item及其索引
```html

    
    <div id="app">
        <h1>Hello {{name}}</h1>
        <!--Hello Vue-->
        <li v-for="(movie,index) in movies" key="index">{{index+1}}.{{movie}}</li>
        <!-- v-for循环会自动创建标签并循环填入数组中的数据 -->
        <!--<li>1.哆啦A梦</li>-->
        <!--<li>2.大话西游</li>-->
        <!--<li>3.星际穿越</li>-->
        <!--<li>4.盗梦空间</li>-->
    </div>
    <script src="../vue.js"></script>
    <script>
        //创建一个Vue对象
        const app = new Vue({
            //el属性：将Vue对象挂载在元素#app上
            el:'#app',
            //data属性：设置挂载元素的属性
            data:{
                name:"Vue",
                movies:["哆啦A梦","大话西游","星际穿越","盗梦空间"]
            }
        })
        </script>
        <!--也可以代理添加
        const obj = {
            name:"Vue",
            movies:["哆啦A梦","大话西游","星际穿越","盗梦空间"]
        }
         const app = new Vue({
            //el属性：将Vue对象挂载在元素#app上
            el:'#app',
            //data属性：设置挂载元素的属性
            data:obj
        })*/
        -->
```


### 遍历对象

`v-for="item in/of obj"` 遍历进行操作
in和of的效果一模一样
可以用 of 替代 in 作为分隔符，因为它更接近 JavaScript 迭代器的语法：for-of
v-for="(item,index) in items" 可以得到item及其索引

```
遍历对象
v-for="value in obj" 
只获取一个值，只会获取到对象键值对中的值

v-for="(value,key) in obj" 
这样会获取完整的对象的键值对

v-for="(value,key,index) in obj"
这样会获取对象的键值对和索引
```



### key属性

在使用v-for时最好给对应的元素和组件加上key属性，目的是在updateChildren阶段修改(插入，删除)for创建的元素时不需要修改的元素会不进行比对，直接复用，提高性能，可以更高效的更新虚拟dom
key值要与元素一一对应，需要不变且唯一

```html


    <div id="app">
        <li v-for="(movie,index) in movies" :key="item">{{index+1}}.{{movie}}</li>
        <!--key与元素一一对应-->
        <!--<li>1.哆啦A梦</li>-->
        <!--<li>2.大话西游</li>-->
        <!--<li>3.赛尔号</li>-->
        <!--<li>4.星际穿越</li>-->
        <!--<li>5.盗梦空间</li>-->
    </div>
    <script src="../vue.js"></script>
    <script>
        const app = new Vue({
            el:'#app',
            data:{
                movies:["哆啦A梦","大话西游","星际穿越","盗梦空间"]
            }
        })
        
        app.movies.splice(2,0,"赛尔号");
        //在下标为2往后0个元素的位置插入一个元素
        //绑定key值的目的就是提高这步操作的性能
    </script>
```



## v-on指令

v-on="方法名"
添加了该指令的元素会被绑定相应事件

语法糖 v-on:click ---> @click

**行内绑定事件函数**

```html
<div id="app">
    <h1>计数器：{{number}}</h1>
    <button v-on:click="number++">+</button>
    <button v-on:click="number--">-</button>
</div>
<script src="../vue.js"></script>
<script>
    //创建一个Vue对象
    const app = new Vue({
        //el属性：将Vue对象挂载在元素#app上
        el:'#app',
        //data属性：设置挂载元素的属性
        data:{
            number:0
        }
    })
</script>
```

**在methods中绑定事件函数**

如果绑定事件函数时加了()就获取不到默认事件参数e，需要手动传入一个`$event`默认事件参数对象

如果绑定事件函数时没加()可以直接获取到默认事件参数e,

```html
    <div id="app">
​        <h1>计数器：{{number}}</h1>
​        <button v-on:click="add($event)">+</button>
		<!--加了括号，需要手动传入$event才能让函数获取默认事件参数对象e-->
​        <button v-on:click="sub">-</button>
    	<!--没加括号，函数可以直接获取默认事件参数对象e-->
​        <!--不需要e和其他参数方法后面的()可以省略-->
​        <!-- <button @click="add">+</button> -->
​        <!-- <button @click="sub">-</button> -->
​    </div>
​    <script src="../vue.js"></script>
​    <script>
​        //创建一个Vue对象
​        const app = new Vue({
​            //el属性：将Vue对象挂载在元素#app上
​            el:'#app',
​            //data属性：设置挂载元素的属性
​            data:{
​                number:0
​            },
​            methods:{
​                add:function(e){
    				//绑定时加了括号，函数需要手动传入e
​                    this.number++;
    				 console.log(e)
​                },
​                sub:function(e){
    				//绑定时没加括号，函数可以直接获取默认事件参数对象e
​                    this.number--;
    				 console.log(e)
​                }
​            }
​        })
​    </script>
```
### v-on的参数
**v-on绑定的方法如果没有参数,方法后面的()可以不添加也可以添加**

**如果方法需要形参但没有实参传入,则默认传入一个默认事件处理参数event**

**如果方法需要多个形参,但实参不足,不足的参数则为undefined,事件处理参数event不会传入**

**如果需要传入某个参数且同时需要event则可以通过$event传入事件处理参数**


```html
<div id="app">
    <button @click="btn1">btn1</button>
    <!-- 不需要参数的方法则()可以省略 -->
    <button @click="btn2('click')">btn2</button>
    <!-- 参数为"click" -->
    <button @click="btn21(abc)">btn2.1</button>
    <!-- 参数为data中的变量adc -->
    <button @click="btn3">btn3</button>
    <!-- 没有传入实参,但方法需要一个参数,则默认传入event,而不是变为undefined -->
    <button @click="btn4('abc',$event)">btn4</button>
    <!--需要event则可以通过$event传入事件处理参数-->
</div>

<script src="../vue.js"></script>
<script>
    const app = new Vue({
        el:'#app',
        data:{
            abc:123
        },
        methods:{
            btn1:function(){
                console.log("btn1click")
            },
            btn2:function(str) {
                console.log('btn2--->', str)
            },
            btn2:function(abc) {
                console.log('btn2.1--->',abc)
            },
            btn3(event){
                console.log('btn3--->', event)
            },
            btn4(str,event){
                console.log(str,"and", event)
            }
        }
    })
</script>
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
监听组件标签的原生事件

#### .once
只触发一次回调
例 ` @click.once="btn1"`
这个按键只有第一次点击触发事件

#### .capture

添加了后缀的事件不遵守冒泡规则，遵守捕获规则，即冒泡的时候不触发，捕获的时候触发

​    捕获：内部到外部

​	冒泡：外部到内部



## v-once指令

添加了v-once指令的对象页面会只显示第一次识别的{{}}内的数据,后续不再修改,未添加的元素中{{}}值进行修改后,页面也会立刻修改
    
```html
<div id="add">
    <h2>hello,{{message}}</h2>     <!-- hello,wtw -->
    <h2 v-once>hi,{{message}}</h2> <!-- hi,Vue -->
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
            //el属性：将Vue对象挂载在元素#app上
            el:'#add',
            //data属性：设置挂载元素的属性
            data:{
                message:"Vue"
            }
        })
    add.message = "wtw" //修改messgae的值
</script>
```



## v-html指令

**添加了该指令的标签会以html格式解析传入标签内，类似于innerHtml，该标签内原本内容会被覆盖**
    

```html
<div id="add">
    <h2>{{url}}</h2>     
    <!-- <a href="javascript:;">超链接</a> -->
    <h2 v-html="url">baidu</h2> 
    <!-- 超链接 -->
    <!-- 标签内原内容被覆盖 -->
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        //el属性：将Vue对象挂载在元素#app上
        el:'#add',
        //data属性：设置挂载元素的属性
        data:{
            url:'<a href="javascript:;">超链接</a>'
    }
    })
</script>
```

## v-text指令

**添加了该指令的标签会以文本格式解析传入标签内，类似于innerText，该标签内原本内容会被覆盖**

```javascript
<div id="add">
    <h2>hi,{{message}}</h2>
    <!-- hi,wtw -->
    <h2 v-text="message">,你好</h2>
    <!-- wtw -->
    <!-- 原内容被超链接覆盖 -->
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:'#add',
        data:{
            message:"wtw"
    }
    })
</script>
```

## v-pre指令

添加了该指令的标签不解析Vue语法

```javascript
<div id="add">
    <h2 v-pre>hi,{{message}}</h2>
    <!-- hi,{{message}} -->
    <h2 v-text="message">,你好</h2>
    <!-- wtw -->
    <!-- 原内容被超链接覆盖 -->
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        //el属性：将Vue对象挂载在元素#app上
        el:'#add',
        //data属性：设置挂载元素的属性
        data:{
            message:"wtw"
    }
    })
</script>
```

## v-cloak指令

cloak斗篷
添加了该指令的标签，在vue解析之前,该标签会存在一个class为v-cloak，在vue解析之前，该标签的v-cloak属性会消失。

```html
<style>
    [v-clock]{
        color : red;
        /*display : none;*/
    }
</style>
...
<div id="add"  v-cloak>
    <h2>hi,{{message}}</h2>
    <!-- 第一秒显示红色的 hi,{{message}} -->
    <!-- 一秒定时器结束后显示 hi,wtw -->
</div>
<script src="../vue.js"></script>
<script>
    setTimeout(function () {
      const add = new Vue({
        //el属性：将Vue对象挂载在元素#app上
        el:'#add',
        //data属性：设置挂载元素的属性
        data:{
            message:"wtw"
        }
      })
    },1000)
</script>
```

## v-bind指令动态绑定元素属性

v-bind指令基本作用：动态绑定属性
Mustache语法只能书写在标签内容里，无法书写在标签属性里，所以使用v-bind指令来将标签属性与vue对象属性动态绑定。
语法糖 v-bind: ---> :

```html
<div id="add">
    <img v-bind:src="imgURL" alt="">
    <a v-bind:href="aHref">baidu</a>
    <!-- 使用v-bind指令动态绑定属性 -->
    <!-- <img :src="imgURL" alt=""> -->
    <!-- <a :href="aHref">baidu</a> -->
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        //el属性：将Vue对象挂载在元素#app上
        el:'#add',
        //data属性：设置挂载元素的属性
        data:{
            imgURL:'img/1.jpg',
            aHref:'http://www.baidu.com'
        }
    })
</script>
```


​    

## v-bind指令动态绑定class样式名

`v-bind:class="对象/数组"`

对象写法：`v-bind:class="{class:bool}" `
对象中键是样式名,值是布尔值,为真则存在该样式

格式：` v-bind:class="{类名1:boolean,类名2:boolean...}"`
如果键值对过于复杂也可以保存为函数返回值进methods和computed中

数组写法：`v-bind:class="[变量1,变量2...]"`
数组写法也可以将样式名设置为变量名，但数组写法不如对象写法灵活，可以混合class和:class添加样式名。

```html
<style>
    .fonts{
        font-size:40px;
    }
    .active{
        color:red;
    }
    .line{
        background:blue;
    }
</style>
...
<div id="add">
    <h1 :class="'active'+' '+'line'">HELLO</h1>
    <!-- 直接绑定不方便移除样式和修改样式 -->
    
    <h2 class="fonts" :class="{active:isActive,line:isLine}">HI</h2>
    <!-- 同时绑定了active和line样式 -->
    
    <h3 class="fonts" :class="getclass()">HI</h3>
    <!-- 保存进methods,调用函数取返回值 结果与上相同-->
    <button @click="btnclick">btn</button>
    
    <!-- 数组写法 -->
    <h4 :class="[active,line]">你好</h4>
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
            //el属性：将Vue对象挂载在元素#app上
            el:'#add',
            //data属性：设置挂载元素的属性
            data: {
                active: "active",
                line: "line",
                isActive: true,
                isLine: true
            },
            methods:{
                btnclick: function(){
                    this.isActive = !this.isActive;
                    this.isLine = !this.isLine;
                },
                getclass:function(){
                    return {active:this.isActive,line:this.isLine};
                }
            }
    })
    //可以通过修改isActive/isLine属性来操作元素的样式
</script>
```

## v-bind指令动态绑定元素css属性

`v-bind:style="对象/数组"`
vue中css属性名推荐使用驼峰名写法，如果键值对太复杂同样可以抽取进methods中作为函数返回值调用
对象语法：`v-bind:style="{key(属性名):value(属性值)}"`

数组语法，数组语法较为繁琐,先将键值对封装成多个对象存在data中，元素行内调用时将这些对象再封装成数组

```html
    
​    <div id="add">
​        <div v-bind:style="{fontSize:'30px'}">行内直接书写</div>
​        <!-- value不加引号会被当做变量处理 -->
​    
​        <!-- 引用data数据动态绑定css属性-->
​        <div v-bind:style="{fontSize:finalSize+'px',color:finalColor}">
​            引用data数据
​        </div>
​        <div :style="getStyle()">抽取为函数返回值调用</div>
​    
​        <!--数组语法-->
​        <div v-bind:style="[baseStyle1,baseStyle2]">数组语法</div>
​    </div>
​    <script src="../vue.js"></script>
​    <script>
​        const add = new Vue({
​            el:"#add",
​            data:{
​            message : "hello",
​            finalColor: "red",
​            finalSize: 40,
​            baseStyle1:{backgroundColor:'blue'},
​            baseStyle2:{fontSize:'30px'}
​            },
​            methods:{
​                getStyle:function(){
​                    return {fontSize:this.finalSize+'px',color:this.finalColor};
​                }
​            }
​        })
​    </script>

```
## v-if指令和v-else指令
判断渲染条件，满足条件才渲染该元素，不满足条件又销毁这个元素
    v-if和v-else必须连续在一起使用，否则报错

```html
<div id="add">
    <h2 v-if="isShow">{{message}}</h2>
    <h1 v-else>isShow为false</h1>
    <!--isShow为真显示h2,为假显示h1-->
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:'#add',
        data:{
            message:""isShow为true"",
            isShow: true
            //通过控制isShow来控制元素的显示
        }
    })
</script>
```



## v-else-if指令

类似于else if的使用

```html
<div id="add">
    <!-- 复杂判断不建议在v-if中使用
    <h2 v-if="score>=90">优秀</h2>
    <h2 v-else-if="score>=80">良好</h2>
    <h2 v-else-if="score>=60">及格</h2>
    <h2 v-else-if="score<60">不及格</h2>
    -->
    <h2>{{result}}</h2><!--获取result函数返回值-->
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:'#add',
        data:{
            score:90
        },
        computed:{
        result(){
            let showMwssage=" ";
            if (this.score >= 90){
                showMessage = '优秀';
            }else if(this.score >= 60){
                showMessage = '及格';
            }else{
                showMessage = '不及格';
            }
            return showMessage;   
        }
        }
    })
</script>
```



## v-show

v-show决定一个元素是否在页面上显示，v-if决定一个元素是否渲染（在dom中存在与否）

**显示隐藏频率高时选择v-show，只有一次切换选择v-if**

```html
<div id="add">
  <span v-show="isShow">hello</span>
</div> 
<!--当v-show为false时，元素display为none-->
<!--切换后元素在dom中存在，只是行内样式display变为none-->
<script src="../vue.js"></script>
<script>
  const add = new Vue({
    el:'#add',
    data:{
      isShow: true
    },
    methods:{}
  })
</script>
```



# v-model

表单控件在开发中十分常用，Vue中使用v-model实现了表单元素和数据的双向绑定

 v-model可以应用于input元素，textarea元素，select元素，radio元素，checkbox元素



 **能实现元素行内上value属性和Vue实例内数据的双向绑定**

```html
v-model原理：第一步v-bind绑定一个value属性
             第二步v-on指令给当前元素绑定input事件，input事件在数据变化时立刻触发
            
    <input type="text" v-model="message">
    等同于
    <input type="text" :value="message" @input="message=$event.target.value">
```



**案例 v-model和input**

v-model绑定input框中的内容

```html
<div id="add">
    <input type="text" v-model="message"> 
    <!--message为input框中的内容绑定的变量-->
    <!--<input type="text" :value="message" @input="message=$event.target.value">-->
    <h2>{{message}}</h2>
</div>
```



**案例 v-model和textarea**

v-model绑定textarea框中的内容

```html
<textarea></textarea><!--错误写法，vue中textarea赋值用v-model-->
<textarea v-model="desc"></textarea>
```





**案例 v-model和cheakbox**

v-model绑定cheakbox是否被选中的bool值

```html
<div id="add">
<input type="checkbox" v-model="isAgree">同意协议 <!--isAgree为bool值-->
<h2>{{isAgree}}</h2>
</div>
```





案例 v-model和radio

v-model绑定sex变量的内容，内容与选项的文本一致

选男，sex变量就为男

```html
<div id="add">
<input type="radio" name="sex" value="男" v-model="sex">男
<input type="radio" name="sex" value="女" v-model="sex">女
    <h2>{{sex}}</h2>
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:'#add',
        data:{
            sex:''
        }
    })
</script>
```



**案例 v-model和select**
v-model绑定选择框选择的选项给变量

```html
<!-- 选中单个 -->
<div id="add">
    <select name="abc" v-model="fruit">
        <option value="苹果">苹果</option>
        <option value="香蕉">香蕉</option>
        <option value="芒果">芒果</option>
        <option value="梨子">梨子</option>
    </select>
    <h2>{{fruit}}</h2>
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:'#add',
        data:{
            fruit:"香蕉"
        }
    })
</script>
```

```html
<!-- 选中多个 -->
<!-- v-model绑定一个数组 -->
<div id="add">
    <select name="abc" v-model="fruits" multiple>
        <option value="苹果">苹果</option>
        <option value="香蕉">香蕉</option>
        <option value="芒果">芒果</option>
        <option value="梨子">梨子</option>
    </select>
    <h2>{{fruits}}</h2>
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:'#add',
        data:{
            fruits:["香蕉"]
        }
    })
</script>
```



## v-mode原理

```vue
<div id="add">
  <input type="text" v-model="message"> 
  <!--<input type="text" :value="message" @input="message=$event.target.value">-->
  <h2>{{message}}</h2>
</div>
```

v-model原理是，显示时通过v-bind将value属性绑定为message属性，然后监控input事件，每当改变内容就触发`message=$event.target.value"`，通过$event.target.value再更新message属性。





## 值绑定

动态通过服务器传过来的数组来动态生成选项，而不是直接在网页中固定

**v-bind和v-for结合使用**

```html
<div id="add">
<label v-for="item in originHobbies" :for="item">
<input type="checkbox" :value="item" :id="item" v-model="hobbies">
    {{item}}
</label>
<h2>{{hobbies}}</h2>
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:'#add',
        data:{
            hobbies:[],
            originHobbies:['篮球','羽毛球','足球','乒乓球']
        }
    })
</script>
```



## v-model修饰符

**.lazy**
避免频繁的实时更新数据，只有敲回车或者失去焦点时才会更新data

```html
<div id="add">
<label :for="item">
<input type="text" :id="item" v-model.lazy="message">
    <!--用户离开input后才会更新message数据-->
    <h2>{{message}}</h2>
</label>

</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:'#add',
        data:{
            message:''
        }
    })
</script>
```

**.number**
默认情况下v-model给data赋值时都是绑定的string类型，.number可以将类型转换为数字类型

```html
<div id="add">
<label :for="item">
    <input type="text" :id="item" v-model.number="age">
    <h2>{{age}}--{{typeof age}}</h2>
</label>

</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:'#add',
        data:{
            age:''
        }
    })
</script>
```

**.trim**
自动剥除字符串两端的空格

```html
<div id="add" for="mes">
<label>
    <input type="text" id="mes" v-model.trim="message">
    <h2>{{message}}</h2>
</label>
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:'#add',
        data:{
            message:''
        }
    })
</script>
```





# 组件化

我们可以开发一个个独立可复用的小组将来构建应用，最后所有组件会构成一颗组件树

每个组件都是一个vue实例



## 组件化模板

```html
<div id="app">
    <cpn></cpn>
</div>
<template id="tem">
	<div></div>
</template>
<script src="../vue.js"></script>
<script>
    const app = new Vue({
            el:'#app',
            data:{
                message:''
            },
            components:{
                cpn:{
                    template:'#tem'
                }
            }
        })
</script>
```



## 注册组件的基本步骤

- 1.调用`Vue.extend()`方法创建组件构造器（可以在component中构建）

  调用Vue.extend()创建的是一个组件构造器，通常在创建组件构造器时，传入tempalte代表自定义组件的模板
  ， tempalte内必须有一个根元素（如所有元素包裹在一个div里），该模板就是复用的html代码。

  

- 2.调用`Vue.component()`方法注册全局组件

  在Vue实例下的components属性下注册局部组件

  调用Vue.component()方法是将刚才的构造器注册为一个组件，并且为它注册一个组件标签名，所以需要传递两个参数Vue.component（注册组件的标签名,组件构造器），将组件注册在Vue类下。



- 3.在Vue实例的作用范围内使用组件标签（组件必须挂载在某个Vue实例下，否则不生效）
```vue
<!--vue组件实现代码复用-->
<div id="app">
    <!--步骤三：使用组件-->
    <mycpn></mycpn>
</div>

<!--Vue实例之外无法使用vue组件-->
<mycpn></mycpn>

<script src="../vue.js"></script>
<script>
    //步骤一：创建组件构造器
    //``为ES6模板字符串
    const myComponent = Vue.extend({
        template:`
        <div>
            <h2>组件标题</h2>
            <p>我是组件的一个段落内容</p>
        </div>
		`
    });

    //步骤二：注册全局组件，并且定义组件标签的名称
    Vue.component('mycpn',myComponent);
    
    const app = new Vue({
        el:'#app',
        data:{
        }
    })
</script>
```



## 全局组件和局部组件

全局组件可以在多个Vue实例下使用,而在某个Vue内部通过Vue的components属性来注册的组件叫局部组件，只能在该Vue的实例下使用
```vue
通过Vue的commponent属性注册局部组件
components:{
    标签名:组件构造器
}

<div id="app">
    <!--步骤三：使用组件-->
    <mycpn></mycpn>
</div>
<div id="app2">
    <!--#app注册的局部组件#app2无法使用-->
    <mycpn></mycpn>
</div>
<script src="../vue.js"></script>
<script>
    //步骤一：创建组件构造器
    const cpnC = Vue.extend({
        template:`
        <div>
            <h2>组件标题</h2>
            <p>我是组件的一个段落内容</p>
        </div>`
    });

    //步骤二：注册组件，并且定义组件标签的名称
    //Vue.component('my-cpn',myComponment);  注册全局组件
    const app = new Vue({
        el:'#app',
        data:{
            message:"Vue组件"
        },
        components:{
            mycpn:cpnC  //注册局部组件,只能在#app内使用
        }
    })

    const app2 = new Vue({
        el:'#app2',
        data:{
            message:"Vue组件"
        }
    })
</script>
```



## 注册组件的语法糖

语法糖省去了调用Vue.extend()这个步骤，用一个对象代替
但内部实现还是先Vue.extend()再Vue.component()

```html
<div id="app">
    <mycpn></mycpn><!--mycpn全局组件-->
    <mycpn2></mycpn2><!--mycpn2局部组件-->
</div>
<script src="../vue.js"></script>
<script>
    //普通注册组件步骤
    //构造器：const myComponent = Vue.extend(...});
    //注册：Vue.component('my-cpn',myComponent);

    //注册全局组件语法糖
    //语法糖写法可以省略extned
    //但内部实现还是先extend再component
    Vue.component('mycpn',{
        template:`
        <div>
            <h2>mycpn组件标题</h2>
            <p>我是组件的一个段落内容</p>
        </div>
		`
    });

    //在Vue内部注册局部组件
    //注册局部组件语法糖
    const app = new Vue({
        el:'#app',
        components:{
            mycpn2:{
                template:`
            <div>
                <h2>mycpn2组件标题</h2>
                <p>我是组件的一个段落内容</p>
            </div>
			`
            }
        }
    })
```



## 组件绑定原生事件

组件标签绑定原生事件不能直接v-on绑定，可以给组件模板中的根标签（div）上v-on绑定事件

想要在组件标签上v-on绑定原生事件，必须加v-on的修饰符`.native`

```html
<div>
    <child @click.native="handleClick"></child>
</div>
<!--child是定义的组件标签，直接@click原生事件是无法监控的，会默认认为是子组件传来的自定义事件，要加上修饰符才能监控原生事件-->
```



## 父组件和子组件

**组件构造器里也可以注册组件，在这个构造器里注册的组件叫子组件，这个构造器叫父组件（父组件还是要在Vue对象中注册，而Vue对象也可以看做一个root组件）。**

子组件只能在父组件模板中调用，子组件因为没有在Vue实例中注册也没有全局注册，所以无法单独使用，子组件也无法引用父组件和Vue实例的数据，如需跳过父组件独立使用，需要重新单独在Vue对象中注册或者全局注册

```html
组件构造器也有components属性，可以用来给注册子组件
子组件只能在这个父组件中被使用

<div id="app">
    <cpn2></cpn2>
    <!--cpn2为父组件，cpn1为子组件-->
    <cpn1></cpn1>
    <!--子组件没有单独注册，无法独立使用，标签失效-->
</div>
<script src="../vue.js"></script>
<script>
    const cpnC1 = Vue.extend({
        template:`
        <div>
            <h2>cpnC1子组件标题</h2>
            <p>我是组件的一个段落内容</p>
        </div>`
    });

    const cpnC2 = Vue.extend({
        template:`
        <div>
            <h2>cpn2父组件标题</h2>
            <p>我是组件的一个段落内容</p>
            <cpn1></cpn1>
        </div>`,
        //cpnC1在cpnC2中注册，cpnC1为子组件，父组件中可以调用子组件
        //编译器编译时在cpnC2中的子组件会直接被替换成cpnC1完整代码
        components:{
            cpn1:cpnC1
        }
    });

    const app = new Vue({
        el:'#app',
        data:{
            message:"Vue组件"
        },
        components:{
            cpn2:cpnC2
            // cpn1:cpnC1
            // 子组件可以重新单独注册
        }
    })
</script>
```



## 组件模板的分离

组件中添加模板时会在js文件里增加很多html代码，不利于代码阅读，Vue提供两种方案来在html文件中书写组件的template模板html代码

template标签内的内容不会出现在dom中

```html
-使用<script type="text/x-template">标签
给script标签添加一个id属性，在绑定模板时绑定'#id'

<div id="app">
    <cpn></cpn>
</div>
<script type="text/x-template" id="tem">
    <div>
        <h2>组件标题</h2>
        <p>我是组件的一个段落内容</p>
    </div>
</script>
<script src="../vue.js"></script>
<script>
    Vue.component('cpn',{
        template:'#tem'
    })
    const app = new Vue({
        el:'#app'
    })
</script>


-使用template标签
给template标签添加一个id属性，在绑定模板时绑定'#id'

<div id="app">
    <cpn></cpn>
</div>

<template id="tem">
    <div>
        <h2>组件标题</h2>
        <p>我是组件的一个段落内容</p>
    </div>
</template>
<script src="../vue.js"></script>
<script>
    Vue.component('cpn',{
        template:'#tem'
    })
    const app = new Vue({
        el:'#app'
    })
</script>
```



# 父子组件通信

业务开发中如果每个子组件都向服务器发送一次请求更新数据的话对服务器的压力很大，所以实际开发中不会让每个子组件都发送请求，而是直接让大组件（父组件）发送请求，然后将数据分发传递给小组件（子组件）。
真实开发中Vue实例（root组件）和子组件的通信和父子组件通信是一样的。

```
                     Pass props--->
    Parent(父组件)------------------child(子组件)
                   <---$emit Events
```
**父子组件通信是单向数据流**

不能用v-model双向绑定直接修改props内获取到的父组件的数据，props内的数据仅用来展示，如果想修改应该使用data()方法拷贝一份新数据,再对新数据data内的新数据进行修改



## 父传子

properties---属性
通过子组件元素行内利用v-bind获取Vue实例(父组件)数据,在通过props创建数组或对象接收数据，props数据最好不使用驼峰法命名变量,因为html不区分大小写,用-小写字母表示大写字母，并且页面渲染后props属性不会出现在dom上。

```
对象类型
如果需要对props进行类型验证就需要使用对象写法
更推荐使用对象写法，因为更加灵活。
支持验证的类型有:
String,Nubmer,Boolean,Array,Object,Date,Function,Symbol
也可以自己写函数验证自定义的构造类型

组件元素行内父组件利用v-bind向子组件传递数据
:子组件变量="父组件变量" 接收数据
```

组件构造器内添加props属性对数据进行限制

```
props: {
	propName:{
    type: 限制类型,类型首字母大写
    default: 父组件未传值时的默认值,
    required: boolean|如果required属性为true则这个变量必须传值
    validator: function(value)|用于自定义校验数据是否能用，返回值为bool类型，为真才通过校验
    }
}
```



对象类型

```html
<div id="app">
    <cpn :cmovies="movies" :cmessage="message"></cpn>
    <!--组件元素行内添加指令接收数据-->
</div>
<template id="tem">
    <div>
        <!--输出获取到的数据-->
        <ul>
            <li v-for="movie in cmovies">{{movie}}</li>
        </ul>
        <h2>this is {{cmessage}}</h2>
    </div>
</template>
<script src="../vue.js"></script>
<script>
    const app = new Vue({
        el:'#app',
        data:{
            message:'Vue',
            movies:['哆啦A梦','海贼王','超人','盗梦空间']
        },
        components:{
            cpn:{
                template: '#tem',
                //props获取父组件传递给子组件的消息
                props:{
                //规定传给子组件的数据必须符合要求
                    //cmovies:Array,
                    //cmessage:String
                    cmovies:{
                        type:Array,
                        default:['1','2']
                    },
                    cmessage:{
                        type:String,
                        default:'aaaa'
                    }
                }
            }
        }
    })
</script>
```

数组类型
组件元素行内添加指令  v-bind:子组件数据名="父组件数据名"
组件构造器内添加属性  props:['字符串类型的子组件数据名']
开发中推荐使用对象类型的写法，数组写法不够灵活

```html
<div id="app">
    <cpn :cmovies="movies" :cmessage="message"></cpn>
</div>
<template id="tem">
    <div>
        <!--输出获取到的数据-->
        <ul>
            <li v-for="movie in cmovies">{{movie}}</li>
        </ul>
        <h2>{{cmessage}}</h2>
    </div>
</template>
<script src="../vue.js"></script>
<script>
    const app = new Vue({
        el:'#app',
        data:{
            message:'Vue',
            movies:['哆啦A梦','海贼王','超人','盗梦空间']
        },
        components:{
            cpn:{
                template: '#tem',
                //props获取父组件传递给子组件的消息
                props:['cmovies','cmessage']
            }
        }
    })
</script>
```



props比data先创建，所以data中可以通过this.propsName拿到props中的内容



## 子传父

子组件通过`$emit Events`发射自定义事件来给父组件发送消息

```
this.$emit('父组件上的自定义事件名',默认参数)
```

```html
<div id="app">
    <cpn @itemclick="cpnClick"></cpn>
</div>

<template id="cpn">
<div>
    <button v-for="item in categories" @click="btnClick(item)">
    	{{item.name}}
    </button>
</div>
</template>

<script src="../vue.js"></script>
<script>
   const cpn ={
        template: '#cpn',
        data(){
            return {
                categories:[
                    {id:'a',name:'热门a'},
                    {id:'b',name:'热门b'},
                    {id:'c',name:'热门c'},
                    {id:'d',name:'热门d'},
                    {id:'e',name:'热门e'}
                ]
            }
        },
        methods: {
            btnClick(item) {
                // 发射事件自定义事件
                this.$emit('itemclick', item)
            }
        }
    }
    const app = new Vue({
        el:'#app',
        data:{
        },
        components:{
            cpn
        },
        methods:{
            cpnClick(item){
                //因为这个不是默认事件所以不会传入默认事件参数$event
                //父组件获取到了子组件传递来的item作为参数
                console.log('cpnClick',item);
            }
        }
    })
</script>

```



## 双向绑定父子组件数据

**props是单向数据流**，不能用v-model双向绑定直接修改props内获取到的父组件的数据，props内的数据仅用来展示，如果想修改应该使用data()方法拷贝一份新数据,再对新数据data内的新数据进行修改。
如果想将父组件子组件内数据进行双向绑定应该通过父传子，子传父结合v-model的内部实现来处理(:value和@input事件，input事件在数据变化时立刻触发)

```html
<div id="app">
    <h2>Fathernum1:{{num1}}</h2>
    <h2>Fathernum2:{{num2}}</h2>
    <cpn :number1="num1"
         :number2="num2"
         @num1change="num1change"
         @num2change="num2change">
    </cpn>
    <!--父传子用props接收v-bind传入的的数据-->
    <!--子传父用发射事件-->
</div>
<template id="tem">
    <div>
        <h2>sonPropsN1: {{number1}}</h2>
        <h2>sonDataN1: {{dnumber1}}</h2>
        <input type="text" :value="dnumber1" @input="num1Input">
        <!-- 
        	通过v-model的内部实现:
        	value和@input来双向绑定dnumber和num并添加发射事件功能
        -->
        <!--input修改dnumber
        	双向绑定的num也会修改
        	props中的number也会修改
        -->
        <h2>sonPropsN2: {{number2}}</h2>
        <h2>sonDataN2: {{dnumber2}}</h2>
        <input type="text" :value="dnumber2" @input="num2Input">
    </div>
</template>
<script src="../vue.js"></script>
<script>
    const app = new Vue({
        el:'#app',
        data:{
            num1:1,
            num2:0
        },
        methods: {
        //默认情况下v-model所赋值时默认都是绑定的string类型
        //需要进行转换
            num1change(value){this.num1=parseFloat(value);},
            num2change(value){this.num2=parseFloat(value);}
        },
        components:{
            cpn:{
                template: '#tem',
                props:{
                    number1:Number,
                    number2:Number
                    },
                data(){
                    return {
                        dnumber1:this.number1,
                        dnumber2:this.number2
                        //拷贝一份props数据
                    }
                },
                methods:{
                    num1Input(event){
                        this.dnumber1 = event.target.value;
                        this.$emit('num1change',this.dnumber1)
                        //双向绑定+发射自定义事件
                    },
                    num2Input(event){
                        this.dnumber2 = event.target.value;
                        this.$emit('num2change',this.dnumber2)
                    }
                }
            }
        }
    })
</script>

```



## 非父子组件传值

不存在父子关系的组件之间如果想传递数据有两种方式

- vuex

- 发布订阅模式，观察者模式---总线机制 

  `VueObj.$on("事件名",callback)`观察调用$on的vue实例，一旦实例触发参数事件，就触发回调
  
  
  
   **总线机制**  event bus
  
  在两个组件中（vue实例外部）引入总线
  
  `Vue.prototype.bus = new Vue()`
  
  
  
  然后在两个组件的vue实例中分别绑定和触发总线事件
  
  **组件A绑定总线事件**
  
  ```
  mounted () {
  	this.bus.$on('事件名',绑定事件函数)
  }
  ```
  
  在vue实例对象中mounted()中使用bus在总线上绑定自定义事件
  
  
  
  **组件B触发总线事件**
  
  `this.bus.$emit('事件名',参数)`  
  
  在其他组件中触发自定义事件，绑定总线事件的组件A就会接收到消息并触发该事件
  
  
  
  
  
  使用完后在绑定总线事件（$on）的组件中销毁的生命周期（beforeDestory）中解绑总线事件避免内存泄漏
  
  ```javascript
  beforeDestory() {
  	this.bus.$off('事件名')
  }
  ```
  
  

```html
<div id="root">
        <child content="wtw"></child>
        <child content="wy"></child>
</div>
<script src="./vue.js"></script>
<script>
  Vue.prototype.bus = new Vue()
  //bus总线是一个Vue实例，并挂载到了Vue的原型上
  //这样每个vue实例都可以观察到bus总线，并根据观察结果做出反应
        
  Vue.component('child',{
    data(){
     return{
       selfContent : this.content
     }
    },
    props:{
      content:String
    },
    template:`<div @click="handleClick">{{selfContent}}</div>`,
      methods:{
        handleClick(){
          this.selfContent = this.content
          //总线发送一个change事件，并传递this.selfContent参数
          this.bus.$emit('change',this.selfContent)
        }
      },
      mounted(){
        //观察bus总线一旦触发change事件
        this.bus.$on("change",(message)=>{
          this.selfContent = message
        })
      }
  })
        
  let vm = new Vue({
    el:"#root"
  })
</script>
```





## 父子组件的访问方式

- 父组件直接访问子组件

父组件中存在一个由所有子组件（VueComponent）组成的数组或对象，父组件可以通过$children对这个数组或$refs对这个对象进行访问来访问相应子组件

$children
是通过数组下标访问对应子组件组成的数组，不太灵活使用较少
例如this.$children[0].name调用子组件name属性



$refs   

reference引用，给子组件标签上加一个属性ref=""
这个子组件就能通过$refs访问子组件组成的对象
例如子组件的ref='adc'
则this.$refs.abc.name调用子组件name属性

```html
<div id="app">
    <cpn></cpn>
    <cpn></cpn>
    <cpn ref='abc'></cpn>
    <!--父组件通过对象的方式访问abc子组件-->
    <button @click="btnClick">btn</button>
</div>
<template id="tem">
    <div>我是子组件</div>
</template>
<script src="../vue.js"></script>
<script>
    const app = new Vue({
            el:'#app',
            data:{
                message:'hello'
            },
            methods:{
                btnClick() {
                    this.$children[0].showMessage();
                    //通过下标访问
                    //showMessage
                    console.log(this.$children[0].name);
                    //我是子组件的name
                    
                    //通过$refs访问
                    console.log(this.$refs.aaa.name);
                    }
            },
            components:{
                cpn:{
                    template:'#tem',
                    methods:{
                        showMessage(){
                            console.log('showMessage');
                        }
                    },
                    data(){
                        return {
                            name:'我是子组件的name'
                    }}}}})
</script>
```



- 子组件访问父组件

  $parent  访问父级组件
  $root    访问根组件
  this.parent可以子组件访问父组件，但不推荐，因为这样子组件的就不够独立了，复用性和独立性降低。

```html
<div id="app">
    <father></father>
</div>
<template id="father">
    <div>
        <h2>father组件</h2>
        <son></son>
    </div>
</template>
<template id="son">
    <div>
        <h2>我是子组件</h2>
        <button @click="btnClick">getParent</button>
    </div>
</template>
<script src="../vue.js"></script>
<script>
    const app = new Vue({
        el:'#app',
        data:{
            message:'hello'
        },
        components:{
            father:{
                template:'#father',
                data(){
                   return{
                       name:'father name'
                   }
                },
                components:{
                    son:{
                        template:'#son',
                        methods:{
                            btnClick(){
                                //this.$parent访问父组件
                                console.log(this.$parent);
                                console.log(this.$parent.name);
                                //father name
                                console.log(this.$root.message);
                                //hello
                                }}}}}}})
</script>
```



# 组件属性

组件的原型指向Vue，所以相同地方很多，也拥有很多功能和属性

## template

template属性用于绑定html代码模板

## props

props属性用于父组件向子组件传递数据

## data

组件中无法直接引用Vue实例中的数据，如果想在组件中使用变量等数据的话需要在组件对象中单独添加一个data添加数据。
**但组件的data属性必须是一个函数并返回一个数据对象**（理由是组件是复用的，很多地方都会使用，为了防止相互污染数据，所以函数每次使用都会返回一个新对象保护数据），这个函数返回一个对象，对象内部保存着数据键值对，**如果想一个组件的所有应用都数据互通就在外部创建一个对象，data函数返回这个对象名，这样每次修改都会修改这个对象内的数据。**

```html
<div id="app">
    <cpn></cpn>
</div>

<template id="tem">
    <div>
        <h2>{{title}}</h2>
        <p>我是组件的一个段落内容</p>
    </div>
</template>
<script src="../vue.js"></script>
<script>
    Vue.component('cpn',{
        template:'#tem',
        //data属性必须是一个函数，返回一个对象
        data(){
        //返回的对象中保存变量的键值对
            return {
                title:'我是data中的变量'
            }
        }
    })
    const app = new Vue({
        el:'#app'
    })
</script>
```

## methods

组件也可以有methods属性供组件功能使用

```html
<div id="app">
<cpn></cpn>
<cpn></cpn>
<cpn></cpn>
</div>
<template id="tem">
    <div>
        <h2>计数：{{count}}</h2>
        <button @click='add'>+</button>
        <button @click='sub'>-</button>
    </div>
</template>

<script src="../vue.js"></script>
<script>
    const obj={
        count:0
    }
    //外部创建一个对象
    //如果想相同组件复用的数据互通，则data函数返回这个对象名
    
    Vue.component('cpn',{
        template:'#tem',
        data(){
            //复用每次返回一个新对象
            return{
                count:0
            }
            // return obj;
            //返回一个对象名，相当于每次都修改这个对象
            //相同组件复用数据就共通了
        },
        methods:{
            add(){
                this.count++;
            },
            sub(){
                this.count--;
            }
        }
    })
    const app = new Vue({
        el:'#app'
    })
</script>
```



## watch

侦听器

可以监听组件属性的改变，一旦监听属性改变就会运行watch中设定的函数，实际开发中，watch会随着组件一并销毁。



**watch可以只给监听的数据绑定一个回调，函数名就为监听的属性，一旦属性发生变化就会执行回调**

```html
<body>
    <div id="root">
        <p>FullName: {{fullName}}</p>
        <p>FirstName: <input type="text" v-model="firstName"></p>
    </div> 

<script src="./vue.js"></script>
<script>
new Vue({
  el: '#root',
  data: {
    firstName: 'wang',
    lastName: 'Taiwei',
    fullName: ''
  },
  watch: {
    //firstName发生变化后就会执行回调函数
    //函数名就为监听的属性
    firstName(newName, oldName) {
      this.fullName = newName + ' ' + this.lastName;
    }
  } 
})
</script>
</body>
```

**但是vue不能监听引用类型的变化，需要深度监听才行**

绑定对象来监听属性值变化可以拥有更多功能，键名为监听的属性名

watch可以给监听的数据绑定一个对象，对象内有很多属性，可以在监听数据变化时产生作用，键名为绑定的属性名，值就为包含以下三个属性对象

1. `handler :  <function>|必需`

   回调函数，一旦数据变化，立刻就会执行回调，回调默认有两个参数,第一个参数默认为newVal（改变后的值），第二个参数默认为oldVal（改变前的值）

   **不应该使用箭头函数来定义 watcher 函数** ，否则this指向会不可预知，不会正确的指向vue实例、

   

2. `immediate:  <boolean>`

   如果这个属性为true，则组件创建时第一次出现该属性就会发生第一次回调

   如果为false，则只会在属性产生变化时才会发生第一次回调

   

3. `deep： <boolean>`

   普通的watch在监听对象时，如果对象指向没有改变，仅改变内部属性不会触发回调

   当deep: true时会触发深度监听，对象发生任何改变都会触发回调

```html
<div>
      <p>obj.a: {{obj.a}}</p>
      <p>obj.a: <input type="text" v-model="obj.a"></p>
</div>
<script>
new Vue({
  el: '#root',
  data: {
    obj: {
      a: 123
    }
  },
  watch: {
    //键名为监听的属性
    obj: {
      handler(newName, oldName) {
         console.log('obj.a changed');
      },
      immediate: true,
      deep: true //深度监听对象
    }
  } 
})
</script>
```



## computed

dom中可以通过插值语法显示一些data中的数据，但一些情况下需要显示数据进行计算后的值，computed类似methods也是添加函数，且函数功能一般是计算，计算属性直接在插值语法中当做属性使用

**计算属性比methods好在它具有缓存，计算结果不变时只调用一次方法,性能较高**，每个计算属性具有一个getter和setter方法，一般调用{{计算属性}}是获取get方法的返回值

**computed里面的函数建议有返回值，不建议去修改data中的属性**

```html
    <div id="add">
        <!-- 通过插值语法来计算 -->
        <h2>{{firstName+' '+lastName}}</h2>
        <h2>{{firstName}} {{lastName}}</h2>
        <h2>{{getFullName()}}</h2>
    
        <!-- 通过computed计算属性计算 -->
        <!-- 计算属性当做属性使用，调用函数时不加小括号 -->
        <!-- 完整的计算函数中fullname是一个对象 -->
        <h2>{{fullName}}</h2>
        <h2>总价:{{totalPrice}}</h2>
    
    </div>
    <script src="../vue.js"></script>
    <script>
        const add = new Vue({
            el:"#add",
            data:{
                firstName:"w",
                lastName:"tw",
                books:[
                    {id:111 , bookName:"计算机原理",price:20},
                    {id:112 , bookName:"数学",price:40},
                    {id:113 , bookName:"语文",price:50},
                    {id:114 , bookName:"英语",price:25},
                ],
            },
            computed:{
                fullName:function() {
                    return this.firstName + ' ' + this.lastName;
                },
                totalPrice:function(){
                    let result = 0;
                    for(let i=0;i< this.books.length;i++)
                    {
                        result+=this.books[i].price;
                    }
                    return result;
                }
    
            },
            methods:{
                getFullName:function(){
                    return this.firstName+' '+this.lastName;
                }
            }
        })
    </script>
```

### getter/setter

在完整的计算属性中元素计算时调用的是一个对象，对象内部添加方法实现各种计算，对象名为一个属性，所以计算函数调用时不用加()，调用该对象时会执行对象内部的get函数获取返回值，给该对象赋值时执行对象内部set函数
**使用方法类似于ES6中set/get关键字**

```html
<div id="add">
    <!-- 通过computed计算属性计算 -->
    <!-- 完整的计算函数中fullname是一个对象,不加小括号 -->
    <h2>{{fullName}}</h2><!-- 获取get方法返回值 -->
</div>
<script src="../vue.js"></script>
<script>
    const add = new Vue({
        el:"#add",
        data:{
            firstName:"Wang",
            lastName:"Taiwei",

        },
        computed:{
            //简略的计算属性如下
            // fullName:function() {
            //     return this.firstName + ' ' + this.lastName;
            // }

            //完整的计算属性实现如下
            fullName:{
            //调用set函数应该为对象赋值
            //例如add.fullName = "Wang Yue"
                set:function(newName){ //set方法一般为空
                    const names = newName.split(' ');
                    this.firstName = names[0];
                    this.lastName = names[1];
                },  
                get:function(){    //get方法
                    return this.firstName + ' ' + this.lastName;
                }
            }
        }
    })
</script>
```

**计算属性和methods的对比**

计算属性具有缓存机制，执行时如果结果没有变化会直接从缓存调用，不会执行函数，而methods每次执行都会重新调用函数，所以计算属性的性能更高





## filters

过滤器

把一个值放进进过滤器,然后过滤器中的函数对此值进行过滤.返回过滤后的数据，让数据以正确的方式显示

```html
    filters是过滤器，是Vue对象的属性
    filters:{
        过滤函数Function
    }
    
    过滤器在插值表达式里使用 lunix 命令行的管道语法.
    {{数据 | 过滤器函数}}
    数据作为参数传入过滤器函数并获取返回值
    
    <div id="add">
        <h2>{{message | final}}</h2>
        <!-- this is Vue -->
    </div>
    <script src="../vue.js"></script>
    <script>
        const app = new Vue({
                el:'#add',
                data:{
                    message:"Vue",
                },
                filters: { //过滤器
                    final(str){
                        return 'this is '+str;
                        //this is Vue
                    }
                }
            })
    </script>
```







# is动态组件

`:is="component-name"`，根据数据，动态渲染的场景，即组件类型不确定

我们使用模板时，通常标签名就是组件名，但想使用普通的html标签，但显示组件模板的内容，就需要is指令，让普通的html标签显示is指令中指向的组件内容

例如很多时候标签时配套使用的，table标签里的tbody标签中只能放tr标签，如果放自定义标签会被挤到table外面去，所以我们需要使用tr标签代替组件，还有ul/ol下配li，select下配option等

```html
<table>
	<tbody>
    	<tr is="row"></tr>
        <tr is="row"></tr>
        <tr is="row"></tr>
		<!--row是vue注册的一个组件-->
        <!--直接使用<row/>组件标签不符合H5中tbody的规则，tbody中必须放tr-->
        <!--使用is，将tr指向row组件即可-->        
    </tbody>
</table>
```

但is一般结合`<component>`元组件做动态组件

```html
<!-- 动态组件由data属性componentId控制 -->
<component :is="componentId"></component>
```



# import()

有些组件体积特别大，如果全部打包进一个js文件会特别卡顿影响体验

- import() 函数
- 按需加载，异步加载大组件



以往引入组件都是在头部直接import全部引入，组件初始化时就会加载进来

```javascript
import componentA from './a'

components: {
	componentA
}
```



某些大的，无需立即引入的组件,可以通过import() 来引入，在webpack中单独打包成一个chunk，只有使用到这个组件时才会去发送一个请求拉取这个组件的js文件。

```javascript
// 在头部无需引入，在components中通过一个函数引入
components: {
	componentA: () => import(/*webpackChunkName: "chunkName"*/ './a')
    // 可以使用webpack魔法注释为chunk命名
}
```



# 插槽slot

组件插槽的目的是组件具有扩展性，后期可以加入新功能，真实开发中大部分组件都要预留插槽
抽取共性保留不同，共性封装进组件，不同的地方封装进插槽
**Vue2.6之后slot被替换为v-slot指令**

```html
<slot>默认插槽代码</slot>
如果组件标签中有内容，默认插槽会被直接替换

<div id="app">
    <cpn>
        <button>插槽1</button>
        <!--btn会替换插槽中的两个默认插槽标签-->
    </cpn>
    <cpn></cpn>
    <cpn></cpn>
    <cpn></cpn>
</div>
<template id="tem">
    <div>
        <h2>我是组件</h2>
        <p>组件内容</p>
        <slot>
        <p>我是默认插槽</p>
        <p>我是默认插槽</p>
        </slot><!--封装一个插槽-->
    </div>
</template>
<script src="../vue.js"></script>
<script>
    const app = new Vue({
        el:'#app',
        components:{
            cpn:{
                template:'#tem'
            }
        }
    })
</script>
```

## 具名插槽
具名插槽是含有name属性的插槽，具名插槽无法被直接替换（普通插槽如果组件中有内容会被直接替换掉）

想要替换具名插槽，只能在替换标签中加上属性**solt="具名插槽name"**

```html
<div id="app">
    <cpn>
        <span>abc</span>
        <!--这个标签无效，没有任何插槽可以被替换-->
        <span slot="left">返回</span>
        <!--用span标签去替换left插槽-->
    </cpn>
</div>
<template id="tem">
    <div>
        <h2>我是组件</h2>
        <p>组件内容</p>
        <slot name="left"><span>左默认插槽</span></slot>
        <slot name="center"><span>中默认插槽</span></slot>
        <slot name="right"><span>右默认插槽</span></slot>
    </div>
</template>
<script src="../vue.js"></script>
<script>
    const app = new Vue({
        el:'#app',
        data:{
            message:''
        },
        components:{
            cpn:{
                template:'#tem'
            }
        }
    })
</script>
```



## 编译作用域

模板使用变量的作用域就是该组件

父组件无法直接访问子组件内容，子组件也无法直接访问父组件内容

父组件模板的所有东西都会在父级组件作用域内编译，而子组件模板的所有东西都会在子级组件作用域内编译

意思是在哪使用，就在哪编译，比如子级组件在父级模板中使用，所以子级组件使用父组件作用域，而子级模板在子级组件中使用所以子级模板使用子级组件作用域。

```html
<div id="app">
  <cpn v-show="isShow"></cpn>
  <!--子级组件cpn在父级组件Vue中使用-->
  <!--所以这个isShow使用Vue中的变量，而不是组件数据中的变量-->
  <cpn v-for="item in names"></cpn>
</div>

<template id="tem">
  <div>
    <h2>我是子组件</h2>
    <p>我是内容, 哈哈哈</p>
    <button v-show="isShow">按钮</button>
    <!--子级模板tem在子级组件中使用-->
    <!--所以这个isShow使用子级组件数据中的变量-->
  </div>
</template>

<script src="../vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      isShow: true
    },
    components: {
      cpn: {
        template: '#tem',
        data() {
          return {
            isShow: false
          }
        }
      }
    }
  })
</script>
```





## 作用域插槽

父组件编写插槽时使用子组件的数据就叫作用域插槽

```html
<slot :items="PLanguages"><!--这里的items对应父组件中获取到的的items-->
</slot>
```

```html
<cpn>
 <template slot-scope="slot">
   <!--这里的slot对应下面的slot-->
   <!--作用域插槽访问插槽传递过来的数据->
   <span v-for="item in slot.items">{{item}}-</span>
 </template>
</cpn>
```



父组件替换插槽的标签，但是内容由子组件来替换，目的是替换默认插槽时父组件能调用子组件数据，**主要用来做不同的数据展示**

由于父组件无法直接访问子组件的数据，想在父组件中用子组件的数据编写代码替换子组件模板插槽不太方便,
所以利用作用域插槽来获取子组件数据

子组件插槽中需要添加指令  :变量名='需要传递的数据变量'
父组件在编写替换代码前需要先套上一个根便签（Vue2.6以下只能用template标签）
在标签内添加属性  slot-scope="变量"
可以通过  变量.数据变量名  来调用之前添加了指令的数据

```html
<div id="app">
    <!--以下代码作用域在父组件Vue中-->
    <cpn></cpn><!--普通子组件数据展示-->
    
    <!--Vue2.6以下只能用template标签-->
    <!--在中间加杠展示-->
    <cpn>
       <template slot-scope="slot">
            <!--作用域插槽访问插槽传递过来的数据->
         <span v-for="item in slot.items">{{item}}-</span>
       </template>
    </cpn>
    
    <!--在中间加*展示-->
    <cpn>
       <template slot-scope="slot">
        <span v-for="item in slot.items">{{item}}*</span>
       </template>
    </cpn>
</div>

<template id="tem">
    <div>
        <slot :items="PLanguages">
            <!--向组件传递PLanguages数据-->
            <ul><li v-for="item in PLanguages">{{item}}</li></ul>
        </slot>
    </div>
</template>
<script src="../vue.js"></script>
<script>
    const app = new Vue({
        el: '#app',
        data: {
        },
        components: {
            cpn: {
                template: '#tem',
                data() {
                    return {
                        PLanguages:[
                        'JavaScript',
                        'C++',
                        'Java',
                        'C#',
                        'Python',
                        'go']
                    }}}}})
</script>
```



# $nextTick

vue是一个异步渲染的框架，data改变后，DOM不会立刻去渲染，所以我们不能在data改变后立刻去获取dom元素并操作dom元素，数据更新之后立刻拿到的dom是进行更新渲染之前的dom.

而$nextTick就是一个异步的方法，用于延迟执行一段代码，$nextTick(callback)的异步回调函数会在DOM渲染之后才被触发，以获取最新的DOM节点，所以我们在更新数据之后，如果需要拿到dom元素并操作，需要将操作dom部分的代码放入$nextTick的回调函数中，这样等dom渲染完后才会去执行其中操作dom的代码。

```javascript
method() {
	addItem() {
		this.list.push('abc')
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





# vue动画

动画的实现，必须通过元素的显示隐藏或销毁创建。(v-show  v-if)

vue中如果需要使用动画的时候，需要使用一个内置组件transition组件 该组件有一个name属性 值为动画的类名（类名随意起）

动画的css样式名名分为6个

　　入场动画

　　　　`<name>-enter  `    　　　　入场前

　　　　`<name>-enter-active`　　  入场持续的过程

　　　　`<name>-enter-to`　　入场后

　　出场动画

　　　　`<name>-leave`　　　　　　出场前

　　　　`<name>-leave-active`　　　出场持续的过程

　　　　`<name>-leave-to`　　　　  出场后



当一个元素被`<transition>`标签包裹后，vue会自动分析元素的css样式，然后构建动画流程

把初始效果给`<name>-enter和<name>-leave`

把目标效果给`<name>-enter-active <name>-leave-active`

　　或`<name>-enter-to <name>-leave-to`都可以，因为他们只相隔第一帧

然后vue就会根据css样式自动完成过渡动画

```html
<style>
  .fade-enter-active, .fade-leave-active{
     transition: opacity 3s;
   }
  .fade-enter, .fade-leave-to{
     opacity: 0;
  }
</style>


<div id="app">
   <transition name="fade">
          <div v-if="show">
              hello,world
          </div>
   </transition>
   <button @click="handleClick">切换</button>  
</div>
      <script src="./vue.js"></script>
      <script>
          const app = new Vue({
              el: '#app',
              data: {
                  show:true
              },
              methods:{
                  handleClick(){
                    this.show = !this.show
                  }
              }
          })
</script>
```



## 显示到隐藏

- vue会给动画第一帧运行之前给元素添加两个class`v-enter`和`v-enter-active`
- 动画第一帧运行完后，vue会把元素的`v-leave`class属性给去掉，再增加一个`v-leave-to`的class属性，动画继续执行
- 动画运行完后会把`v-leave-to`和`v-leave-active`两个class属性也去掉



## 隐藏到显示

- vue会给动画第一帧运行之前给元素添加两个class`v-leave`和`v-leaver-active`
- 动画第一帧运行完后，vue会把元素的`v-leave`class属性给去掉，再增加一个`v-leave-to`的class属性，动画继续执行
- 动画运行完后会把`v-leave-to`和`v-leave-active`两个class属性也去掉 



## animate.css库

使用库就不用写css样式了

- 下载animate.css样式文件或者使用link引入网络连接

`<link href="https://cdn.bootcss.com/animate.css/3.7.2/animate.css" rel="stylesheet">`

- 在html中引入

- 在`<transition>`标签中设置

  `enter-active-class="animated 入场动画名"`设置入场动画

  `leave-active-class="animated 出场动画名"`设置出场动画

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link href="https://cdn.bootcss.com/animate.css/3.7.2/animate.css" rel="stylesheet">
</head>
<body>
    <div id="app">
        <transition 
          name="fade"
          enter-active-class="animated swing"
          leave-active-class="animated tada">
          <div v-if="show">
              hello,world
          </div>
        </transition>
        <button @click="handleClick">切换</button>  
    </div>
      <script src="./vue.js"></script>
      <script>
          const app = new Vue({
              el: '#app',
              data: {
                  show:true
              },
              methods:{
                  handleClick(){
                    this.show = !this.show
                  }
              }
          })
      </script>
</body>
</html>
```







# mixins混入

组件之间的重复代码可以使用混入(类直接的重复代码可以使用继承)，当组件直接存在复用代码时可以使用mixin混入来减少代码量，组件的某一个相同属性具有大量代码相同时就可以使用混入

mixins的缺点：代码不利于阅读，对象来源不明确，多mixin容易引起变量命名冲突，mixin和组件可能存在多对多的关系，复杂度高，所以vue3中提出了Composition API来解决这些问题



1. 相同的代码封装成一个js文件并导成一个对象itemMixin
   例如两个组件created属性相同则往这个对象添加created属性并写相同部分的代码

```javascript
export const itemMixin={
    created(){
        console.log('混入');
    }
}
```



2. 在需要使用这段代码的组件的vue实例中设置一个mixins属性，属性值是一个数组，数组内的值是引入的包含代码的对象itemMixin
   `mixins:[itemMixin]`



3. 必须要书写这个相同的属性，格式和普通的vue属性一样



4. 混入完成，如果需要添加特定代码直接写入这个created属性最后代码会合并起来



案例 A与B组件vue实例中created属性代码相同

```javascript
定义一个混入文件mixins.js
export const itemMixin={
    created(){
        console.log('混入');
    }
}


//A.vue组件
import {itemMixin} from 'mixins.js'

//实例
export default {
    name: "A",
    mixins:[itemMixin],
    created(){
       console.log('混入A'); 
    }
	//混入的created会和实例的created合并
}


//B.vue
import {itemMixin} from 'mixins.js'

//实例
export default {
    name: "B",
    mixins:[itemMixin],
    created(){
        console.log('混入B');
    }
}


```


## 全局混入

```
使用全局混入，所有Vue实例都会被混入
Vue.mixin(options)
```





# runtime

Vue程序的运行过程，模板template会先保存在vm.options.template中，vm.options.template然后parse（解析）成ast（抽象语法树），然后ast会compile(编译)成render函数(vm.option.render)，render函数会创建虚拟dom并形成虚拟dom树，虚拟dom树最后会渲染到页面（UI）上

```javascript
template --> AST语法树 --> render --> virtual dom --> 真实dom(UI)
```

vue的runtime-compiler和runtime-only模式的区别在于，runtime-compiler的main.js中Vue实例中直接使用模板，runtimee-only的main.js中Vue实例中没有模板，直接从render->vdom->UI

runtime-only的性能更高，源码的代码量更少（小6kb左右），真实开发推荐使用runtime-only模式



## render函数

vue实例中的render函数的参数是createElement函数，简写是h，createElement函数传入标签名`createElement(tag)`，则用这个h2标签直接替换掉app中的内容

```javascript
render: function(createElement){
    return createElement('h2')
}
```



第二个参数是可以传入对象,键值对储存标签的属性

`createElement('标签',{标签的属性})`

```javascript
render: function(createElement){
    return createElement('h2',{class:'box'})
}
// <h2 class='box'></h2>代替app
```



第三参数是一个数组，放置标签的内容

`createElement('标签',{标签的属性},['标签内容'])`

```javascript
render: function(createElement){
    return createElement(
        'h2',
        {class:'box'},
        [ 'hello world', createElement('button',['btn']) ]
    )
}

<h2 class='box'>hello world<button>btn</button></h2>
就会代替index模板中的
<div id="app"></div>
```



createElement函数也传入组件对象

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
  
  render: createElement => createElement(cpn) //传入组件对象
})
```



## runtime效率更高的原因

render函数传入组件效果和注册组件效果一样，但效率更高

所以在runtime-only的工程源文件的main.js中，在引入了App.vue文件后用createElement(别名h)传入达到提升效率的作用之后App.vue文件中的template同样也会被 **render **同样解析

```javascript
//runtime-only的main.js
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  render: h => h(App)
})
```

而之前我们在完全分离vue代码，把.js文件转换为.vue文件时，安装的vue-loader和vue-template-compiler

vue-loader用于webpack加载.vue文件，vue-template-compiler用于将template编译成render函数

选择runtime模式创建工程会默认开发式依赖下载安装vue-template-compiler，其他文件的template模板都会被vue-template-compiler自动编译为render函数



# webpack配置Vue

1. npm引入Vuejs，vue会被加载进node环境

```
npm install vue --save
```

2. 在main.js文件里引入vue

```
import Vue from 'vue'
```

3. webpack中配置vue

   直接使用代码运行vue会报错runtime-only，import的vue默认使用runtime-only版本，所以需要在webpack配置文件中module.exports对象下配置版本。

   runtime-only版本：代码中不可以有任何template

   runtime-compiler版本：代码中可以有template

```javascript
resolve:{
  alias:{
    'vue$':'vue/dist/vue.esm.js'
    //import的vue.runtime.js会被替换为vue.esm.js
   }
}
```

4. 使用Vue

```javascript
import Vue from 'vue'
const app = new Vue({
    el:'#app',
    template:`
        <div>
            <h2>{{message}}</h2>
            <button @click="btnClick">btn</button>
            <h2>{{name}}</h2>
        </div>
    `,
    methods:{
      btnClick(){
      }
    },
    data:{
        message:'vue',
        name:'wtw'
    }
})

```



# vue项目

html主页模板确定后，后期不方便频繁修改，当使用vue时，Vue实例内同时有el和template，template会将el替换掉，主页html只需要一个el挂载的div就可以了，这样修改html就不用修改主页文件了。

但vue实例里加太多template，数据和方法会很臃肿，需要抽取出来，所以实际开发中都是使用后缀`.vue`的文件实现分离。



## 使用template

```javascript
import Vue from 'vue'
const APP = {
    template:`
        <div>
            <h2>{{message}}</h2>
            <button @click="btnClick">btn</button>
            <h2>{{name}}</h2>
        </div>
    `,
    data(){
        return{
        message:'vue',
        name:'wtw'
        }
    },
    methods:{
        btnClick(){}
    }
};

new Vue({
    el:'#app',
    template:`<APP></APP>`,
    components:{APP}
});
```



然后再将app组件封装进一个js文件，在将模块导入入口js文件，可以使入口文件更简洁

```javascript
//入口文件main.js
import Vue from 'vue'
import APP from './VUE/app'

new Vue({
    el:'#app',
    template:`<APP></APP>`,
    components:{APP}
});

//app.js
export default {
    template:`
        <div>
            <h2>{{message}}</h2>
            <button @click="btnClick">btn</button>
            <h2>{{name}}</h2>
        </div>
    `,
    data(){
        return{
            message:'vue',
            name:'wtw'
        }
    },
    methods:{
        btnClick(){}
    }
}
```



## 使用.vue

1.安装依赖

安装vue-loader解析`.vue`结尾的文件
安装vue-template-compiler用于将template编译成render函数

```
npm install --save-dev vue-loader vue-template-compiler
```



2.配置rules

在webpack配置文件中的module对象下rules配置，将以`.vue`为后缀的文件通过vue-loader解析

```javascript
{
  test: /\.vue$/,
  use: { loader:'vue-loader' }
}
```



3.创建.vue文件

```javascript
//main.js
import Vue from 'vue'
//引入App组件
import APP from './VUE/App.vue'
new Vue({
  el:'#app',
  template:`<APP></APP>`,
  components:{APP}
});
```

```vue
//App.vue
<template>
    <div>
        <h2>{{message}}</h2>
        <button @click="btnClick">btn</button>
        <h2>{{name}}</h2>
        <Cpn></Cpn>
    </div>
</template>

<script>
  import Cpn from './Cpn.vue';
  //从另一个文件里导入Cpn组件
  export default {
    name: "App",
    components:{
      Cpn
    },
    data(){
      return{
        message:'vue',
        name:'wtw'
      }},
    methods:{
      btnClick(){}
    }}
</script>

<style scoped>
.title{
    color: green;
}
</style>
```

```vue
//Cpn.vue
//组件APP的子组件Cpn
<template>
    <div>
        <h2>我是Cpn标题</h2>
        <p>内容</p>
        <h2>{{name}}</h2>
    </div>
</template>

<script>
    export default {
        name: "Cpn.vue",
        data(){
            return{
                name:'cpn组件的name'
            }
        }
    }
</script>
<style scoped>
</style>
```







# 脚手架CLI

大型项目一定会使用到CLI，Command-Line Interface，简写CLI，Vue CLI是Vue官方发布的脚手架项目，使用vue-cli可以快速搭建Vue开发环境以及对应的webpack配置

```html
Vue CLI依赖node，npm，webpack

node的npm安装太慢时可以设置淘宝镜像
下载淘宝镜像
npm install -g cnpm --registry=//registry.npm.taobao.org
然后就可以使用cnpm命令进行下载 cnpm install
```



## 安装脚手架

```html
npm install -g @vue/cli
这样安装的是Vue CLI3
因为Vue CLI3和旧版使用了相同的Vue命令
所以Vue CLI3无法按照Vue CLI2的方式初始化项目

npm install -g @vue/cli-init
如果想使用Vue CLI2可以之后安装一个桥接工具拉取CLI2的模板
vue init的运行效果将会和vue cli@2.x相同
```





# Vue CLI2

```html
Vue CLI2初始化项目
vue init webpack 项目名称
```

## 脚手架初始化后文件结构

- node_modules  项目依赖的包下载在这个文件夹里
- static       静态资源文件夹
- config     存放配置文件的文件夹，其中基础配置信息放进`index.js`，开发环境的配置信息放进`dev.env.js`，线上环境的配置信息放进` prod.env.js`
- build       build文件夹放webpack项目打包的相关配置
- package.json   项目需要使用的第三方模块和各种依赖包
- package-lock.json    安装的第三方模块和各种依赖包的版本号，保持团队协作的统一
- .postcssrc.js       配置css相关内容
- .gitignore            跟git相关的配置，无需配置提交到git仓库的文件配置在这里
- .eslintignore      eslint不检测语法的文件配置在这里
- .editorconfig      编辑器代码格式化配置，例如编码utf-8，`indent_size = 2`tab键一次缩进两个空格等
- .babelrc               babel语法转换器配置





# Vue CLI3

Vue CLI2是基于webpack3设计的，Vue CLI3是基于webpack4设计的，Vue CLI3设计原则是0配置，隐藏了配置文件根目录下的build和config配置文件夹，提供了vue ui命令，提供可视化配置移除了static文件夹，新增了public文件夹，并且将index.html移动到了public中。

CLI3创建工程命令：`vue create projectname`，CLI3创建工程后会自动创建一个.git文件夹，可以用git命令进行操作

node文件夹包括各种node包，public相当于CLI2的static文件夹，放静态文件，src是源代码文件夹

因为CLI3通过vue-cli-service管理了配置文件实现0配置，将CLI中的dev修改为serve，`"serve": "vue-cli-service serve"`，所以启动本地服务器的命令变为：`npm run serve`



## CLI3配置修改方式

CLI3修改隐藏了的配置有三种方式

1.启动本地服务器：vue ui
运行命令 vue ui
会启动一个本地服务器，可以可视化设置配置文件，导入项目等操作

2.打开`node_modules/@vue/webpack.congig.js`
webpack.congig.js引入了 ./lib/Service.js，Service.js具有一些配置，并引入了lib文件夹内其他配置完成整体配，这些配置就是cli3隐藏的配置文件

3.在工程根目录下创建vue.config.js文件
在文件内编写导出的配置，编写后自己写的配置文件就会和隐藏的配置文件合并起来

```
module.exports={
    //编写配置代码
}
```





