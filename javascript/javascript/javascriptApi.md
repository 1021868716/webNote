# Object类

对象属性的调用，对象的属性可以用点方法或者中括号调用，**中括号内必须使用字符串形式的键**

```javascript
let a ={
	name:"wtw",
	age:"21"
}
console.log(a.name)//wtw
console.log(a['age'])//21
```

例如使用for-in循环遍历是要使用中括号取值，点语法会出现语法错误报错

```javascript
let a ={
	name:"wtw",
	age:"21"
}
for(const key in a){
	console.log(a[key])
}
```



## delete关键字

delete关键字可用于删除对象的属性

```javascript
let a ={
	name:"wtw",
	age:"21"
}
delete a.name
console.log(a)
//{ age: '21' }
```



## 引用地址

对象的变量名是对象再内存中的地址

```javascript
let user = {}
let hd = user
//拷贝的是引用地址，浅拷贝
user.name = 'wtw'
console.log(hd.name)
//wtw
```

当函数的形参传入地址时，直接修改的也是内存上的内容

```javascript
let user = {name:"wtw"}
function show(a){
	a.age = 18
}
show(user)
console.log(user)
//{ name: 'wtw', age: 18 }
```



## 解构语法{ }

解构取值

```javascript
let user = {name:"wtw",age:18}
//完整语法：let {user:user,age:age} = user
//ES6解构语法
let {name="123",title="abc",age} = user
//解构也只支持默认值，如果目标中没有就用默认值

console.log(name)//wtw
console.log(age)//18
console.log(title)//abc



//数组的解构语法
let arr=['a','b']
let [a,b]=arr
console.log(a,b)//a  b
```

解构赋值

```javascript
let name="wtw"
let age=18
let p = {name,age}
console.log(p)
//{ name: 'wtw', age: 18 }
```



## 对象属性特征

我们通过对象属性特征来控制一个对象是否可写可遍历可被删除，然后通过`Object.defineProperty()`对对象属性特征进行修改

属性特征又叫描述符

### Object.getOwnPropertyDescriptor()

`Object.getOwnPropertyDescriptor(obj,"属性名")`

提取一个对象某个属性的特征描述对象

`Object.getOwnPropertyDescriptors(obj)`

提取一个对象全部属性的特征描述

1.`value：  属性值`

2.`writable：<boolean>属性是否可写，默认true`

3.`enumerable：<boolean>属性是否可遍历，默认true`

4.`configurable：<boolean>属性是否可被删除或者重新被配置，默认true`

5.`get`

6.`set`





### 配置属性特征

`Object.defineProperty()`方法可以修改对象的属性值，也能配置属性特征对象

也能用于给对象添加getter/setter

```javascript
let user = {
	name: 'wtw',
	age: 18
}
Object.defineProperty(uesr,"name",{
	value:"abc",
	//将属性值修改为abc
	writable: false
	//该属性不能被修改
	enumerable:false
	//该属性不能被遍历，输出这个对象不能看到该属性
	configurable:false
	//变量可以默认被外部delete删除，修改为false后外部delete不能被删除
	//也不能再一次被Object.defineProperty配置
})
```



## Object类api

Object类的api所有引用类型（例如数组，还有String类等）都能使用，因为Object在所有引用类型原型链的最上层，所有引用类型的原型链都指向Object。



### Object.is()

`Object.is(value1, value2)` 方法判断两个值是否是相同的值。



因为===	具有两种局限

```javascript
+0 === -0 // true，但我们期待它返回false
NaN === NaN // false，我们期待它返回true
```

Object.is修复了 === 这两种判断不符合预期的情况

```javascript
// 手撕Object.is
function is(x, y) {
  if (x === y) {
    // 处理为+0 != -0的情况
    return x !== 0 || y !== 0 || 1 / x === 1 / y
  } else {
    // 处理 NaN === NaN的情况
    return x !== x && y !== y
  }
}
```



`Object.is()` 判断两个值是否相同。如果下列任何一项成立，则两个值相同：

- 两个值都是 `undefined`
- 两个值都是 `null`
- 两个值都是 `true` 或者都是 `false`
- 两个值是由相同个数的字符按照相同的顺序组成的字符串
- 两个值指向同一个对象
- 两个值都是数字并且
  - 都是正零 `+0`
  - 都是负零 `-0`
  - 都是 `NaN`
  - 都是除零和 `NaN` 外的其它同一个数字



可以看出Object.is可以对基本数据类型:null,undefined,number,string,boolean做出非常精确的比较，但是对于引用数据类型是没办法直接比较的，只能比较引用地址是否相同。





### Obj.hasOwnProperty()

检测当前实例对象上是否存在对应参数属性（属性名以字符串形式作为参数）

在原型链上的属性，实例虽然能直接使用，但是无法通过`hasOwnProperty()`检测是否存在

```javascript
let user = {name:"wtw",age:18}
console.log(user.hasOwnProperty("name"))//true
```





### Object.getOwnPropertyNames(obj)

这个api可以只遍历出在对象和数组本身的属性（类似于in关键字），忽略原型上的属性，并集合成一个数组

```
const obj= {a:1 ,b:2}
console.log(Object.getOwnPropertyNames(obj))
// ['a', 'b']
```





### in关键字

in和hasOwnProperty()不同

```
"属性名" in 引用类型对象
```

返回值为boolean类型

**in关键字可以检测实例对象和原型链上是否具有这个属性**

```javascript
let arr = [1,2,3]
console.log(arr.hasOwnProperty("concat"))//false,concat方法在arr的原型链上
console.log('concat' in arr)//true
```



### Object.assign()

合并两个参数对象返回的新对象，如果属性重名，后一个参数优先级高

```javascript
let hd = Object.assign({a:1},{b:2})
console.log(hd)//{a:1,b:2}
```

可以利用这个api进行对象的深拷贝，因为这个方法返回的是新对象

```
let hd = Object.assign({},{b:2})
console.log(hd)//{b:2}
```



### Object.keys()

