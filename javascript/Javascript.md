# Javascript

编译型和解释型语言最重要的区别是编译型语言需要很长的时间来准备执行。因为它需要对整个代码进行词法分析、做一些极致的优化等工作。而解释型语言几乎在执行后一瞬间就开始，但是没有任何代码优化，所以每一条语句都是分开转换（编译）的。

MDN 明确地说明了：JavaScript 是一个解释型语言。

但是为什么JS存在词法分析，变量提升，JIT等解释型语言无法做到的特性？



## 解释型语言vs编译型语言

| 类型   | 原理                                                         | 优点                                                         | 缺点                         |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------------- |
| 编译型 | 通过专门的编译器，将所有源代码一次性转换成特定平台（Windows、Linux 等）执行的机器码（以可执行文件的形式存在）。 | 编译一次后，脱离了编译器也可以运行，并且运行效率高。         | 可移植性差，不够灵活。       |
| 解释型 | 由专门的解释器，根据需要将部分源代码临时转换成特定平台的机器码。 | 跨平台性好，通过不同的解释器，将相同的源代码解释成不同平台下的机器码。 | 一边执行一边转换，效率很低。 |

编译型语言：**代码在运行前就通过编译器将编程语言转换成机器码。**在编译后的机器码能直接在机器上运行。

解释型语言：**编程语言在运行时根据解释器转换为特定平台的机器码。**所以执行前需要在环境中提前安装了解释器，在运行时边解释边运行。这也是解释型语言慢的原因，因为多了一个解释编译的过程。

JavaScript 代码需要在环境（node /浏览器）上安装一个JS 引擎才能执行，由JS引擎生成 CPU 可以执行的机器码。这才是解释型语言需要的本质，而编译型语言程序是能够自由地直接运行。所以JS明确的是一个解释型语言。

JS虽然存在词法分析，变量提升等特性，但是变量提升不是代码修改，在这个过程中没有生成中间代码。变量提升和词法分析只是 JS 解释器处理JS的方式。



## 编译器/解释器

编译器：编译器是把编译型语言代码转换成（翻译）低级语言的程序，编译器是把源程序的每一条语句都编译保存成二进制文件（机器码），这样运行时计算机可以直接以机器语言来运行此程序，速度很快。

解释器：解释器是可以直接执行由解释型代码编写的指令的程序，而解释器则是只在执行程序时,才一条一条的解释成机器码给计算机来执行，所以运行速度是不如编译后的程序运行的快的。



## 机器码/字节码

机器码（机器语言指令）是计算机能认识的唯一语言，所有的编程语言都需要被转化为机器码才能被CPU运行。机器码是一串二进制文件，机器码，完全依附硬件而存在，根本不存在跨平台性，不同构架的CPU机器码不同。

字节码（Bytecode）是一种包含执行程序、由一序列 op 代码/数据对组成的二进制文件（例如java的.class文件），字节码比机器码更抽象，体积也更小。字节码是一种中间码，CPU无法直接执行字节码文件，需要直译器（例如JVM）转译后才能成为CPU能执行的机器码（可执行文件）。字节码用于实现跨平台性。机器码依赖硬件，而字节码依赖直译器，通过直译器将字节码转化为当前CPU适应的的机器码来实现跨平台。



对于JavaScript，之前V8是直接将JS编译为机器码，V8 5.9之后，引入了字节码解释器，将JS默认编译为字节码。

机器码的执行性能已经非常之高，而这次引入字节码则是选择编译 JS 代码到一个中间态的字节码，执行时是解释执行，性能是低于机器代码的。那么 V8 为什么要做这样一个退步的选择呢？

1. 主要动机：减轻机器码占用的内存空间，即牺牲时间换空间

   因为机器码占空间很大，有的大型网站，v8 没有办法把所有 js 代码编译成的机器码缓存下来，因为这样不仅缓存占用的内存、磁盘空间很大，而且退出 Chrome 再打开时序列化、反序列化缓存所花费的时间也很长，时间、空间成本都接受不了。所以有些网站V8无法对其的JS脚本进行缓存，每次打开都要重新加载。

   机器码占空间大还有另外一个坏处，就是一些只运行一次的代码浪费了宝贵的内存资源，由于机器码占空间大，这些只执行一次的代码也会在内存中长期存在、长期占用空间。很多的堆空间都用于储存这些无用的机器码。

   而引入字节码之后，占空间的问题就可以得到缓解。通过恰当地设计字节码的编码方式，字节码可以做到比机器码紧凑很多。V8所占用的内存也会小很多，这样对于低内存设备也更加友好。

2. 提高代码的启动速度

   由于字节码更小，V8对于大型网站就可以选择提前编译该网站全部 JS 代码到字节码，并把字节码缓存下来，这样该网站第二次打开的时候启动速度就变快了，因为无需重新解析和编译JS脚本。

3. 对 v8 的代码进行重构，降低 v8 的代码复杂度

   v8 自身的重构方面，有了字节码，v8 可以朝着简化的架构方向发展，消除 Cranshaft 这个旧的机器码编译器，并使用新架构Ignition（字节码解释器） +  TurboFan（JIT 编译器），其中Ignition（字节码解释器）负责编译转化字节码，性能提升部分让 Turbofan（JIT编译器） 实现。





## JIT

现代JS引擎中存在 JavaScript的即时编译机制JIT(just in time)，JIT，全称是 Just In Time，即时编译，混合使用编译器和解释器的技术。

JIT不是 JavaScript 所特有的。其他语言比如 Java 也有一些在执行前编译代码的机制。JavaScript的JIT是JS引擎赋予的能力，而不是语言本身的特性。

在现代JS引擎中：

- 在 JavaScript 中如果一段代码运行超过一次，那么就称为 warm。如果一个函数开始变得 warmer（即运行更多次），JIT 将把这段代码的字节码送到编译器中编译并且缓存编译后的机器码，下一次同样代码执行的时候，引擎会跳过翻译过程直接使用编译后的机器码（更加高效）而不是字节码。

- 如果一段 warm 的代码变得 hot 或者 hotter（指运行更多次以及比更多还要多的次数），JIT 会尝试更多的优化。在编译器进行优化的过程中会做一些关于变量类型和运行环境中值的假设，如果假设不成立就将这个优化的版本回退，如果假设成立的话，这将让代码性能更高。

**也就是对于高频使用的代码段，JIT会将其的字节码转化为特定的高效机器码并缓存下来，再次执行的时候就无需解释js并编译为字节码了，可以直接运行机器码。**

Ignition（字节码解释器） +  TurboFan（JIT 编译器）的黄金组合。在很多的JS引擎中都有使用，例如V8，JScore等。





# 解析JS过程

js的解析过程分为以下四个步骤

**1.词法分析**

词法分析会将js代码中的字符串分割为有意义的代码块，这些代码块我们可以称之为词法单元(Token)。比如简单的如下代码:`var a = 1;` 

那么这行代码会被分为以下词法单元： var、a、=、1 那么这些零散的词法单元会组成一个词法单元流进行解析。

语法分析在这个过程中会将词法单元token流转换成一颗抽象语法树(AST)。



**2.语法检测（有没有基本的语法错误,例如中文,关键字错误...）**

我们对生成的AST树节点需要进行处理下，比如我们使用ES6编写的代码，比如用到了let,const这样的，我们需要转换成var。
​因此我们需要对AST树节点进行转换操作。转换AST的时候，我们可以添加、移动、替换及删除AST抽象树中的节点操作



**3.预编译（变量提升，函数提升，函数提升优先级高于变量提升）**

函数提升大于变量提升，注册表中变量名可以函数名被覆盖，函数名不能被变量名覆盖，也就是在变量提升环节，如果有同名函数提升，则变量提升无效。

在js文件或script里面的代码在正式开始执行之前，会进行一些解析工作。比如上在全局中寻找var关键字声明的变量和通过function关键字声明的函数。 找到全局变量或函数后，我们会对该进行作用域提升，但是在变量提升声明的情况下不会赋值操作，因此它的默认值是undefined。通过声明提升，对于函数来讲，函数可以在声明函数体之上进行调用。变量也可以在赋值之前进行输出，只是变量输出的值为undefined而已。



**4.解析**

基于事件循环（Event Loop）执行JS，从上到下依次执行



# 预编译阶段

预编译阶段主要基于运行环境做三件事：创建变量对象（创建arguments对象（函数运行环境下），函数声明提前解析，变量声明提升），确定作用域链以及this指向。**总的来说预编译阶段就在是创建执行上下文。**

创建执行上下文有两个步骤：

**第一步：首先判断js的运行环境**

运行环境主要由三种：

- 全局环境（js代码加载完毕后，进入到预编译也就是进入到全局环境）

- 函数环境（函数调用的时候，进入到该函数环境，不同的函数，函数环境不同）

- eval环境（不建议使用，存在安全、性能问题）

**第二步：为每进入到一个不同的运行环境都创建一个相应的执行上下文（execution context）。**

在一段js程序中一般都会创建多个执行上下文，js引擎会以栈的数据结构对这些执行进行处理，形成函数调用栈（call stack），栈底永远是全局执行上下文（global execution context），栈顶则永远时当前的执行上下文。

预执行上下文可以理解成当前的执行环境，与该运行环境相对应。创建执行上下文的过程中，主要是做了下面三件事：

- 创建变量对象：首先初始化函数的参数arguments，提升函数声明和变量声明。
- 创建作用域链
- 确定this指向



在完成所有上下文执行的创建后本次预编译阶段结束。





# 上下文执行栈Context

执行上下文就是当前 JavaScript 代码被解析和执行时所在环境的抽象概念， JavaScript 中运行任何的代码都是在执行上下文中运行。

执行上下文的生命周期包括三个阶段：创建阶段→执行阶段→回收阶段，**我们重点介绍创建阶段。**

创建一个新的执行上下文阶段（当函数被调用，但未执行任何其内部代码之前）会做以下三件事：

- 创建变量对象（variable object）

  创建变量对象有以下三个步骤

  1. 创建arguments对象，检查当前上下文的参数，建立该对象的属性与属性值，仅在函数环境（非箭头函数）中进行的，全局环境没有此过程。
  2. 检查当前上下文的**函数声明**，按照代码顺序查找，将找到的函数提前声明，如果当前上下文的变量对象没有该函数名属性，则在该变量对象以函数名建立一个属性，属性值则指向该函数所在**堆内存地址引用**，如果存在，则会被新的引用覆盖掉。
  3. 检查当前上下文的**变量声明**，爱去哪找代码顺序查找，将找到的变量提前声明，如果当前上下文的变量对象没有变量名属性，则在该变量对象以变量名建立一个属性，属性值为undefined；如果存在，则忽略该变量声明。

  说明：在全局环境中，window对象就是全局执行上下文的变量对象，所有的变量和函数都是window对象的属性方法。所以函数声明提前和变量声明提升是在创建变量对象中进行的，且函数声明优先级高于变量声明。

- 创建作用域链（scope chain）

  作用域链由当前执行环境的变量对象（未进入到执行阶段前）与上层环境的一系列活动对象组成，保证了当前执行还款对符合访问权限的变量和函数有序访问。

- 确定this的指向

  在全局环境下，全局执行的上下文中变量对象的this属性指向为window，在函数环境下的this指向比较灵活，需要根据执行环境和执行方法确定this指向。



这是因为当函数执行的时候,**首先会形成一个新的私有的作用域**，然后依次按照如下的步骤执行：

- 如果有形参，先给形参赋值
- 进行私有作用域中的预解释，函数声明优先级比变量声明高，最后后者会被前者所覆盖，但是可以重新赋值
- 私有作用域中的代码从上到下执行

函数多了，就有多个函数执行上下文，每次调用函数创建一个新的执行上下文，那如何管理创建的那么多执行上下文呢？

JavaScript 引擎创建了执行栈来管理执行上下文。**可以把执行栈认为是一个存储函数调用的栈结构，遵循先进后出的原则**。