打印出参数对象的所有键，并存入一个数组返回，传入数组则返回index组成的数组（例如['0', 1', '2', '3']）

**也能使用for-in循环来遍历对象的键**



### Object.values()

打印出参数对象的所有值，并存入一个数组返回

**不能试图使用for-of来操作一个对象**



### Object.entries()

将参数对象的每一对键值对都单独存入一个数组，最后返回所有键值对数组组成的大数组



### Object.defineProperty()

`Object.defineProperty(obj,"属性名",属性值)`

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
```





### Object.create()

**创建返回一个新对象并指定其原型**

```javascript
Object.create(proto[,{
	//创建的对象的自带属性
	//这里的属性必须写上完整的属性特征否则报错
}])
```

------

```javascript
let parentObj = {
    show(){
        console.log(this.name)
    }
}

let childObj = Object.create(parentObj,{
    //这里是childObj具体内容
    //不写这个参数则创建空对象
    name:{
        value:"wtw",
        writable:true,
    	configurable:true
    }
})

childObj.show()
```

**自己实现一个Object.create()**

```javascript
//谷歌浏览器支持__proto__，用以下写法
Object.create() = function(proto){
	let obj = {};
	obj.__proto__ = proto
	return obj
}

//ie浏览器不支持__proto__需要换写法
//利用 实例.__proto__=构造函数.prototype
Object.create() = function(proto){
	function Fn(){}
    Fn.prototype = proto
    return new Fn()
}
```



### Object.freeze()

```
Object.freeze(obj)
```

冻结对象，对象所有属性特征的`writable`和`configurable`会被设置为false

对象的所有属性无法被修改（删除或改写都不行），且不能再被重新配置特征，是最简单封闭对象的api



### Object.isFrozen()

判断一个对象特征是否被冻结，返回bool值



### Object.getPrototypeOf()

`Object.getPrototypeOf(obj)`

返回一个对象的原型



### Object.preventExtensions()

禁止向对象添加属性,可以禁止外部再向对象添加属性

```
Object.preventExtensions(obj)
```



### Object.isExtensible()

```
Object.isExtensible(obj)
```

方法判断一个对象是否是可扩展的（是否可以在它上面添加新的属性，它们的 `__proto__`属性可以被更改。）。可拓展返回真，否则返回假，设置`Object.preventExtensions(obj)`后该api返回假



### Object.seal()

`Object.seal(obj)`

设置封闭对象，封闭对象后，对象特征`configurable`会变为false，然后对象不能在添加属性



### Object.isSealed()

```
Object.isSealed(obj)
```

判断对象是否处于封闭状态



### JSON.stringify()

将对象转换为json格式的数据



## 浅拷贝

浅拷贝是指复制以后新引用类型（包括数组）不完全独立，两者之间还能进行互相影响

以下四种复制对象的方式属于浅拷贝

- **=直接赋值**

```javascript
let a = {name="wtw",age=18}
let	b = a
//只复制了栈中的内存地址，实际上b和a指向同一片内存
//这就是一个浅拷贝
```

**以下三种复制方式看似深拷贝实则浅拷贝，因为如果对象属性还是一个对象，没办法复制干净**

- **for in循环只循环一层**

```javascript
let a = {name:"wtw",
         age:18,
         school:{
             class:5,
             school:"abc"
         }}
let b = {}
for(const key in a){
    b[key] = a[key] 
}
b.school.class=6
console.log(a.school.class)//6
```

- **Object.assign()**

  Object.assign()合并两个对象并返回一个新对象，所以第一个对象我们设置为空对象，第二个对象设置为需要深拷贝的对象，最后返回一个对象。

```javascript
let a = {name:"wtw",
         age:18,
         school:{
             class:5,
             school:"abc"
         }}
let b = Object.assign({},a)
b.school.class=6
console.log(a.school.class)//6
```

- **解构语法**

```javascript
let a = {name:"wtw",
         age:18,
         school:{
             class:5,
             school:"abc"
         }}
let b = {...a}
b.school.class=6
console.log(a.school.class)//6
```



## 深拷贝

- **使用递归一层一层for-in复制**

```javascript
let a = {name:"wtw",
         age:18,
         school:{
             class:5,
             school:"abc"
         }}
         
function deepClone(obj){
    let objClone = Array.isArray(obj)?[]:{};
    if(obj && typeof obj==="object"){
        for(key in obj){
            if(obj.hasOwnProperty(key)){
                //判断ojb子元素是否为对象，如果是，递归复制
                if(obj[key]&&typeof obj[key] ==="object"){
                    objClone[key] = deepClone(obj[key]);
                }else{
                    //如果不是，简单复制
                    objClone[key] = obj[key];
                }
            }
        }
    }
    return objClone;
}    
let b = deepClone(a)
b.school.class=6
console.log(a.school.class)//5
```

- 通过**JSON对象**来实现深拷贝

  **JSON.parse(JSON.stringify(obj)) 用JSON实现深拷贝**

  **缺点： 无法实现对对象中方法的深拷贝，会显示为undefined**

  ```javascript
  function deepClone2(obj) {
    var _obj = JSON.stringify(obj),
      objClone = JSON.parse(_obj);
    return objClone;
  }
  ```

- 其他深拷贝方法：https://www.jianshu.com/p/1c142ec2ca45



## 工厂模式

使用工厂模式创建出来的是Object实例

```javascript
function user(name){
	return {
		name,
		show:function(){
			console.log(`hello,`+this.name+this.school.schoolName)
        },
        school:{
            schoolName:"nkbh",
            class:"3"
        }
	}
}

let a = user('wtw')
let b = user('wy')
a.school.schoolName='78999'
//工厂模式创建的对象完全是独立的个体
a.show()//hello,wtw78999
b.show()//hello,wynkbh
```



## 构造函数

构造函数比工厂模式好在，如果有两个不同的工厂，我们很难分辨一个对象属于哪个工厂生产出来的，因为返回的直接是一个Object实例对象，构造函数生产的是属于构造函数的实例对象，工厂模式生产的直接是Object实例

```javascript
function User(name){
	this.name = name,
	this.show = function(){
		console.log(this.name)
	}
}

let a = new User('wtw')
a.show()
```

用基本类型的构造函数也可以创建出一个对应类型的对象，例如new Number() ，new String()，new Boolean()



## 控制器和访问器

对象的属性尽量设置控制器/访问器方法暴露一个借口给外部来访问和修改

注意set/get设置的访问器和控制器每次只能设置一个属性，**也就是每个属性都必须设置一个专属的访问器控制器**

**每个属性都编写一个访问器控制器十分繁琐，这就需要使用Proxy代理来解决这个问题**

### set/get关键字

为了更高效更安全的建立控制器访问数据，ES6引入了set/get关键字设置控制器

这两个关键字是用了在类中建伪属性（pseudo-property）

**伪属性是指，我们遍历的时候并不会遍历出伪属性（例如我们输出对象，伪属性并不会出现在对象里），但我们却可以使用伪属性提供的方法**

------

```javascript
const user = {
    data:{name:"wtw",age:18},
	set name(name) {
        this.data.name = name
		console.log(`name修改为${this.name}`)
    },
    get name(){
        return this.data.name
    }
}
//控制器直接修改属性
user.name="abc"
//访问器可以直接访问数据
console.log(user.name)

```

函数前加了set/get关键字，首先实现了js中不能实现的多态，两个函数名字相同却不会被覆盖

加了两个关键字的函数分别为getter/setter，用于改值和取值

- getter，访问器，负责查询值，**它不带任何参数**，将数据返回给外部
- setter，设置器，则负责设置键值，**必须带有参数**，一般没有返回值，值是以参数的形式传递，在他的函数体中，一切的return都是无效的



**使用get关键字设置的getter想读取数据，伪属性的同级字面量（同级目录）下不能出现同名普通属性或同名伪属性否则会报错，所以我们将对象中的数据全部封装到data属性中存储起来，方便getter/setter操作数据（例如不允许使用 `{ get x() { }, get x() { } }` 和 `{ x: ..., get x() { } }`）**



**通过setter/getter操作的数据我们都是可以在函数里进行监控和防范的，而通过点语法直接操作的数据我们是不可控的。**



### 用set实现观察者模式

对象中控制器因为是伪属性，所以是可以与普通属性重名的，而我们采取调用属性的时候，如果发生重名，优先调用控制器。

```javascript
const user = {
    data:{name:"wtw",age:18},
    name(){
        console.log('hello')
    },
    //同级字面量下出现同名属性，伪属性的优先级更高
	set name(name) {
        this.data.name = name
		console.log(`name修改为${this.name}`)
    },
    get name(){
        return this.data.name
    }
}
//user对象中name属性有两个，一个是普通属性，一个是伪属性访问器
//优先级高
user.name="abc" //控制器
//每次修改数据会进入伪属性name函数修改，可以在name里执行操作，所以做到了观察者模式，
console.log(user.name)//访问器
```

### 构造函数和class语法糖中使用访问器

```javascript
class Person {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }
                
    set name(name) {
        console.log("setter");
        this.name = name;
    }
                
    get name() {
        console.log("getter");
        return this.name;
    }
    
    set age(age) {
        console.log("setter");
        this.age = age;
    }
                
    get age() {
        console.log("getter");
        return this.age;
    }
}
```





# Array类

数组是引用类型，浅拷贝共享一块内存空间

```javascript
let array1 = [1,2,3,4,5]
let array2 = array1;
array1[0] = 9
console.log(array2)//[9,2,3,4,5]
```

数组中如果跳过位置来插值或者创建数组的时候声明大小却未赋值，未填的部分自动填充上undefined

```javascript
let array = [1]
array[3] = 3
console.log(array)//[ 1, <2 empty items>, 3 ]
console.log(array[1])//undefined

let array2 = new Array(3)
console.log(array2.length)//3
console.log(array2)//[ <3 empty items> ]
console.log(array2[0])//undefined
```

数组用typeof判断永远是object类型，检测数组应该使用`Array.isArray()`方法

```javascript
let arr = [1]
console.log(typeof arr) //object
console.log(Array.isArray(arr))//true
```

## 数组方法

`var numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]`

### 获取数组长度

将数组的length设置为0可以清空数组

1. **Array.length**

   ```javascript
   console.log(numbers.length)
   ```

2. **forEach循环**

   ```javascript
   let i = 0
   numbers.forEach(function () {
      i++;
   })
   ```



### 增加元素

**首位增加元素**

- for循环

  ```javascript
  //最后一位开始往前移动，每位元素往后挪动一位，然后往空出来第一位添加元素
  for (var i = numbers.length; i > 0; i--) {
      numbers[i] = numbers[i-1]
  }
  numbers[0] = -1
  console.log(numbers) // -1,0,1,2,3,4,5,6,7,8,9
  ```

  

- **Array.unshift()**

  ```javascript
  numbers.unshift(-1)
  console.log(numbers) // -1,0,1,2,3,4,5,6,7,8,9
  ```
  
  返回length



**末尾增加元素：Array.push()**

```javascript
   numbers.push(10)
   console.log(numbers)//0,1,2,3,4,5,6,7,8,9,10
```

返回length



### 删除元素

**删除首位元素**

- for循环

```javascript
	//从第二位开始每一位都将前一个元素直接覆盖掉
	for (var i = 0; i < numbers.length; i++) {
	    numbers[i] = numbers[i+1]
	}
	console.log(numbers) //1,2,3,4,5,6,7,8,9
```



- **Array.shift()**

  ```javascript
  numbers.shift()
  console.log(numbers) //1,2,3,4,5,6,7,8,9
  ```

  返回被删除的元素
  
  

**删除末尾元素：Array.pop()**

```javascript
numbers.pop()
console.log(numbers)//0,1,2,3,4,5,6,7,8
```

返回被删除的元素



### splice() 

**Array.splice()**	在数组上任意位置删除，插入或替换元素（会改变原数组）



**splice在只有2个参数的时候是删除元素，返回值是删除元素组成的数组**

第一个参数为起始坐标，第二个参数为删除个数

如果第二个参数省略则坐标以及之后的元素全部删除

```javascript
numbers.splice(5,3)
//从索引5开始往后删除三个元素
console.log(numbers)//0,1,2,3,4,8,9
```



**splice有三个及以上参数（第二个参数必须为0）的时候是插入或替换元素**

```
第一个参数为起始坐标

当第二个参数为0时，则表示的是插入元素，之后的元素从第一个参数的下标插入，之后的元素依次往后排
```

```javascript
numbers.splice(5,0,3,4)
//从下标为5的位置开始插入元素，原本下标为5的元素会被挤到后面去
console.log(numbers)
//0,1,2,3,4,3,4,5,6,7,8,9
```

```
当第二个参数不为0时，则表示替换几个元素，从元素从起始坐标开始替换
```

```javascript
numbers.splice(5,2,3,4)
console.log(numbers)
//0,1,2,3,4,3,4,7,8,9
```

```
第三以及之后参数为插入的元素
```



### concat() 

**Array.concat()**  连接两个数组并返回一个新数组（不会对原数组产生影响），也可以利用点语法进行数组连接

注意使用+号进行相加，会将数组元素包括逗号变为字符串然后拼接

```javascript
let a = [1,2]
let b = [3,4]
var c = a.concat(b)
console.log(c)  //[1,2,3,4]
//点语法连接数组
console.log([...a,...b])//[1,2,3,4]

var d = a + b
console.log(d)	//1,23,4
console.log(typeof d)//string
```



### forEach() 

循环执行回调函数

**ArrayObj.forEach(callback)**  对数组中的每一项运行都运行一次回调，回调的第一个参数就是轮到执行的函数,但这个方法没有返回值

```javascript
// 定义数组
var names = [1,2,3]