![](https://img2018.cnblogs.com/blog/1207100/201907/1207100-20190702112303470-329127782.png)

从上面的流程图，我们需要记住几个关键点：

- JavaScript执行在单线程上，所有的代码都是排队执行。
- 一开始浏览器执行全局的代码时，首先创建全局的执行上下文，压入执行栈的顶部。
- 每当进入一个函数的执行就会创建函数的执行上下文，并且把它压入执行栈的顶部。当前函数执行完成后，当前函数的执行上下文出栈，并等待垃圾回收。
- 浏览器的JS执行引擎总是访问栈顶的执行上下文。
- 全局上下文只有唯一的一个，它在浏览器关闭时出栈。

**从根本上来说，作用域是基于函数的，而上下文是基于对象的。**





# 作用域链scope chain

ES6 到来JavaScript 有全局作用域、函数作用域和块级作用域（ES6新增）。

我们可以这样理解：**作用域就是一个独立的地盘，让变量不会外泄、暴露出去。也就是说作用域最大的用处就是隔离变量，不同作用域下同名变量不会有冲突**。

**函数作用域**：顾名思义就是在这个函数体里边才能访问的变量；当然可以利用闭包来实现跨区域访问局部作用域的变量；

**块级作用域**：ES6新增，用let命令新增了块级作用域，外层作用域无法获取到内层作用域，非常安全明了。即使外层和内层都使用相同变量名，也都互不干扰；

**自由变量**：当前函数中没有来源却需要使用的变量，**自由变量需要从作用域链中去寻找，依据的是函数定义时的作用域链（ 向父级作用域 (创建该函数的那个父级作用域)寻找），而不是函数执行时。**

如果父级也没有再一层一层向上寻找，直到找到全局作用域还是没找到，就宣布放弃。这种一层一层的关系，就是作用域链

```js
var a = 100
function F1() {
  console.log(a) // 自由变量a
}

function F2(f1) {
  var a = 200
  f1()
}

F2(F1) // 100
```



# JS的单线程

js是单线程的，那么是否代表参与js执行过程的线程就只有一个？

不是的，一个浏览器通常有以下几个常驻的线程，会同时执行：

- js引擎线程(js引擎有多个线程，一个主线程，其它的后台配合主线程)
  作用：执行js任务(执行js代码，用户输入，ajax网络请求)，所以发起ajax会使UI线程阻塞

  js线程和ui渲染线程相互阻塞

- GUI渲染线程
  作用：渲染页面(js可以操作dom，影响渲染，所以js引擎线程和UI线程是互斥的。js执行时会阻塞页面的渲染，ui渲染是宏任务。)

- 浏览器事件触发线程
  作用：控制交互，响应用户

- http请求线程
  作用：ajax请求等

- 定时触发器线程
  作用：setTimeout和setInteval

- 事件轮询处理线程
  作用：轮询消息队列，event loop



JavaScript语言的单线程是指JavaScript同一个时间只能做一件事。js和ui渲染线程互斥。所以HTML渲染从上到下依次执行时，遇见script标签会停下来执行完再继续渲染，所以建议将script标签放在最后加载，以免影响dom加载速度造成卡顿。

JavaScript的单线程，与它的用途有关。作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

所以，为了避免复杂性，从一诞生，JavaScript就是单线程，这已经成了这门语言的核心特征，将来也不会改变。

为了利用多核CPU的计算能力，HTML5提出Web Worker标准，允许JavaScript脚本创建多个进程，但是子进程完全受主线程控制，且不得操作DOM。所以，这个新标准并没有改变JavaScript单线程的本质。

**ui渲染线程和JS引擎线程是不能同时进行的。也就是说在执行代码时，渲染会挂起；渲染DOM时，JS代码也不会执行。**

**虽然JS是单线程，但是浏览器是多线程的，在遇到像setTimeout、DOM事件、ajax等这种任务时，会转交给浏览器的其他工作线程(上面提到的几个线程)中，在Event Loop顺序下排队等待执行。**



# JS与css的阻塞

当HTML解析器（HTML Parser）遇到script标记阻塞时，解析器会停止构建 DOM，执行JavaScript脚本的资源直至脚本执行完再开始构建DOM。

JS在预编译阶段如果发现该脚本还操作了CSS，而正好这个CSSOM还没有下载和构建，这时就存在阻塞的资源，浏览器会延迟JavaScript脚本执行，直至完成其CSSOM的下载和构建再执行。



# Event Loop

任务按照优先级和顺序依次执行的过程叫事件轮询（Event Loop），优先级：同步任务 > 微任务 > 宏任务

队列分为微任务队列和宏任务队列，微任务队列优先级更高，微任务队列执行完了才会去执行宏任务，例如Promise.catch/then属于微任务，setTimeout属于宏任务

例如当前宏任务正在执行，宏任务队列也有任务排队，如果此时微任务队列插入了任务，当前宏任务执行完后，会立刻执行微任务队列中的任务。因为微任务优先级高。

浏览器环境在HTML Standard（标准）中定义了Event Loop，Node.js使用了libuv库来实现Event Loop

| 微任务                                              | 浏览器 | Node |
| --------------------------------------------------- | ------ | ---- |
| process.nextTick                                    | x      | √    |
| MutationObserver（一个可以监听DOM结构变化的接口。） | √      | x    |
| Promise.then/catch                                  | √      | √    |

| 宏任务                           | 浏览器 | Node |
| -------------------------------- | ------ | ---- |
| setTimeout                       | √      | √    |
| setInterval                      | √      | √    |
| setImmediate                     | x      | √    |
| requestAnimationFrame 请求动画帧 | √      | x    |
| IO（图片，资源等加载）           | √      | √    |
| UI rendering                     | √      | x    |
| 普通的回调函数                   | √      | √    |



## 宏任务macroTask

定时器setTimeout属于宏任务，定时器的时间即使设置为0也有4ms延迟，定时器的执行时间并不固定的，并不是定时到了就执行回调，而是定时到了以后将任务放入宏任务队列中，宏任务必须等主线程和微任务队列全部执行完后才能执行。所以定时器的执行时间并不一定为定时器设置的时间。

## 微任务microTask

微任务的代表是promise，promise的构造函数时同步任务立刻执行，但then和catch里的任务是会放入微任务队列中

```javascript
setTimeout(()=>{
    //定时器回调是宏任务
	console.log("定时器")
},0)
new Promise(resolve=>{
//Promise构造函数是同步任务
console.log("Promise")
	resolve()
}).then(()=>{
    //promise.then/catch是微任务
	console.log("then")
})
console.log("abc")

//promise
//abc
//then
//定时器
```



# Web Worker

JavaScript 语言采用的是单线程模型，也就是说，所有任务只能在一个线程上完成，一次只能做一件事。前面的任务没做完，后面的任务只能等着。随着电脑计算能力的增强，尤其是多核 CPU 的出现，单线程带来很大的不便，无法充分发挥计算机的计算能力。

Web Worker 的作用，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行。在主线程运行的同时，Worker 线程在后台运行，两者互不干扰。等到 Worker 线程完成计算任务，再把结果返回给主线程。这样的好处是，一些计算密集型或高延迟的任务，被 Worker 线程负担了，主线程（通常负责 UI 交互）就会很流畅，不会被阻塞或拖慢。

**Web Worker一般只在后台进行一些运算操作，但不会阻塞页面渲染。所以web worker不能去操作dom。**



Worker 线程一旦新建成功，就会始终运行，不会被主线程上的活动（比如用户点击按钮、提交表单）打断。这样有利于随时响应主线程的通信。但是，这也造成了 Worker 比较耗费资源，不应该过度使用，而且一旦使用完毕，就应该关闭。

Web Worker 有以下几个使用注意点。

（1）**同源限制**

分配给 Worker 线程运行的脚本文件，必须与主线程的脚本文件同源。

（2）**DOM 限制**

Worker 线程所在的全局对象，与主线程不一样，无法读取主线程所在网页的 DOM 对象，也无法使用`document`、`window`、`parent`这些对象。但是，Worker 线程可以操作`navigator`对象和`location`对象。

（3）**通信联系**

Worker 线程和主线程不在同一个上下文环境，它们不能直接通信，必须通过消息完成。

（4）**脚本限制**

Worker 线程不能执行`alert()`方法和`confirm()`方法，但可以使用 XMLHttpRequest 对象发出 AJAX 请求。

（5）**文件限制**

Worker 线程无法读取本地文件，即不能打开本机的文件系统（`file://`），它所加载的脚本，必须来自网络。





# defer/async

在前端页面加载过程中，JavaScript代码的下载、解释和执行会阻塞页面的其他执行（此时页面会一片空白），浏览器在遇到body标签才开始呈现内容。

script标签在html文件中用于书写js代码，script标签一共有6个属性，所有script标签都会按照先后顺序依次被解析(带有async,defer属性除外)，**为了保证页面加载流畅，一般把script标签放在body标签的最后处**

- src：表示引入外部js文件，值为js文件地址

- type：language的替代属性，默认为text/JavaScript。一般来说可以省略。

- async：表示应该立即下载脚本，但不应妨碍页面中其他操作，如下载其他资源或者等待加载其他脚本，该属性只对外部引入的js文件生效(在XHTML文档中写作async="async")

- defer：表示脚本可以延迟到文档完全被解析和显示之后再被执行，该属性只对外部引入的js文件生效.(在XHTML文档中写作defer="defer")

- language(已废弃)：用于表示编写代码使用的脚本语言，如JavaScript，JavaScript1.2或VBScript等
  charset：通过src属性指定代码的字符集



## defer属性脚本延迟执行

    <script type="txet/javascript" defer src="url"></script>

defer属性只对引入的js文件生效，可以设置该脚本被延迟最后执行而不是由上到下依次被执行，最好页面只包含一个延迟脚本。
    

##  async属性设置异步响应

    <script type="txet/javascript" async src="js1"></script>
    <script type="txet/javascript" async src="js2"></script>

async只适用于外部引入的js文件，意思是**让浏览器立即下载脚本文件但不应妨碍页面中其他操作，脚本下载完成后立即执行**。指定async的目的是不让页面等待两个脚本文件下载完后再执行，而是异步加载页面其他内容，如下载其他资源或者等待加载其他脚本，**async设置的异步脚本之间并不保证按照指定他们的先后顺序执行**。

两个async的脚本之中js2可能会在js1之前执行，所以保证异步脚本之间没有逻辑顺序的关联是非常重要的。异步脚本一定会在页面的load事件前执行，但可能会在DOMContentLoaded事件触发之前或之后执行，所以异步脚本不要再加载期间修改DOM。







# 严格模式 "use strict";

- **严格模式下this没有设定或者没用new调用构造函数，this则会指向undefined**
- 不使用声明变量直接使用严格模式中将不通过，在循环中如果没有声明变量在非严格模式中很危险，i 会不小心溢出成为全局变量，在严格模式中会报错，严格模式中变量必须显示声明
- 严格模式下函数内不允许重名参数，不允许在非函数代码块（if，for等）声明变量
- 不能随意delete变量
- 没有with函数
- 可以为脚本(script标签)或函数开启严格模式
- 语法规范更加严格，class，super等关键字不能作为变量名





# BOM

操作浏览器

## BOMapi

- window.open('url')   打开一个窗口



- window.getComputedStyle(ele) 

   获取元素最终的样式,ie不兼容，ie8使用ele.currentStyle()获取

  ele.style读取的只是元素的内联样式（style可读可写，getComputedStyle只读），即写在元素行内的style属性上的样式；而 getComputedStyle 读取的样式是最终样式，包括了内联样式、嵌入样式和外部样式。

  

- window.scrollTo(0, 0)      跳转当前窗口位置，左侧位置0，顶部位置0



- window.event.preventDefault();  	阻止浏览器的默认的事件



- window.alert()	 系统对话框



- window.prompt()          系统弹出输入框



- window.confirm()        系统确认弹窗

有两个返回值，点击确定为true，点击取消为false



- window.onload    页面加载完成事件

```javascript
window.onload=function{};
//页面加载事件是在页面所有内容加载完毕后才会触发
```



- window.DOMContentLoaded    

  仅当DOM加载完成（不包括样式表，图片，flash）时就会触发事件



- window.onunload      页面关闭后事件，谷歌不支持

```javascript
 window.onunload = function{};
//页面关闭后才触发，只有ie8支持，谷歌不支持
```



- window.onbeforeunload    页面关闭前事件，谷歌不支持

```javascript
 window.onunload=function{};
//页面关闭前才触发，只有ie8支持，谷歌不支持
```





## window

​	BOM的核心对象是window，它表示浏览器的一个实例。在浏览器中，window对象有双重角色，它既是通过JavaScript访问浏览器窗口的一个接口，又是ECMAScript规定的Global对象。很多全局 JavaScript 对象（如 location 和 navigator）实际上都是 window 对象的属性，但window的属性一般都可以省略window，例如alert()等待

如果在全局环境中用var定义了对象、变量或函数都会被添加到window对象中。





## 阻止浏览器默认行为

- window.event.returnValue = false;
- event.preventDefault();





## location 对象

 location 是最有用的 BOM 对象之一，location提供了与当前窗口中加载的文档有关的信息，还提供了一些导航功能。
	事实上，location 对象时很特别的一个对象，因为它既是 window 对象的属性，也是 document 对象的属性；换句话说，window.location 和 document.location 引用的是同一个对象。

| 属性     | 作用                                           | 案例                  |
| -------- | ---------------------------------------------- | --------------------- |
| hash     | 设置或返回从井号 (#) 开始的 URL（锚）。        | "#contents"           |
| host     | 设置或返回主机名和当前 URL 的端口号。          | "www.wrox.com:80"     |
| hostname | 设置或返回当前 URL 的主机名。                  | "www.wrox.com"        |
| href     | 设置或返回完整的 URL。                         | "http://www.wrox.com" |
| pathname | 设置或返回当前 URL 的路径部分。                | "/WileyCDA/"          |
| port     | 设置或返回当前 URL 的端口号。                  | "8080"                |
| protocol | 设置或返回当前 URL 的协议。'http:'或者'https:' | "http:"               |
| search   | 设置或返回从问号 (?) 开始的 URL（查询部分）。  | "?q=javascript"       |



**location.方法**

| 方法      | 说明                   |
| :-------- | :--------------------- |
| assign()  | 载入一个新的文档       |
| reload()  | 重新载入当前文档       |
| replace() | 用新的文档替换当前文档 |



## navigator 对象

识别客户端浏览器的事实标准。

H5中navigator 对象有些属性可以访问用户地理位置等

- navigator.userAgent      查看当前浏览器信息



## screen对象

Screen 对象包含有关客户端显示屏幕的信息。

**Screen 对象属性**

| 属性        | 说明                                     |
| :---------- | :--------------------------------------- |
| availHeight | 返回屏幕的高度（不包括Windows任务栏）    |
| availWidth  | 返回屏幕的宽度（不包括Windows任务栏）    |
| colorDepth  | 返回目标设备或缓冲器上的调色板的比特深度 |
| height      | 返回屏幕的总高度                         |
| pixelDepth  | 返回屏幕的颜色分辨率（每象素的位数）     |
| width       | 返回屏幕的总宽度                         |



## history 对象

history 对象保存着用户上网的历史记录，从窗口被打开的那一刻算起。因为 history 是 window 对象的属性，因此每个浏览器窗口、每个标签页乃至每个框架，都有自己的 history 对象与特定的 window 对象关联。

```javascript
// 后退一页
history.go(-1);

// 前进一页
history.go(1);

// 前进两页
history.go(2);

// 后退一页
history.back();

// 前进一页
history.forward(); 
```







# DOM

操作网页文档

DOM的数据结构是一颗树，dom操作可能引起dom树的重新渲染，dom查询和渲染都比较耗费性能

```javascript
for(let i = 0 ; i< document.getElementsByTagName('p').length; i++)
// 每次循环都会查询，性能消耗较高
  
const pList = document.getElementsByTagName('p')
let plength = pList.length
for(let i = 0 ; i< plength; i++)  // 对dom节点进行缓存，提供性能
```

## DOMapi

| 方法                             |                                                              |
| -------------------------------- | ------------------------------------------------------------ |
| getElementById("id")             | 返回带有指定 ID 的元素。                                     |
| **querySelector("选择器")**      | 返回第一个符合选择器的元素。                                 |
| **querySelectorAll("选择器")**   | 返回第所有符合选择器的元素组成的伪数组。                     |
| getElementsByTagName("Tag")      | 返回包含带有指定标签名称的所有元素的节点列表（集合/节点数组）。 |
| getElementsByClassName("Class")  | 返回包含带有指定类名的所有元素的节点列表。                   |
| **appendChild(nodeObj)**         | 把参数新子节点添加进指定节点，如果参数是dom树上已有的节点，则是移动该节点。 |
| removeChild()                    | 删除子节点。                                                 |
| replaceChild()                   | 替换子节点。                                                 |
| insertBefore()                   | 在指定的子节点前面插入新的子节点。                           |
| **createAttribute()**            | 创建属性节点。                                               |
| **createElement('tagName')**     | 创建元素节点。                                               |
| createTextNode()                 | 创建文本节点。                                               |
| **getAttribute('name')**         | 返回元素指定的属性值。                                       |
| **setAttribute('name','value')** | 把调用元素的指定属性（例如class）设置或修改为指定的值。      |
| write('html')                    | 向节点中写入html代码并解析                                   |



- nodeObj.innerHTML      - 节点（元素）的包含html标签的文本值

  domObj.innerHTML属性设置为字符串，这串字符串会识别html并代替原本节点中的内容。

  ```javascript
  my$("dv").innerHTML="<p>添加一个p</p>"  
   //利用innerHTML识别标签的特性将p添加进div盒子"dv"中
   //innerHTML的代码会替换掉原来盒子dv中的代码
  ```




- innerText/textContent     - 指的是从起始位置到终止位置的文本值,但它去除Html标签。

  设置元素的文本内容并代替原有内容，不识别html

  两者区别在于：textContent属性,谷歌,火狐支持,IE8不支持，innerText属性,谷歌,火狐,IE8都支持。

  ```javascript
  my$("dv").innerText="<p>添加一个p</p>"  
  // p标签不会被识别，会当作字符串
  ```

  

- nodeObj.getAttribute('key')    -获取 节点（元素）的自定义属性节点

  nodeObj.setAttribute('key', 'value')    - 设置节点（元素）的自定义属性节点



- style属性 **控制元素的行内style属性**

  ```javascript
  不占位
  my$("dv").style.diaplay="none";
  占位
  my$("dv").style.visibility="hidden";
  my$("dv").style.opacity=0;
  my$("dv").style.height="0px";
  
  my$("dv").style.width = "300px";
  my$("dv").style.height = "200px";
  my$("dv").style.backgroundColor = "pink";
  
  //.属性等价于["属性"]  
  my$("dv").style.backgroundColor = "pink";
  my$("dv").style["backgroundColor"] = "pink";
  ```




- nodeObj.classList

  操作dom元素的class属性



- nodeObj.nodeType    属性返回节点的类型。nodeType 是只读的。

  | 元素类型 | NodeType |
  | :------- | :------- |
  | 元素     | 1        |
  | 属性     | 2        |
  | 文本     | 3        |



- nodeObj.nodeName 属性规定节点的名称。

  1）nodeName 是只读的

  2）元素节点的 nodeName 与标签名相同

  3）属性节点的 nodeName 与属性名相同

  4）文本节点的 nodeName 始终是 #text



- nodeObj.nodeValue 属性规定节点的值。

  1）元素节点的 nodeValue 是 undefined 或 null

  2）文本节点的 nodeValue 是文本本身

  3）属性节点的 nodeValue 是属性值



- 获取其他元素

  ```javascript
  //获取当前节点的父级元素
  node.parentElement
  
  //获取当前节点的子级元素数组
  node.children
  
  //获取当前节点的第一个子级元素
  node.firstElementChild
  
  //获取当前节点的最后一个子级元素
  node.lastElementChild
  
  //获取当前节点的前一个兄弟元素
  node.previousElementSibling
  
  //获取当前节点的后一个兄弟元素
  node.nextElementSibling
  ```

  

  获取其他节点

  childNodes方法以及firstChild方法在现代浏览器中使用，都会把元素标签中的空白节点检测出来，而children方法则是只检测element元素节点
  
  ```javascript
  //获取当前节点的所有子级节点数组
  node.childNodes
  
  //获取当前节点的父级节点
  node.parentNode
  
  //获取当前节点的第一个子级节点
  node.firstChild
  
  //获取当前节点的最后一个子级节点
  node.lastChild
  
  //获取当前节点的前一个兄弟节点
  node.previousSibling
  
  //获取当前节点的后一个兄弟节点
  node.nextSibling
  ```



- doucument.domain 	属性可返回当前文档的服务器域名。



- prompt()  方法用于显示一个可提示用户进行输入的对话框。



- Element.getBoundingClientRect()   方法返回元素的大小及其相对于视口的位置。

  返回值是一个 DOMRect对象，这个对象是由该元素的 getClientRects()方法返回的一组矩形的集合，就是该元素的 CSS 边框大小。返回的结果是包含完整元素的最小矩形，并且拥有`left`, `top`, `right`, `bottom`, `x`, `y`, `width`, 和 `height`这几个以像素为单位的只读属性用于描述整个边框。除了`width` 和 `height` 以外的属性是相对于视图窗口的左上角来计算的。

  

- Element.dataset  这个简单的API能够让用户`get`或`set`HTML页面元素上的`data-*`属性。

  可以自定义`data-*`属性，然后通过这个api快速拿到

```html
<div id="myDiv" 
	data-name="myDiv" 
  data-id="myId"
	data-my-custom-key="This is the value"
>
</div>
       
<script>
// Get the element
var element = document.getElementById("myDiv");

// Get the id
var id = element.dataset.id;

// Retrieves "data-my-custom-key"
var customKey = element.dataset.myCustomKey;
  
// Sets the value to something else
element.dataset.myCustomKey = "Some other value";
</script>
```



## nodeType

nodeObj.nodeType    属性返回节点的类型。nodeType 是只读的。

| 元素类型 | NodeType |
| :------- | :------- |
| 元素     | 1        |
| 属性     | 2        |
| 文本     | 3        |



## classList

是h5新增的api，可以操作dom元素的class属性

```javascript
document.getElementById("myDIV").classList.add("mystyle");
```

| 方法                          | 描述                                                         |
| :---------------------------- | :----------------------------------------------------------- |
| add(*class1, class2, ...*)    | 在元素中添加一个或多个类名。  如果指定的类名已存在，则不会添加 |
| contains(*class*)             | 返回布尔值，判断指定的类名是否存在。可能值：true - 元素包已经包含了该类名false - 元素中不存在该类名 |
| item(*index*)                 | 返回元素中索引值对应的类名。索引值从 0 开始。  如果索引值在区间范围外则返回 *null* |
| remove(*class1, class2, ...*) | 移除元素中一个或多个类名。                                   |
| toggle(*class,* true\|false)  | 在元素中切换类名。  第一个参数为要在元素中移除的类名，并返回 false。 如果该类名不存在则会在元素中添加类名，并返回 true。  第二个是可选参数，是个布尔值用于设置元素是否强制添加或移除类，不管该类名是否存在。例如：  移除一个 class: *element*.classList.toggle("classToRemove", false); 添加一个 class: *element*.classList.toggle("classToAdd", true); |





## open/close

```
document.open()
document.close()
```

html页面渲染时会开启文档流，渲染完毕后会关闭文档流

open() 方法可打开一个新文档，并擦除当前文档的内容。该方法将擦除当前 HTML 文档的内容，开始一个新的文档，并开启文档流，用 write() 方法或 writeln() 方法编写。文档流编写结束后应该调用close()方法关闭这个文档流，并显示选定的数据在页面上。

也就是open打开文档流（会清空当前页面上的内容），然后使用write写入新html内容，最后使用close()关闭文档流并将html渲染在页面上。



## write

 `document.write()`是Javascript中对`document.open()`所开启的文档流操作的API方法。

它能够直接将文档流中写入字符串，一旦文档流已经关闭，那么 document.write()就会重新运用document.open()打开新的文档流并写入，此时原来的文档流会被清空，已经渲染好的页面就会被覆盖，浏览器将重新构建DOM并渲染新的页面。也就是说write覆盖原本内容与否要看原本的文档流是否关闭。

利用`document.write`来在HTML中插入标签非常方便，就如同让浏览器在解析HTML的时候动态添加标签，而且只需要一行代码即可，不需要使用`document.createElement`再将其插入到DOM中，但是注意使用该write是原本网页的文档流是否已经关闭了。



浏览器就解析HTML构建DOM的时候，如果遇到`script`就会暂停下来，解析`script`中的代码并执行，然后再继续解析剩余HTML。因为执行write时文档的文档流还没结束，所以write不会覆盖原本的内容.

```html
<body>
<h1>Head</h1>
    <script>
        document.write('<p>hello document</p>');
    </script>
<h2>Tail</h2>
</body>
<!--
Head
hello document
Tail
-->
```



一旦html输入流关闭了再使用write就会覆盖原本的html文档

```html
<body>
<h1>Head</h1>
    <script>
        setTimeout(function(){
            document.write('<p>hello document</p>');
        }, 500);
    </script>
<h2>Tail</h2>
</body>
<!--
hello document
-->
```



### 应用场景

（1）加载需要配合JS脚本使用的外部CSS文件

利用下面的语句加载外部样式文件：

```javascript
<script>document.write('<link rel="stylesheet" href="style_neads_js.css">');</script>
```

​       将所有需要用到JS的样式都放到这个外部样式表中，如果浏览器不禁用JS，那么该样式表就会被顺利加载，否则页面就不会使用该样式。



（2）在新的窗口中写入新的页面数据时（新建一个页面就不会重写之前的）

既然在一个已加载完成的页面中调用`document.write`会重写整个页面，那么在一个新的窗口的空白页面中调用这个方法，就不存在这样的的问题了。

另外，在调用`document.write`，最好不要把`document.open`和`document.close`漏掉，尽管多数时候浏览器会帮忙完成这些操作。即，一个标准的document.write应该是这样的：

```javascript
document.open();document.write('anthing')document.close();
```







## 三大系列

### offset

- offset系列：获取元素最终的样式（offset会加上border，padding一起计算出最终值）

  获取宽高属性值：offsetWidth，offsetHeight  （会加上border，padding）

  获取定位属性值：offsetTop，offsetLeft

  子级元素没脱标时得到的offsetLeft/Top值包含父级元素的margin，padding和border值和自己的margin，left值，脱标后和父级元素没有关系，只和自己的left值和自己的margin的值有关系。
  
  ```javascript
  获取格式为offset+属性名首字母大写
  offsetWidth,offsetHeight
  offsetTop,offsetLeft:子级元素没脱标时得到的offsetLeft/Top值包含父级元素的margin，padding和border值和自己的margin，left值，脱标后和父级元素没有关系，只和自己的left值和自己的margin的值有关系。
  
  my$("box").offsetLeft
  //获取定位中left属性值
  my$("box").offsetWidth
  //获取width属性值      
  ```
  
  

### scroll

- scroll系列：获取元素卷曲属性

  **获取元素实际的宽高**，包括需要滚动条滚动出来的部分（没有边框），如果没有内容或者内容没有超出元素范围那这个值就是元素的宽高

​	scrollWidth：元素中内容的实际的宽(如果内容很少,没有内容,元素自身的宽),没有边框

​	scrollHeight：元素中内容的实际的高(如果内容很少或者没有内容,元素自身的高),没有边框

​	scrollLeft：当前元素滚动条已经向左滚动的距离

​	scrollTop： 当前元素滚动条已经向上滚动的距离

```javascript
// 案例：固定导航栏
function getScroll(){//将获取代码封装成一个函数
return { 
//获取页面卷曲的距离的兼容代码
//不同浏览器兼容代码不同，都不支持返回0
top: window.pageYoffset||document.documentElement.scrollTop||document.body.scrollTop||0;
//获取页面向左卷曲的距离的兼容代码
left: window.pageXoffset||document.documentElement.scrollLeft||document.body.scrollLeft||0;
};//返回一个无名对象存储Top和Left值
}

window.onscroll=function(){//浏览器滚动事件
  console.log(getScroll().top);  //调用封装函数输出页面向上卷曲值
};
```



### client

client系列获取元素可视区域的属性

```
console.log(ele.clintX);//对象e当前可视区域x坐标，所有浏览器支持 
console.log(ele.clintY);//对象e当前可视区域y坐标，所有浏览器支持
* clientWidth:元素可视区域的宽(没有边框),边框内部的宽度
* clientHeight:元素可视区域的高(没有边框),边框内部的高度
* clientLeft:获取左边框的宽度
* clientTop:获取上边框的宽度

// 获取当前窗口可是区域宽高
var w = document.documentElement.clientWidth;
var h = document.documentElement.clientHeight;
```






## 新增节点

1.创建dom节点  document.createElement('tagName')

```javascript
const div = document.createElement('div')
div.innerHTML = 'hello,world'
```



2.插入dom节点  domObj.appedChild(domObj)

```javascript
div1.appedChild(div)  // 将上一步创建的dom元素插入div1中
```





## property

DOM节点上的property是dom节点上原生自带的属性，例如style，className，nodeName，nodeType等，这些属性不会显示在html标签上。这些属性直接用dom节点调用即可访问到。

设置property属性不会显示在html标签上



## attribute

attribute是dom节点上设置的自定义属性，会显示在html标签上，dom节点需要通过api访问。

设置attribute属性会显示在html标签上

attribute有两个api：

- domObj.getAttribute('key')  获取标签上的自定义属性

```javascript
<li score="60">student1</li>//score为自定义属性
<li score="30">student2</li>
...
<script>
    var lis = getElementsByTagName("li");
    for(var i=0;i<lis.length;i++){
        //alert(this.score);
        //undefined, 自定义属性无法直接调用
        alert(this.getAttribute("score"));
        //getAttribute是对象专门用于获取自定义属性的方法
    }
</script>
```



- domObj.setAttribute('key', value)  设置获取标签上的自定义属性

```javascript
var lis = my$("ul").getElementsByTagName("li");
    for(var i=0;i<lis.length;i++){
        //lis[i].score=60;
        //为每个li标签的dom对象都添加60的score自定义属性值,但标签中没有
        lis[i].setAttribute("score","60");
        //用setAttribute方法为html标签内自定义属性添加60的值
        //这个score="60"会添加进html标签中
        //例如<li score="60">student1<li>
    }
```







# 事件

为元素绑定事件(DOM):

1. 点方法直接添加on事件，事件名有on，所有浏览器都兼容，但一个对象的一种类型的事件只能有一项，

   点方法添加事件，**但一个对象的一种类型的事件只能有一项**，重复相同的事件以最后一项为准，如为一个按钮注册五个点击事件，点击按钮时执行最后一项点击事件。

   

2. `对象.addEventListener("没on事件类型",事件处理函数,boolean);`

   **false用于控制事件阶段是冒泡，改为true为事件捕获**

   **事件名没有on,谷歌和火狐支持,IE8不支持**

   解绑事件：`对象.removeEventListener("没on事件类型",事件处理函数,boolean)`

   

3. `对象.attachEvent("有on的事件类型",事件处理函数)`

   **事件名有on,谷歌不支持,火狐不支持,IE8支持**

   解绑事件：`对象.detachEvent("on事件类型",事件处理函数);`



第一种绑定事件的方式成为dom0级，第二（三）种称为dom2级，主要区别有

- dom0级同一元素一种类型的事件只能有一个方法，dom2级基于事件池机制完成，每增加一个绑定的方法，就会往事件池里丢一个，当事件触发时就会依次执行事件池内的事件，发布订阅其实就是模拟的事件池机制（可以给同一元素的某个事件绑定多个不同的方法）
- dom0只能绑定默认的事件类型，dom2可以绑定自定义事件类型和一些特殊的事件加载类型（例如DOMContentLoaded）



## 事件类型

click   点击事件

dblclick 双击事件

focus   获得焦点事件

blur    失去焦点事件   

mouseover 鼠标进入事件

mouseout  鼠标离开事件

mouseup   鼠标抬起事件

mousemove 鼠标移动事件

keyup   键盘抬起事件

input  输入事件





## 触发器

直接触发一次事件

    对象.click();
    //让对象自动触发一次他绑定的click点击事件



## event

事件处理函数中有一个参数event（e），这个参数和事件有关系，是一个事件处理对象，谷歌和火狐支持直接调用e，ie8中没有e，ie8中用window.event来代替。Event 对象代表事件的状态，比如事件在其中发生的元素、键盘按键的状态、鼠标的位置、鼠标按钮的状态。



- event.target 	指向触发该事件的节点



- event.currentTarget 	指向绑定事件的元素



- event.eventPhase	判断事件阶段

    用事件参数对象.enentPhase的值可以判断事件的阶段，返回值为123
    e.enentPhase
    1----事件捕获阶段（从外向内）
    2----事件目标阶段（最开始选择的事件）
    3----事件冒泡阶段（从里到外）




## 事件池机制

通过addEventListener/attachEvent往事件池加方法，removeEventListener/detachEvent移除方法，新版浏览器会对新增的方法进行重复校验，一模一样的方法会被去重

当事件行为触发后，会把这个事件的事件池中的方法按照增加的顺序依次执行，但ie6-8执行顺序不固定



## 特殊事件

- DOMContentLoaded  

  DOM树加载完成就会触发

- transitionend

  css3动画结束后触发



## 事件冒泡/捕获

DOM事件流：1.事件捕获阶段  2.处于目标阶段  3.事件冒泡阶段

触发一个事件，先执行处于事件捕获的父元素，然后执行到子元素后再依次往外执行处于冒泡阶段的父元素



事件冒泡：点击子元素，事件先执行子元素的事件，然后依次向外会执行父元素的相同事件，所有事情绑定时默认事件冒泡。

事件捕获：点击子元素，事件先从父元素的同样的事件开始执行，然后再执行子元素的相同事件



阻止事件往外冒泡：

谷歌火狐：	event.stopPropagation()

Ie8: 	window.event.cancelBubble=true



## 阻止浏览器默认行为

- window.event.returnValue = false;
- event.preventDefault();



## 事件委托

e是浏览器的事件默认处理参数，作为事件处理函数的参数传入(ie不用主动传入，ie使用`window.event`代替)

绑定在父元素上的事件，子元素也能触发，并利用`e.target`可以获取具体触发事件的子元素(ie是`window.event.srcElement`)

```javascript
document.getElementById('box').addEventListener("click",function(e){
	var event = e||window.event  //兼容处理
	var target = event.target||event.srcElement //兼容处理
	if(target.nodeName == "p"){
		console.log('事件由p标签触发')
	}
	if(target.nodeName == "div"){
		console.log('事件由div标签触发')
	}
})
```

**事件委托的好处**

1）绑定事件越多，浏览器所占内存越大，性能越低，可以只给父元素绑定事件，子元素通过事件委托，触发事件处理函数内相应代码可以提高性能

2）ajax实现局部刷新，导致每次加载完，都要重新绑定事件，可以只给父元素绑定事件，子元素更新不会重新绑定事件

3）部分浏览器移除元素时不会移除元素绑定的事件，导致内存泄漏

4）代码耦合低，利于后期维护



## 自定义事件

`DOM`也提供了类似node的`EventEmitter`的`API`，基本使用：

```js
//1、创建事件
var myEvent = new Event("myEvent");

//2、注册事件监听器
elem.addEventListener("myEvent",function(e){
  ...
})

//3、触发事件
elem.dispatchEvent(myEvent);
```






# 栈内存和堆内存

在js引擎中对变量的存储主要有两种位置，**堆内存和栈内存**。

栈内存给人的感觉就像一个线性排列的空间，每个小单元大小基本相等。

和java中对内存的处理类似，**栈内存**主要用于存储各种**基本类型的**变量，包括Boolean、Number、String、Undefined、Null，symbol以及**对象变量的指针（引用类型的内存地址）**，由操作系统自动分配释放存放函数的参数值、局部变量的值等也存在栈里，像我们浅拷贝时，都只复制栈上的内容，所以基本类型会重新复制一份，而引用类型只会复制一份指针出来，两个指针实际指向同一块地址同一个对象。

当脚本要调用一个函数时，JS解析器把该函数推入执行栈（函数调用栈）中（push）并执行，当函数运行结束后，JS解析器将它从堆栈中推出（pop）



而堆内存主要负责像对象Object这种引用类型变量的存储。堆内存一般由程序员分配释放，若程序员不释放，由垃圾回收机制回收。





# 垃圾回收

JS有垃圾处理器，所以无需手动回收内存，而是由垃圾处理器自动处理。一般来说，垃圾处理器有自己的回收策略。

譬如对于那些执行完毕的函数，如果没有外部引用（被引用的话会形成闭包），则会回收。（当然一般会把回收动作切割到不同的时间段执行，防止影响性能）

常用的两种垃圾回收规则是：

- 标记清除
- 引用计数

Javascript引擎基础GC方案是（`simple GC`）：`mark and sweep`（标记清除），步骤为

1. 遍历所有可访问的对象。
2. 回收已不可访问的对象。



- 标记清除

当变量进入环境时，例如，在函数中声明一个变量，就将这个变量标记为“进入环境”。

从逻辑上讲，永远不能释放进入环境的变量所占用的内存，因为只要执行流进入相应的环境，就可能会用到它们。

而当变量离开环境时，则将其标记为“离开环境”。

垃圾回收器在运行的时候会给存储在内存中的所有变量都加上标记（当然，可以使用任何标记方式）。

然后，它会去掉环境中的变量以及被环境中的变量引用的变量的标记（闭包，也就是说在环境中的以及相关引用的变量会被去除标记）。

而在此之后再被加上标记的变量将被视为准备删除的变量，原因是环境中的变量已经无法访问到这些变量了。

最后，垃圾回收器完成内存清除工作，销毁那些带标记的值并回收它们所占用的内存空间。



- 引用计数

```javascript
let a = {name:'wtw'}
//这段代码的意思是 内存开辟一块空间放入{name:"wtw"}数据，并让指针a指向他
let b = a
//指针b指向指针a指向的内存地址
```

变量a和b指向同一块内存地址，此时这块地址被引用次数为2

```javascript
a = null
//让指针a指向空，此时那块内存地址只有b引用
console.log(b)
//{name:'wtw'}

b=null
//b也指向空
```

设置a和b都指向null后，没有人指向那块内存地址，引用次数为0，此时原来那块地址变为了垃圾，系统的垃圾回收会自动把这块地址回收





# 变量

变量声明了但未赋值使用会输出`undefined`

未声明就直接使用会报错`  is not defined`



## 数据类型

JS中有六种基本类型（值类型）和一种引用类型(对象，数组，null，function)

基本类型比较只要相等，就可以用==或者===来比较，但是引用类型，即使let s = {};let s1 = {};但他们的内存地址不一样，比较依然不相等。

值类型是存储在栈上，引用类型（对象，数组，null，function）的指针（内存地址）存储在栈上，内容存储在堆上



**ps：为什么基本类型也可以使用js提供的同名构造函数提供的方法？**

​	基本数据类型被当作构造函数创建的一个对象来使用时（基本包装类型）， JS 会将其转换为一个对象，以便其可以使用对象的特性（如存在原型链中的方法），使用完后抛弃对象性质，并将它变回到原始数据类型

​	例如string在typeof查看类型时基本数据类型，但是可以使用构造函数String提供的一系列方法，使用这些方法时他被当做String实例，使用完后又被回退到基本类型string

​	所以为什么var一个字符串用typeof进行检测的时候是基本类型string，却可以使用引用类型String的方法，但如果new一个String实例，那这个实例始终都是Object类型，永远不会回退到string类型

例如使用`'1'.toString`，其实在这个语句运行的过程中做了这样几件事情：

```js
var s = new Object('1');
s.toString();
s = null;
```

第一步: 创建Object类实例。注意为什么不是String ？ 由于Symbol和BigInt的出现，对它们调用new都会报错，目前ES6规范也不建议用new来创建基本类型的包装类。

第二步: 调用实例方法。

第三步: 执行完方法立即销毁这个实例。

整个过程体现了`基本包装类型`的性质，而基本包装类型恰恰属于基本数据类型，包括Boolean, Number和String。



###  7种基本数据类型

#### number

JavaScript数字都是浮点型，支持二进制、八进制、十进制和十六进制

**字符串类型用*1会尝试转换成数字类型，乘其他数字会返回NAN**

JS 中的`Number`类型只能安全地表示`-9007199254740991 (-(2^53-1))` 和`9007199254740991(2^53-1)`之间的整数，任何超出此范围的整数值都可能失去精度。

JS 提供`Number.MAX_SAFE_INTEGER`常量来表示 最大安全整数，`Number.MIN_SAFE_INTEGER`常量表示最小安全整数

当不是数字类型的变量调用数字类型的方法或者不是两个数字类型使用* / - 运算符会返回NAN，typeof NAN为number

```javascript
console.log(typeof NAN) //number
```



由于JS仅有Number这个数值类型，而Number采用的时IEEE 754 64位双精度浮点数编码。而浮点数表示方式具有以下特点：

- 浮点数可表示的值范围比同等位数的整数表示方式的值范围要大得多；

- 浮点数无法精确表示其值范围内的所有数值，而有符号和无符号整数则是精确表示其值范围内的每个数值；

- 浮点数只能精确表示m*2e的数值；

- 当biased-exponent为2e-1-1时，浮点数能精确表示该范围内的各整数值；

-  当biased-exponent不为2e-1-1时，浮点数不能精确表示该范围内的各整数值。

由于部分数值无法精确表示（Number类型的有效整数范围是-0XFFFFFFFFFFF至0X1FFFFFFFFFF,所以无法精确到超过这个范围的整数），于是在运算统计后偏差会愈见明显。



js 能表示的最大值为1 * (2^53 - 1) * (2^971) = 1.7976931348623157e+308，而这个值恰好是 **Number.MAX_VALUE** 的值；同理可以推出 js 能表示的大于 0 的最小值是1 * 1 * (2 ^ -1074) = 5e-324，这个值恰好是 **Number.MIN_VALUE** 的值。

当十进制小数的二进制表示的有限数字超过 52 位时，在 JavaScript 里是不能精确存储的，所以js小数浮点数可能会产生精度丢失问题（只要是采用IEEE 754 Floating-point的浮点数编码方式来表示浮点数时，则会产生这类问题），最著名的就是

```
0.1+0.2=== 0.30000000000000004
```

数字都是以二进制存储的，所以我们要先将 0.1 和 0.2 转化成二进制，对于十进制转二进制，整数部分除二取余，倒序排列，小数部分乘二取整，顺序排列，所以

0.1 转化为二进制
0.0 0011 0011 0011 0011 0011 0011 ... （0011循环）

0.2 转化为二进制
0.0011 0011 0011 0011 0011 0011 0011 ... （0011循环）

由于小数无限循环，两者相加后二进制小数会溢出52位，导致小数部分超出js的存储范围，会自动四舍五入，导致最终计算结果转为十进制后为0.30000000000000004



解决方案

- ES6提供的Number.EPSILON方法来代替===进行比较

```javascript
function numbersequal(a,b){ 
	return Math.abs(a-b)<Number.EPSILON;
} 
var a=0.1+0.2， b=0.3;
console.log(numbersequal(a,b)); //true
```



- 把计算数字 提升 10 的N次方 倍 再 除以 10的N次方。

```js
(0.1*1000+0.2*1000)/1000==0.3
// true
```



#### boolean

以下变量转变为bool类型后为false，其余变量变为bool为true

```javascript
!!0 === false

!!NAN === false

!!'' === false

!!null === false

!!undefined === false

!!false === false

!![] === true
```

以上变量用于与（&&）或（||）非（!）判断时当做false

```javascript
console.log(''||'abc') //abc
```



空数组比较特殊，**当空数组作为if判断条件时,相当于true。当空数组与布尔值直接比较时,相当于false**

```javascript
console.log([] == false) // true
console.log(!![] === true) // true
```





#### null

typeof null时，会显示null是一个Object类型，原理是，不同的类型的数据在底层都表示为二进制，以32位为单位存储，其中包含一个小型标记（1-3位）和实际的数据值。在javascript中要是二进制前三位小型标记都是0的话就表示对象，而null的二进制都是0，那么前三位自然也是0，就被认为是object，所以typeof null 返回的是object。这是一个无法修复的历史遗留错误。



#### undefined



#### string

es6模板字符串，可以换行，可以通过${}使用变量

```javascript
const name = 'wtw'
const a = `hello, ${name}` // hello, wtw
```



#### symbol (ES6)

Symbol是由ES6规范引入的一项新特性，它的功能类似于一种标识唯一性的ID。通常情况下，通过调用Symbol()函数来创建一个Symbol实例

```javascript
let s1 = Symbol('这个Symbol的描述信息,不一定是字符串类型，可以是任意类型')
let s2= Symbol('这个Symbol的描述信息,不一定是字符串类型，可以是任意类型')
console.log(s1)//Symbol(这个Symbol的描述信息,不一定是字符串类型，可以是任意类型)
console.log(s1==s2)//false
```

​	由于`Symbol`是一种基础数据类型，所以当我们使用`typeof`去检查它的类型的时候，它会返回一个属于自己的	类型`symbol`

​	每个Symbol实例都是唯一的。因此，当你比较两个直接定义的Symbol实例的时候，将总会返回`false`

##### Symbol.for()

Symbol.for()也可以定义symbol类型，与直接定义不同的是，这种方式定义的symbol记录在内存注册表中，下次用for再定义描述信息一样的symbol时，直接返回上次定义的Symbol

```javascript
let a = Symbol.for('a')
let b = Symbol.for('a')
console.log(a==b)
//true
```

##### Symbol.keyFor()

Symbol.keyFor()能输出用for方法创建的注册表中的Symbol类型的描述信息，但直接定义的symbol类型则无法找到，输出undefined

```javascript
let s = Symbol.for("脚本之家");
console.log(Symbol.keyFor(s));  //"脚本之家"
let s1 = Symbol("脚本之家");
console.log(Symbol.keyFor(s1));	// undefined
```

##### Symbol.description

`Symbol.description`可以输出symbol类型的描述信息

```javascript
let s1 = Symbol('这个Symbol的描述信息,不一定是字符串类型，可以是任意类型')
console.log(sl.description)
//这个Symbol的描述信息,不一定是字符串类型，可以是任意类型
```

##### Symbol的使用场景

​	**应用场景1：使用Symbol来作为对象属性名(key)**

Symbol类型的key是不能通过`Object.keys()`或者`for...in`来枚举的，它未被包含在对象自身的属性名集合(property names)之中。所以，利用该特性，我们可以把一些不需要对外操作和访问的属性使用Symbol来定义。

也正因为这样一个特性，当使用`JSON.stringify()`将对象转换成JSON字符串的时候，Symbol属性也会被排除在输出内容之外

```javascript
const PROP_NAME = Symbol()
let obj = {
  [PROP_NAME]: "一斤代码"
}
obj[PROP_NAME] // '一斤代码'
```

```javascript
let obj = {
   [Symbol('name')]: '一斤代码',
   age: 18,
   title: 'Engineer'
}

Object.keys(obj)   // ['age', 'title']

for (let p in obj) {
   console.log(p)   // 分别会输出：'age' 和 'title'
}
//输出普通属性并存入数组
Object.getOwnPropertyNames(obj)   // ['age', 'title']
// 使用Object.getOwnPropertySymbols的API输出对象中的Symbol并存入数组
Object.getOwnPropertySymbols(obj) // [Symbol(name)]
//Reflect.ownKeys(obj)的API可以输出所有属性并存入数组
Reflect.ownKeys(obj) // [Symbol(name), 'age', 'title']
```

​	我们可以利用这一特点来更好的设计我们的数据对象，让“对内操作”和“对外选择性输出”变得更加优雅。

​	当然也有一些api能专门输出对象中的Symbol属性：`Reflect.ownKeys(obj)`和`Object.getOwnPropertySymbols(obj)`

```javascript
// 使用Object的API
Object.getOwnPropertySymbols(obj) // [Symbol(name)]
```

**应用场景2：使用Symbol来替代常量**

```javascript
//用const定义常量还需要附上无用的值
const TYPE_AUDIO = 'AUDIO'
const TYPE_VIDEO = 'VIDEO'
const TYPE_IMAGE = 'IMAGE'



//现在利用Symol的唯一性定义三个常量
const TYPE_AUDIO = Symbol()
const TYPE_VIDEO = Symbol()
const TYPE_IMAGE = Symbol()
```

**应用场景3：使用Symbol来给添加对象同名属性**

对象中普通的同名属性作为键，后出现的会覆盖前面的

使用Symbol类型唯一标识作为存储的键可以尽量避免这种问题

例如缓存容器就是类似这样的思想来存储数据

```javascript
let user1 = {
	name:'wtw',
	key:Symbol()
}
let user2 = {
	name:'wtw',
	key:Symbol()
}
let grade = {
//对象中key使用变量要加[]
	[user1.key]:{js:100,css:89}
	[user2.key]:{js:90,css:80}
}
console.log(grade[user2.key])
```

##### 缓存容器

前端组件化开发中，有一些数据是所有组件都需要使用的，我们就可以将这部分数据放入缓存容器中，但如果将这部分数据以字符串的形式存入，可能出现重名，导致数据被覆盖，这时使用Symbol解决这个问题

```javascript
//Cache缓存池
class Cache{
	static data ={};
	//公共数据存进data中
	//set存入方法
	static set(name,value){
		return (this.data[name]=value)
	}
//get提取方法
	static get(name,value){
		return this.data[name]
	}
}

Cache.set('name','wtw')
//将键值对'name':'wtw'存入缓存池中
console.log(Cache.get('name'))
//'wtw'

let user ={
    name:'apple',
    price:'11',
    key:Symbol('水果')
}

let cart ={
    name:'orange',
    price:'12',
    key:Symbol('水果')
}
//将对象的key对应的唯一Symbol属性作为这个存入缓存池时对象的唯一标识
//这样即使字符串的描述信息同名也不会冲突
//缓存池中user和cart是值，他们的key属性对应的唯一Symbol属性才是键
Cache.set(user.key,user)
Cache.set(cart.key,cart)
console.log(Cache.get(user.key))
//{ name: 'apple', price: '11', key: Symbol(水果) }
```



#### bigint (ES10)

在此标准下，无法精确表示的非常大的整数将自动四舍五入。确切地说，JS 中的`Number`类型只能安全地表示`-9007199254740991 (-(2^53-1))` 和`9007199254740991(2^53-1)`之间的整数，任何超出此范围的整数值都可能失去精度。

```
BigInt是一种新的数据类型，用于当整数值大于Number数据类型支持的范围时。这种数据类型允许我们安全地对大整数执行算术操作，表示高分辨率的时间戳，使用大整数id，等等，而不需要使用库。
```

`BigInt`数据类型的目的是比`Number`数据类型支持的范围更大的整数值。在对大整数执行数学运算时，以任意精度表示整数的能力尤为重要。使用`BigInt`，整数溢出将不再是问题。

此外，可以安全地使用更加准确时间戳，大整数ID等，而无需使用变通方法。 `BigInt`目前是第3阶段提案， 一旦添加到规范中，它就是JS 第二个数字数据类型。现在的兼容性并不怎么好，只有chrome67、firefox、Opera这些主流实现。

在JS中，所有的数字都以双精度64位浮点格式表示，那这会带来什么问题呢？

这导致JS中的Number无法精确表示非常大的整数，它会将非常大的整数四舍五入，确切地说，JS中的Number类型只能安全地表示-9007199254740991(-(253-1))和9007199254740991（(253-1)），任何超出此范围的整数值都可能失去精度。

```js
console.log(999999999999999);  //=>10000000000000000
```

同时也会有一定的安全性问题:

```js
9007199254740992 === 9007199254740993;    // → true 居然是true!
```



##### 创建BigInt

要创建BigInt，只需要在数字末尾追加n即可。

```js
console.log( 9007199254740995n );    // → 9007199254740995n 
console.log( 9007199254740995 );     // → 9007199254740996
```

另一种创建BigInt的方法是用BigInt()构造函数、



```js
BigInt("9007199254740995");    // → 9007199254740995n
```

简单使用如下:

```js
10n + 20n;    // → 30n  
10n - 20n;    // → -10n 
+10n;         // → TypeError: Cannot convert a BigInt value to a number 
-10n;         // → -10n 
10n * 20n;    // → 200n 
20n / 10n;    // → 2n   
23n % 10n;    // → 3n   
10n ** 3n;    // → 1000n    

const x = 10n;  
++x;          // → 11n  
--x;          // → 9n
console.log(typeof x);   //"bigint"
```



##### 值得警惕的点

- BigInt不支持一元加号运算符, 这可能是某些程序可能依赖于 + 始终生成 Number 的不变量，或者抛出异常。另外，更改 + 的行为也会破坏 asm.js代码。



- 因为隐式类型转换可能丢失信息，所以不允许在bigint和 Number 之间进行混合操作。当混合使用大整数和浮点数时，结果值可能无法由BigInt或Number精确表示。

```js
10 + 10n;    // → TypeError
```



- 不能将BigInt传递给Web api和内置的 JS 函数，这些函数需要一个 Number 类型的数字。尝试这样做会报TypeError错误。

```js
Math.max(2n, 4n, 6n);    // → TypeError
```



- 当 Boolean 类型与 BigInt 类型相遇时，BigInt的处理方式与Number类似，换句话说，只要不是0n，BigInt就被视为truthy的值。

```js
if(0n){//条件判断为false

}
if(3n){//条件为true

}
```



- 元素都为BigInt的数组可以进行sort。

- BigInt可以正常地进行位运算，如|、&、<<、>>和^



### **引用类型**

​	**引用类型**包含

​		普通对象-Object，

​		数组对象-Array，

​		正则对象-RegExp，

​	    日期对象-Date，

​		数学函数-Math，

​		函数对象-Function

对象内键值对的键如果出现同名属性，后面的同名属性会覆盖前面的属性,并且键类型如果为其他类型会自动转换为字符串类型，并且不显示双引号

```
let obj ={
1:"wtw",
"1":"abc"
}
console.log(obj)
//{1:"abc"}
```



#### 对象转原始类型

对象转原始类型，会调用内置的[ToPrimitive]函数，对于该函数而言，其逻辑如下：

1. 如果Symbol.toPrimitive()方法，优先调用再返回
2. 调用valueOf()，如果转换为原始类型，则返回
3. 调用toString()，如果转换为原始类型，则返回
4. 如果都没有返回原始类型，会报错

```js
var obj = {
  value: 3,
  valueOf() {
    return 4;
  },
  toString() {
    return '5'
  },
  [Symbol.toPrimitive]() {
    return 6
  }
}
console.log(obj + 1); // 输出7
```



#### 引用类型垃圾回收机制

```javascript
let a = {name:'wtw'}
//这段代码的意思是 内存开辟一块空间放入{name:"wtw"}数据，并让指针a指向他
let b = a
//指针b指向指针a指向的内存地址
```

变量a和b指向同一块内存地址，此时这块地址被引用次数为2

```javascript
a = null
//让指针a指向空，此时那块内存地址只有b引用
console.log(b)
//{name:'wtw'}

b=null
//b也指向空
```

设置a和b都指向null后，没有人指向那块内存地址，引用次数为0，此时原来那块地址变为了垃圾，系统的垃圾回收会自动把这块地址回收





## null和undefined

基本类型未赋值时初值为`undefined` ，引用类型未赋值时初值为`null`

let/var声明一个变量，会默认为`undefined` ，const声明必须赋值





## var

所有用var声明的变量都会挂载到window全局对象下，可以直接用变量名访问，也可以使用`window.变量`来访问

这样做可能会污染原来window的自带属性

全局var的变量如果挂载到window下则不能被deleted给删除掉



### 普通变量提升

```javascript
//变量可以不声明直接使用，变量会被当做window的属性，变成一个全局变量
//这个变量不会被垃圾回收清理，容易造成内存泄漏
//严格模式下会报错
a = 'wtw'
console.log(a)
//wtw
```

```javascript
console.log(web) //undefined，变量声明提升了，赋值没有提升
//undefined  因为有变量提升，var web会被提前到最开始，但是没赋值
var web = (url = name ="hdcms");
//变量会提升 var web 会被提前，但赋值不会提升
//可以一次对多个值赋值，web，url，name全为"hdcms"
console.log(web,url,name)
//hdcms hdcms hdcms
```

```javascript
function run(a=b,b=3){
  //报错  不声明变量直接使用会报错
}
run()
```

```javascript
function run(a=3,b=a){
    //正常运行，先定义a=3，再把a的值赋值给b
    console.log(a,b)
    //3 3
}
run()
```



### 函数的变量提升

普通变量变量提升只提升声明，而不提升赋值

function直接定义的函数变量提升会包括函数内容一起提升

但是var/let定义的函数不具有变量提升

```javascript
show();//hello  function直接定义的函数具有变量提升、
show2()//报错，var/let定义的函数没有变量提升
show3()//报错
function show(){
console.log('hello')
}
var show2 = function(){
console.log('hello')
}
let show3 = function(){
console.log('hello')
}
```



### 函数作用域

作用域的意思就是保存在哪里

全局var的变量作用域为全局作用域和函数作用域

函数作用域内var的变量为局部变量，函数内var的变量保存进函数的内存

函数作用域能使用全局作用域的变量，但全局作用域无法使用函数作用域的变量

并且赋值是从右到左

```
var a = b = 5
等同于
b = 5
var a = b
```



```javascript
var a = 'wtw';
//a声明为全局变量
function show(){
    //函数外定义的a的作用域为全局，function直接调用a就是全局的a
	a = 'wy';
	console.log(a); 
}
show();
console.log(a);
//wy
//wy
```

```javascript
var a = 'wtw';
function show(){
    //函数内定义的变量作用域为函数作用域，局部变量的优先级比全局变量高
	var a = 'wy';
	console.log(a);
}
show();
console.log(a);
//wy
//wtw
```

```javascript
//不使用var直接定义变量会生成一个全局变量
//但这样这个变量不会被释放，会被定义为window的一个属性挂载在全局
//这样容易造成内存泄漏

function show(){
    //不用var，直接定义变量，变量会变成window的一个属性，作用域为全局
	a = "wtw"
}
show()
console.log(a)
//wtw
```

```javascript
var a = 3
function c(){
var a = (b = 5)
//相当于 b = 5;var a = b
//b没有var，所以是全局变量
console.log(a)//5
console.log(b)//5
}
c()
console.log(b)//5
console.log(a)//3
```



## let/const

- var全局定义变量时会挂载到window上（且不能被deleted删除），let/const不挂载
- var具有变量提升（定义函数没有提升，不用var的隐式声明也没有提升），但是只提升定义不提升赋值，let/const不具有变量提升
- var是函数作用域，let/const是块级作用域
- var了以后可以重新var一个同名变量，let/const不行
- let声明的变量具有暂时性死区特性，在块级作用域内使用的let声明变量会被绑定在这个块级作用域中，不受外部代码影响，如果外部var声明了与let声明了同名全局变量，则let声明的局部变量的优先级更高，在这个块级作用域中完全无法对var声明的同名变量进行操作
- const定义变量必须定义时赋值



### 块级作用域

只有一对大括号包裹的作用域才叫一个单独的块级作用域，没在一对大括号内的变量常量都在全局作用域下。不同script标签内也同属一个全局作用域。

let和const都不具有变量提升，且作用域为块级作用域。相当于变量保存进这个块级。

块级作用域内也可以使用全局作用域下的变量与常量。如果局部变量重名，块级作用域内的局部变量优先级高。

```javascript
{
	let web = 'web'
	var web2 = 'web2'
}
console.log(web2) //web2，var不具有块级作用域
console.log(web)  //报错，let的作用域仅在{}内，外部访问不到
```



### const常量声明

let声明的是变量，const声明的是常量

const声明常量时，不能更改的是对**内存地址的引用**，而不是内存地址保存的属性值

所以const声明的引用类型保存的值是可以进行修改不会报错的，但是不能修改引用的指向

但基本数据类型更改就会重新开辟内存空间（内存地址改变），所以const定义的基本数据类型一定不能修改

```javascript
const obj = {
	name:'wtw'
}
//obj指向的内存地址没有改变，地址保存的值没变
obj.name='abc'
console.log(obj.name)  //abc
obj = {}  //改变了引用类型的指向,报错
```

```javascript
<script>
        const a ='a'
</script>
<script>
		const a ='b'  //报错，全局作用域已经有了常量a，常量无法修改指向
</script>
```



#### Object.freeze(const) 锁死常量

const定义常量以后虽然无法改变指向，但还是能改变引用类型的属性的值，如果我们希望将一个常量锁死，完全不能修改就使用`Object.freeze(const) `这个方法

```javascript
const student = {
	name: 'wtw',
	age: 21
}
Object.freeze(student)  //锁死常量
student.age = 5  //常量被锁死修改无效，严格模式下会直接保存
console.log(student.age) //21
```



### 不挂载window

window对象是整个BOM的核心，所有对象和集合都以某种方式回到window对象，window对象表示整个浏览器窗口，而DOM的核心document对象是window对象的一个属性。

在浏览器环境下，所有用var声明的变量都会挂载到window全局对象下，可以直接用变量名访问，也可以使用`window.变量`来访问（而let和const不会挂载到window下）。node环境下，因为NodeJS没有bom和dom，所以没有window全局变量。

但window全局对象本身保存了很多信息，如窗口当前缩放的宽度高度等，我们使用var定义变量以后可能会与window保存的一些bom属性重名，挂载到window上就会把bom属性覆盖掉，导致我们无法监听bom属性。

所以用let和const定义变量则不会产生这种情况









# 传值和传址

传值：基本数据类型（数据量较小）在使用等于来赋值时都是直接传值

```javascript
let a = 1;
let b = a;
console.log(a,b) //1,1
b=2;
console.log(a,b) //1,2
```

传址：引用类型（数据量较大）来使用等于赋值都是传址，两个变量名指向同一个对象，属于浅拷贝

```javascript
let a = {
name:'wtw'
}
let b = a
console.log(a,b)  //{name:'wtw'} {name:'wtw'}
b.name = 'abc'
console.log(a,b)  //{name:'abc'} {name:'abc'}
```





# instanceof

Object构造函数是所有引用类型原型链的最顶端

`instanceof `是一个双目运算符，**用来测试一个对象是否是一个类的实例（通过原型判断）**

`obj instanceof F `用来测试一个对象是否是一个构造函数的实例，判断的标准是`F.prototype`是否在obj的原型链上，可以用这个来判断一个变量是不是数组

```javascript
//String是一个javascript提供的一个构造函数，它而不是一个基本类型，而是一个引用类型
//用var = ''构建的字符串，是一个基本类型string
//new String的实例是一个引用类型，原型链最终指向Object
var str = new String("hello world"); 
console.log(typeof str);//Object
console.log("1" instanceof String); // false
console.log(str instanceof String);//true
console.log(str instanceof Object);//true


//判断foo是否是 Foo 类的实例
function Foo(){} 
var foo = new Foo();
console.log(Foo instanceof Function)//true
console.log(foo instanceof Foo)//true

// 判断 foo 是否是 Foo 类的实例 , 并且是否是其父类型的实例
function a(){} 
function b(){} 
a.prototype = new b();
//JavaScript 原型继承  b类在a类的原型链上端
//foo是a类的实例，也算b类的实例
var foo = new a();
console.log(foo instanceof a)//true 
console.log(foo instanceof b)//true

// 判断arr是不是数组
let arr = []
console.log(arr instanceof Array) // true

// 判断func是不是函数
function func () {}
console.log(func instanceof Function) // true

```





# typeof

这个关键字可以查询变量的类型（基本类型，Object，function）

- **typeof基本类型，会直接显示该变量的基本类型（null除外，null显示Object）**



- **typeof引用类型（对象，数组，function），除了function类型外其他会显示Object**

  因为引用类型原型链的源头就是Object，Array类，Data类等都是Object原型链的下层

  typeof正是通过原型链找到源头来判断这个变量是什么类型



- **没有定义变量就进行typeof不会报错会undefined**



```javascript
let a =321
let b
console.log(typeof a)//number
console.log(typeof b)//undefined


//typeof引用类型，除了function类型外其他会显示Object
let arr= [1,2,3]
console.log(typeof arr)//Object
//想要判断引用类型要使用instanceof
console.log(arr instanceof Array)

//typeof函数会显示function
let c = function(){}
console.log(typeof c)//function

//没有定义变量就进行检查不会报错会undefined
console.log(typeof d) //undefined
```







# 运算符

## 赋值运算符

​	+   -   *   /   =

字符串 + 数字直接进行字符串拼接

字符串-*/会尝试使用数学运算



## 逻辑运算符

`||`		或 ,逻辑或会短路运算，一个为真其他就不运算了，直接返回真

`&&`	    与， ,逻辑与会全部运算，所有为真才返回真，否则返回假

`!	`		  非



`==`      等于，会隐式转换类型，如果 1== '1' 也会返回true

`!=`	 不等于



`===`	强等于，不会进行隐式转换类型

`!==`	不强等于





## 比较字符串

javascript比较字符串大小的方法：JavaScript中可以直接使用大于(>)、小于(<)运算符进行字符串大小比较。此方法会根据第一个不同的字符的ascii码进行比较,相同比下一个。

当数字的字符串之间比大小时，转化为ascall码比较

当数字(number)与字符串(string)进行比较大小时，会尝试将字符串隐式转化为数字进行比较，不能parseInt的话返回NAN，永远无法进行比较

```javascript
console.log('13' > '3'); // 输出：false，比较第一个数的ascall码
console.log(5 > '6');  // 输出： false，'6'隐式转换为数字6比较
console.log('d' > 'ABDC') // 输出： true
console.log(19 > 'ssf') // 输出 false，parseInt('ssf')为NAN，无法比较，永远为false
console.log('A' > 'abcdef') // 输出 false
```





## []==![]

**如果有一个操作数是布尔值，则在比较相等性之前先将其转换为数值——false转换为0，而true转换为1**

**如果一个操作数是对象，另一个操作数是基本类型，则调用对象的valueOf()方法，用得到的基本类型值按照前面的规则进行比较，如果对象没有valueOf()方法，则调用 toString()**

**当数字的字符串之间比大小时，转化为ascall码比较**

**如果一个操作数是字符串，另一个操作数是数值，在比较相等性之前先将字符串转换为数值在进行比较**

```javascript
console.log([]==![]) //true
console.log({}==!{}) //false
```

```
![]=false，false需要转换为0
[]==0 属于对象和非对象的比较，调用对象的valueOf方法，没有则调用toString方法
[].toString = ""
转换过来就是''==0
Number("")为0
所以0==0返回true
```



## == 和 ===

首先先看 ==，由于JS是弱类型的，如果使用 == 进行比较，== 操作符会自动将 0，''（空字符串），null，undefined 转成布尔型false，这样就会出现

```javascript
0 == ' '  // true
null == undefined // true
null === undefined // false
[1] == true // true
```

**当空数组作为判断条件时,相当于true。当空数组与布尔值直接比较时,相当于false**

```javascript
[] == false // true
```



这显然是不符合预期的。所以JS为我们提供了全等操作符 ===，它不会进行类型转换，也就是说如果两个值一样，必须符合类型也一样。但是，它还是有两种疏漏的情况，Object.is()提供了弥补以下两种情况的全等比较。

```javascript
+0 === -0 // true，但我们期待它返回false
NaN === NaN // false，我们期待它返回true
```



除了==null之外，其他一律使用===，因为

```javascript
obj.a == null 全等于 obj.a === null || obj.a === undefined
```

 `==` 运算符在判断相等前对两边的变量(如果它们不是同一类型) 进行强制转换 (这种行为的结果会将 `"" == false` 判断为 `true`)

 `===` 运算符 (也包括 `==` 运算符) 将数字 `-0` 和 `+0` 视为相等 ，而将`Number.NaN`与`NaN`视为不相等.



# ES6解构

解构取值

```javascript
let user = {name:"wtw",age:18}
//完整语法：let {user:user,age:age} = user
//ES6解构语法
let {name="123", title="abc", age} = user
//解构也只支持默认值，如果目标中没有就用默认值
console.log(name)//wtw
console.log(age)//18
console.log(title)//abc

//数组的解构语法
let arr=['qqq','bbb']
let [a, b]=arr
console.log(a,b)//qqq  bbb
```



解构赋值

```javascript
let name="wtw"
let age=18
let p = {name,age}
console.log(p) // { name: 'wtw', age: 18 }
```



## 拓展运算符解构

拓展运算符是es6语法，拓展运算符可以对数组，对象等进行解构

在数组上使用扩展运算符

```javascript
var obj= ['zs','男']
console.log(...obj);
// 结果
// zs 男
1234
```

以相同的方式在对象上使用扩展运算符会报错

```javascript
var obj = {
    name:'zs',
    sex:'男'
}
console.log(...obj);
// 报错，对象不能直接拓展运算符解构
// TypeError: Found non-callable @@iterator
```

在对象上使用扩展运算符的正确方式应该如下

```javascript
var obj = {
    name:'zs',
    sex:'男'
}
console.log({...obj});
// 结果
// { name: 'zs', sex: '男' }
```

还可以将对象的特定属性单独解构出来

```javascript
const person = {
  name: 'www',
  age: 18,
  height: 180
}
const {name, ...others} = person
// 将person.name属性解构成单独的name，其他属性解构成一个对象others

console.log(name) // "www"
console.log(others) // { age: 18, height: 180 }
```







# 类型转换与计算

进行计算时（例如加减乘除和==）可能会产生隐式的强制类型转换



基本类型和字符串相加会进行字符串拼接

```javascript
let result = true + "10
// true10
result = 100 + '10'
// 10010
```



数字类型和字符串进行数学运算（除了加法，加法是字符串拼接），会尝试将字符串转化为数字，字符串无法转换为数字则返回NAN。例如空字符会转换为false，然后转换为0。

```javascript
console.log(1 - '1') // 0
console.log(1 - 'a1') // NAN
console.log(1*'') // 0
```



数字类型和bool类型进行数学运算，bool会转换为数字

```javascript
console.log(1 + true) // 2
console.log(1 - true) // 0
```





### in关键字

in和hasOwnProperty()不同，hasOwnProperty()不查找原型链上的属性。

**in关键字可以检测实例对象和原型链上是否具有这个属性**，返回值为boolean类型

```
"属性名" in 引用类型对象
```

```javascript
let arr = [1,2,3]
console.log(arr.hasOwnProperty("concat"))//false,concat方法在arr的原型链上
console.log('concat' in arr)//true
```





# console

控制台console

## console.log()

控制台输出



## console.time()/timeEnd()

```javascript
console.time('a')  //打上一个标记a，并开始计时
...程序代码
console.timeEnd('a') //结束a标记的计时，输出程序从标记a到这里执行所用时间
```



## console.table()

将数据（对象数组等键值对形式）以表格的形式打印在控制台



## console.error()

```javascript
console.error('err')
//控制台输出错误  err
```

在控制台输出一个红色的错误信息





# 捕获异常

- try catch

```javascript
try {
 // todo
} catch(err) {
  console.log(err)
}
```



- throw 抛出异常

可以设置条件，throw new Error，会在控制台输出一个红色的错误

```javascript
const user = {
	age,
	setAge(newAge){
		if(typeof newAge != "number"){
			throw new Error("请输入数字")
			//控制台会输出错误
		}else{
			this.age = newAge
		}
	}
}
```



- window.onerror 自动捕获异常

  跨域js不会有详细报错，压缩的js要配合sourceMap查到原本源码上的错误位置

  ```javascript
  window.onerror = function (message, source, lineNum, colNum, error) {
  
  }
  ```

  

  



# 函数

函数是引用类型，是一种特殊对象

```javascript
var a = function(a,b,c){}
console.log(typeof a)//object
```

所有函数都可以看做Function的后代实例

```javascript
console.log(a instanceof Function)
```

可以使用函数名.length获取函数所需参数个数

```javascript
console.log(a.length) //3
```



## 定义函数

**在对象中定义函数**

```javascript
let user={
    setName:function(){},
    //es6语法可以省略:function
    setAge(){}
}
```

**在对象外的全局里定义函数**

使用function或者var来定义函数会将函数压入window中，可能污染window属性

```javascript
function f1(){
}
var f2 = function(){
};
window.f1()
window.f2()
```

使用let来定义变量保存函数则不会污染window

```javascript
let f3 = function(){
};
window.f3()//报错
```



## 函数变量提升

function直接定义的函数具有整体变量提升，可以声明在使用之后

var/let定义的函数没有变量提升，必须先声明完整的函数才能再使用

```javascript
show();//hello  function直接定义的函数具有整体变量提升

show2()//报错，var/let定义的函数没有变量提升
show3()//报错
function show(){
console.log('hello')
}
var show2 = function(){
console.log('hello')
}
let show3 = function(){
console.log('hello')
}
```



## 匿名函数

有些地方函数只用一次，无需命名，就可以使用匿名函数

```javascript
function sum(...args){
	return args.reduce(function(a,b){
        //匿名函数
		return a+b
	})
}
console.log(sum(1,2,3)) //6
```



## 立即执行函数

```javascript
let a = 'ad';
//运行到这段代码自动执行函数，无需调用
(function(){
    a = 4
	console.log(a)
})()
//4
```



## 实参，形参

传递基本类型，形参直接在内存中复制一份实参

```javascript
let a ='123'
function set(a){
    a='wtw'
}
set(a)
console.log(a)
```

传递引用类型，形参复制的是实参的内存地址（指针），形参和实参还是指向同一片内存空间

```javascript
let a ={name:'123'}
function set(a){
    a.name='wtw'
}
set(a)
console.log(a)
//{name:'wtw'}
```



## 默认参数

函数的形参可以设置默认值，但有默认值的参数都放最后，防止参数不足时被覆盖导致没有默认值的形参拿不到实参

```javascript
function sum(a,b=2){
	return a+b;
}
console.log(sum(1)) //3
console.log(sum(1,4))//5
```



## arguments

每一个函数都有一个arguments伪数组对象，它包括了函数所要调的参数，通常我们把它当作数组使用，用它的length得到参数数量，但它却不是数组

伪数组对象和数组的区别

  1）拥有length属性，其它属性（索引）为非负整数（对象中的索引会被当做字符串来处理）；
  2）不具有数组所具有的方法；
      javascript中常见的类数组有 arguments 对象和 DOM 方法的返回的数组

**使用api可以将伪数组转化为真正的数组**

```
var arr1 = Array.prototype.slice.call(arguments);

var arr2 = [...arguments]
```



```javascript
function sum(){
	console.log(arguments)
    //[Arguments] { '0': 1, '1': 2, '2': 3, '3': 4, '4': 5, '5': 6 }
    
    console.log(arguments.length)
    //6
    
    let total = 0
    for(let i = 0;i<arguments.length;i++){
        total+=arguments[i];
    }
    console.log(total)//21
    
    
}
sum(1,2,3,4,5,6)
```



## 剩余参数

除了arguments对象能接收所有参数外，使用ES6的点语法展开语法也可以接收不定的参数

剩余参数创建的是真正的数组，可以使用数组的方法，比arguments灵活

```javascript
function sum(...args){
	console.log(args)
    //[ 1, 2, 3, 4, 5, 6 ]
    
    console.log(args.length)
    //6
    
    let total = 0
    for(let i = 0;i<args.length;i++){
        return total+=args[i];
    }
    console.log(total)//17
}
sum(1,2,3,4,5,6)
```



## 箭头函数

箭头函数中如果函数体中只有一句代码，且函数执行结果就是返回值或者没有返回值（相当于返回undefind），相当于函数体只有一行代码，可以省略大括号和return，箭头函数中形参只有一个，小括号可以省略

```
()=>{}
```

**箭头函数不绑定this,箭头函数中的this默认指向上一层作用域中绑定的this**

**箭头函数不能使用arguments**，但可以使用**剩余参数**来传入不定数量参数

```javascript
let a = {
    site:"123",
    arr :['abc','ccc','qqw'],
    b:function(){
    //方法的this指向实例对象
    	console.log(this) //a实例
    	return this.arr.map(function(){
        	//普通函数的this指向window
        	//window
        	console.log(this)
        })
    },
    c:function(){
        //方法的this指向实例对象
        console.log(this) //a实例
        return this.arr.map(()=>{
            //箭头函数绑定上一层作用域的this
            //a实例
            console.log(this)
        })
    }
}
a.b()
a.c()
```



定时器中回调使用普通函数this指向window，使用箭头函数指向当前作用域的this

```javascript
const a = {
  wait1() {
    setTimeout(function(){
				console.log(this) // window
		})
  },
  
	wait2() {
		setTimeout(() => {
				console.log(this) // a
		})
	}
}
```





## this指针

**this用来保存函数中可变的变量，this的值是在函数执行的时候被确定的，而不是函数定义的时候**

**全局环境下的this指向window，对象内的方法的this指向对象实例，但是对象方法内的普通函数的this依然指向window，严格模式下this没有设定或者没用new调用构造函数，this则会指向undefined**

 * 普通函数中的this是谁?-----window

     

 * 对象.方法中的this是谁?----当前的实例对象

     

 * 定时器回调使用普通函数中的this是谁?----window
    	setTimeout完整写法是：window.setTimeout

        	```
        	setTimeout(function() {
        	  console.log(this) // [object window]
        	}, 0)
        	```


​    	
 * 定时器回调使用箭头函数的this是谁?----当前作用域的this

     

 * 构造函数中的this是谁?-----实例对象

     

 * 原型对象方法中的this是谁?---实例对象

     

 * 箭头函数的this指向上下文



## 通过常量保存this指针

```javascript
let a = {
    site:"123",
    arr :['abc','ccc','qqw'],
    b:function(){
    //方法的this指向实例对象
    	console.log(this) //a实例
        //利用一个常量保存this指针
    	const self = this
    	return this.arr.map(function(){
        	//通过常量来代替上一层的this指针
          //普通函数的this指向window，使用self提前缓存好上级作用域的this
        	console.log(self)//a实例
        })
    }
}
```



## call和apply

```
call(this,参数1,参数2)
apply(this,[参数1,参数2])
```



**call和apply的作用是改变函数运行时内部的this指针的指向并立即执行该函数**，方法第一个参数就是改变的指向目标，后面的参数再是函数的参数

call和apply唯一的不同是，call方法的函数参数在后面一个一个以call的参数传入即可，而apply方法的函数参数需要先全部装进一个数组，然后作为apply的第二个参数传入

```javascript
let lisi = {
	name:'李四'
};
let wangwu = {
	name:'王五'
};
function User(a,b){
	console.log(this.name)
}
User.call(lisi,'函数参数a','函数参数b') //李四
User.apply(wangwu,['函数参数a','函数参数b']) //王五
```



## bind

**bind也是用于改变函数中this指针指向，但bind不会立刻执行函数，而是会返回一个新函数**

```javascript
let lisi = {
	name:'李四'
};

function User(a,b){
	console.log(this.name)
}
let f = User.bind(lisi,'函数参数a','函数参数b') 
f()//李四
```

**bind拷贝的函数不同于 = 号，bind拷贝属于深拷贝，返回的是一个新的函数**

但用bind拷贝函数时，注意函数参数的传入位置

如果bind拷贝时就传入了函数参数，则之后新函数传入的实参无效，默认使用bind传入的参数

如果bind拷贝时没有传递参数，之后则新函数可以传入实参来调用函数

```javascript
let a = function(a,b){
    console.log(this.name,a+b)
}
let b = a
console.log(a==b) //true

b = a.bind({name:"wtw"},1,2)
console.log(a==b) //false
b()//wtw  3  
//如果bind拷贝时传参了，新函数传实参无效
b(2,3)//wtw  3  

//如果bind拷贝时没传参了，新函数传参有效
c = a.bind({name:"abc"})
c(2,3)//abc  5   
```



**bind也可以直接加在匿名函数后面**，省略了接下来a调用bind拷贝新函数的过程，因为a就是被匿名函数拷贝出来的新函数

```javascript
let a = function(a,b){
    console.log(this.name,a+b)
}.bind({name:"wtw"})

a(3,4)//wtw   7
```







# 闭包与自由变量

**自由变量**：当前函数不能找到变量数据来源，需要向上级作用域查找，这个变量就是自由变量。

所有自由变量的查找是在函数定义的地方向上级作用域查找，而不是在使用函数的地方向上级作用域查找（无论他是不是闭包）。



**闭包（closure）**：数据在内存中没有被及时回收。闭包有两种形式且闭包函数定义的地方和函数执行的地方不一样的。**红宝书(p178)上对于闭包的定义：闭包是指有权访问另外一个函数作用域中的变量的函数。**

第一种形式的闭包函数，需要返回一个函数，其次这个返回函数不能独立完整的运行，其中使用了自由变量，需要上层作用域提供数据支持。

第二种形式的闭包是给一个函数传入参数函数，并在参数函数中使用了自由变量从而形成闭包。

第三种形式的闭包是非典型闭包IIFE（立即执行函数表达式）



JS的全局环境的中定义的数据（变量，函数等）会被一直保留在栈或堆中，因为指不定什么时候就会被用到，全局环境是不会被回收的，除非标签或者浏览器被关掉，所以我们在全局定义的全局变量作用范围就在整个全局。

而函数的环境则比全局环境要小一级，在一个函数的一对{ }包裹函数体就叫函数环境，**在执行函数前的词法分析（预编译）阶段**，函数环境就在内存中开辟了一块空间。

**在函数体内定义的变量叫局部变量（局部数据），局部变量作用域默认情况下只在该函数体内有效（外部是无法访问的），局部变量优先级大于全局环境的同名变量，并且函数内还可以再定义，返回或使用函数**。

局部数据不算在全局环境内，在执行完函数后，函数体内定义的局部数据过段时间如果没有被用到，就会被GC清理掉，**下次重新使用会重新开辟一次另一块空间保存局部数据，所以一个函数可能开辟了多块内存地址存放数据。**但如果函数内有任何数据被外部使用着（例如全局数据，全局数据不会被GC清理），这个函数的内存块就一直不会被GC清除。





## 闭包原理

按照常理，函数内部的数据只有函数自己能使用，全局无法访问，如果在全局环境下，我们通过父函数内部返回一个子函数来访问到了父函数内部的数据，就形成了闭包，闭包中GC无法清除父函数的内存地址，父函数的数据会一直随全局数据一起保留。

**总结：如果返回的函数或者传入的参数函数中使用的自由变量能直接能找到数据来源，返回的函数能自圆其说，完成函数功能，就不形成闭包。但如果自由变量数据找不到来源，还要去定义闭包函数的作用域中寻找数据来源来实现函数功能，就形成了闭包。**



## 闭包应用

变量长期驻扎在内存中，避免全局变量的污染，私有成员的存在

- 模块化封装，以及HOC等
- 立即执行函数
- 函数柯里化
- for循环+setTimeout
- for循环中绑定事件
- 为私密变量提供外界访问的getter和setter





## 闭包缺点

- 闭包会引用外层函数的变量，会占用更多内存
- 过度使用闭包，使函数调用栈无法清理外层函数，可能引起内存泄漏



## 闭包案例

- 第1个案例

  a变量接收到f2函数，运行后发现n不能找到，要去原函数找n的原始值完成功能，所以形成闭包

```javascript
function f1(){
  let n = 1;
	return function f2(){
		console.log(++n)
	}
}

f1() 
//直接调用f1是错误的，不会有任何结果，因为没有变量接收f1的返回值

//变量a接收f1的返回值f2函数，a空间不会被回收导致f2不会被回收，保存f2的f1也不会被回收
//一直使用同一块内存空间，所以数据保留，外部通过f1暴露的子函数f2访问f1的数据形成闭包
let a = f1()
/*
相当于
let a= function f2(){
		console.log(++n)
	}
外部访问到了n这个函数内部变量，没有找到来源，就去定义函数的上层作用域中找，所以形成了闭包
*/
a() // 2
a() // 3
a() // 4

//let声明变量b又重新开辟了空间保存
//所以数据又重置了
let b = f1()
b() // 2
b() // 3

// a, b内存空间相互独立，互不干扰
a() // 5
```



- 案例2

  下面这个例子函数f1返回sum函数，a运行时发现m直接就能找到，自己就能独立实现功能，所以m不形成闭包，但n需要向上查找，所以n形成闭包

```javascript
function f1(){
    let n = 1;
    return function sum(){
        let m = 1
        console.log(++m)
        function f2(){
            console.log(++m) // 变量m不形成闭包
          	console.log(++n) // 变量n形成闭包
        }
        f2()
    }

}

let a = f1()
//每次调用a都为变量m重新创建了内存空间，但是n引用的是闭包中的数据
a()//2  3  2
a()//2  3  3
a()//2  3  4
/*
相当于
let a = function (){
        let m = 1
        console.log(++m)
        function f2(){
            console.log(++m)
            console.log(++n)
        }
        //每次调用m都能直接找到来源，不能形成闭包，调用n需要向上查找，形成闭包
        f2()
    }
*/
```



- 案例3

  利用闭包隐藏数据并提供getter/setter接口

```javascript
function createCache() {
	const data = {}
	return {
		set: function (key, val) {
			 data[key] = val
		},
		get: function (key) {
			 return data[key]
		} 
	}
}

const cache = createCache()
// 利用闭包，调用方法，访问的是闭包内部的data对象
c.set('a', 100)
console.log(c.get('a'))
```



- 案例4

  函数作为参数传入，且参数函数中使用了自由变量从而形成闭包

  无论是不是闭包，所有自由变量的查找是在函数定义的地方，向上级作用域查找，而不是在使用的地方向上查找。

```javascript
let a = 100 
// a是全局变量，不会被回收。

function fn() {
  a = a + 1
  console.log(a)
}

function print (fn) {
  let a = 200 
  fn() // 101, fn中访问的a不是参数也不是自定义的变量，而是自由变量，所以要在全局环境中查找
  fn() // 102
}

print(fn)
```





- 案例5

  当onfocus执行时，item.content才确定，此时循环已经结束，三个闭包共享的item已经指向数组最后一项。

```js
function setContent(){
        var infoArr = [
            {'id':'email','content':'your email address'},
            {'id':'name','content':'your name'},
            {'id':'age','content':'your age'}
        ];
        for (var i = 0; i < infoArr.length; i++) {
            var item = infoArr[i];
            document.getElementById(item.id).onfocus = function(){
                showContent(item.content)
            }
        }
    }
    setContent()
    //循环中创建了三个闭包，他们使用了相同的词法环境item，item.content是变化的变量
    //当onfocus执行时，item.content才确定，此时循环已经结束，三个闭包共享的item已经指向数组最后一项。
```

使用闭包解决

解决方法1     通过函数工厂，则函数为每一个回调都创建一个新的词法环境

```javascript
    function showContent(content){
        document.getElementById('info').innerHTML = content;
    };

    function callBack(content){
        return function(){
            showContent(content)
        }
    };

    function setContent(){
        var infoArr = [
            {'id':'email','content':'your email address'},
            {'id':'name','content':'your name'},
            {'id':'age','content':'your age'}
        ];
        for (var i = 0; i < infoArr.length; i++) {
            var item = infoArr[i];
            document.getElementById(item.id).onfocus = callBack(item.content)
        }
    }
    setContent()
```



解决方法2        绑定事件放在立即执行函数中

```javascript
function showContent(content){
        document.getElementById('info').innerHTML = content;
    };

    function setContent(){
        var infoArr = [
            {'id':'email','content':'your email address'},
            {'id':'name','content':'your name'},
            {'id':'age','content':'your age'}
        ];
        for (var i = 0; i < infoArr.length; i++) {
            (function(){
                var item = infoArr[i];
                document.getElementById(item.id).onfocus = function(){
                    showContent(item.content)
                }
            })()//放立即执行函数，立即绑定，用每次的值绑定到事件上，而不是循环结束的值
        }
    }
    setContent()
```



解决方案3        用ES6声明，避免声明提前，作用域只在当前块内

```js
    function showContent(content){
        document.getElementById('info').innerHTML = content;
    };

    function setContent(){
        var infoArr = [
            {'id':'email','content':'your email address'},
            {'id':'name','content':'your name'},
            {'id':'age','content':'your age'}
        ];
        for (var i = 0; i < infoArr.length; i++) {
            let item = infoArr[i];      //限制作用域只在当前块内
            document.getElementById(item.id).onfocus = function(){
                showContent(item.content)
            }
        }
    }
    setContent()
```



- 案例6

  在react中class组件中在定时器中会有以上问题

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
  componentDidUpdate() {
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
  componentDidUpdate() {
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



解决方案2：使用hooks，hooks依赖闭包，所以会自动形成闭包保存每次的props和state

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





## this在闭包中的问题

this指针很特殊，因为普通函数中this会指向winsow，导致无法访问原函数中的数据，所以为了防止this指偏，闭包不要轻易使用箭头函数

```javascript
let hd = {
  user:"abc",
  get: function(){
    console.log(this)  //hd
	return function(){
      console.log(this)  //window
	  return this.user
	}
  },
  get2:()=>{
    console.log(this)  //window
	return function(){
      console.log(this)  //window
	  return this.user
	}
  },
  get3:function(){
    console.log(this)  //hd
	return ()=>{
      console.log(this)  //hd
	  return this.user
	}
  },
  get4:()=>{
    console.log(this)  //window
	return () =>{
      console.log(this)  //window
	  return this.user
	}
  }
}

console.log(hd.get()()) //undefined
console.log(hd.get2()()) //undefined
console.log(hd.get3()()) //abc
console.log(hd.get4()()) //undefined
```





# Proxy代理

**Proxy** 也就是代理，可以帮助我们完成很多事情，例如对数据的处理，对构造函数的处理，对数据的验证，说白了，**就是在我们访问引用类型（对象/数组）前添加了一层拦截，可以过滤很多操作。**

用户访问的是代理对象，而不是原对象，代理对象再验证操作后再去修改原对象，代理对象可以根据设置来对一些操作进行监控，不合理的操作在代理上就直接拦截了，合理的操作再由代理对象对原对象进行操作

`Proxy` 无需一层层递归为每个属性添加get/set或其他操作，一次即可完成以上操作，性能上更好，并且原本的实现有一些数据更新不能监听到，但是 `Proxy` 可以完美监听到任何方式的数据改变，唯一缺陷可能就是浏览器的兼容性不好了



Proxy也是设计模式的一种：代理模式

```javascript
let a = {name:"wtw"}
a.name//wtw，普通的访问
//如果我们想要在访问a.name的时候弹出一个提示禁止去访问，就可以用Proxy代理来拦截

let obj = new Proxy(a,{
    get(target,property){
        console.log(`当前放问的是${target}对象的${peoperty}属性`)
    }
})
//使用代理的访问器来访问a对象
obj.name
//输出：当前放问的是[object Object]对象的name属性
```



## Proxy构造函数

Proxy类用来实例一个代理对象

```
new Proxy(target, handler)
```

构造参数第一个参数target表示要拦截的代理引用类型（函数或对象）

第二个参数是一个对象handler，用来定制拦截行为,handler对象有一些属性



## handler

handler是json格式的对象，用来定制Proxy拦截器中的拦截行为

**get**：`<function>`|第一个参数是目标对象，第二个参数是要访问的属性，拦截访问操作

**set**：`<function>`|第一个参数是目标对象，第二个参数是要修改的属性，第三个属性为修改的值，拦截设置操作

```javascript
const a = {name:"wtw"}
const proxy = new Proxy(a,{
	get(obj,property){
        //设置的过滤行为
      if(property in obj){
        console.log(`访问${property}`)
				return obj[property]
      }else{
         	throw new Error("属性不存在")   
      }
	},
	set(obj,property,value){
        //访问的过滤行为
    console.log(`设置${property}为${value}`)
		obj[property] = value
    return true
	}
})
console.log(proxy.name)
proxy.name = "wy"
console.log(proxy.name)
console.log(proxy.titile)
//访问name
//wtw
//设置name为wy
//访问name
//wy
//报错：属性不存在
```

**has**: ` <function> `第一个参数是目标对象，第二个参数是要访问的属性，拦截对象 in关键字 的遍历操作

**apply**: `<function>`apply方法专用于拦截函数的调用，第一个参数func是函数本体，第二个参数是上下文对象，第三个参数args是参数组成的数组

```javascript
var p = new Proxy(function() {}, {
  apply: function(target, thisArg, argumentsList) {
    console.log('called: ' + argumentsList.join(', '));
    return argumentsList[0] + argumentsList[1] + argumentsList[2];
  }
});

console.log(p(1, 2, 3)); // "called: 1, 2, 3"
                         // 6
```



## Reflect

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









# JSON

JSON(JavaScript Object Notation)是前后端交换信息的通用数据格式，本质是一串字符串

JSON 是一种语法，用来序列化对象、数组、数值、字符串、布尔值和 null 。它基于 JavaScript 语法。

JSON由两种结构组成：

1. 键值对的无序集合对象(或者叫记录、结构、字典、哈希表、有键列表或关联数组等)

   对象是一个无序键值对的集合，以"{"开始，同时以"}"结束，键值对之间以":"相隔，不同的键值对之间以","相隔

   ```json
   {
   	"key1":"value",
   	"key2":"value"
   }
   ```

2. 值的有序列表数组

   数组是值（value）的有序集合。一个数组以“[”（左中括号）开始，“]”（右中括号）结束。值之间使用“,”（逗号）分隔。

```json
[
    {
        "key1":"value"
    }
]
```



## JSON.stringify()

`JSON.stringify(Obj)`**将参数对象中可序列化的部分（对象、数组、数值、字符串、布尔值和 null ）转换为JSON格式的字符串**

只有一个参数时，传入一个对象，将这个对象全部属性转换为json格式

```javascript
let data={
	name:"wtw",
	age:"21",
    teacher:{
        chinese:"wy",
        math:"abc"
    }
}

let json = JSON.stringify(data)
console.log(json)
//{"name":"wtw","age":"21","teacher":{"chinese":"wy","math":"abc"}}
```



添加第二个参数是一个数组，表示只转换目标对象在数组中存在的属性，全选可以设置为null

```javascript
let data={
	name:"wtw",
	age:"21"
}

let json = JSON.stringify(data,["name"])
console.log(json)
//数组中只标明了name属性，所以age属性会被忽略不会转换进json
//{"name":"wtw"}
```



第三个参数是number/string类型，指json每一项前保留多少个空格，使用这个参数必须有第二个参数（全选设置为null）

指定缩进用的空白字符串，用于美化输出（pretty-print）；如果参数是个数字，它代表有多少的空格；上限为10。该值若小于1，则意味着没有空格；如果该参数为字符串（当字符串长度超过10个字母，取其前10个字母），该字符串将被作为空格；如果该参数没有提供（或者为 null），将没有空格。

```javascript
let data={
	name:"wtw",
	age:"21",
    teacher:{
        chinese:"wy",
        math:"abc"
    }
}

let json = JSON.stringify(data,null,2)
console.log(json)
/*
{
  "name": "wtw",
  "age": "21",
  "teacher": {
    "chinese": "wy",
    "math": "abc"
  }
}
*/
```



## toJSON函数

在对象中设置`toJSON`函数属性，可以自定义JSON的序列的内容

```javascript
let data={
	name:"wtw",
	age:"21",
    teacher:{
        chinese:"wy",
        math:"abc"
    },
    toJSON(){
    	return {
    	//自定义JSON序列化的内容对象
       	//规定这个对象转换为JSON后的键与值
    		name:this.name,
    		teacher:this.teacher.chinese
    	}
    }
}

let json = JSON.stringify(data)
console.log(json)
//{"name":"wtw","teacher":"wy"}
```



## JSON.parse()

可以将一个JSON转化为普通的JS对象

JSON.parse(json)第一个参数为需要转为对象的JSON

```javascript
let data={
	name:"wtw",
	age:"21",
    teacher:{
        chinese:"wy",
        math:"abc"
    }
}

let json = JSON.stringify(data,null,2)
let obj = JSON.parse(json)
console.log(obj)
//{ name: 'wtw', age: '21', teacher: { chinese: 'wy', math: 'abc' } }
```



可以设置第二个参数，回调函数

如果传入该参数(函数)，可以拦截生成的原始值，并进行用来修改过滤，调用时机在 parse 函数返回之前。

回调会遍历所有JSON数据，每个键值对都会执行一次回调

回调第一个参数为key，键值对的键，第二个参数为value，键值对的值

```javascript
JSON.parse('{"p": 5}', function (k, v) {
    if(k === '') return v;     // 如果到了最顶层，则直接返回属性值，
    return v * 2;              // 否则将属性值变为原来的 2 倍。
});                            // { p: 10 }

JSON.parse('{"1": 1, "2": 2,"3": {"4": 4, "5": {"6": 6}}}', function (k, v) {
    console.log(k); // 输出当前的属性名，从而得知遍历顺序是从内向外的，
                    // 最后一个属性名会是个空字符串。
    return v;       // 返回原始属性值，相当于没有传递 reviver 参数。
});

// 1
// 2
// 4
// 6
// 5
// 3 
// ""
```





# 原型

JS本身是一门基于原型继承的语言

`实例对象.__proto__`对象指向其`构造函数.prototype`对象，而`构造函数.prototype.__proto__`对象又指向`Object.prototype`对象，如果是直接定义的对象没用显示的构造函数则`obj.__proto__`直接指向`Object.prototype`对象（直接定义的对象直接父级就为Objcet）。

```javascript
let obj ={}
console.log(obj.__proto__)//Objcet

let people = function(name){}
let a = new people()
console.log(a.__proto__ == people.prototype)//true

console.log(Object.prototype.__proto__)//null
```

最终`Object.prototype.__proto__`和`Object.__proto__.__proto__`指向null

这就是一个对象的原型链

不要滥用原型，如果在同一层原型上被不同框架或文件加上同名原型会产生不可意料的覆盖



## 原型是什么

对象拥有隐式原型`__proto__`对象和`constructor`属性

`__proto__`对象（又称隐式原型），指向（等于）构造函数用于保存数据的prototype对象，所以`构造函数.prototype`是实例的父级对象

`constructor`属性(**constructor属性可能在原型链上**)保存了构造函数的代码



函数拥有`prototype`对象（又称显式原型），但因为JS中函数也是一个对象，所以函数也拥有`__proto__`对象和`constructor`属性，当函数作为构造函数时会调用`prototype`对象作为实例父级对象为实例赋值。

函数的`__proto__`对象上保存了函数的方法，例如call，apply等

函数的`prototype`对象上保存了创造实例，给实例公用的属性和方法

函数的`constructor`对象则保存了构造函数本身的代码，指向构造函数本身`函数对象.constructor 等于函数对象本身`



普通构造函数（子类）的`__proto__`对象和`prototype`对象中的`__proto__`则都指向父类的显式原型prototype（没有父类的则是`Objcet.prototype`），Objcet.prototype.__proto__`最终指向null

```javascript
class people {}
class student extends people{}

console.log(student.prototype.__proto__ == people.prototype)//true
console.log(student.__proto__.__proto__ == people.prototype)//true

console.log(student.__proto__.__proto__ == student.prototype.__proto__)//true
```



因为所有对象都是Object这个构造函数构造出来的，Objcet作为构造函数对象也有`prototype`对象和`__proto__`对象，最终`Object.prototype.__proto__`和`Object.__proto__.__proto__`指向null。



### Function

1.Function也可以被当做一个构造函数

2.通过Funciton()   new出来的函数可以被当做是实例化的对象

3.Function这个构造函数也有原型对象：一个空的函数

4.Funciton的prototype的原型对象是Objecy.prototype

```javascript
console.log(Function.prototype.__proto__ == Object.prototype)//true
```







## 隐式原型proto

**总结：实例对象的`__proto__`指向（等于）构造函数用于保存数据的的`prototype`对象**

每个实例对象都有一个原型属性`__proto__`，`__proto__`指向存储了这个实例对象的父级对象的功能的内存地址（构造函数的prototype），例如我们创建了一个数组，数组内没有任何方法我们却可以使用`.length`，`.concat`等功能，就是因为数组内隐藏了一个`__proto__`属性，当然父级元素也有个`__proto__`属性指向父级元素的父级元素，这样一层一层的延续下去就形成了原型链，原型链最终指向Object这个构造函数，而最后`Object.prototype.__proto__`指向null

它的作用就是当访问一个对象的属性时，如果该对象内部不存在这个属性，那么就会去它的`__proto__`属性所指向的那个对象（可以理解为父对象或者构造函数的Protype对象）里找，如果父对象也不存在这个属性，则继续往父对象的`__proto__`属性所指向的那个对象（可以理解为爷爷对象）里找，如果还没找到，则继续往上找…直到原型链顶端null

平时引用类型调用的字符串方法、数组方法、对象方法、函数方法等都是靠原型链继承而来的。

```javascript
let a = []//完整写法为new Array
console.log(a.__proto__ == Array.prototype)//true
console.log(a.__proto__.__proto__ == Object.prototype)//true
```

实例自有方法的优先级比原型上的同名方法高。

并且对`__proto__`进行手动指向时会系统进行拦截（getter/setter监控设置操作），如果目标不是一个对象不会执行这段语句，也就是如果给一个对象的`__proto__`赋值一个非对象类型，系统setter会直接拦截操作







## 显式原型prototype

每一个函数都内置一个原型属性：`prototype`对象，里面放置的是共有、公有的属性或者方法。(一般情况属性是私有的)。`prototype`对象是个对象所以他只有`__proto__`和`constructor`。

**只有函数才有prototyoe属性**（同时因为函数也是一种对象，所以同时函数也具有`__proto__`属性和`constructor`属性）

prototype是从**一个函数指向一个对象**。它的含义是**函数的原型对象**，也就是这个函数（所有函数都可以作为构造函数）所创建的实例的原型对象

因为prototype用于保存创建实例的数据，所以我们继承时，也是通过往prototype增加属性和方法的方式来使实例拥有这个方法



`实例对象.__proto__`对象指向其`构造函数.prototype`对象，而`构造函数.prototype.__proto__`对象又指向`Object.prototype`对象，最终`Object.prototype.__proto__`指向null，形成一条原型链

```javascript
let people = function(name){
	this.name = name,
	function show(){
	console.log(this.name)
	}
}

let a = new people("abc")
console.log(people.prototype == a.__proto__)//true
```

prototype对象的**作用**就是包含可以由特定类型的所有实例共享的不变的属性和方法，也就是让该函数所实例化的对象们都可以找到公用的属性和方法，函数用作构造函数调用（使用new操作符调用）的时候，新创建的对象会从原型对象上调用公用的属性和方法。



prototype既然是一个对象，也具有`__proto__`和constructor属性

**任何函数在创建的时候，其实会默认同时创建该函数的原型对象prototype。所以`函数.prototype.constructor`就等于这段函数的代码**

`函数.prototype.constructor`就等于这段函数的代码，说明函数prototype就是由自己本身创建的

```
function Foo() {}
console.log(Foo.prototype.constructor) === Foo // true
```

`函数.prototype.__proto__`则指向了`Object.prototype`，说明函数对象的构造函数就Objcet



## 对象的constructor属性

`constructor`属性也是**对象才拥有的**，它是从**一个对象指向一个函数**，含义就是**指向该对象的构造函数**

每个对象都可以找到其对应的constructor，因为创建对象的前提是需要有constructor，而这个constructor可能是对象自己本身显式定义的或者通过`__proto__`在原型链中找到的。

可以通过实例对象的`constructor`属性找到对象的构造函数的代码块

```javascript
let people = function(){}
console.log(people.prototype.constructor == people)//true
```



在构造函数外**重新定义**构造函数的原型prototype时，需要手动添加constructor

```
function User(name){
	this.name = name
}

User.prototype={
//手动绑定构造函数的原型的构造函数
	constructor: User,
	show(){
		console.log(this.name)
	}
}
```





## Object.setPrototypeOf()

**设置对象原型对象**

`Object.setPrototypeOf(childObj,parentObj)`

此时`childObj.__proto__`就指向了`parentObj`，parentObj就成为了childObj的父级元素，childObj可以使用parentObj原型链上的方法



## Object.getPrototypeOf()

**获取一个对象的原型**

`Object.getPrototypeOf(obj)`





## 借用原型链

利用`apply`和`call`借用其他函数原型链上的方法

```javascript
let hd = {
	data:[1,2,3,4,56,8]
}
Object.setPrototypeOf(hd,{
    //为hd原型链添加了max方法，可以筛选出最大值
	max(){
		return this.data.sort((a,b)=>b-a)[0]
	}
});


console.log(hd.max())//56
let a = {
	lessons:{js:99 ,php:100 ,node:66},
	get data(){
		return Object.values(this.lessons)
	}
}

//借用原型链
console.log(hd.max.apply(a))//100
```

**DOM借用数组原型链进行过滤操作**

arguments和DOM方法返回的数组都是类数组，不能使用Array的方法，可以采用借用原型链的方式使用Array方法

```javascript
let btn = document.querySelectorAll("button")
//借用过滤函数,返回有class属性的dom结点组成的新数组
btn = Array.prototype.filter.call(btn,(item)=>{
	return item.hasAttribute("class")
})
```





## 改变原型指向

通过`Object.setPrototypeOf(childObj,parentObj)`来更改对象的原型`__proto__`指向来让两个对象进行继承

```javascript
let childObj={
	name:"a"
}
let parentObj = {
	show(){
		console.log(this.name)
	}
}

Object.setPrototypeOf(childObj,parentObj)
//childObj继承了parentObj的方法和属性
// 相当于parentObj = childObj.__proto__ 

childObj.show()
//a
```



## super

super和this一样类似一个指针，super指向当前对象的原型对象，this指向当前对象

this和super的区别：

- this在对象中指向当前对象
- super在对象中指向对象的原型对象
- super只能在对象中a(){}这样的方法中使用。无法在 a: function(){}这样的函数中使用，会报错



### 实现一个super

```
super.name 
在对象中
name如果是属性，等同于这个：Object.getPrototypeOf(this).name
name如果是方法，等同于这个：Object.getPrototypeOf(this).name.call(this)   
```



### super调用父类属性

```javascript
const father = {
	name:'father'
}

const child = {
	sayName(){
		return super.name;
	}
}
Object.setPrototypeOf(child,father);
console.log(child.sayName())//father
```



### super调用父类方法

**注意super指向原型对象，但通过super调用原型方法时，原理是使用call()传入子级对象的this然后再执行**

因为super调用方法时相当于call方法传入子对象的this后再执行，所以即使调用父类方法，this依然指向子类实例

```
super.showName()等于使用
Object.getPrototypeOf(this).showName.call(this)
```

```javascript
const father = {
    name:'father',
    showName(){
        return this.name
    }
}

const child = {
    name:"child",
    fn(){
        return super.showName()
    }
}
Object.setPrototypeOf(child,father);
console.log(child.fn())  //child
```







# 继承

继承：子类继承父类中的属性和方法

JS继承中，每个实例私有的属性会用this放进构造函数，但公有的方法和属性应该通过，放进构造函数的原型，让所有实例都访问同一片内存存储的属性，避免公有属性也每个实例创建一份，浪费空间



## instanceof

`obj instanceof F `用来测试一个对象是否是一个构造函数的实例

判断的标准是`F.prototype`是否在obj的原型链上

```javascript
function A(){}
function B(){}
function C(){}

let c= new C()
B.prototype = c;
let b= new B()
A.prototype = b;
let a= new A()

console.log(a instanceof A)//true

console.log(a instanceof C)//true
/*
c原型链上包含C.prototype
B.prototype = c;
b原型链上包含C.prototype
A.prototype = b;
a原型链上包含C.prototype
所以a instanceof C为true
*/

console.log(b instanceof C)//true
/*
c原型链上包含C.prototype
B.prototype = c;
b原型链上包含C.prototype
*/

console.log(b instanceof A)//false
```



## 实现一个instanceof

原理：递归查找原型链

```javascript
function myInstanceof(obj,prototype){
	if(!obj.__proto__) return false
	if(obj.__proto__ = constructor.prototype) return true
	return myInstanceof(obj.__proto__,prototype)
}
```





## obj.isPrototypeOf()

`Obj1.isPrototypeOf(boj2)`用于检测obj1是否在obj2的原型链上（obj1是否是obj2的原型链上的对象），返回bool值



## 继承的六种方式

https://www.cnblogs.com/ranyonsue/p/11201730.html

```javascript
function Person(name){
    this.name = name
    this.show = function(){
        console.log(this.name)
    }
}
Person.prototype.age = 21
```

继承是函数与函数之间的，用于构造实例的函数叫类，类创建实例叫构建实例

如果People类构建的实例想使用Person类的属性和方法，就需要让People函数继承于Person函数



### 原型链继承

**通过重定向子类的原型prototype，让父类的属性和方法出现在子类实例的原型链上**

原型链继承不是拷贝继承，是查找型继承，继承原型链上的公用方法和属性，如果一个子类可以对原型链上的属性进行修改，从而会对其他子类实例产生影响，例如age属性是所有子类实例共享的，一个实例对其进行修改，所有实例都会受到影响

子类实例可拥有的属性有：实例的构造函数的属性（name私有），父类构造函数属性（公有name，公有show），父类原型的属性（公有age）。

缺点：1.新实例无法向父类构造函数传参。因为新的实例是子类的实例，跟父类并有没直接关系

​			2.继承单一，子类只能继承一个父类

　　　3.同一个子类实例都会共享父类构造函数上的属性，**父类构造函数私有属性到了子类变为了原型链上的共有属性**



父类原型上的属性是共享的，一个子类实例修改了父类原型属性，另一个子类实例的原型属性也会对应修改，并且父类构造函数因为构建了子类的prototype，父类构造函数中的私有属性也变为了所有子类实例公有属性。

我们想把name设置为每个实例的私有属性，因为父类继承下来的name是在原型链上，所有People1实例共有的，必须在子类People1中手动生成一个私有的name属性，再每个实例去定义私有的name。

```javascript
let People1 = function(name){
    //要求name是私密属性，每个实例独有
    //所以只能在子类里重新添加name属性，让子类使用时私有name优先级比原型链上的公有name高
    this.name = name
}

//封装一个继承函数
function f(childFn,fatherFn){
    childFn.prototype = new fatherFn()
    
    //为了保证类的完整性，重定向原型后要手动加上构造函数
	//People1.prototype.constructor = People 
	//但是不希望constructor属性可被遍历，所以改为使用Object.defineProperty()添加
    Object.defineProperty(childFn.prototype,"constructor",{
    	value: childFn,
    	enumerable: false
	})
}

f(People1,Person)

let a = new People1("wtw")
//a.__proto__ = People.prototype
//People.prototype为new Person()的实例
//所以People.prototype.__proto__ = Person.prototype
//因此a继承到了Person的属性和方法

console.log(a.age)//21
a.show()//wtw
console.log(a instanceof Person)//true
```



### 借用构造函数继承

**用.call()和.apply()将父类构造函数引入子类函数**

原本直接运行父类函数，this指向window，相当于给window加属性，使用call修改this指向子类实例，相当父类直接给子类实例加属性，相当于先创建子类实例，再往实例里加父类的构造函数属性（注意构造函数原型的属性并没有加上去）

因为跟父类的原型（prototype）没有关系，所以无法继承到原型上的私有属性或方法。子类只能拥有父类构造函数的属性（name私有，show私有）

并且这种方式因为没有修改原型链，所以instanceof并不能检测出父类。

特点:   1、只继承了父类构造函数的属性并变为私有，没有继承父类原型上公有的属性。
　　　2、可以继承多个构造函数的私有属性（call多个）。
缺点：1、子类只能继承父类构造函数的属性，**没有继承父类原型prototype的属性**
　　　2、无法实现构造函数的复用。（每次用每次都要重新调用）
　　　3、每个新实例都有父类构造函数的副本，臃肿。

```javascript
function People2(name){
	Person.call(this,name)
    //直接运行Person(name)，this指向window，相当于给window的name属性赋值wtw
    //使用call将this转到b实例上，运行父类函数相当于给b实例加了个属性name，值为wtw
}
let b = new People2("wtw")
b.show()//wtw
//call借用构造函数，给实例b加了构造函数属性，但跟父类的prototype没产生关系，所以只有构造函数的属性
console.log(b.age)//undefined
console.log(b instanceof Person)//false

//b.__proto__ = People2.prototype
//People2.prototype.__proto__ = Object.prototype
//Person没有在b的原型链上
```



### 组合继承

子类构造函数内先借用构造函数，先让父类私有属性赋值子类实例私有属性，然后让子类的原型指向一个空对象，这个空对象的原型再指向父类原型，做到将父类原型连接到子类原型链上

**组合继承**
特点：1、可以继承父类原型上的属性，可以传参，可复用。
　　　2、**父类的私有属性，子类实例中也是私有的，父类原型上的公有属性，子类实例也能使用**
缺点：**调用了两次父类构造函数（耗内存）**，子类的构造函数会代替原型上的那个父类构造函数。

```javascript
function People3(name){
	Person.call(this,name)  //第二次调用父类构造函数
}

//封装一个继承函数
function f(childFn,fatherFn){
    childFn.prototype = new fatherFn()
    //但new会创建一些无用的父类私有属性在这个对象上，并且又调用了一次父类构造函数浪费性能
	//childFn.prototype = Object.create(fatherFn.prototype) 
    //代码改成这样属于寄生组合继承，提高了性能
    
    Object.defineProperty(childFn.prototype,"constructor",{
    value: childFn,
    enumerable: false
})
}
f(People3,Person)
//先借用构造函数，继承父类私有属性
//子类的原型指向一个空对象，这个空对象的原型再指向父类原型

let c = new People3("wtw")
let d = new People3("wy")
c.show()//wtw
d.show()//wy
console.log(c.age)//21
console.log(c instanceof Person)//true
```





### 寄生式继承

寄生式继承，类似设计模式中的工厂模式，此时子类不是一个构造函数了，而是一个普通函数

将构造函数变为普通函数，所有继承过程都在函数内部进行封装，函数结果返回一个新对象，实例时父类的实例，而不是子类的实例，子类变成了父类用来不断创建父类实例的工厂

特点：

1. 不限制调用方式，不管是`new 子类()`还是`子类()`,返回的对象具有相同的效果

缺点：

1. **实例是父类的实例，不是子类的实例**
2. 不支持多继承

```javascript
function people4(name,sex){
    const instance = new Person(name)
    //添加子类私密的属性
    instance.sex = sex
    //返回实例
    return instance
}

let e = people4("wtw","man")
e.show()//wtw
console.log(e.age)//21
console.log(e.sex)//man
//实例是父类的实例，不是子类的实例，子类只是往实例上加属性的工厂
console.log(e instanceof Person)//true

console.log(e instanceof people4)//false
```



### 寄生组合式继承（最常使用）

寄生组合继承和组合继承很相似几乎一模一样,只改动了一行代码

当性能高在只调用了一次父类构造函数

```javascript
function People6(name){
	Person.call(this,name)
}
//封装一个继承函数
function f(childFn,fatherFn){
    // childFn.prototype = new fatherFn() 组合继承，会多调用一次父类构造函数
	var cPrototype = Object.create(fatherFn.prototype)
    Object.defineProperty(cPrototype,"constructor",{
    	value: childFn,
    	enumerable: false
	})
    childFn.prototype = cPrototype
}

f(People6,Person)

let g = new People6("wtw")
g.show()//wtw
console.log(g.age)//21
console.log(g instanceof Person)//true
```



### ES6 class继承

见下一章类



## Mixin模式

可以将一些公用的功能写成对象，需要使用功能的类的实例，可以向类的原型上合并这些对象，来实现这些功能

这样的实现方法叫混合模式

```javascript
let set = {
    setName(name){
        this.name = name
    }
}
let show = {
	showName(){
		console.log(this.name)
	}
}

let people = function(name){
	this.name = name
}

people.prototype = Object.assign(people.prototype,show,set)
let a = new people("wtw")
a.showName()//wtw
a.setName("wy")
a.showName()//wy
```







# 类

class书写构造函数，内部实现还是原型链的方式，class只是ES6提供的创建构造函数语法糖



## constructor构造函数

```javascript
class User{
	constructor(name){
        //这里相当于构造函数User的实例对象的私有属性
        //需要new时赋值的私有属性放这里
		this.name = name
	}
    //无需new时赋值的实例私有变量可以放在构造函数外
    age = 21 //age是实例私有的，但不用new时赋值

    //普通定义的方法相当于构造函数User原型中的实例对象的公有方法
    showName(){
        console.log(this.name)
    }
	
	//箭头函数定义的方法相当于挂载在实例对象上的私有方法
	showAge = () => {
        console.log(this.name)
    }
}

let a = new User('wtw')
console.log(a) //{ age: 21, showAge: [Function: showAge], name: 'wtw' }
```



## super()

在原型那一章介绍过super，在构造函数中第一行调用super()指继承父类的全部私有属性

```javascript
// 父类构造函数
constructor(name) {
	this.name = name
}

// 子类构造函数
constructor(name) {
	super(name) // 调用父类的构造函数
}
```





## 方法

类中定义普通函数是放在类的原型上，使用箭头函数定义类中的函数，则该函数放在对象实例上

```javascript
class User{
    f1(){
        console.log('f1在User原型上')
    }
    f2 = () => {
        console.log('f2在对象实例上')
    }
}

let a = new User()
console.log(a) // { f2: [Function: f2] }

User.prototype.f1() // f1在User原型上
a.f1() // f1在User原型上
// 类原型上的属性实例对象也能访问

a.f2() // f2在对象实例上
a.f2 = function() {
    console.log('123')
}
a.f2() // 123
a.f1() = () => { console.log('456') } // 报错，对象不能直接修改原型上的方法
```





## extends继承

class定义的只能是构造函数，且外部构造函数的原型不能重定向

ES6使用class定义的类的继承要使用关键字：class 子类 extends 父类

**并且子类私有属性constructor中第一行必须使用super()获取父类的constructor私有属性并变为实例的私有属性（类似call的作用），**如果子类没写constructor，系统会默认把constructor和supe填上

`extend+super` 内部实现原理还是寄生组合继承（类似封装好的原型工厂），父类原型接上子类原型链，子类可以向上获取公有属性，并且父类本身私有属性通过`super()`变为子类私有属性

```javascript
class Person{
	constructor(sex){
		this.sex = sex
	}
	showName(){
		console.log(this.name)
	}
    showSex = () => {
        console.log(this.sex)
    }
}
class People extends Person{
	constructor(name,age,sex){
        //子类constructor第一行必须为super()继承父类的私有属性
		super(sex)
		this.age = age
        this.name = name
	}
	showAge(){
		console.log(this.age)
	}
}

let a = new People("wtw",21,"man")
console.log(a) //People { showSex: [Function], sex: 'man', age: 21, name: 'wtw' }
a.showName()//wtw

```





## 静态static

**静态属性**

直接在class类里写变量，则是实例的私有变量（注意不要用let/var/const定义，直接定义，因为编译器不支持这种语法），如果使用static关键字定义变量，相当于往类上挂载了只有类本身能访问的变量（放在类本身上），这个变量实例访问不到，只有类能访问到

```javascript
class User{
    //直接在类内定义变量则为无需new赋值的实例私有变量
    age = 21
	static age = 30
    show(){
        console.log(this.age)
    }
}

let a = new User()
console.log(a.age) // 21
console.log(a.show()) // 21
console.log(User.age) // 30
console.log(User.show())
// 报错，类不能直接使用类中的普通方法或变量，所以找不到show()，会报错show is not a function
```



**静态方法**

和静态变量一样，加入static的方法就不能使用实例来调用了，只有构造函数自己能调用

```javascript
class User{
	show(){
		console.log('实例对象调用')
	}
	static show(){
        console.log(this == User)
		console.log('构造函数自己调用')
	}
}
let  a = new User()
a.show()//实例对象调用

User.show()
// ture
//构造函数自己调用
```



静态属性和静态方法都可以extends给子类，子类可以调用父类挂载的静态属性和静态方法。



## 保护属性(get/set)

**对象保护的属性不被随意的查询到或者被修改**，只能通过内部访问

保护属性不是绝对安全，如果找到了属性名，外界还是能够通过点语法直接访问，只有设置私有属性才能避免外界直接访问



### 设置访问器保护属性

不把属性名直接暴露出去，外部通过访问器控制器来访问(注意变量名与控制器名不能相同)

类中使用构造器访问器需要注意不能让属性名和访问器名构造器名一样，因为因为constructor只是语法糖，实际数据还是直接放在构造函数内，构造器访问器也直接放在构造函数内，这样生成实例后属性就和构造器访问器在同一级了，而get和set关键字规定了同一级下不能有同名的属性或者伪属性

```javascript
// demo1
class User{
	constructor(name){
        //名字需要和访问器构造器不同,否则无法识别this.name是指这个属性还是构造器访问器
        //因为constructor只是语法糖，实际数据还是放在构造函数，这样就和构造器访问器一级了
        //get和set规定了同一级下不能有同名的属性或者伪属性
		this._name = name
	}
    //想给构造函数添加访问器，让对象实例通过点访问和修改属性都能监控到
    //类中构造器访问器需要和属性名不同
	set name(name){
        if(!name){
            console.log('请输入名字')
        }else{
            console.log('修改成功')
            this._name = name
        }
    }
    get name(){
        console.log('访问到name')
        return this._name
    }
}
let a = new User("wtw")
a.name = "abc"
console.log(a.name)
/*
修改成功
访问到name
abc
*/

// demo2
class OrderLine {
  price = 10;
  amount = 10;
  get total() { 
    return this.price * this.amount;
  }
}

let b = new OrderLine()
console.log(b.total) // 100
```



### Symbol保护属性

将变量名设置为Symbol（Symbol的用处），遍历也不能遍历到这个被保护的对象，只能通过访问器访问，保护属性子类可以继承

```javascript
const NAME = Symbol()
class User{
	constructor(name){
		this[NAME] = name
	}

	set name(name){
        if(!name){
            console.log('请输入名字')
        }else{
            console.log('修改成功')
            this[NAME]= name
        }
        
    }
    get name(){
        console.log('访问到name')
        return this[NAME]
    }
}
let a = new User("wtw")
a.name = "abc"
console.log(a.name)
/*
修改成功
访问到name
abc
*/
```



## 私有属性#

外部完全不能直接访问的属性，保护属性如果外界找到了最终的属性名还是能够通过点访问，并且也会子类实例也能通过继承使用和访问

而私有属性或者私有方法外界找到了变量名也不能通过点访问，只能通过内部来访问，子类也不能通过原型链使用

私有属性直接写在class体内，属性名前加#

私有方法则需要用`#私有方法变量名 = 函数`的方式定义，但注意不要用let/var/const定义，直接定义，因为编译器不支持这种语法

```javascript
class User{
	#name = "wtw"
	
	set name(name){
        if(!name){
            console.log('请输入name')
        }else{
            console.log('修改成功')
            this.#name = name
        }
        
    }
    
    get name(){
        console.log('访问到name')
        this.#check() //类内部可以调用私有方法
        return this.#name
    }
	
	//注意不要加let，编译器不支持这种语法会报错
	#check = ()=>{
    	console.log('调用私有方法')
	}
    
}
let a = new User("wtw")
//私有属性只能通过内部访问，外界不能直接访问
a.name = "abc"
console.log(a.name)
/*
修改成功
访问到name
调用私有方法
abc
*/
console.log(a.#name)  //报错，私有属性外界不能直接访问，会报错
 a.#check()  //报错，私有方法外界不能直接访问，会报错
```



## 类和原型链构造对象的区别

通过`构造函数.prototype.方法`添加的公共方法能被遍历到，因为对象特征默认`enumerable`可被遍历

class内的添加的方法对象特征`enumerable`是默认不可被遍历的



## 类自动严格模式

声明类时，类内部自动启动严格模式

```javascript
class User{
    show(){
     function test(){
       console.log(this)
     }
     test()
    }
    showThis = () => {
      const test = () => {
       	console.log(this)
      }
      test()
    }
}
let a = new User()
a.show()
//undefined，如果不为严格模式普通定义函数test内this默认应该指向window
a.showThis()
// a对象，因为定义test时使用箭头函数
```





# 异步

异步加载也不按照先后顺序，会有一个模块，谁先加载完谁就进入任务队列，所以有的时候为了保证有顺序的执行异步会出现很多不断的回调地狱

## 定时器

定时器是值达到设置时间后将任务放入宏任务队列中等待执行

- setInterval()  循环定时器

```
window.setInterval(callback,time)
每过time时间，就将任务放入宏任务队列一次

window.clearInterval(定时器对象) 
清除定时器
```

- setTimeout()  一次性定时器



定时器中回调使用普通函数this指向window，使用箭头函数指向当前作用域的this

```javascript
const a = {
  wait1() {
    setTimeout(function(){
				console.log(this) // window
		})
  },
  
	wait2() {
		setTimeout(() => {
				console.log(this) // a
		})
	}
}
```





## 文件加载

文件加载（例如加载图片）也是异步执行的宏任务，也要加入轮询队列等主进程执行完后执行文件加载任务

```javascript
function loadImg(src){
	let image = new Image()
	iamge.src = src
}
loadImg('xxxxx')
console.log('这里比图片先加载')
//因为文件加载时宏任务，需要等到同步任务微任务执行完在执行宏任务
```



## ajax

ajax请求也是异步的



## promise

promise也是异步的，promise的构造函数的任务同步任务，promise.catch()/then()的任务是微任务队列，当promise接到通知执行回调就会把任务放进微任务队列





# Promise

Promise是ES6异步编程的一种解决方案，解决callback hell（回调地狱），常见的场景比如网络请求时需要异步，否则用户页面会卡顿。所以往往我们会使用ajax传入另外一个函数，在数据请求成功时，将数据通过传入的函数回调出去。但这样可能会出现复杂繁琐的的回调。Promise能优雅的解决这个问题。
sync--->同步

async--->异步



## Promise基本使用

Promise是对异步操作setTimeout进行封装，Promise构造函数要求传入一个函数
这个函数要求包括两个参数resolve，reject（可选）
Promise构造函数会根据参数链式调用then函数，catach函数，finally函数

**finally()是无论成功或者失败都会执行的回调函数，当执行成功或者失败的回调后就会去执行finally**



then函数的参数也是一个函数，并且then或catch也可以默认返回（默认返回的是成功的promise并且返回值为传入的参数）或者主动返回一个新的Promise对象，注意最后一个then或者catch默认返回的promise是一个解决状态的promise，所以catch()之后的then还会被执行！！！

```javascript
let a = new Promise((resolve，reject)=>{
	reject('err')
}).then(null,(err)=>{
	console.log(err)
})
// 变量a最后是一个解决状态返回的的promise
```



reject()是失败时调用的函数，会自动调用离他最近的catch()或抛出异常的地方进行处理，

**多重Promise时可以只写一个catch处理所有的错误，把catch写最后，一旦promise不断回调中某一步触发reject，就直接会跳转到最后的catch这里运行**



主动调用resolve或者reject立即执行then或catah
Promise 构造函数是同步执行的，promise.then 中的函数是在微任务队列中异步执行的。

```javascript
new Promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve('hello world')
        reject('first error message')
    },1000)
}).then((data)=>{
    console.log('第一次网络请求结果'+data);
    return new Promise((resolve,reject)=>{
        //第二次网络请求代码
        setTimeout(()=>{
            resolve('hello world')
        },1000)
    }).then((data)=>{
        console.log('第二次网络请求结果'+data);
        return new Promise((resolve,reject)=>{
            //第三次网络请求代码
            setTimeout(()=>{
                resolve('hello world')
            },1000)
        }).then((data)=>{
            console.log('第三次网络请求结果'+data);
        })
    })
}).catch((err)=>{
    //请求失败代码，所有的失败都会跳转到这里执行
    console.log(err)
}).finally(()=>{
    //只会执行一次finally
    console.log('无论失败成功都会执行')
})

/*
第一次网络请求结果hello world
第二次网络请求结果hello world
第三次网络请求结果hello world
无论失败成功都会执行
*/
```

也可以给then传入两个函数
分别在网络请求成功(调用resolve)或者失败(调用reject)的时候调用

```
new Promise((resolve,reject)=>{
    setTimeout(()=>{
        //resolve('hello')
        reject('err')
    },1000)
}
).then(data=>{
    console.log(data)
},err=>{
    console.log(err)
})
```



**当pormise的resolve和reject接收到的参数也是一个promise时，会根据参数影响到后面的执行**

```javascript
let p = new Promise((resolve,reject)=>{
    reject('错误')
}).then(null,err=>{})
new Promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve(p) //err错误
        //原本应该执行成功的回调
        //但参数p也是一个promise并且是失败的promise
        //所以影响这里去执行失败回调
    },1000)
}
).then(data=>{
    console.log(data)
},err=>{
    console.log('err'+err)
})


```



## 什么时候使用Promise

一般情况下是异步操作的频繁嵌套时，使用Promise对异步操作进行一步步封装

```javascript
new Peomise()后会保存一些状态信息并执行参数函数
在执行传入的参数函数是会传入resolve，reject参数函数
处理数据的函数建议放入then(()=>{})并使用resolve()调用
根据数据处理结果再一次进行网络请求
在then的函数使用return new Promise()下一次进行请求操作
```



## Promise单一状态

promise一共有三种状态，但同一时间只可能出现一种，

pending：等待状态，比如正在进行网络请求，或者定时器没有到时间。

fulfilled：满足状态，当我们主动回调了resolve时，就处于该状态，并且会回调.then()

rejected：拒绝状态，当我们主动回调了reject时，就处于该状态，并且会回调.catch()

可以输出一个promise对象来查看这个对象的当前状态





## Promise状态不可逆

每次Promise只会在reject和resolve中选择一个执行，执行一个后状态不可逆不可撤销，不可能再执行另外一个回调，而且必定会执行finally函数





## Promise的链式调用

无论是then还是catch都可以返回一个P新romise对象。并且他不返回任何值时默认也是返回一个成功的promise对象





### 默认返回一个成功的promise

```javascript
new Promise((resolve,reject)=>{
	resolve('ok1')
}).then((data)=>{
	return data
},null).then((data)=>{
	console.log(data) //ok1
},null)
```

可以看到在第一个then回调里返回了一个data但第二个then也可以成功输出，说明第二个then就是对第一个then返回的Promise的处理，并且第一个then返回的promise默认是成功的，如果我们没有返回参数。参数默认为undefined，如果需要返回值为一个失败的promise则需要返回一个new Promise对象并调用失败reject



### 手动返回一个新promise

这里我们直接通过Promise包装了一下新的数据，将Promise对象返回了

Promise.resovle()：将数据包装成Promise对象，并且在内部回调resolve()函数

Promise.reject()：将数据包装成Promise对象，并且在内部回调reject()函数

```
return new Promise((resolve)=>{
    resolve(res+'111')
})
简写 return Promise.resolve(res+'111')
再简写 return res+'111'
内部会进行Promise.resolve包装

请求错误：return Promise.reject('error')
也可以用抛出异常 throw 'error'
```

```javascript

new Promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve('aaa')
    },1000)
}).then((res)=>{
    console.log(res+'第一次处理')
    //aaa第一次处理
    //每次处理完成对结果再进行一次处理
    //这次返回Promise目的是处理数据不是异步操作
    
    return Promise.resolve(res+'111')
    //简写调用方法如上，普通写法如下
    // return new Promise((resolve)=>{
    //     resolve(res+'111')
    // })
},null).then(res=>{
    console.log(res+'第二次处理')
    //aaa111第二次处理
    return Promise.resolve(res+'111')
    // return new Promise((resolve)=>{
    //     resolve(res+'222')
    // })
},null).then(res=>{
    console.log(res+'第三次处理')
    //aaa111222第三次处理
},null)
```



## Promiese API

### resolve()

Promiese.resolve()  返回成功的语法糖

```javascript
new Promise.resolve(1)
等价于
new Promise(resolve => resolve(1))
```



### reject()

Promiese.reject()  返回失败的语法糖

```javascript
new Promise.reject(1)
等价于
new Promise((resolve, reject) => reject(1))
```



### all()

all()为只有所有请求都成功时才执行then函数,如果其中有一个失败，会立即返回结果。
**Promise.all()参数为一个数组，数组每个元素为一个promise对象并链式调用then方法**，**数组内存储new Promise，只有等到所有Promise都成功resolve时then才会执行，只要有一个promise触发拒绝，会立刻执行拒绝回调**
并且所有resolve传入的值都在then中集成了一个数组results，下标与在all数组中Promise所处下标一致

```javascript
Promise.all([
  new Promise(resolve => {
     setTimeout(()=>{
        resolve('result1')
     },1000)
  }),
  new Promise(resolve => {
     setTimeout(()=>{
        resolve('result2')
     },5000)
  })
])
.then(results=>{
    //5秒等第二个Promise执行后才会执行then
        console.log(results)
    //[ 'result1', 'result2' ]
})
```





### allSettled()

allSettled()也必须传入一个数组，数组内是promise对象，需要等所有promise执行完后就会执行成功回调（并不需要每个promise都被解决）,数组内所有promise都执行完后，allSettled()也返回一个promise对象，then传入的参数result也是一个数组，但是是一个对象数组，每个对象存放了参数promise数组的传入的数据和他最后是解决状态还是拒绝状态。

```javascript
Promise.allSettled([
    new Promise(resolve => {
              setTimeout(()=>{
                  resolve('result1')
              },1000)
          }),
    new Promise((resolve,reject) => {
              setTimeout(()=>{
                  reject('result2')
              },5000)
          })
      ])
  .then(
  	results=>{
      console.log(results)
  	})
/*
[
  { status: 'fulfilled', value: 'result1' },
  { status: 'rejected', reason: 'result2' }
]
*/
```



### race()

Promise.race()依然传入一个promise数组，他执行最快获得结果的数组中的promise对象

他的then回调将使用promise数组中第一个返回结果的那个promise对象传入的参数，并执行回调函数

```javascript
Promise.race([
  new Promise(resolve => {
         setTimeout(()=>{
              resolve('result1')
          },1000)
      }),
  new Promise((resolve,reject) => {
          setTimeout(()=>{
               reject('result2')
           },500)
      })
])
.then(results=>{
  console.log(results)
},results=>{
  console.log('err '+results)
  //记得填上失败函数
  //如果数组中第一个返回的是失败的promise就会执行这里的失败函数
})

//err result2
```



### finally()

Promise.finally()是无论返回结果成功或者失败都会执行的函数，一般放在最后执行一个固定逻辑



### done()

Promise 对象的回调链，不管以`then`方法或`catch`方法结尾，要是最后一个方法抛出错误，都有可能无法捕捉到（因为 Promise 内部的错误不会冒泡到全局）。因此，我们可以提供一个`done`方法，总是处于回调链的尾端，保证抛出任何可能出现的错误。



## Promise队列

普通的promise中then回调会返回一个默认成功的promise对象，然后立马就会执行下一个then，并不会等到第一个then中全部任务完成

```javascript
let p = Promise.resolve('a')
p.then(
 ()=>{
    setTimeout(()=>{
        console.log('1')
        console.log('2')
    },1000)
   console.log('3')
    //这句语句执行后立马就会默认返回成功的promise并执行下一个then
    //不会等待宏任务队列中的定时器执行完
 },
 ()=>{
    console.log('err1')  
})
.then(
()=>{
    console.log("4")
 },()=>{
    console.log('err2')
})
/*
3
4
1
2
*/
```

**Promise队列就是指我们在调用回调时，手动返回Promise，并且将resolve/reject放到所有任务执行完后再执行，当这个then的全部任务执行完后再执行下一个then**

**所以想要保证上一个then执行完再执行下一个then，需要让then()主动返回一个Promise并且让resolve最后执行**

```javascript
let p = Promise.resolve('a')
p.then(()=>{
    return new Promise((resolve,reject)=>{
        setTimeout(()=>{
            console.log('1')
            console.log('2')
            resolve()
        },1000)
       console.log('3')
    })
},()=>{
    console.log('err1')
})
.then(
()=>{
    console.log("4")
 },()=>{
    console.log('err2')
})
/*
3
1
2
4
*/
```



# Generator/yield

生成器关键字Generator/yield

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同，Generator 最大的特点就是可以控制函数的执行。Generator在形式上和函数差不多，只是在function和函数名之间多了一个*。Generator内部必须使用yield关键字。

```javascript
function * gen(){
  var result1 = yield 'hello';
  var result2 = yield 'world';
  return result1 + result2;
}
```

当调用Generator函数时，并不会执行函数内部的代码，而是返回一个遍历器对象g，该遍历器包含一个next方法。每次执行next方法，Generator函数体会开始执行，直到遇到yield语句，执行该语句并在此暂停。用法如下:

```javascript
var g = gen();
console.log(g.next()) //{value : 'hello', done : false}

console.log(g.next(1)) // 将result1赋值为1
//{value : 'world', done : false}

console.log(g.next(2)) // result2赋值为2，所以打印出return的值为3
//{value : 3, done: true}

console.log(g.next())
//{value : undefined, done: true}
```

调用next方法会返回一个对象，这个对象包含两个属性，value和done，value即是当前yield语句的值。done表示Generator函数体是否被执行完。

next方法同时接受一个参数，这个参数会作为**上一个**yield语句的返回值，可以被后面的程序所使用。当程序执行完或者遇到return语句，value即为函数体的返回值，done就变成了true。

至此，如果再执行next方法，value就为undefined， done依然是true。



# async/await

async/await是Promise+then的语法糖，能写出更优雅的异步函数，**注意await只能在async中使用**

async相当于promise的语法糖，async 函数返回的是一个 Promise 对象。async 函数（包含函数语句、函数表达式、Lambda表达式）会返回一个 Promise 对象，如果在函数中 `return` 一个直接量，async 会把这个直接量通过 `Promise.resolve()` 封装成 Promise 对象，没有return则会return一个`undefined`

```javascript
async function a(){return 'abc'}
console.log(a())
//a输出是一个已解决的promise对象
//Promise {'abc'}
```

```javascript
async function a(){
	return 'abc'
}
a().then((data)=>{
	console.log(data)
})
//abc
```



## await

await关键字是等待的是一个表达式（表达式不限定结果，不过更多的是放一个返回promise 对象的表达式），等待这个表达式的计算结果，**await相当于then的语法糖**

```javascript
async function a(){
  console.log('1')
  let b = await 5+5
  console.log(b)
}
a()
setTimeout(()=>{
    console.log('3')
},0)
console.log('2')
//1
//2
//10
//3
```



**但是await后面一般会返回一个promise对象，而之后到下一个await的代码相当于都放进了then中处理，then的微任务队列解释了为什么会停下来先执行之后的代码，却又比宏任务定时器先执行**

**接收await的变量相当于这个promise中resolve中的参数，之后到下一个await的代码块相当于then中的处理函数**

之前Promise队列说过，想要保证Promise的then之间顺序调用，必须每一个then都返回一个promise对象，并且把resolve最后调用。

await后面跟Promise对象（**用async定义的函数默认返回promise对象**），相当于后面的代码（到下一个await的之间的代码）全部放进这个then/catch中，用一个变量接收await中promise中resolve/reject传入的参数，后面的代码直接用这个变量当传入的参数即可

**并且`await`会引起`async`函数内部代码的阻塞，必须要这个await的状态改变，执行了后面的代码，才会执行下一个await的**

```javascript
async function a(){
  console.log('1')
  let b = await new Promise((resolve,reject)=>{
    console.log('wtw')
    resolve('abc')
    console.log('456')
  })
  //b相当于resolve/reject中的参数'abc'
  console.log(b)
 //console.log(b)相当于在后面接了个.then((b)=>{console.log(b)})
}
a()
setTimeout(()=>{
    console.log('3')
},0)
console.log('2')
// 1
// wtw
// 456
// 2
// abc
// 3
```





# switch

最基本的switch可以判断某个固定值

```javascript
let err = 'warning'
switch(e){
	case 'notice':
		console.log('notice'); break;
	case 'warning': 
		console.log('warning');
	case 'err': 
		console.log('err');break;
	default:
    	console.log('未知');break;
}

运行结果：
warning
err
在第一次匹配成功之后switch就会把接下来全部的表达式运行直到遇到break
```

如果switch后接true，然后case后接bool类型，可以达到if/else的效果

```javascript
function message(score){
	switch(true){
    case score>=80:
			console.log('优秀');break;
	case score>=60:
			console.log('合格');break;
    default:
         	console.log('不及格');break;
	}
}
message(81);
//优秀
```





# requestAnimationFrame

`requestAnimationFrame`是浏览器用于定时循环操作的一个接口，类似于setTimeout，主要用途是按帧对网页进行重绘。可以使用`requestAnimationFrame()`来制作动画。

动画想要流畅，更新频率要60帧/s，即16.67ms更新一次视图，requestAnimateFrame这个api就是自动更新动画的api，类似于setTimeout。

setTimeout需要手动控制更新频率并计算每次更新多少（例如3秒180帧，一共需要增加540px，每帧就需要手动计算增加3px），RAF可以实现自动控制更新频率（一秒自动更新60次）

**切换到其他网页标签或隐藏在iframe中，RAF会自动暂停，而setTimeout依然执行，浪费性能**



- setTimeout 更新动画

  需要手动计算更新频率16.7ms

```javascript
let curWidth = 100
const maxWidth = 640

function animate() {
  curWidth = curWidth + 3
  document.querySelector('#div1').width = curWidth
  if(curWidth < maxWidth) {
     setTimeout(animate, 16.7)
  }
}

animate()
```



- requestAnimationFrame 自动执行动画函数

```javascript
let curWidth = 100
const maxWidth = 640

function animate() {
  curWidth = curWidth + 3
  document.querySelector('#div1').width = curWidth
  if(curWidth < maxWidth) {
     window.requestAnimationFrame(animate) // 无需手动控制时间
  }
}

animate()
```





# 循环

## for

设置了10个a标签，点击会打印数字，这样写每个a点击都打印10，不形成闭包。因为i在全局定义的，每次点击时i已经是10了。改为for( let i = 0; i < 10 ; i++)可以修复，let是块级作用域，每次for都是一个新块

```javascript
window.onload = () => {
  let i , a
  for( i = 0; i < 10 ; i++) { // 修改为for( let i = 0; i < 10 ; i++)可以修复，let是块级作用域，每次for都是一个新块
    a = document.createElement('a')
    a.innerHTML = i + '<br>'
    a.addEventListener('click',function(e) {
      e.preventDefault()
      alert(i)
    })
    document.body.appendChild(a)
  }
}
```

​	

使用var以后，点击每个a也弹出10

```javascript
window.onload = () => {
  let a
  for(var i = 0; i < 10 ; i++) {
    a = document.createElement('a')
    a.innerHTML = i + '<br>'
    a.addEventListener('click',function(e) {
      e.preventDefault()
      alert(i)
    })
    document.body.appendChild(a)
  }
}
```

可以利用闭包来解决



### var和let在for循环中的不同

因为for循环也是一个块级作用域，循环条件也在作用域内，所以for循环中let定义的i无法在外部访问

```javascript
for(var i =0;i<=3;i++){
	console.log(i)
    //0 1 2 3
}
console.log(i) //4

for(let t =0;t<=3;t++){
	console.log(t) //0 1 2 3
}
console.log(t) //报错，t未定义，t在块级作用域中，外部无法访问
```



### var和let在for循环中定时器下的不同

var没有块级作用域导致定时器运行时for循环已经运行完毕，i=4了

```javascript
for(var i =0;i<=3;i++){
    //for循环会先执行完，定时器压入任务队列等其他任务执行完再执行
	setTimeout(function(){
		console.log(i) 
	},0)
}
console.log(i,"-----")//这里会比定时器先执行
//4 -----
//4
//4
//4
//4
```



**var没有块级作用域但是有函数作用域，可以利用函数作用域解决上面那个问题**

每次循环到自动执行函数都会把当前的i值当做参数保存进函数作用域，等定时器运行时调取函数作用域内保存的i值

```javascript
for(var i=0;i<=3;i++){
	(function(i){
        console.log("....")
		setTimeout(function(a){
			console.log(i)
		},0)
	})(i);
    //分别开了四个函数作用域保存了i
}
console.log("-------")
/*
....
....
....
-------
0
1
2
3
*/
```



let定义的循环体可以正常输出预期。因为let的值保存在块级作用域中，所以就等于每一次循环都重新定义一次let i，这样循环就只能一次次给函数传值。**在这里let本质上就是形成了一个闭包**，i每次运行完不会更新，而是保存进闭包，因为之后的定时器无法单独完成功能需要数据来源i。

```javascript
for(let i=0;i<=3;i++){
  console.log('*****')
	setTimeout(function(){
		console.log(i)
	},0)
}
console.log('-----')

/*
*****
*****
*****
*****
-----
0
1
2
3
*/

```





## while

```javascript
let n = 0;
while(n<5){
	n++;
}
console.log(n);//5
```





## do...while

先进一次循环再判断，至少执行一次循环

```javascript
let n = 0;
do{
	n++;
}while(n>5)
console.log(n);//1
```



## break/continue/label

break 退出整个循环体

continue 跳出本次循环

label  调到指定标签位置，不推荐，类似goto，会导致程序难以阅读



## for-in

`for(var 变量 in 对象)`

循环可以遍历引用类型的数据，如数组，对象等，**for-in获得的是引用的索引值**

使用for in会遍历引用所有的可枚举属性（包括原型链上添加的），因此for in更适合遍历对象，**不建议用for-in遍历数组**，因为如果给数组的原型上添加属性，for in也会把添加的属性遍历出来

for循环的效率比for in高

```javascript
Array.prototype.method=function(){}

var arr = [99,100,55,66]
for (var index in arr) {
    console.log(index);
}

//0
//1
//2
//3
//method 
```



## for-of

for-of适用遍历数组/字符串/map/set等拥有迭代器对象的集合.但是不能遍历对象,因为没有迭代器对象.与forEach()不同的是，它可以正确响应break、continue和return语句



**for-of遍历的是值而不是索引**

```javascript
Array.prototype.method=function(){}
var arr = [99,100,55,66]
for (var value of arr) {
    console.log(value);
}
//99
//100
//55
//66
```



## forEach

forEach() 方法用于调用数组的每个元素，并将元素传递给回调函数执行。

```
forEach(Array,callback)
```

**使用foreach遍历数组的话，使用break不能中断循环，使用return也不能返回到外层函数。**





# 模块化

常见的模块化规范：CommonJS，AMD，CMD，ES6的Modules

使用模块化需要导入相应语法环节，模块化有两个核心：导入和导出

浏览器环境下不能直接使用模块化，因为浏览器不认识模块化的代码，需要使用webpack打包编译后的js文件才能被浏览器识别。



## ES6的Modules

浏览器环境仅支持ES6的模块化语法

ES6模块化：export，export default，import

```html
type="module" 
表示这个文件是一个模块，不同文件都是不同空间，不会出现命名冲突
但这样不同模块之间不能轻易互相访问
只能访问模块导出的数据

<script src="aaa.js" type="module"></script>
<script src="bbb.js" type="module"></script>
```



### export

```javascript
//aaa.js
var name = '小明';
var flag = true;
var age = 18;
function sum(num1,num2){
    return num1+num2;
}
if(flag){
    console.log(sum(20, 30));
}

//导出方式一：对象导出
export{
    //变量填入导出数组
    flag, sum
}

//导出方式二：export逐个导出
export var height = 170;
export var weight = 140;


//导出函数/类
export function mul(weight,height){
    return weight+height;
}

export class Person{
    run(){
        console.log('runing');
    }
}
```



### export default

导出变量后可以由导入者命名变量，但export default只能有一个，不然导入文件无法分清导入哪个变量

```javascript
//导出文件
//允许导出者可以重新给变量命名
//export default只能有一个
const address='北京';
export default address;

//第二个导出文件
//一个文件只能有一个export default
export default function(){
    console.log('export default');
}

//导入文件
//导入这个变量并重新命名为addr
import addr from './aaa.js'
console.log(addr);

```



### import

导出的变量名不能变，导入时也得以这个变量名导入

```javascript
//import {接收对象} from '文件路径'
//mmm.js
//导入导出对象的变量

import {flag,sum} from "./aaa.js";
if(flag){
    console.log('小明哈哈');
    console.log(sum(30, 40));
}
//导入export的变量
import {height,weight} from "./aaa.js";
console.log(height);
console.log(weight);
//导入export的function
import{mul,Person} from "./aaa.js";
console.log(mul(30,50));
const P = new Person();
P.run();

//通配符整体导入
//导入导出文件导出的所有变量
import * as aaa from "./aaa.js";
//这样导入使用变量要用对象aaa代理
console.log(aaa.flag,'......');

```



### import()

`import`命令会被 JavaScript 引擎静态分析，先于模块内的其他模块执行（叫做”连接“更合适）。所以，下面的代码会报错。

```js
 // 报错
if (x === 2) {
  import MyModual from './myModual';
}
```

 上面代码中，引擎处理`import`语句是在编译时，这时不会去分析或执行`if`语句，所以`import`语句放在`if`代码块之中毫无意义，因此会报句法错误，而不是执行时错误。也就是说，`import`和`export`命令只能在模块的顶层，不能在代码块之中（比如，在`if`代码块之中，或在函数之中）。

这样的设计，固然有利于编译器提高效率，但也导致无法在运行时加载模块。在语法上，条件加载就不可能实现。如果`import`命令要取代 Node 的`require`方法，这就形成了一个障碍。因为`require`是运行时加载模块，`import`命令无法取代`require`的动态加载功能。

`import()`函数，完成动态加载，`import()`返回一个 Promise 对象，加载的模块会作为一个对象，当作`then`方法的参数。。`import()`类似于 Node 的`require`方法，区别主要是前者是异步加载，后者是同步加载。

- 按需加载

`import()`可以在需要的时候，再加载某个模块。

```js
// 当触发点击事件才去加载'./moduleA.js'
button.addEventListener('click', event => {
  import('./moduleA.js')
  .then(dialogBox => {
    dialogBox.open();
  })
});
```

- 条件加载

   `import()`可以放在`if`代码块，根据不同的情况，加载不同的模块。

```js
// 根据条件加载不同模块
if (condition) {
  import('./moduleA.js').then(...);
} else {
  import('./moduleB.js').then(...);
}
```

- 动态的模块路径

 `import()`允许模块路径动态生成，在参数中传入函数，根据函数`f`的返回结果生成不同的加载路径，加载不同的模块。

```
 import(f()).then(...);
```

 



### import as

`import * as obj from 'xxx'`  会将 xx中所有 export 导出的内容组合成一个对象obj返回，例如import * as obj from 'xx'  这种写法是把所有的输出包裹到obj对象里;



### export/export default 同时存在

当export/export default同时存在时import的方式需要有所区别

导入export的需要使用`{}`包裹导入，导入匿名的模块就可以直接import

```javascript
// A.js
const obj1 = {}
export const obj2 = {}
export default obj1
```

```javascript
// B.js
import o1 from './A.js'
import { obj2 } from './A.js'
// import obj2 from './A.js' 这样导入的默认是A1

```







## CommonJS

CommonJS是node支持的模块化语法规范

CommonJS：module.exports，exports，require



### module.exports/exports

`module.exports`才是最终导出模块对象，exports只是`module.exports`导出模块对象的一个别名指针
导出单个属性的时候两者作用完全相同。但exports不能用于导出对象，否则就是修改了原本指针指向，系统就找不到导出模块。
导出文件文件最终导出的对象永远是`module.exports`，但`module.exports={}`只能导出一个对象，如果需要导出多个对象不能再来一次`module.exports={}`(上一个导出对象会被覆盖)。

`module.exports.对象名={}`这样可以用于导出多个对象而不怕被覆盖

```javascript
var flag = true;
function sum(num1,num2){
    return num1+num2;
}
let a = 123
let b = 234
console.log(a)
//属性分开各自导出，exports不能导出对象，只可以导出单个属性
exports.a = a
module.exports.b = b;

//module.exports还可以直接导出一个对象，包含所有导出属性
//exports不能导出对象
//注意module.exports导出对象会直接覆盖之前的所有导出属性的导出
module.exports={
       flag:flag,
       sum:sum
}
```



exports.default可以匿名导出

```js
const config = {};
exports.default = config;
```





### require

CommonJs是动态语法可以写在判断里（即最终可能并不加载运行），属于运行时加载，都只有在代码运行时才能确定这些东西。

第一次执行导入时会先运行一次导出文件用于初始化，后续再发生导入该文件操作时不会再次运行导出文件

```javascript
var a = require('导出文件路径');
//打印123
//第一次导入会运行一次导出文件，所以会打印一次123
var flag = a.flag;
var sum = a.sum;
//var {flag,sum} = require('导出文件路径')
//语法糖写法，一步当以上三步
```





# 模块化特点

## CommonJS

1. **对于基本数据类型，属于复制。即会被模块缓存。同时，在另一个模块可以对该模块输出的变量重新赋值。**
2. **对于复杂数据类型，属于浅拷贝。由于两个模块引用的对象指向同一个内存空间，因此对该模块的值做修改时会影响另一个模块。**
3. 当使用require命令首次加载某个模块时，就会运行整个模块的代码，并将变量下载到缓存
4. 当使用require命令加载同一个模块时，不会再执行该模块，而是取到缓存之中的值。也就是说，CommonJS模块无论加载多少次，都只会在第一次加载时运行一次，以后再加载，就返回第一次运行的结果，除非手动清除系统缓存。
5. 循环加载时，属于加载时执行。即脚本代码在require的时候，就会全部执行。一旦出现某个模块被"循环加载"，就只输出已经执行的部分，还未执行的部分不会输出。

## ES6Module模块

1. ES6模块中export的值属于【动态只读引用】。

2. 对于只读来说，即不允许修改引入变量的值(包括基本类型)，import的变量是只读的（类似const），不论是基本数据类型还是复杂数据类型。当模块遇到import命令时，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值，即运行时再去对应文件找值，所以不允许修改引入的变量，但是可以给应用类型添加属性和方法，类似与const。

   export default除外，export default是复制到缓存，所以可以修改

3. 对于动态来说，原始值发生变化，import加载的值也会发生变化。不论是基本数据类型还是复杂数据类型。

4. 循环加载时，ES6模块是动态引用。只要两个模块之间存在某个引用，代码就能够执行。



## CommonJs和ES6 Module的区别

1、

CommonJs导出的是变量（包括引用类型）的一份拷贝。

ES6 Module导出的是变量的指针(export default除外)，也就是说ES6 module导入的变量是const的，该变量与原本的变量相绑定。



2、

CommonJs是单个值导出，ES6 Module可以导出多个



3、

CommonJs是动态语法可以写在判断里（即最终可能并不加载运行），属于运行时加载，都只有在代码运行时才能确定这些东西。

ES6 Module静态语法只能写在顶层，即静态加载，在编译时就能够确定模块的依赖关系，以及输入和输出的变量。可以使用import()做到运行时加载，但import()属于异步加载。



4、

CommonJs的this是当前模块

ES6 Module的this是undefined。



5、

Module的 import() 实现运行时加载属于异步加载

CommonJs的 require() 运行加载时属于同步加载。



6、

CommonJs需要运行并加载全部的文件（首次加载时会把被加载文件先运行一次）

ES6 Module可以只加载模块部分所需要的内容。





# postMessage

postMessage是html5引入的API,postMessage()方法允许来自不同源的脚本采用异步方式进行有效的通信,可以实现跨文本文档，多窗口，跨域消息传递。多用于窗口间数据通信,这也使它成为跨域通信的一种有效的解决方案.



## 语法

```
otherWindow.postMessage(message, targetOrigin, [transfer]);
```

- `otherWindow`

  其他窗口的一个引用，比如iframe的contentWindow属性、执行window.open返回的窗口对象、或者是命名过或数值索引的window.frames。

- `message`

  将要发送到其他 window的数据。它将会被结构化克隆算法序列化。这意味着你可以不受什么限制的将数据对象安全的传送给目标窗口而无需自己序列化。

- `targetOrigin`

  通过窗口的origin属性来指定哪些窗口能接收到消息事件，其值可以是字符串"*"（表示无限制）或者一个URI。在发送消息的时候，如果目标窗口的协议、主机地址或端口这三者的任意一项不匹配targetOrigin提供的值，那么消息就不会被发送；只有三者完全匹配，消息才会被发送。这个机制用来控制消息可以发送到哪些窗口；例如，当用postMessage传送密码时，这个参数就显得尤为重要，必须保证它的值与这条包含密码的信息的预期接受者的origin属性完全一致，来防止密码被恶意的第三方截获。**如果你明确的知道消息应该发送到哪个窗口，那么请始终提供一个有确切值的targetOrigin，而不是\*。不提供确切的目标将导致数据泄露到任何对数据感兴趣的恶意站点。**

- transfer 可选

  是一串和message 同时传递的 [`Transferable`](https://developer.mozilla.org/zh-CN/docs/Web/API/Transferable) 对象. 这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。



接收消息的页面可以绑定message事件来得知消息的发送

```js
window.addEventListener("message", receiveMessage, false);

function receiveMessage(event)
{
  // For Chrome, the origin property is in the event.originalEvent
  // object. 
  // 这里不准确，chrome没有这个属性
  // var origin = event.origin || event.originalEvent.origin; 
  var origin = event.origin
  if (origin !== "http://example.org:8080")
    return;

  // ...
}
```

event的属性有:

- `data`

  从其他 window 中传递过来的对象。

- `origin`

  指的是发送消息的窗口的源，调用 `postMessage`  时消息发送方窗口的 [origin](https://developer.mozilla.org/en-US/docs/Origin) . 这个字符串由 协议、“://“、域名、“ : 端口号”拼接而成。例如 “`https://example.org` (隐含端口 `443`)”、“`http://example.net` (隐含端口 `80`)”、“`http://example.com:8080`”。请注意，这个origin不能保证是该窗口的当前或未来origin，因为postMessage被调用后可能被导航到不同的位置。

- `source`

  对发送消息的窗口对象的引用; 您可以使用此来在具有不同origin的两个窗口之间建立双向通信。

  



## 安全问题

**如果您不希望从其他网站接收message，请不要为message事件添加任何事件侦听器。** 这是一个完全万无一失的方式来避免安全问题。

如果您确实希望从其他网站接收message，请**始终使用origin和source属性验证发件人的身份**。 任何窗口都可以向任何其他窗口发送消息，并且您不能保证未知发件人不会发送恶意消息。 但是，验证身份后，您仍然应该**始终验证接收到的消息的语法**。 否则，您信任只发送受信任邮件的网站中的安全漏洞可能会在您的网站中打开跨网站脚本漏洞。

**当您使用postMessage将数据发送到其他窗口时，始终指定精确的目标origin，而不是\*。** 恶意网站可以在您不知情的情况下更改窗口的位置，因此它可以拦截使用postMessage发送的数据。





# decorator装饰器语法

Decorator是在声明阶段实现**类与类成员**注解的一种语法。装饰器在声明阶段就会执行。

装饰器语法还未被实装，所以需要安装babel插件防止webpack报错

`npm install babel-plugin-transform-decorators-legacy -D`

```javascript
// webpack babel-loader配置
// transform-class-properties是class类语法插件
// transform-decorators-legacy是装饰器语法插件
rules:[{
  test: /\.js$/,
  exclude: /node_modules/,
  use: {
    loader: 'babel-loader',
    options: {
      presets: ['env'],
      plugins: ['transform-decorators-legacy','transform-class-properties']
    }
  }
}]
```



## 装饰器类型

- 类装饰器

  类的装饰器函数的第一个参数，就是所有装饰的目标类，装饰器对类的行为的改变是代码编译时发生的，而不是在运行时。这意味着，装饰器能够在编译阶段运行代码。也就是说，装饰器本质就是编译时执行的函数。

```js
// 类装饰器
// 在类声明阶段执行
function log(target) {
    // 此处的target为类本身
    const desc = Object.getOwnPropertyDescriptors(target.prototype)
    for (const key of Object.keys(desc)) {
        if(key === 'constructor') {
            continue;
        }
        const func = desc[key].value
        if('function' === typeof func) {
            // 如果实例调用类的方法则执行
            Object.defineProperty(target.prototype, key, {
                value(...args) {
                    console.log('before ' + key) // 函数执行之前调用
                    const ret = func.apply(this, args)
                    console.log('after ' + key) // 函数执行之后调用
                    return ret
                }
            })
        }
    }
}
```



- 属性装饰器

  第一个参数target为类生成的实例对象，第二个参数key为装饰器的类成员的名称，第二个参数descriptor为该类成员的属性特征

```js
// 属性装饰器
function readonly(target, key, descriptor) {
// 第一个参数target为类的实例对象
// 第二个参数key为装饰器的类成员的名称（即number）
// 第二个参数descriptor为该类成员的属性特征
    descriptor.writable = false
}
```



- 方法装饰器

  与装饰类不同，对类方法的装饰本质是操作其描述符，可以把此时的装饰器理解成是 `Object.defineProperty(obj, prop, descriptor)` 的语法糖（**ps：装饰器的本意是要装饰类的实例，但编译阶段实例还未生成，所以此时方法装饰器只能装饰类的原型，而类装饰器中的target则指类本身**）

  第一个参数target为类的原型对象，即方法`Class.prototype`
  第二个参数key为要装饰的方法(属性名)
  第三个参数descriptor为要修饰的方法(属性名)的描述符

```javascript
// 方法装饰器
// 检查方法的每一个参数是不是都是数字，如果不是则抛出错误，如果是则正常执行
function validate(target, key, descriptor) {
    const func = descriptor.value
    descriptor.value = function(...args) {
        for(let num of args) {
            if('number' !== typeof num) {
                throw new Error(num + 'is not a number')
            }
        }
        return func.apply(this, args)
    }
}

```



```js
@log
class N {
    @readonly number=123

    @validate
    add(...nums) {
        return nums.reduce((p,n) => p + n , 0 )
    }
}

console.log(new N().add(1, 2))
// before add
// after add
// 3

console.log(new N().add('1', '2'))
// before add
// 抛出错误：1 is not a number

console.log(new N().number = 100) 
// 报错，因为number属性被修饰器将属性特征修改为不可修改
```





# HTML原生生命周期

使用window.生命周期 = 匿名执行函数，就能定义原生的生命周期

- DOMContentLoaded 

  浏览器已经加载了 Html, DOM 树已经构建完毕，但是 img 和外部样式表等资源可能还没有下载完毕。

  带有`async`的脚本也许会在页面没有完全下载完之前就加载，这种情况会在脚本很小或本缓存，并且页面很大的情况下发生。

  带有`defer`的脚本会在页面加载和解析完毕后执行，刚好在 `DOMContentLoaded`**之前**执行。

  

- load - 浏览器已经完全加载了所有资源。



- beforeunload - 用户即将离开页面，beforeunload 会给用户弹出个确认框，unload 则不会。

```javascript
window.onbeforeunload = function() {
  return "确定离开?";
};
```



- unload - 用户完全离开页面。



- readyState - readyState不是个生命周期，而是document上的一个只读属性

　　document.readyState 这个只读属性可以告诉程序当前文档加载到哪一个步骤，它有三个值：

   　　　　1. loading - 加载，document 仍在加载中；
            　　　　2. interactive - 互动，文档已经完成加载，文档已被解析，但是诸如图像，样式表和框架之类的子资源仍在加载。
                        　　　　3. complete - 文档和所有子资源已完成加载。状态表示 `load` 事件即将被触发。

而这个属性的每次改变同样有一个事件可以监听：

```javascript
document.addEventListener('readystatechange', () => console.log(document.readyState));
```

不过这个 change 事件很少会被用到，可能出现的地方在某些第三方类库中判断一些依赖关系等地方。

　　