// forEach的使用
names.forEach(function (item){
    console.log(item+1)
    //2,3,4
})
```



在forEach中用return不会返回，函数会继续执行。

```go
let nums = [1, 2, 3];nums.forEach((item, index) => {  return;//无效})
```

中断方法：

1. 使用try监视代码块，在需要中断的地方抛出异常。
2. 官方推荐方法（替换方法）：用every和some替代forEach函数。every在碰到return false的时候，中止循环。some在碰到return ture的时候，中止循环



### join()

 拼接字符串

将所有的数组元素连接成一个字符串，如果有参数就会以这个参数分隔

```
var arr = new Array(3)
arr[0] = "a"
arr[1] = "b"
arr[2] = "c"
console.log(arr.join("."))
//a.b.c
```



### reverse() 

颠倒数组

reverse() 方法用于颠倒数组中元素的顺序。



### indexOf() 

 查找第一个指定值

返回值为**第一个**与给定参数相等的数组元素的索引，没有找到则返回-1



### lastIndexOf() 

查最后的指定值

返回在数组中搜索到的与给定参数相等的元素的最大索引的值



### toString() 

转化为字符串

将数组元素以逗号间隔转化为一条字符串



### slice()  

截取，返回下标start截取到下标end-1（不包括end）的新数组，不改变原数组

```javascript
ArrayObj.slice(start,end)
```



### map()  

运行回调并返回新数组

map()方法提供的是一种映射函数.

首先会遍历数组中每一个元素作为第一个参数value传入到函数中.元素会经过回调, 生成新的元素, 并且将新的元素作为返回值返回.

map() 方法返回一个新数组，数组中的元素为原始数组元素调用函数处理后的返回值。

**但是注意数组存的元素为引用类型的情况下**：

因为回调传入的形参是每个引用类型元素指针，直接对形参操作并返回形参，修改的是原数组内存里的元素，如果不想对原来的元素进行操作，应该返回一个新的对象，而不是对形参进行操作后再返回形参

```javascript
let arr= [{title:'1'},{title:'2'},{title:'3'}]
let arr2 = arr.map((value)=>{
	value.title = 'wtw'
    //直接对引用类型形参进行修改，相当于修改原来的元素
	return value
    //返回的是指针，arr2数组内的元素和arr内的元素指向同一片空间
})
console.log(arr)
console.log(arr2)
arr2[0].title = 'abc'  
console.log(arr)
console.log(arr2)
//[ { title: 'wtw' }, { title: 'wtw' }, { title: 'wtw' } ]
//[ { title: 'wtw' }, { title: 'wtw' }, { title: 'wtw' } ]
//[ { title: 'abc' }, { title: 'wtw' }, { title: 'wtw' } ]
//[ { title: 'abc' }, { title: 'wtw' }, { title: 'wtw' } ]
```

```javascript
[10, 20 ,30].map(parseInt)
等价于
[10, 20 ,30].map((num, index) => {
	return parseInt(num, index)
})
```







### filter()  

过滤器

 首先会遍历数组中每一个元素作为第一个参数item传入到回调函数中，第二个参数（可选）为index，当前元素下标

函数的结果返回true, 那么这个元素会被添加到最新的数组中, 返回false, 则忽略该元素.

最终会形成一个新的数组, 该数组就是filter()方法的返回值

```javascript
var names = ["abc", "cb", "mba", "dna"]

// 获取names中所有包含'a'字符的元素
var newNames = names.filter(function (t) {
    //返回值为真就加入新数组
    return t.indexOf("a") != -1
})
console.log(newNames)
```

实现一个filter函数

```javascript
function filter(array,callback){
	let newArray = []
	for(let value of array){
		if(callback(value)){
			newArray.push(value)
		}
	}
	return newArray
}

console.log(filter([1,2,3,4,5,6],function(num){
	return num>=3;
}))
//[3,4,5,6]
```



### sort()   

排序

对数组按照一定规则（规则作为sort()参数）进行排序

如果不添加规则将转化为字符串，安装首个字符的ascall码进行比较

```javascript
let arr = [1,5,3,15,24]
arr.sort()
console.log(arr)//[ 1, 15, 24, 3, 5 ]
```

- 如果 `compareFunction(a, b)` 返回值小于 0 ，那么 a 会被排列到 b 之前；
- 如果 `compareFunction(a, b)` 等于 0 ， a 和 b 的相对位置不变。备注： ECMAScript 标准并不保证这一行为，而且也不是所有浏览器都会遵守（例如 Mozilla 在 2003 年之前的版本）；
- 如果 `compareFunction(a, b)` 返回值大于 0 ， b 会被排列到 a 之前。
- `compareFunction(a, b)` 必须总是对相同的输入返回相同的比较结果，否则排序的结果将是不确定的。

默认的升序排序 a<b返回-1，a>b返回1，相等返回0

```javascript
function compare(a, b) {
  if (a < b ) {           // 按某种排序标准进行比较, a 小于 b
    return -1;
  }
  if (a > b ) {
    return 1;
  }
  // a must be equal to b
  return 0;
}
```



纯数字数组进行排序的情况下可以简写

return a-b 升序

return b-a 降序

```javascript
 let arr = [1,5,7,8,0,2]
 //根据回调进行排序
 arr = arr.sort(function(a,b){
     return a-b
 })
console.log(arr)
//[ 0, 1, 2, 5, 7, 8 ]
```

特殊排序

因为a比b小，如果想降序就使返回值依然符合最初的排序规则，a<b返回-1，a>b返回1，相等返回0

```javascript
 let arr = [{age:1},{age:5},{age:7},{age:8}]
 //根据回调进行排序
 arr = arr.sort(function(a,b){
     if(a.age<b.age){
         return -1
     }
     if(a.age>b.age){
         return 1
     }
     return 0
 })
console.log(arr)
//[ { age: 1 }, { age: 5 }, { age: 7 }, { age: 8 } ]
```





**实现一个sort方法**

```javascript
let arr = [1,5,3,9,7]
fucntion sort(array){
	for(let n in array){
		for(let m in array){
			if(array[n]<array[m]){
			//< 升序 ， > 降序
				const temp = array[n]
				array[n] = array[m]
				array[m] = temp
			}
		}
	}
	return array
}
console.log(sort(arr))
//[ 1, 3, 5, 7, 9 ]
```



### reduce()

累加器

ArrayObj.reduce(function(total,currentValue, index,arr)[,num])
累加器第一个参数回调函数的四个参数：
	total	必需。初始值，是本次计算的返回值，也是传递给下次计算的值
	currentValue	必需。当前元素
	currentIndex	可选。当前元素的索引
	arr	可选。当前元素所属的数组对象。

累加器的可选参数num是设置函数的total初始值

整个累加器的返回值就是最后一次的返回值total

```javascript
arr = [1,2,3,4]
let add = arr.reduce((total, currentValue) => {
  return total = total + currentValue  
})
console.log(add) // 10
```



### isArray()

判断参数是否为数组



### from()

将参数字符串按字符转化为数组

```javascript
var myArr = Array.from("RUNOOB");
console.log(myArr)
//[ 'R', 'U', 'N', 'O', 'O', 'B' ]
```



### fill()

填充数组

```
arr.fill(value[, start[, end]])
```

- `value`

  用来填充数组元素的值。

- `start` 可选

  起始索引，默认值为0。

- `end` 可选

  终止索引，默认值为 `this.length`。

`fill()` 方法用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。不包括终止索引。

```javascript
const array1 = [1, 2, 3, 4];

// fill with 0 from position 2 until position 4
console.log(array1.fill(0, 2, 4));
// [1, 2, 0, 0]

// fill with 5 from position 1
console.log(array1.fill(5, 1));
// [1, 5, 5, 5]


console.log(array1.fill(6));
// [6, 6, 6, 6]
```



### find()/findIndex()

find() /findIndex()方法返回通过返回条件的数组的第一个元素的值/下标。

find() 方法为数组中的每个元素都调用一次函数执行：

- 当数组中的元素在测试条件时返回 *true* 时, find() 返回符合条件的元素，之后的值不会再调用执行函数。
- 如果没有符合条件的元素返回 undefined

```javascript
var ages = [3, 10, 18, 20];
 
function checkAdult(age) {
    return age >= 18;
    //数组里遇到第一个满足条件的就返回元素/下标
}

console.log(ages.find(checkAdult))
//18
console.log(ages.findIndex(checkAdult))
//2
```

实现一个find方法

```javascript
//实现一个find方法
function find(array,callback){
    for(let value of array){
        if(callback(value )){
            return value 
        }
    }
    return undefined
}

console.log(find([1,2,5,4,5],function checkAdult(age) {
    return age >3;
}))
//5
```

### entries()

entries() 方法返回一个数组的迭代对象，该对象包含数组的键值对 (key/value)。

迭代对象中数组的索引值作为 key， 数组元素作为 value。

### every()/some()

every/some方法使用指定函数检测数组中的所有元素：

```
ArrayObj.every(callback)/some(callback)
```



every() 方法用于检测数组所有元素是否都符合指定条件（运行回调），回调返回值为布尔类型，只要有一个元素运行回调后返回不为真，整个`ArrayObj.every()`方法就返回false



some() 方法用于检测数组是否有一个元素符合指定条件（运行回调），回调返回值为布尔类型，只要有一个元素运行回调后返回为真，整个`ArrayObj.every()`方法就返回true



### includes()

相当于返回bool类型的indexof，indexof返回第一个找到的下标，没找到返回-1

而includes()返回bool类型，找到了返回true，没找到返回false

也可以添加第二个参数意思是从参数下标开始检索，之前的字符忽略。





# String类

```
let str = 'abcd'
```

## 转义符

将字符串内的一些具有意义特殊符号进行转义，让计算机不去使用这个特殊符号，当做一个普通字符处理

```javascript
let a ="我：\"hello,world\""
console.log(a) //我："hello,world"
```

## 空白符

在字符串中多个空格可以使用`&nbsp`占位，因为渲染到html后字符串内无论多少个空格最后都会渲染成一个，使用`&nbsp`当做空格来使用就可以渲染多个空白

## ES6模板字符串

也叫模板字面量

```javascript
let a = 1
let b = 2
let str = `
${a}+${a+b}+${b}
`
/*

1+3+2

*/
```

模板字符串内可以保留空行，可以在`&{}`语法内使用变量和运算符

**而且`&{}`语法内还可以再套模板字符串用于使用标签模板**

## ES6标签模板

函数名不加括号直接接上模板字符串的语法叫标签模板

标签模板的函数第一个参数是模板字符串中所有普通字符组成的数组

第二个参数则是模板字符串中所有变量组成的数组

**标签模板第一个应用便是将模板字符串中普通字符串与变量进行分离**

```javascript
let name = 'wtw'
let age = 10
function tag(strings,...vars){
	console.log(strings)
	console.log(vars)
}
//标签模板语法：函数名`模板字符串`
tag`i am ${name} and age is ${age}`

//[ 'i am ', ' and age is ', '' ]
//[ 'wtw', 10 ]
```

------

**标签模板第二个应用是计算出一个字符串并将其返回**

```javascript
function format(strings,...values){
    return strings.reduce((s,v,idx)=>{
        if(idx>0){
            const prev=values[idx-1]
            if(typeof prev==='number'){
                //第一个$是美元符
                s+=`$${prev.toFixed(2)}`
            }else{
                s+=prev
            }
        }
        return s+v
    },'')

}

const item='orange'
const price=3.5554
//标签模板返回保留两位小数并且加上$美元符的字符串
const text=format`the ${item}'s price is ${price}`
console.log(text)
// the orange's price is $3.56
```

------

**标签模板还有一个重要应用，就是过滤HTML字符串，防止用户输入恶意内容**

## 隐式转换

字符串直接跟数字相加会默认为字符串拼接而不是数学运算

```javascript
console.log('5'+9)
//59
```

但是当字符串参与除了加法意外的数学运算时，字符串会隐式转换为数字类型

```javascript
let a = '5'
//字符串参与乘法，隐式转换为数字类型
console.log(a*1+9)//14
```

数字类型也可以隐式转换string类型，当数字类型加上一个空字符串也会隐式转换为strng类型

```javascript
let str = 66 + ''
console.log(typeof str)//string
```

## String类

String类是一个构造函数，并不是基本string类型。

同名基本类型也可以使用构造函数提供的方法。

### StringObj.length

求字符串长度

### StringObj.toUpperCase()

将字符串的小写字母转大写

### StringObj.toLowerCase()

将字符串的大写字母转小写

### StringObj.trim()

清除字符串两端空白

### StringObj.slice()

截取字符串

只有一个参数时，从start（包括start）开始往后截取字符串`str1.slice(start)`

```javascript
let str1 = 'abcdefg'
console.log(str1.slice(1))//'bcdefg'
```

有两个参数时，表示从start（包括start）截取到end（不包括end）`str1.slice(start,end)`

```javascript
let str1 = 'abcdefg'
console.log(str1.slice(1,3))//'bc'
```

### StringObj.substring()

类似于有两个参数的slice()方法

`String.substring(start,end) `方法用于提取字符串中介于两个指定下标之间的字符。

返回一个新的字符串，其内容是从 *start* 处到 *stop*-1 处的所有字符。



### StringObj.substr()

```javascript
String.substr(start,length)
```

从start开始，截取固定长度字符串



### StringObj.indexof()

根据参数检索字符串，找到了返回第一个找到的位置下标，没有找到返回-1。

可以添加第二个参数，意思是从参数下标开始检索，之前的字符忽略。

```javascript
let a =  'abcdefg'
console.log(str1.indexof('c')) //2
```



### StringObj.includes()

相当于返回bool类型的indexof，indexof返回第一个找到的下标，没找到返回-1

而includes()返回bool类型，找到了返回true，没找到返回false

也可以添加第二个参数意思是从参数下标开始检索，之前的字符忽略



### StringObj.startWith()

查询是不是以参数字符开头，返回bool类型



### StringObj.endWith()

查询是不是以参数字符结尾，返回bool类型



### StringObj.search()

```
String.search(str/regexp)
```

search() 方法用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串。

返回第一个与 regexp 相匹配的子串的起始位置，没有匹配返回-1



### StringObj.replace()

这个方法返回一个新字符串

```
String.replace(regexp/substr,replacement)
```

replace() 方法用于在字符串中用一些字符替换另一些字符，或替换一个与正则表达式匹配的子串。

如果第一个参数为正则表达式，则用第二个参数字符串替换匹配到第一个的字符串，如果正则表达式加了g则匹配到的所有字符串全部替换

如果第一个参数为普通字符串，则会用第二个参数字符串替换对象中所有与这段字符串相同的部分



### StringObj.repeat()

```
String.repeat(number)
```

repeat()方法用于将字符串重复几次

```javascript
console.log('*'.repeat(3))
//***
```



### StringObj.split()

拆分字符串成一个字符串数组

String.split(str,maxlength)
第一个参数str必须填写，string类型，表示指定的str边界处将字符串分割成子串创建的。返回的数组中的字串不包括分割字符 str自身（分割字符str将会被抛弃），如果str为空字符串，则每个按字符来拆分这个字符串

第二个参数为数组最大长度，返回的数组大小长度不会大于maxlength。



```javascript
let str = 'wtw'
console.log(str.split(""))
//['w','t','w']
```



### String()

构造函数String()可以将其他类型转化为**string基本类型**

```javascript
let a = String(123)
console.log(a)//字符类型的123
```



### parseInt()

`parseInt()`是一个全局函数，可以尝试将string类型转化为数字类型

```javascript
parseInt(string,进制)
```

如果字符串开头为数字，则有几位数字转化几位

如果字符串开头没有数字，则输出NAN

```javascript
console.log(parseInt('123sh'))//123
console.log(Number.isNaN('asdd123'))//NAN
```

第二个可选参数为转化为几进制（2-36）的数，不写或者0默认为十进制，如果以0x开头为16进制

```javascript
console.log(parseInt('2',1))//NaN
//1进制中缝1进1，没有数字2，所以返回NaN
console.log(parseInt('3',2))//NaN
//2进制中缝2进1，没有数字3
```



### StringObj.match()

match() 方法可在字符串内检索指定的值，或找到一个或多个正则表达式的匹配。返回匹配结果组成的数组。

没找到返回null

```javascript
let a ="abc12s5s8".match(/\d/g)
console.log(a) //[ '1', '2', '5', '8' ]
```





# RegExp类

创建正则对象

```javascript
// 创建正则对象
var re = new RegExp('\\d+\\.\\d+');
console.log(re.test('22.36')) // true
// 直接使用正则
console.log(/\d+\.\d+/.test('22.36'))
```

正则对象有一个属性lastindex，记录了当前正则对象的上次匹配成功的搜索点，使用g修饰符就是从上次的lastindex开始匹配。

使用new创建正则所省略最外侧的`//`，但自带\表达式都要多加一个\，例如`\d+`写成`\\d+`，转义`\.`写成`\\.`



## 方法

**支持正则规则的String方法**

| 方法名  |                                                              |
| ------- | ------------------------------------------------------------ |
| search  | 检索与正则表达式相匹配的值。                                 |
| match   | 找到一个或多个正则表达式的匹配，返回数组，不加g返回一个，加g返回全部 |
| replace | 替换与正则表达式匹配的子串。                                 |
| split   | 把字符串分割为字符串数组。                                   |



### string.match()

找到一个或多个正则表达式的匹配，返回数组，不加g返回一个，加g返回全部。

不加g匹配到的数值就是数字第一项，加g就所有匹配返回一个数组

```js
console.log("1212151577889".match(/[123456]/))
//[ '1', index: 0, input: '12121515', groups: undefined ]
//正则没加g，匹配成功即返回，返回找到的第一个符合的元素

//正则如果加了g进行全局匹配就，会返回所有匹配结果返回的数组
console.log("1212151577889".match(/[123456]/g))
/*
[
  '1', '2', '1',
  '2', '1', '5',
  '1', '5'
]
*/
```



### RegExpObj.test()

test() 方法用于检测参数字符串中是否有匹配RegExp的值

如果字符串中有匹配的值返回 true ，否则返回 false。

```javascript
let hd = "abcud"
console.log(/u/.test(hd))//true
```



### RegExpObj.exec()

exec() 方法用于检索字符串中的正则表达式的匹配，如果字符串中有匹配的值返回该匹配值，返回一个数组，其中存放匹配的结果。如果未找到匹配，则返回值为 **null**

字符串中如果找到了匹配的字符串，则返回该段字符串片段，没匹配的返回null。

并且下次同一正则进行相同匹配，会找寻下一个字符串片段，输出的index+1

正则对象的lastIndex记录了最后匹配到的字符下标

```javascript
var str = "Visit W3School"; 
var patt = new RegExp("i","g");
var result = patt.exec(str) 
console.log(result); 
//[ 'i', index: 1, input: 'Visit W3School', groups: undefined ]
console.log(patt.lastIndex); 
//2
result = patt.exec(str) 
console.log(result); 
//[ 'i', index: 3, input: 'Visit W3School', groups: undefined ]
console.log(patt.lastIndex); 
//4
```



### RegExpObj.toSting()

将正则转变为普通字符串形式

```javascript
myExp = new RegExp("a+b+c");
console.log(myExp.toString());       // 显示 "/a+b+c/"
foo = new RegExp("bar", "g");
console.log(foo.toString());         // 显示 "/bar/g"
```



### eval

将字符串转化为正则表达式，这样就可以在正则中使用变量了

```js
var r = 'u'
console.log(eval(`/${r}/`).test('abcdefgu')) // true
```

也可以使用new将正则规定为变量

```js
var r = 'u'
console.log(new RegExp(r, 'g').test('abcdefgu')) // true
```





## 修饰符

修饰符用于执行区分大小写和全局匹配。修饰符放在//外或者new RegExp的第二个参数上

| 修饰符 | 描述                                                     |
| :----- | :------------------------------------------------------- |
| i      | 执行对大小写不敏感的匹配。                               |
| g      | 执行全局匹配（查找所有匹配而非在找到第一个匹配后停止）。 |
| m      | 执行多行匹配。字符串每一行都当做独立个体单独处理         |

```js
let hd = "abc@d"
console.log(/u|@/g.test(hd))//true

let reg = new RegExp('u|@', 'g')
console.log(reg.test(hd))//true
```



## 逻辑符

|  逻辑或

```javascript
let hd = "abc@d"
console.log(/u|@/.test(hd))//true
//匹配u或者@，hd中有@所以匹配成功
```



## 原子表

方括号用于查找某个范围内的字符：

| 表达式             | 描述                               |
| :----------------- | :--------------------------------- |
| [abc\]             | **查找方括号之间的任何字符。**     |
| (abc|efg)         | 查找abc或者efg这个整体字符串       |
| [^abc\]            | 查找任何不在方括号之间的字符。     |
| [0-9]              | 查找任何从 0 至 9 的数字。         |
| [a-z]              | 查找任何从小写 a 到小写 z 的字符。 |
| [A-Z]              | 查找任何从大写 A 到大写 Z 的字符。 |
| [A-z]              | 查找任何从大写 A 到小写 z 的字符。 |

```javascript
console.log("12121515".match(/[123456]/))
//[ '1', index: 0, input: '12121515', groups: undefined ]
[]是元子表，只要字符串出现[]中任意一个字符就能匹配成功
没加g匹配成功即返回，返回找到的第一个符合的元素

如果加了g进行全局匹配就，会返回所有匹配结果返回的数组
console.log("12121515".match(/[123456]/g))
/*[
  '1', '2', '1',
  '2', '1', '5',
  '1', '5'
]
*/

console.log("12121515".match(/(123456)|(123333)/))
//null
()是元子组，()内是一个整体，必须匹配整个字符串，没有|这种特殊表示并或情况不用加()

console.log("1281215159".match(/[^123456]/g))
//元子表加^,[^],表示匹配除了元子表内字符的其他字符
//[ '8', '9' ]
```



## 原子组

元字组是括号，表示一个整体

```js
console.log("12121515".match(/(123|121)/))
// 匹配123或者121
// [ '121', '121', index: 0, input: '12121515', groups: undefined ]
```





## 元字符

元字符（Metacharacter）是拥有特殊含义的字符：

| 元字符 | 描述                                        |
| :----- | :------------------------------------------ |
| .      | 除换行符外任何字符                          |
| \      | 转义字符                                    |
| \|     | 或                                          |
| \w     | 查找字母数字和_。                           |
| \W     | 查找非字母数字和_。。                       |
| \d     | 查找数字。                                  |
| \D     | 查找非数字字符。                            |
| \s     | 查找空白字符。空格符，换行符(\n)            |
| \S     | 查找非空白字符。                            |
| \b     | 匹配单词边界。                              |
| \B     | 匹配非单词边界。                            |
| \0     | 查找 NULL 字符。                            |
| \n     | 查找换行符。                                |
| \f     | 查找换页符。                                |
| \r     | 查找回车符。                                |
| \t     | 查找制表符。                                |
| \v     | 查找垂直制表符。                            |
| \xxx   | 查找以八进制数 xxx 规定的字符。             |
| \xdd   | 查找以十六进制数 dd 规定的字符。            |
| \uxxxx | 查找以十六进制数 xxxx 规定的 Unicode 字符。 |

```javascript
console.log(/\d+\.\d+/.test("484.36"))
//匹配 任意数组+.+任意数字  .是特殊字符需要转义使用
```







## 条件量词

条件量词只对前一个字符或者元字组有效，并且会使匹配贪婪

| 量词   | 描述                                                         |
| :----- | :----------------------------------------------------------- |
| n+     | 贪婪匹配任何包含至少一个 n 的字符串。例如，/a+/ 匹配 "candy" 中的 "a"，"caaaaaaandy" 中所有的 "a"。 |
| n*     | 匹配任何包含零个或多个 n 的字符串。例如，/bo*/ 匹配 "A ghost booooed" 中的 "boooo"，"A bird warbled" 中的 "b"，但是不匹配 "A goat grunted"。 |
| n?     | 匹配任何包含零个或一个 n 的字符串。例如，/e?le?/ 匹配 "angel" 中的 "el"，"angle" 中的 "le"。 |
| n{X}   | 匹配包含 X 个 n 的序列的字符串。例如，/a{2}/ 不匹配 "candy," 中的 "a"，但是匹配 "caandy," 中的两个 "a"，且匹配 "caaandy." 中的前两个 "a"。 |
| n{X,}  | X 是一个正整数。前面的模式 n 连续出现至少 X 次时匹配。例如，/a{2,}/ 不匹配 "candy" 中的 "a"，但是匹配 "caandy" 和 "caaaaaaandy." 中所有的 "a"。 |
| n{X,Y} | X 和 Y 为正整数。前面的模式 n 连续出现x-y次匹配。例如，/a{1,3}/ 不匹配 "cndy"，匹配 "candy," 中的 "a"，"caandy," 中的两个 "a"，匹配 "caaaaaaandy" 中的前面三个 "a"。注意，当匹配 "caaaaaaandy" 时，即使原始字符串拥有更多的 "a"，匹配项也是 "aaa"。 |
| n$     | 匹配任何结尾为 n 的字符串。                                  |
| ^n     | 匹配任何开头为 n 的字符串。                                  |
| ?=n    | 匹配任何其后紧接指定字符串 n 的字符串。                      |
| ?!n    | 匹配任何其后没有紧接指定字符串 n 的字符串。                  |

```javascript
console.log("sdasd".match(/^[a-z]{3,6}$/))
// [ 'sdasd', index: 0, input: 'sdasd', groups: undefined ]
console.log("sdasdsadad".match(/^[a-z]{3,6}$/))
//null，正则匹配开头结尾3-6位字母，也就是只匹配3-6位字母，这里位数超出，所以没有匹配返回null

// 默认只匹配一个字符。使用条件量词将会开启贪婪
console.log("121215157889".match(/\d/))
// [ '1', index: 0, input: '121215157889', groups: undefined ]
console.log("12121+5157889".match(/\d+/))
// [ '12121', index: 0, input: '121215157889', groups: undefined ]
console.log("12121+5157889".match(/\d+/g))
// [ '121215', '157889' ]
```



## 中文匹配

`\p{sc=xxx}`可以根据xxx匹配对应文字，Han是中文，使用`\p`匹配多字节必须加上修饰符u，/u 表示按unicode(utf-8)匹配（主要针对多字节比如汉字）

```js
var str = '123abc,!你好78世界9&……%'
console.log(str.match(/\p{sc=Han}/gu))
// [ '你', '好', '世', '界' ]
```





## 禁止贪婪

条件量词可以开启贪婪，使用?可以使正则表达式不贪婪的进行匹配

```js
console.log("b123b456b789b".match(/b.+b/g))
// [ 'b123b456b789b' ]

console.log("b123b456b789b".match(/b.+?b/g))
/*
[ 'b123b', 'b789b' ]
*/
```



## 断言匹配

断言只是条件，不再匹配结果当中

- (?=xxx) 

只匹配后面紧跟xxx的字符串

```js
let str = '123456123789'
console.log(str.match(/123(?=789)/))
// [ '123', index: 6, input: '123456123789', groups: undefined ]
// index为6说明第一个123没有被匹配，因为它后面没有紧跟789
```



- (?!xxx)

只匹配后面不是xxx的字符串

```js
let str = '123456123789'
console.log(str.match(/123(?!789)/))
// [ '123', index: 0, input: '123456123789', groups: undefined ]
// index为0说明第一个123被匹配，因为它后面不是789
```



- (?<=xxx) 

只匹配前面紧跟xxx的字符串

```js
let str = '123abc456abc'
console.log(str.match(/(?<=456)abc/))
// [ 'abc', index: 9, input: '123abc456abc', groups: undefined ]
```



- (?<!xxx)

只匹配前面不是xxx的字符串

```js
let str = '123abc456abc'
console.log(str.match(/(?<!456)abc/))
// [ 'abc', index: 3, input: '123abc456abc', groups: undefined ]
```



# Boolean类

基本类型布尔类型boolean只有true和false两个值

```
let bool = true
```

## 隐式转换bool类型

当需要使用bool类型却填入其他类型的时候，js解释器会自动将其他类型**隐式转换**为bool类型

JS中转化规则是：**非0，非null，非undefined，非空基本类型即为真**

像空数组，空对象在判断bool都为真，因为他们是引用类型，只有空基本类型才为假

空字符串在判断bool类型时就为假，因为字符串为基本类型string

## 显示转换boolean类型

!	显示转换取反boolean类型

!!   显示转换boolean类型

```
let num = 0;
console.log(!num)   //true
console.log(!!num)  //false
```

构造函数Boolean()可以将其他类型显示转换为**bool基本类型**

```javascript
let num = 0;
console.log(Boolean(num))   //false
```

## Boolean类

Boolean类是一个构造函数，并不是基本boolean类型。

同名基本类型也可以使用构造函数提供的方法。

### BooleanObj.valueOf()

将Boolean类实例转化为基本类型boolean

```javascript
const booleanObj = new Boolean(false)
//创建一个Boolean类实例
console.log(typeof booleanObj) //Object
//所有new出来的变量都是Object类型
console.loh(booleanObj )//[Boolean: false]

//boolean并不是一个bool基本类型而是一个对象类型所以无法直接用于判断
//valueof()方法是将Boolean类实例转化为bool基本类型

console.log(booleanObj.valueOf())
```

### Boolean()

构造函数Boolean()可以将其他类型显示转换为**bool基本类型**

```javascript
let num = 0;
console.log(Boolean(num))   //false
```





# Number类型

number类型为数字类型

```javascript
let num = 99;
```

## NaN

not a number

当使用数字与字符串进行非加法数学运算会返回NaN

或者非number类型调用Number类方法时会报错NaN

## Number类

Number类是一个构造函数，并不是基本nunmber类型。

同名基本类型number也可以使用构造函数提供的方法。

Number实例可以用于直接计算并且计算后结果为基本类型number

```javascript
let num = new Number(99);
console.log(num+1)//number类型的100
```

### Number()

构造函数`Number()`可以将一个字符串类型转化为数字类型

Number()跟`parseInt()`方法不同之处在于：

**`Number()`构造函数中的参数字符串如果不能完整的转化为数字类型则会直接返回NAN**

```javascript
console.log(parseInt('123sh'))//123
console.log(Number('123sh'))  //NaN
console.log(Number('123'))  //123
```

### NumberObj.valueOf()

typeof Number实例 是Object类型，valueOf可以使实例转为基本类型number

```javascript
const num = new Number(99)
console.log(num)//[Number:99]
console.log(typeof num..valueOf())//number
```

### NumberObj.toString()

number类型转为string类型

可选参数为进制（2-36），不传入默认这个数为十进制

```
NumberObj.toString(num)
```



### NumberObj.isInteger()

判断是否是整数

整数返回真，如果为浮点数小数就返回假

### NumberObj.toFixed()

保留几位小数，会四舍五入

```
NumberObj.toFixed(number)
```

```
let num = 97.755554
NumberObj.toFixed(3)
console.log(num)//97.756
```

### isNaN()

isNaN()是一个全局函数

它的本意是**通过Number()方法**（不是全局函数parseInt()），尝试将这参数转换成Number类型

如果能转换为number类型返回false，如果不能转换为number类型则返回true。

**所以isNaN只是判断传入的参数是否能转换成数字，并不是严格的判断是否等于NaN。**

```javascript
console.log(isNaN(123))//false
console.log(isNaN('123'))//false
console.log(isNaN(true))//false

console.log(isNaN('123abc'))//true
//通过Number()方法不能转换带有非数字字符的字符串为number类型
```

### Number.isNaN()

**Number.isNaN()判断传入的参数是否严格的等于NaN(也就是 ===)，其他都输出false**

一般只有使用数字与字符串进行非加法数学运算的结果才为NaN

```javascript
console.log(Number.isNaN(1/'测试')); //true
console.log(Number.isNaN('测试')); //false
```



# Math对象

**Math 对象是一个js内置对象（不是类），用于执行数学任务。**



## Math.max()/min()

参数只能是数字，不支持数组，返回参数中最大值/最小值

如果是数组可以使用apply方法转类型或者解构数组

```javascript
let grade = [1,2,3,4,5,6]
console.log(Math.max.apply(null,grade))
console.log(Math.max(...grade))
```

## Math.ceil()/floor()

将参数向上/向下取整



## toFixed()

```
num.toFixed(num)
```

给调用者取几位小数

```javascript
console.log((5.556).toFixed(2))//5.56
//四舍五入取两位小数
```



## Math.round()

把参数四舍五入为最接近的整数。



## Math.max()/main()

```
Math.max(x,y)/main(x,y)
```

获取两个参数中较大或较小的一个



## Math.random()

```
let random = Math.random()
获取一个0≤x<1的随机数x
```

```
let random = Math.floor(Math.random()*5)
获取一个0≤x<4直接的整数随机数x
```

```
let random = 2+Math.floor(Math.random()*(5-2))
获取一个2≤x<5直接的整数随机数x
```

## Math.sqrt()

sqrt() 方法可返回一个参数的平方根。



## Math.pow()

Math.pow(值，次方数)

```
Math.pow(3,2);   3的平方

Math.Pow(2,3);   2的立方
```



# Date类

Date类是js构建时间相关操作的类

```javascript
const date = new Date()
console.log(typeof date)//Object
//new出来的是时间对象

const time = Date()
console.log(typeof time)//string
//运行函数生成的是字符串形式的当前时间

//设置一个指定时间对象
const date = new Date("1990-9-22 3:22:18")
const date = new Date(1999,9,22,3,22,18)
```

## 获取时间戳

标准事件格式ISO   ，年月日时分秒

时间戳TIMESTAMP，1970年1月1日0时0分0秒到现在的毫秒数

```javascript
const date = new Date()
console.log(date*1)
console.log(Number(date))
cinsole.log(date.valueOf())
cinsole.log(date.getTime())
const date2 = Date.now()
console.log(date2)
//以上方法返回一个时间戳，1970年1月1日0时0分0秒到现在的毫秒数

const time = new Date(date2)
//将时间戳放入Date构造函数，则转变为一个对应的时间对象
```

```javascript
const start = new Date.now()
for(let i=0;i<10000000;i++){}
const end = new Date.now()
console.log(end-start)//判断for循环用了多少时间
```

## 获取时间

| api                   | 作用   |
| --------------------- | ------ |
| dateObj.getFullYear() | 获取年 |
| dateObj.getMouth()+1  | 获取月 |
| dateObj.getDate()     | 获取日 |
| dateObj.getHours()    | 获取时 |
| dateObj.getMinutes()  | 获取分 |
| dateObj.getSeconds()  | 获取秒 |

## 第三方库Moment.js

下载库`npm install moment --save `

```javascript
日期格式化
moment().format('MMMM Do YYYY, h:mm:ss a'); // 三月 31日 2020, 5:30:30 下午
moment().format('dddd');                    // 星期二
moment().format("MMM Do YY");               // 3月 31日 20
moment().format('YYYY [escaped] YYYY');     // 2020 escaped 2020
moment().format();                          // 2020-03-31T17:30:30+08:00

相对时间
moment("20111031", "YYYYMMDD").fromNow(); // 8 年前
moment("20120620", "YYYYMMDD").fromNow(); // 8 年前
moment().startOf('day').fromNow();        // 18 小时前
moment().endOf('day').fromNow();          // 6 小时内
moment().startOf('hour').fromNow();       // 31 分钟前

日历时间
moment().subtract(10, 'days').calendar(); // 2020/03/21
moment().subtract(6, 'days').calendar();  // 上星期三17:30
moment().subtract(3, 'days').calendar();  // 上星期六17:30
moment().subtract(1, 'days').calendar();  // 昨天17:30
moment().calendar();                      // 今天17:30
moment().add(1, 'days').calendar();       // 明天17:30
moment().add(3, 'days').calendar();       // 下星期五17:30
moment().add(10, 'days').calendar();      // 2020/04/10
```









# Set类

Set类似一个去重的数组。

set类跟数组不同的地方在于set无法存储相同的值，存入相同的值会被自动删除，但是不同类型的值不相等，比如字符串类型的"5"和数字类型的5不是同样的值，不会被删除。大多数时候Set实例用来去重，许多操作还是要转化为数组操作。

set类型作为对象，只有值，没有键。

也就是说Set类跟普通对象的不同在于，普通对象的类型检测是宽松的，如果属性键值对中的键不为字符串，统一转化为字符串类型并且不显示双引号，而Set类类型检测是宽松的，像数组一样，不同类型的值都可以存入Set类实例。

```
Set { 1, 2, 3, 4, 5, '5' }
Set实例对象内的属性，字符串类型的"5"和数字类型的5不是同样的值，数字不会自动转换为字符串
```

数组转set实例直接传入Set构造函数即可，数组存入Set实例后会自动去重

```javascript
let set = new Set([1,2,3,4,5,5,"5"])
//初始化时赋值
console.log(set)
//Set { 1, 2, 3, 4, 5, '5' }
console.log(typeof set)
//object
```

Set构造函数如果直接传入字符串，Set会将字符串拆分为字符存入实例

```javascript
let set = new Set("wtw")
console.log(set)
//Set { 'w', 't' }

//这样传入的是数组而不是字符串
let set2 = new Set(["wtw","wy","abc"])
console.log(set2)
//Set { 'wtw', 'wy', 'abc' }
```

## setObj.add()

为set实例添加元素

## setObj.size

查看set实例有多少元素

## setObj.has()

判断set实例内是否存有参数元素，返回布尔值

```javascript
let set2 = new Set(["wtw","wy","abc"])
console.log(set2.has("wtw"))
//true
```

## setObj.delete()

删除set实例内的参数元素，并且这个函数有返回值，存在这个元素并删除返回true，不存在这个元素返回false

```javascript
let set2 = new Set(["wtw","wy","abc"])
console.log(set2.delete("wtw"))
//true
console.log(set2)
//Set{'wy','abc'}
```

## setObj.clear()

清空set实例

## set转数组

数组转set类型直接调用构造函数Set

```
let set2 = new Set(["wtw","wy","abc"])
```

但很多时候set类型不能做到的操作数组能做，就可以再转化为数组后再操作

1. Array.from()

```
let arr = Array.from(set2)
```

```
2.  [...set]
```

```
let arr2 = [...set]
```

## 遍历set

 `Set`结构**没有键名只有键值**，所以 `keys()`和 `values()`是返回结果相同。

遍历方法： 

- `keys()`：返回**键名**的遍历器。

- `values()`：返回**键值**的遍历器。

- `entries()`：返回**键值对**的遍历器。

- `forEach()`：使用回调函数遍历**每个成员**。

  

## set并集交集差集算法

并集

```javascript
let a = new Set([1,2,3,4,5])
let b = new Set([6,7,8])
console.log(new Set([...a,...b]))
```

交集

```javascript
[...a].filter((item)=>{
	return b.has(item)
})
```

差集

```javascript
[...a].filter((item)=>{
	return b.has(item)
})
```

## WeakSet类

```javascript
let weakset = new WeakSet();
weakset.add({name:'1123'})
```

它和 Set对象的区别有两点:

- 与`Set`相比，`WeakSet` 只能是**对象的集合**，而不能是任何类型的任意值（例如数组和基本类型）。

  

- `WeakSet`持弱引用：集合中对象的引用为弱引用。 如果没有其他的对`WeakSet`中对象的引用，那么这些对象会被当成垃圾回收掉。 这也意味着WeakSet中没有存储当前对象的列表。 正因为这样，`WeakSet` 是不可枚举的（不能遍历循环，例如for-of ，keys()，values()等功能都无法使用），但像has(),delete()等方法还是有的。

  **弱引用：**

  原本引用类型引用了的内存地址，那块地址的被引用次数会加1，只要还有引用类型在引用那块地址，垃圾回收机制就不会去回收那块内存，但是WeakSet类是弱引用类型，不加引用次数，一旦其他位置没有引用类型引用了对象的内存地址，即使WeakSet类实例引用了，垃圾回收机制依然会去回收那块内存。

  所以WeakSet实例引用的内存可能读不出来。

  

WeakSet 存在于需要考虑生命周期防止内存泄漏的地方。像 js 等一些高级语言由于存在 GC，让程序员不需要直接地去操作内存，避免了很多问题，同时也让一些内存泄漏地问题变得更加隐晦，所以活用 WeakSet/WeakMap 还是很有必要的。

WeakSet/WeakMap 的一个用途是保存Dom节点引用，当DOM消失时，WeakSet/WeakMap 中以该dom节点为key的元素就可以进行内存垃圾回收了。还有一个用途是作为私有变量，把this当作key，一旦this对象不再被其他变量引用，就可以进行垃圾回收了。



# Map类

**Set类像数组一样保存数据，而Map类集合则可以像对象一样以键值对的形式保存数据**

并且普通对象键值对的键永远是以字符串的形式保存在对象里，而Map则不同，任意类型的数据都能作为Map的键保存

## MapObj.set()

set方法用于往集合存入数据

```javascript
let map = new Map()
map.set(function(){},'wtw').set('name','wy')
//可以链式调用set
console.log(map)
//{function(){}}=>'wtw','name'=>'wy'}

```

可以用链式调用set的方式往集合一次存入多个值，也可以给Map构造函数存入数组形式存如多个值，一个数组只能有两个值，第一个为键第二个为值，但是可以存入多对数组以保存多个数据，最后用一个大数组把所有数组包起来。

```javascript
let map = new Map([['a','wtw'],['b','wy']])
console.log(map)
//{'a'=>'wtw','b'=>'wy'}
```

## MapObj.get()

get函数用于取数据

```javascript
let objb = {
name:'wtw'
}
let map = new Map();
map.set(obj,'wy')
//以对象引用作为键
console.log(map.get(obj))
//输出值：wy
```

## MapObj.delete()

和set的delete方法一样，删除集合的对应元素，并且这个函数有返回值，存在这个元素并删除返回true，不存在这个元素返回false

```javascript
console.log(map.delete(obj))//true,有这个数据并删除
console.log(map)//{},删除了唯一数据为空集合
```

## MapObj.clear()

清空集合

## MapObj.has()

和set一样，判断Map实例内是否存有参数元素，返回布尔值，注意查找的是键

## **MapObj.length**

求Map类实例的长度



## 遍历Map

- `.keys()`遍历键

- `.values()`遍历值

- `.entries`遍历键值对

- 循环遍历

  注意forEach回调第一个参数为值，第二次参数才为键

  ```javascript
  let hd = new Map([['a','A'],['b','B']])
  hd.forEach((value,key)=>{
  	console.log(key,value)
  })
  //A a
  //B b
  
  for(const [value,key] of hd){
  	console.log(key,value)
  })
  //A a
  //B b
  ```

## 类型转换

- Map转数组

```javascript
let hd = new Map([['a','A'],['b','B']])
console.log([...hd])
/*
[['a','A'],['b','B']]
*/
```

- 单独转换Map的键/值

```javascript
console.log([...hd.keys()])
//['a','b']
console.log([...hd.values()])
//['A','B']
```

## WeakMap

跟WeakSet类似，**WeakMap里的键只能是引用类型，并且WeakMap也是弱引用类型**

```javascript
let map = new WeakMap()
let arr = []
map.set(arr,'abc')
```

因为是弱引用类型，所以跟WeakSet一样不能遍历循环，例如for-of ，keys()，values()等功能都无法使用，只能使用像has，delete等无需遍历的方法

弱引用类型WeakMap还是主要用来保存DOM元素，以便使用完以后可以及时释放内存



# Image类

Image类主要用来实现预加载图片

`<img>`这个标签加载大图片的时候，图片会一层一层的显示处理，使用Image类通过js代码加载图片会一次显示整张图片。也可以给image实例提前设置宽高占位，避免突然加载图片网页跳一下

利用js代码提前加载图片，用户需要时可以直接从本地缓存获取，但是会增加服务器前端的压力。这样做可以提高用户的体验，因为同步加载大图片的时候，图片会一层一层的显示处理，但是经过预加载后，直接显示出整张图片。但是在图片响应时间上优化不是那么明显。

```javascript
const img = new Image()
img.src = '1.jpg' //加载图片
document.body.appendChild(img) //提前占位
img.onload = ()=>{
//onload是图片成功后加载的回调
//将图片加载进dom
//回调异步的
   console.log('成功')
}
img.onerror = ()=>{
//onerror是图片加载失败的回调 
    console.log('加载失败')
}
console.log('123')
//123
//成功
```

## 封装加载图片

```javascript
function loadImage(src){
	return new Promise((resolve,reject)=>{
		const img = new Image()
		img.src = src
		img.onload = ()=>{
		 resolve(img)
		}
		img.onerror = ()=>{
			reject(img)
	  }
   })
}

loadImage('CLI2.png').then(img=>{
  document.body.appendChild(img) 
},img=>{
  console.log('加载失败')	
})
```

