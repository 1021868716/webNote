# TypeScript

ts兼容js语法，但ts不能直接在node或者浏览器环境下运行，必须将ts编译为js后再运行。

ts的静态类型使得编写代码时就可以快速定位一些错误并且在编辑器中ts的代码提示更友好。例如js中我们访问一个对象中没有的属性不会报错，需要在运行后才能定位错误，但是ts中访问一个对象不存在的属性就会直接报错。

javascript是动态类型语言，变量的类型是可变的，但是typescript是静态类型的语言，变量类型不可改变



**安装ts基础环境**

```
npm install typescript -g
```



## 初始化工程

```
npm init
```

初始化package.json

```
tsc --init
```

初始化后会生成一个tsconfig.json，包含了一些工程信息



## 编译js

运行tsc命令将ts编译为js，编译后会出现一个同名js文件

```
tsc demo.ts
```

运行js文件

```
node demo.js
```



## 直接运行ts

也可以使用插件ts-node直接运行ts文件

```
npm install -g ts-node
```

使用ts-node命令直接运行ts文件

```
ts-node demo.ts
```



# ts常用符号

- ?

属性或参数中使用?表示该属性或参数为可选项



- !

属性或参数后使用!表示强制解析，类型推断排除null、undefined，意思是告诉typescript编译器，这里一定有值，防止ts报错，常用于vue-decorator中的@Prop等。



- ?.

可选链：js中很长一段点调用中，如果有任何一个属性是null或者undefined就会报错，而ts的可选链上，如果遇到 `null` 或 `undefined` 就可以立即停止某些表达式的运行。

```javascript
// js
// 如果abcdef中出现任意一个null或者undefined就会报错
console.log(a.b.c.d.e.f)
```

```typescript
// ts
// 如果遇到null或 undefined就立即停止表达式的运行
console.log(a?.b?.c?.d?.e?.f)
```

可选链的polyfill

```typescript
const val = a?.b;
等同于
var val = a === null || a === void 0 ? void 0 : a.b;
```



- `// @ts-ignore`

TypeScript 2.6支持在.ts文件中通过在报错一行上方使用`// @ts-ignore: 注释`来忽略错误。如果遇到ts无法解决的报错，在确定正确的情况下可以在报错代码上方加上这行注释，这行代码的ts报错就会被忽略。

```typescript
if (false) { // ts会报错，因为这里的代码根本不会有机会执行
    // @ts-ignore：跳过下一行的ts报错
    console.log("hello");
}
```





# 数据类型

在TypeScript中有以下基本数据类型

　　• boolean

　　• number

　　• string

​		• null/undefined

​		• symbol

​		• any/unknown

　　• void/never

　　• 数组类型（array）

　　• 元组类型（tuple）

　　• 枚举类型（enum）

　　

基本数据类型：bool，string，undefined，null，symbol，number

引用类型：对象，函数，数组，元组，枚举



ts新增的数据类型：

- **tuple 元组**

元组类型用来描述已知数量和类型的数组



- **enum 枚举**

用来标识相互映射的枚举



- **any 任意类型**

用于表示可以为任意类型



- **unknown**

TypeScript 3.0 引入了新的`unknown` 类型，它是 `any` 类型对应的安全类型。

`unknown` 和 `any` 的主要区别是 `unknown` 类型会更加严格：在对 `unknown` 类型的值执行大多数操作之前，我们必须进行某种形式的检查。而在对 `any` 类型的值执行操作之前，我们不必进行任何检查。



- **void**

和any相反，表示没有类型，常用于表示函数没有返回值的情况



- **never**

never类型表示的是那些永不存在的值的类型，常用于注解函数返回值。 例如， never类型是那些总是会抛出异常或根本就不会有返回值的函数的返回值类型，用于表示函数总是会异常，不能执行完毕的情况，例如抛出异常的函数，无限循环的函数。变量也可能是 never类型，当它们被永不为真的类型保护所约束时。

never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。 即使 any也不可以赋值给never。

```typescript
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
  throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
  return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
  while (true) {}
}
```









# 类型注解/推断

type annotation类型注解：手动规定这个变量应该是什么类型，可以使用联合类型 `| `为变量注解多个类型

`变量: 类型`

```typescript
let count: number|string;
count = 123
```

注解也可以为对象类型，表示创建的对象必须完全符合注解，但这样下次创建一个新对象又要写一个注解，所以如果需要创建多个具有相同属性的对象可以使用接口interface或者类型别名type创建描述。

```typescript
let user: {
    name: string
} = {
	name: '123'
}
```



解构语法使用也可以使用注解

```typescript
function add( {num1, num2}: {num1: number, num2: number} ): number{
	return num1 + num2
}
const total = add({num1: 1, num2: 2})
```



**type inference类型推断**：ts会自动分析变量类型并规定该变量的类型

```typescript
let a = '123'
a = 456 // 报错，ts类型自动推断为string，后续无法修改类型
```





# 引用类型注解

引用类型为对象，数组，元组或者函数类型

**注解对象**

对象可以注解为interface，也可以使用类来注解对象，限定为这个类的实例，也可以注解为object，就表示任意对象

```typescript
let teacher: {
  name: string;
  age: number;
} = {
	name: 'dell',
	age: 18,
    // height: 180 // 报错，详细对象的注解必须完全符合描述
}

let people: object = {
  name: 'wtw'
}

class person{}
const dell:Person = new Person() // 注解为Person类的实例
```



**注解数组/元组**

```typescript
const arr: (number | string)[] = [1,'2',3];
// 定义一个元素为数字和字符串数组

let tom: [string, number] = ['Tom', 25]; 
//定义一个包含两个元素的元组，第一个元素string，第二个元素number：
```







# interface

interface创建用于描述**一个对象或者函数**的接口对象，**接口只是编码时用于语法校验的工具，并不会编译进js文件中。**

注解html元素可以使用ts自带的HTMLElement，注解css可以使用ts自带的CSSDeclaration

## **描述对象**

```
interface 接口名 {
  变量名: 变量描述;
  方法名(参数名: 参数类型): 函数返回值类型;
}
```

```typescript
//对象（类）型的接口
interface Person {
  firstName: string;
  secondName: string;
  repeat(input: string): void//声明对象方法的输入和返回值
  //等同与repeat: (input: string) => void;
}

let obj: Person = {
  firstName: '123',
  secondName: '123',
  repeat(str) {
    return 'hello,'+str
  }
}
```



## 强弱校验

使用接口作为注解有两种校验接口的方式

强校验时，使用接口作为注解的对象必须完全符合接口描述（属性一一对应，不能多不能少，且类型符合描述）

弱校验时，使用接口作为注解的对象除了拥有接口描述的属性，还能额外拥有自己的属性和方法，不会导致报错（属性可以多但不能少）**，弱校验使用较少，一般使用强校验配合?符的方式来创建interface注解不确定的对象**

```ts
interface Human {
  name: string;
  age: number;
}
```

**强校验**：构建对象时直接以接口作为注解来创建

```typescript
let y: Human= {
  name:'xxx',
  age:18,
  gender:'yyy'
}
// 报错，强校验不能拥有多余的属性gender
```

**弱校验**：创建缓存，再将缓存引用给新建的对象，启用弱校验。

```typescript
let y = {
  name: 'xxx',
  age: 18,
  gender: 'yyy'
}
let x: Human = y // 不报错
// 创建一个符合弱校验的对象，然后在创建接口作为注解的对象时将这个对象引用去就属于弱校验，不会报错
```



## **描述函数**

**描述函数形参数量不要求完全一致（但类型必须一致），返回值必须符合描述**

```
interface 函数名 {
  (形参名1: 形参描述1, 形参名2: 形参描述2):返回值描述;
}
```

```typescript
//函数集合型接口
interface myFunction {
  (arg1: string, arg2: number): string;
}

var func: myFunction = (arg1: string) => {
  return "this is " + arg1;
}

var func2: myFunction = (arg1: string, arg2: number) => {
  return arg1 + arg2;
}
```



接口和类型别名作为函数参数时具有强弱校验两种校验参数是否符合标准的方式

**强校验**：函数中使用字面量对象作为实参启用的是强校验，必须完全符合接口标准才能通过校验

```typescript
interface Point {
  x: number;
}

const getX = (point: Point):void =>{
    console.log(point.x)
}

getX({x: 3, y: 4}) // 强校验，报错
```



**弱校验**：函数中使用变量缓存以后作为实参启用的是弱校验，只要对象中包含接口中规定的属性就认为他符合接口规范，多出来的属性不会导致报错

```typescript
interface Point {
  x: number;
}
const getX = (point: Point):void =>{
    console.log(point.x)
}

let point = {
  x: 3,
  y: 4
}
getX(point) // 3
// 弱校验，不报错顺利运行
```





## ?

如果对象中某个属性可有可无，可以接口属性名后上加?，表示对象的该属性可有可无。

```typescript
interface Point {
  x: number;
  y?: number;
}

let point: Point = {
  x: 3
}
```



## readonly

在接口属性名前加readonly表示对象的该属性在赋予初值后只可读，不能修改

`readonly` 只能用在类或者接口中的属性上

```typescript
interface Point {
  readonly x: number;
}

let point: Point = {
  x: 3
}

point.x = 2 //报错
```



## propName

`[propName: 变量名注解]: 变量注解`属性表示不定变量（数量不定），可以为任意变量。

例如` [propName: string]: any`表示变量名为字符串类型，变量为any类型

propName后的变量注解推荐为any，如果propName的变量类型设置为具体某一类型应该保证该接口其他所有注解都为这个类型，如果其他注解分别为不同的类型，那propName的变量注解应该为any，不应该设置为具体类型，否则会报错

```typescript
interface Point {
  x: number;
  y: number;
  [propName: string]: any;
}

let point: Point = {
  x: 3,
  y: 2,
  z: '13',
  d: 14 // 多个不会报错，propName可以数量不定
}
```





## implements

implements应用，根据一个接口来应用一个类（class），类创建后类内的静态属性必须通过接口的弱校验（属性可以多但不能少），否则就会报错

```typescript
interface Person {
  name: string
  say(): string;
}

class User implements Person {
  name = 'wtw';
  say() {
    return 'hello';
  }
  age = 18;
}

const a = new User()
console.log(a.age) // 18
```

 

## extends

接口之间可以互相继承，子接口拥有父接口所有属性还可以添加额外的新属性

```typescript
interface Person {
  name: string;
  say(): string;
}

// User接口继承了Person接口所有属性，又添加了自己的新属性
interface User extends Person {
  login(): string;
}

let user: User = {
  name: 'wtw',
  say() {
     return 'hello'
  },
  login() {
      return 'ok'
  }
}
```







# type alias

Type alias类型别名，关键字为type，和interface一样用于描述对象或者函数，并且**type还可以直接描述一个基本类型**，type也适用于强校验和弱校验的规则

```typescript
// 接口只能描述对象或者函数
// 类型别名可以描述对象，函数和基础类型
type str = string
const a: str = '12344'

type User = {
  name: string;
  age: number;	
}

let wtw: User = {
  name: 'wtw',
  age: 18
}
```



## 两者的区别

interface用于描述对象或者函数，type可以描述对象，函数和基本类型

interface只能定义对象类型，type声明的方式可以定义组合类型，交叉类型和原始类型，如果用type声明的方式，会导致一些功能的缺失，所以尽量使用接口进行描述。

- interface方式可以实现接口的extends/implements，而type 不行

- interface可以实现接口的merge，但是type不行







# 高级类型Record

高级类型Record会将一个类型的所有属性值都映射到另一个类型上并创造一个新的类型

```ts
type proxyKType = Record<K,T>
```

将K中的每个属性([P in K]),都转为T类型，并将返回的新类型（描述一个对象）返回给proxyKType，K可以是联合类型、对象、枚举、基本类型等

```typescript
type petsGroup = 'dog' | 'cat' | 'fish';
interface IPetInfo {
  name:string,
  age:number,
}

type IPets = Record<petsGroup, IPetInfo>;

const animalsInfo:IPets = {
  dog:{
    name:'dogName',
    age:2
  },
  cat:{
    name:'catName',
    age:3
  },
  fish:{
    name:'fishName',
    age:5
  }
}
```

可以看到 IPets 类型是由 `Record<petsGroup, IPetInfo>`返回的。将petsGroup中的每个属性(dog，cat，fish)都转为 IPetInfo 类型。从原来的字符串变成了IPetInfo对象。

IPets类型描述的即为一个对象内含有三个属性对象dog，cat，fish，每个属性对象又都是一个符合IPetInfo描述的对象。



同时Record还有一种用法，用以避免TypeScript不知道对象中哪些键实际存在，所以不允许访问任何键

该object类型旨在抽象出对象的任何键，而`Record<K, T>`存在以专门定义类型的键。这意味着尝试访问对象属性时存在差异。
TypeScript将允许访问类型对象的任何属性，`Record<any, any>`即使特定键未知，因为第一个通用参数是any。

```typescript
let a: Record<any, any>;
a.foo; // 正常运行

//object然而，在类型的对象上，不假设键any。与此一样Record<any, …>，TypeScript不知道哪些键实际存在，但它不允许访问任何键：

let b: object;
b.foo; 
// 报错: Property "foo" does not exist on type "object"
// ts不知道b中有没有foo属性，为了安全，ts报错

//实例：
//为防止提示res 没有data属性，所以拒绝res访问，使用Record来解决
let res: Record<string, any>;
res.data && JSON.parse(res.data)
```







# 函数

ts定义函数的方式与js完全一致

```typescript
function func1 () {}
const func2 = function() {}
const func3 = () => {}
```



可以在()后对函数的返回值进行注解，注解void为没有返回值

```typescript
function func (num1: number, num2:number):number {
  return num1 + num2
}

// 规定返回值必须为number类型
const func = (str: string): number=> {
	return 123
}
//等价于以下写法
//(str: string) => number意思是代表参数为string类型，返回值为number类型
const func: (str: string) => number = (str) => {
	return 123
}
```

函数注解为never一般表示函数永远不可能执行完，中途一定会被阻塞住（错误或者死循环等）

```typescript
function func(): never{
  while(true) {}
}
```



**ts运行函数时，如果函数要求有形参，但是调用时未传入实参或者实参不足，js中不会报错，但是ts中会报错**

```typescript
function demo(data: {x: number, y: number}) {
  return data.x+data.y
}
demo()//报错，要求传入一个对象类型的形参，没有传入就会报错
```





## 函数重载

ts没有严格意义上的函数重载，函数重载是可以定义多个同名函数，有不同的函数体，但是ts中只能声明多个不同的函数，函数体只有一个。

函数声明可以声明多个同名函数，实现时利用?和逻辑判断让每个声明都能执行且没有逻辑错误，函数重载就不会报错，这样函数的**实体**仍然只有一个，只是**声明**有多个。



1）声明不同参数

```typescript
function add (arg1: number): number
function add (arg1: string, arg2: string): string
function add (arg1: number, arg2: number): number
// 上边是声明
// 因为我们在下边有具体函数的实现，所以这里并不需要添加 declare 关键字

function add (arg1: string | number, arg2?: string | number) {
  // 在实现上我们要注意严格判断两个参数的类型是否相等，而不能简单的写一个 arg1 + arg2
  // 否则认为有逻辑错误而报错
  if (typeof arg1 === 'string' && typeof arg2 === 'string') {
    return 'hello,'+arg1 + arg2
  } else if (typeof arg1 === 'number' && typeof arg2 === 'number') {
    return arg1 + arg2
  }else {
    return arg1
  }
}

console.log(add(1)) //1
console.log(add('1','2')) //hello,12
console.log(add(1,2)) //3
```



2）interface重载

interfaces可以同时声明多个类型，然后函数利用?来动态声明传入参数数量类型，这样每个类型返回值必须为同一类型

```typescript
interface myFunction {
  (arg1: string): string;
  (arg1: string, arg2: string): string;
}

var add: myFunction =  (arg1: string, arg2?: string) => {
  // 在实现上我们要注意严格判断两个参数的类型是否相等，而不能简单的写一个 arg1 + arg2
  // 否则认为有逻辑错误而报错
  if (typeof arg1 === 'string' && typeof arg2 === 'string') {
    return arg1 + arg2
  }else {
    return arg1
  }
}

console.log(add('1')) //1
console.log(add('1','2')) //12
```



3)  **利用?和|来实现动态传入参数**

```ts
interface myFunction {
  (arg1: string, arg2?: string): number|string;
}

var add: myFunction =  (arg1: string, arg2?: string) => {
  if (arg1 && arg2) {
    return 1
  }else {
    return '只有1个参数'
  }
}

console.log(add('1')) // 只有1个参数
console.log(add('1','2')) //1
```





# 数组

ts的数组和js的数组是一样的，数组也可以进行注解，规定元素必须为某种类型

```
数组名: 类型注解[] = []
```

```typescript
const numberArr: number[] = [1, 2, 3];
// 定义一个数字数组

const arr: (number | string)[] = [1, '2', 3];
// 定义一个元素为数字和字符串数组

const objArr: {name: string}[] = [{name: 'abc'}]
// 定义一个数组，只能存放对象类型，成员对象必须有且只能有一个string类型的name属性
```





# 元组tuple

**元组类型用来描述已知数量和类型的数组，**各元素的类型不必相同，数组中的数据类型必须和规定的类型顺序对应起来，元素个数不能多也不能少且每一项对应的数据类型必须对应规定的数据类型

```typescript
const mtTuplt: [string, string, number] = ['a','b',123]
```

```typescript
// 使用元组来注解数组
const user: [string, string, number][] = [
    ['a','user1',18],
    ['b','user2',18],
    ['c','user3',18],
]
```

PS：在 Typescript 2.7之后， Tuple 的定义已经变成了有限制长度的数组了，所以不能越界访问了，在ts2.7之前元组可以越界访问，并且类型为元组所有类型的联合类型。



# 枚举enum

枚举（enum）是typescript新增的类型，枚举类型被编译成了一个枚举成员与枚举值双向映射的关系。枚举成员和枚举值（枚举值不是下标，而是一个映射名）是在进行逻辑判断时是等价的。

每个枚举成员具有一个枚举值，枚举值（number类型）为上一个枚举成员的枚举值加1，第一个元素的枚举值默认为0。枚举成员相当于枚举值的变量名。

枚举成员和枚举值是双向映射的关系，可以相互查找

```
查找枚举值：枚举对象.枚举元素
查找枚举成员：枚举对象[枚举值]
```



**枚举值可以进行修改，下一个枚举成员的枚举值会在上一个枚举值的基础上加1**，枚举值手动更改以后该枚举值就不能被查询了

```typescript
enum Status {
  zero, // 枚举值0
  one, // 枚举值1
  two = 3,  // 将原本为2的枚举值设置为3
  three // 枚举值4
}

console.log(Status[2]) // undefined
//枚举值2已经被修改掉了，查找枚举值为2的目标就查不到返回undefined
```



```typescript
// js中使用对象存储
// const Status = {
//   zero: 0,
//   one: 1
// } 

// ts中使用枚举
// 枚举元素值从0开始递增
enum Status {
  zero, // 第一个枚举值默认为0
  one, // 枚举值1
  two  //枚举值2
}

console.log(typeof Status) //object
console.log(typeof Status.one) //number

// 双向映射
console.log(Status.zero) //0
console.log(Status[0]) //zero

function getResule(statu: any){
  if(statu === Status.one) {
    return 'one'
  }else if(statu === Status.two ) {
    return 'two'
  }
}

console.log(getResule(1)) // one
console.log(getResule(Status.one)) // one

```





# 类

ts的类和js的类很相似，但具有更多功能，例如重写父类中的方法，而且类中constructor中需要new时赋值的属性必须在类中先进行注解

```typescript
class teacher {
  constructor(name) {
    this.name = name;
  };
  name: string; // ts中constructor中的new赋值属性在类中必须注解

 // constructor(public name: string) {};
 // 可以在构造函数形参部分注解constructor(public name: string)，但是必须加上访问类型public
 // 相当于 this.name = name和name: string的语法糖
    
  getName() {
    console.log（'father'）
    return this.name
  };
}
```



## extends

如果父类存在构造函数，子类构造函数中必须先用super()函数调用一次父类构造器，并且super关键字是调用父类的意思，我们在重写的方法中如果想使用父类的同名方法也可以通过super调用出来

```typescript
class User extends teacher {
  constructor(name) {
    super(name)
  }
  say() {
    return '123'
  }
  getName() { // 子类重写父类方法
    console.log（'child'）
    // 再使用super调用父类被重写的同名方法方法
    return super.getName() + 'abc'
  }
} 

let user = new User('wtw')
console.log(user.say())
// 123
console.log(user.getName()) // User重写了get方法，所以User实例执行重写后的get方法
// child
// father
// wtwabc
```



## 访问权限

**访问类型限制了类实例对象中该属性的访问权限**，ts的类中的属性有三种访问类型private，protected，public

| 访问类型  | 权限                                               |
| --------- | -------------------------------------------------- |
| public    | 允许该属性被继承和在类内类外被实例直接调用         |
| protected | 允许在类内使用以及子类继承，但实例无法调用         |
| private   | 只允许在这个类内使用，子类无法继承，实例也无法调用 |

```typescript
class Person {
  public name: string;
  private age: number = 18; //私有属性只能在类中访问和修改
  protected height: number = 170;
  public sayHi() {
    console.log('hi,' + this.name + ',age：' + this.age + ',height：' + this.height);
  };
}

const p = new Person()
p.name = 'wtw'  // 公有属性可以在类外实例直接访问
p.sayHi() // hi,wtw,age：18,height：170
//私和保护属性不能实例直接调用
// console.log(p.age) 报错
// console.log(p.height) 报错
```

```typescript
class Teacher extends Person {
  public sayBye() {
    console.log('name:'+ this.name)
    // name属性父类中没有预设，需要自己定义,否则为undefined
    console.log('height:'+ this.height)  // height保护属性，子类可以继承
    // console.log(this.age) 报错私有属性无法继承
  }
}

const t = new Teacher()
t.name = 'abc'
t.sayBye()
// name:abc  
// height:170
```



## constructor

constructor构造函数，会在new实例的时候执行，通过new附带的参数，为实例属性赋初值。

如果父类也有构造函数，则子类的构造函数也必须第一步调用super调用父类构造函数进行赋值。

跟js不同的是constructor的形参必须进行注解否则报错，有两种注解方式，一是在构造函数这种的形参部分进行注解，这样必须加上访问类型，这样可以省略`this.name = name`这一步，二是在类中进行注解。

```typescript
class Person {
  constructor(public name: string) {}
  getName() {
    console.log('name: ' + this.name)
  }
}

class Teacher extends Person {
  constructor(name,public age: number) {
    super(name)
  }
}

const t = new Teacher('wtw',23)
t.getName()  // name: wtw
```



## get/set

get/set 用于创建提供访问对象的接口，让外部通过接口访问对象内部的属性，能够对访问进行监控。在外部调用getter和setter时当做属性使用，无需加()。

get/set与私有属性配合让外部能够安全的访问对象属性。

```typescript
class Person {
  constructor(private _name: string) {}
  set name(name: string) {
    this._name = name
  }
  get name() {
    return 'name is '+this._name
  }
}
const p = new Person('wtw')
console.log(p.name) //name is wtw
p.name = '123'
console.log(p.name) //name is 123
```



## static

 **静态属性**

直接在class类里写变量，则是实例的私有变量，如果使用static关键字定义变量，就变为了所有对象的公用变量，相当于往对象的原型链上加了个公用变量

**静态方法**

在class内直接写方法，方法会存在构造函数的原型上，变成实例对象调用的方法，而在方法前加static，这个方法就会存入构造函数的`__proto__`对象，变为函数调用的方法（例如apply，call等）



### 利用static构建单例模式

```typescript
class Demo {
  private static instance: Demo;
  private constructor(public name: string) {};
  static getInstance(name: string) {
    if(!this.instance) {
        this.instance = new Demo(name)
    }
    return this.instance;
  }
}
// const demo =  new Demo() 报错，构造函数私有不能new
const demo1 = Demo.getInstance('wtw');
const demo2 = Demo.getInstance('abc');
console.log(demo1 === demo2) // true
console.log(demo2.name) //wtw
```



## 抽象类abstract

抽象类作为公共父类使用，不用来构造对象，只能用来继承，抽象类用来抽离兄弟类公共的属性和方法。

抽象类中含有方法和属性给子类继承，如果抽象类属性前也有abstract修饰，代表这是一个抽象属性/方法，不能在抽象类中实现，应该子类使用同名属性去覆盖（子类来具体实现），并且子类必须去实现这个抽象属性。

```typescript
abstract class Animal {
  eat() {
      return this.name + ' eatting'
  };
  name: string;
  age: number;
  abstract talk(): string; //抽象属性
}

class Cat extends Animal {
  constructor(public name: string,public age: number) { super()}
  talk() {
    return '喵'
  }
}

class Dog extends Animal {
  constructor(public name: string,public age: number) { super()}
  talk() {
    return '汪'
  }
}

const cat = new Cat('cat',5)
const dog = new Dog('dog',5)
console.log(cat.name,cat.age)// cat 5
console.log(cat.talk()) // 喵
console.log(cat.eat()) //cat eatting
```





# 类型保护

联合类型是在注解时使用 `| `，让变量类型可以对应多个注解，但是这样可能会调用不存在的属性和方法而导致错误。

所以在使用联合类型的时候，ts只允许使用联合类型的同名属性或方法，不允许使用联合类型中某一类型的不同名属性或方法。**如果调用变量某一属性会导致逻辑错误，ts就会进行报错，这种措施叫类型保护。**

```typescript
interface Bird {
  fly: boolean;
  sing: () => {};
}

interface Dog {
  fly: boolean;
  eat: () => {};
}

function Animal(animal: Bird | Dog) {
  if(animal.fly){
     animal.sing()  
  }
  //报错，因为animal可能为Dog类型，Dog类型没有sing方法，可能导致出错，所以ts不允许联合类型调用不同名属性
}
```

可以在接口中设定好某一属性为唯一标识，或者提前判断变量是否具有该属性，进行逻辑判断后如果语法不会导致逻辑错误，ts就不会报错

```typescript
interface Bird {
  fly: true;
  sing: () => {};
}

interface Dog {
  fly: false;
  eat: () => {};
}

function Animal(animal: Bird | Dog) {
  // 经过判断再调用属性不会导致逻辑错误，则ts不会报错
  if(animal.fly){
     animal.sing()  
  }
  // 或者
  if('sing' in animal) {
     animal.sing() 
  }
} 
```



# 类型断言as/<>

类型断言（type assertions）是我们告诉ts在这里的变量具体对应联合类型中某一类型，让其可以调用这个类型的非同名方法和属性。让其跳出类型保护的语法限制，使用断言，会手动指定这个变量的类型，就可以调用该类型的非同名属性，并且ts不会报错，断言就当做一个指定了类型的变量来使用。

断言有两种形式：`(变量 as 注解)`或者`(<注解>变量)`，两者等同，但是tsx中只能使用as断言

```typescript
// as断言
function Animal(animal: Bird | Dog) {
  if(animal.fly) { // 如果fly为true，进入断言
    (animal as Bird).sing()
  } else {
    (animal as Bird).eat()
  }
}
// 尖括号断言
function Animal(animal: Bird | Dog) {
  if(animal.fly) { // 如果fly为true，进入断言
    (<Bird>animal).sing()
  } else {
    (<Bird>animal).eat()
  }
}
```



## const断言

const断言（const assertions ）是 TypeScript 3.4 的杀手级新功能，官方解释：

TypeScript 3.4 引入了一个名为 const 断言的字面值的新构造。"const" 断言只能用于引用枚举成员、字符串、数字、布尔值、数组或对象文本。它的语法是一个类型断言，用 const 代替类型名称（例如 `123 as const`）断言构造新的文字表达式时，我们可以向语言发出以下信号：

该表达式中的字面类型不应被扩展（例如：不能从“hello”转换为字符串）

对象字面量获取只读属性

数组字面量成为只读数组



- **没有类型扩展的字面类型**

```typescript
let a = 'x';   // has the type string
const b = 'x'; // has the type 'x' 
```

let定义变量，变量是可以被重新分配的栈上内存地址的（也就是基本类型无法重新赋值，引用类型无法修改引用），const定义的常量是无法被重新分配栈上内存地址的。

as const的第一个用法就是模拟const定义一个常量，可以在无法使用const关键字的地方定义常量，例如对象中初始化值

```typescript
let c = 'x' as const; // has the type 'x'
c = 'qqqq' // 报错，c无法重新赋值
let d = {
  id: 'zzzzzz' as const 
}
d.id = 'qwerrrr' // 报错，d.id无法重新赋值
```



- **对象字面量获取只读属性**

const对于引用类型来说，只能让其栈上的引用地址无法修改，堆上的属性值可以进行修改

```typescript
const obj = {
  id: 'wtw',
  age: 18
}
obj.id = 'qqq'// 成功修改
obj = { //报错，无法修改引用
  id: 'qww'
}
```

可以对对象每个属性使用readonly注解或者固定注解来防止修改（两者区别在于报错不同），但是一旦属性数量多了后很难维护，现在可以使用const断言语法糖，实现原理是给对象内所有属性都加上readonly

```typescript
// 使用readonly注解
interface person  {
  readonly id: 'wtw',
  readonly age: 18  
} 
const obj1:person = {
  id: 'wtw',
  age: 18  
}
obj1.id = 'qqq' // 报错，无法修改只读属性
```

```typescript
// 使用固定注解
interface person{
  id: 'wtw',
  age: 18  
}
const obj2:person = {
  id: 'wtw',
  age: 18  
} 
obj2.id = 'qqq' // 不符合固定注解，报错
```

```typescript
// 使用const断言，原理等同与使用readonly注解
const obj3 = <const>{
  id: 'wtw',
  age: 18
}
obj3.id = 'qqq' // 报错，无法修改只读属性
```



- **数组字面量成为只读数组**

普通元组可以控制数组的类型和数量，但是内部属性可以修改，无法做到完全的只读数组

```typescript
const mtTuplt: [string, string, number] = ['a','b',123]
mtTuplt[2] = 456 // 可以修改
```

使用const泛型可以修改为只读数组，原理是给数组每个成员都加上readonly

```typescript
const mtTuplt = <const>['a','b',123]
mtTuplt[2] = 456 // 报错，无法修改只读属性
```





# 泛型generic

设计函数时，形参可能并不知道实参传入的类型，所以没办法注解。

泛型是指泛指的类型，是一个我们定义的变量，用来作为注解变量，在调用函数传入实参时注明泛型的类型传给泛型，形参再根据泛型作为注解，做到根据实参传入的泛型来决定形参的注解类型，泛型也具有类型推断，可以省略实参的泛型类型由ts自动推断传入。

泛型可以让我们在函数可能传入不同类型的实参时也能方便的对传入的形参进行注解和约束。

```typescript
function 函数名<泛型名>(参数: 泛型名) {} // 定义泛型函数
函数名<泛型变量的类型>(参数)  // 使用泛型函数
```

```typescript
function join<T>(a: T,b: T) {
  if(typeof a === 'number'&& typeof b === 'number') {
      return a + b
  }
  return `${a} with ${b} `
}

// 泛型也具有类型推断，可以省略由ts自动推断
console.log(join<string>('w','tw')) // w with tw
console.log(join('w','tw')) // w with tw
console.log(join<number>(1,2)) // 3
```



## 泛型继承

在形参中，泛型可以选择继承某一接口，继承后要求传入的泛型必须继承接口中的属性（因为是继承，所以还可以多出属性）

```typescript
interface Item {
  name: string
}
// 要求泛型必须继承接口属性
function join<T extends Item>(item: T) {
  return item.name
}

console.log(join({name: 'wtw',age: 18})) //wtw 
```





## 类中的泛型

泛型同样可以应用于类中，用构造器于来构造对象

```typescript
interface Item {
  name: string
}

class Data<T extends Item> {
  constructor(private data: T[]) {};
  getName(index: number): string{
    return this.data[index].name
  };
  getItem(index: number): T{
    return this.data[index]
  };
}

const data = new Data([
  {
    name: 'wtw',
    age: 18
  },
  {
    name: 'wy',
    age: 20
  }
])

console.log(data.getName(1)) // wy
console.log(data.getItem(1)) // { name: 'wy', age: 20 }
```



# 索引查询keyof

keyof是索引类型查询操作符，TypeScript 允许我们遍历某种类型的属性，并通过 keyof 操作符提取其属性的名称。keyof操作符是在 TypeScript 2.1 版本引入的，该操作符可以用于获取某种类型的所有键，其返回类型是联合类型。

typescript的keyof关键字，将一个类型T映射为它**所有成员名称**的联合类型。



- keyof interface

```typescript
interface Person {
  name: string;
  age: number;
  location: string;
}
 
type K1 = keyof Person; // K1 = "name" | "age" | "location"
type K2 = keyof Person[]; 
// keyof的是一个数组，K2 = "length" | "push" | "pop" | "concat" | ...
type K3 = keyof { [x: string]: Person };  // k3 = string | number
```



- keyof class

```typescript
class Person {
  name: string = "Semlinker";
}

let a: keyof Person;
console.log(a) // undefined
a = "name" // 成功赋值
a = "age" 
// Type '"age"' is not assignable to type '"name"'.
// 报错，a被注解为string类型的"name"，所以只能赋值为name
```



- keyof 基本数据类型

会将基本类型上的方法，属性等作为联合类型返回

```typescript
let K1: keyof boolean; // let K1: "valueOf"
let K2: keyof number; // let K2: "toString" | "toFixed" | "toExponential" | ...
let K3: keyof symbol; // let K1: "valueOf"
```





## keyof和泛型应用

调用对象的属性查询`this.info[key] `，为了避免TS类型保护导致报错，我们需要写`key === 'name' || key === 'age' || key === 'gender'`很大一长串逻辑判断来保护程序安全，属性多了以后是不可能写完的。

```typescript
interface Person {
  name: string;
  age: number;
  gender: string;
}

class People {
  constructor(private info: Person) {}
  // getInfo(key: string){
  //   return this.info[key] 
  // 触发类型保护报错，因为key可以是任意字符，info中可能查找不到，ts为了保护程序而报错
  // }
  getInfo(key: string) {
    if(key === 'name' || key === 'age' || key === 'gender') {
      return this.info[key] 
    }else {
      return 'not find'
    }
  }
 
}
const people = new People ({
  name: 'wtw',
  age: 18,
  gender: 'male'
})

console.log(people.getInfo('name')) //wtw
console.log(people.getInfo('birthday')) //not find
```

**可以利用keyof和泛型来解决这个问题**：利用keyof和泛型结合保护了程序的安全，避免了意料之外的访问

```typescript
getInfo<T extends keyof Person>(key: T) {
  return this.info[key];
}

console.log(people.getInfo('name')) //wtw
console.log(people.getInfo('birthday')) //报错，泛型限定了只能输入Person中的三个属性之一
```







# 命名空间namespace

命名空间一个最明确的目的就是解决重名问题。命名空间将全局变量收集起来通过向外export的方式让外部访问，让多个全局变量封装成一个命名空间全局变量，大大减少了全局变量的数量，减少了命名冲突的可能。

定义的全局变量作用域都是全局，引入其他模块或者包后可能会产生很多命名冲突问题，可以将一个模块的全局变量放入一个命名空间内，并对外export暴露的接口，外部通过`命名空间.接口`的方式来访问，防止全局作用域下存在太多变量导致重名问题。

外部只能通过`命名空间.接口`访问namespace向外export的接口

```
namespace 命名空间 {
  export 变量/接口/类...
}

外部通过 命名空间.接口 来调用
```

```typescript
namespace SomeNameSpaceName { 
   export interface ISomeInterfaceName {}  
   export class SomeClassName {}  
   class User {}
}

const a: SomeNameSpaceName.ISomeInterfaceName = {}
const b = new SomeNameSpaceName.SomeClassName()
const c = new SomeNameSpaceName.User() // 报错，命名空间没有暴露User的接口，外部无法使用
```



## 引入命名空间

普通的 JS 模块文件可以相互引用一样，包含 namespace 的命名空间文件也可以相互引入，还可以组合成一个更大的命名空间，引入之后可以正确的显示语法提示。无论是在其他命名空间中引入命名空间，还是在一个ts文件中引入命名空间来使用都可以通过完全限定名的方式。

reference 注释引用命名空间，在ts中reference注释引用不算注释，会正确的引用对应的文件的命名空间，即可通过“完全限定名”进行访问。

```
/// <reference path='文件路径' />
```

**在命名空间中引入命名空间**

```typescript
/// <reference path="./components.ts" />
namespace Home {
  export class Page {
    constructor () {
      new Components.Header
      new Components.Content
      new Components.Footer
    }
  }
}
```

**在ts文件中引入命名空间**

```typescript
/// <reference path="./page.ts" />
const page = new Home.page()
```





# @装饰器

装饰器是一种特殊类型的声明，本质上就是一个方法，可以注入到类、方法、属性、参数上，扩展其功能；

常见的装饰器：类装饰器、属性装饰器、方法装饰器、参数装饰器

装饰器在写法上有：普通装饰器(无法传参)、装饰器工厂(可传参)

ts装饰器和js装饰器有个最大最明显的区别就是，ts装饰器的参数**必须写全**，否则报错，相反，js装饰器就没这要求。而**ts装饰器在修饰不同东西时装饰器的参数是不同的，参数数量也是不同的**，所以这就导致ts装饰器上手比js装饰器难得多，并不是一个简单的语法糖。



## 类的装饰器

类的装饰器是一个函数，在class类之前@装饰器函数就可以运行装饰器了，装饰器属于实验类型的语法所以还需要配置，在tsconfig.json中打开以下两项

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
  }
}

```

装饰器在创建类时运行（class的时候运行），**装饰器函数接受的参数是构造器函数constructor**，然后会对类的构造函数进行一些修改，**当装饰的对象是类时，我们操作的就是这个类本身**。



```typescript
function testDecorator(constructor: any) {
  console.log('装饰器运行')
  constructor.prototype.getName = () => {
    console.log('getName1')
  }
}


@testDecorator
class Test {} 
/*
装饰器运行
*/

const test = new Test();
(test as any).getName(); // getName1
```



**可以利用工厂模式制造装饰器工厂，可以根据我们传入的bool来决定对构造函数怎样操作**

```javascript
function testDecorator(flag: boolean) {
  if(flag){
    return function (constructor: any) {
      console.log('装饰器1运行');
      constructor.prototype.getName = () => {
        console.log('getName1');
      };
    };
  }else {
     // 传入false则不进行装饰
    return function (constructor: any) {}
  }
}


@testDecorator(true)
class Test {
  constructor() {
      console.log('构造函数运行')
  }
} 
/*
装饰器1运行
*/

const test = new Test(); // 构造函数运行
(test as any).getName(); // getName1
```

因为getName不是类本身的属性，所以`test.getName()`会直接报错，需要将test注解为any来强行避免类型保护，但是将constructor和test注解为any是不规范的，应该使用泛型来进行注解。



`new (...args: any[]) => any`是指new 一个构造函数，这个构造函数的参数任意类型且任意多，然后这个构造函数返回一个any类型。

`T extends new (...args: any[]) => any `表示T是一个包含构造函数的类。然后装饰器函数返回一个类，`  return class extends constructor {}`，可以在返回的类中对装饰的类的构造函数进行扩展。

**@装饰器后，装饰器就能直接拿到类的构造函数并在{}中操作，在类的构造函数执行完后，装饰器中return的类就会执行并去修改构造函数的内容。**

```typescript
function testDecorator<T extends new (...args: any[]) => any> (constructor: T) {
  console.log('1')
  return class extends constructor {
    name = 'wang taiwei'
    getName() {
        return this.name
    }
  }
};

@testDecorator
class Test {
    name: string;
    constructor(name: string) {
      console.log('2')
      this.name = name
    }
}

const test = new Test('wtw');
/*
1   
2   
*/
console.log((test as any).getName()) //wang taiwei
```

但是这样test依然不能直接访问getName，因为类构造完以后再去装饰，语法提示无法获取装饰之后的属性，只能使用断言为any来强行避免类型保护。



要解决这个问题还是需要使用工厂模式，先用装饰器装饰一个类，然后再把装饰之后的类赋值给Test，这样类在一开始就被装饰过了，就可以获得语法提示且不报错了

```typescript
function testDecorator() {
  return function <T extends new (...args: any[]) => any> (constructor: T) {
    console.log('1')
    return class extends constructor {
      name = 'wang taiwei';
      getName() {
        return this.name;
      };
    };
  };
}


const Test = testDecorator()(
  class {
    name: string;
    constructor(name: string) {
      console.log('2')
      this.name = name
    }
  }
)
/*
1
2
*/

const test = new Test('wtw');
console.log(test.getName()) //wang taiwei
```



## 方法装饰器

方法是类中的函数，装饰器在创建类的时候立刻就会执行，装饰器函数在修饰方法时有三个参数，target，key和descriptor。

普通方法，target对应的是该方法的类的prototype（原型），key对应被修饰的方法名
静态方法，target对应的是类的构造函数

descriptor是类似于js的`Object.defineProperty(obj, prop, descriptor)`方法，可以用来修改对象的属性特征。descriptor中有enumerable，configurable，value，writable这些属性用于修改该方法的特征



方法装饰器可以对装饰的方法进行一些变更。

```typescript
// 普通方法，target对应的是Test类的prototype，key对应修饰的方法名getName
function getNameDecorator(target: any, key: string, descriptor: PropertyDescriptor) {
  //console.log(target) // Test { getName: [Function] }
 // console.log(key) // getName
  descriptor.writable = true //被修饰的方法不允许修改s不允许修改
  descriptor.value = function() {
    // 将函数内容进行修改
    return 'descriptor'
  }
}

class Test {
  constructor(public name: string) {}
  @getNameDecorator
  getName() {  // 该方法被@getNameDecorator装饰
    return this.name
  }
}

const test = new Test('wtw')
test.name = 'abc'
// test.getName = () => {  // 运行时报错，因为getName被修饰为不可修改
//   return '123'
// }
console.log(test.getName()) // descriptor
```



## 访问器装饰器

访问器装饰器，target对应的是该方法的类的prototype（原型），key对应被修饰的方法名

descriptor是类似于js的`Object.defineProperty(obj, prop, descriptor)`方法，可以用来修改对象的属性特征。descriptor中有enumerable，configurable，value，writable这些属性用于修改该方法的特征

getter/setter只能有一个使用装饰器

```typescript
function visitDecorator(target: any, key: string, descriptor: PropertyDescriptor) {
  descriptor.writable = false;
}

class Test {
  private _name: string
  constructor(name: string) {
    this._name = name
  }
  get name() {
    return this._name
  }
  @visitDecorator
  set name(name: string) {
    this._name = name
  }
}

const test = new Test('wtw')
test.name = 'abc'  // 报错，装饰器认为=是在对访问器name做修改
```



## 属性装饰器

属性装饰器只有target和key两个参数，target表示类的prototype，key表示装饰的属性名，因为属性装饰器不自带PropertyDescriptor，所以我们需要手动创建并返回，替换掉属性原本的属性特征

```typescript
function nameDecorator(target: any, key: string): any{
  // 因为属性装饰器不自带PropertyDescriptor，所以我们需要手动创建并返回
  // 我们定义的属性特征就会应用在被装饰的属性上
  target[key] = '原型属性'
  const descriptor: PropertyDescriptor = {
    writable: false
  }
  return descriptor;
}

class Test {
  @nameDecorator
  name = 'wtw'
}

const test = new Test()
// test.name= 'abc' // 报错，该属性已被装饰为不可修改
console.log(test.__proto__.name) // 原型属性
console.log(test.name) // wtw 
```



target是类的原型，类的原型可以通过实例对象`__proto__`访问

```typescript
function nameDecorator(target: any, key: string): any{
  target[key] = '原型属性'
}

class Test {
  @nameDecorator
  name = 'wtw'
}

const test = new Test()
// test.name= 'abc' // 报错，该属性已被装饰为不可修改
console.log((test as any).__proto__.name) // 原型属性
console.log(test.name) // wtw 
```



## 方法参数装饰器

参数装饰器有三个参数，target表示类的prototype，key表示参数所在的方法名（不是修饰的属性名），第三个参数paramIndex表示当前参数在方法中所处第几个位置

```typescript
function paramDecorator(target: any, key: string, paramIndex: number){
  console.log(key)// getInfo
  console.log(paramIndex) // 0
}

class Test {
  getInfo(@paramDecorator name: string, age: number) {
    console.log(name, age)
  }
}

const test = new Test()
test.getInfo('WTW',20)
```





# ts项目

## 初始化

初始化npm项目，生成npm项目信息package.jsoon

```
npm init
```



安装typescript

```
npm install typescript -g
```



安装TypeScript后，可以通过运行 `tsc --init` 初始化TypeScript。`tsc` 是“ TypeScript编译器”的缩写，是TypeScript的命令行工具。

 `tsc --init` 	会自动生成一个`tsconfig.json`文件

`tsconfig.json`是ts的编译配置文件，决定了编译哪些ts文件，ts文件按照那种方式编译为js文件，如js的版本，模块化的方式等

```json
{
  "include": ["./demo.ts"], // 运行tsc命令只编译这个字段包含的ts文件
  "exclude": ["./demo.ts"], // 运行tsc命令，除了这个字段包含的ts文件，其他ts文件都编译
  "files": ["./demo.ts"], // 作用和include相同
  "compilerOptions": {      // 编译配置
    "outFile": "./build/page.js",  // 多个ts文件打包到一个js文件中，并规定存放路径
    "rootDir": "./src", // 运行tsc命令，编译目标文件夹下的ts文件
    "outDir": "./dist", // 编译的js文件放进build文件夹
    "incremental": true, // 增量编译，只有新增的ts文件才编译，以前编译过的不再编译，开启后会生成一个文件存放文件信息，已经编译过的文件会被记录在上面，下次编译自动排除这些文件
    "target": "es5", // 将ts编译为es5的js文件
    "module": "commonjs", // 编译后的js文件的模块化选择commonjs
    "alwaysStrict": false, // 在编译后的js文件中注入"use strict";开启严格模式
    "allowJs": true, // 在ts文件中允许引入js模块，如果目标文件夹（rootDir或者根目录）下有js文件，会一起被编译到outDir中，会和ts一样使用es6转es5，开启严格模式等配置进行编译
    "checkJs": true, // 对目标文件夹（rootDir或者根目录）中的js文件进行和ts相同的语法检测，如检测变量是否存在等
      
    // ts代码约束配置
	"strict": true, // 以下约束ts编码风格的字段全部设置为true
	"removeComments": true, // 删除注释 
	"noImplicitAny": true, // 不允许隐式的any类型，所有any类型都必须注解
	"strictNullChecks": false, // 设置true不允许把null、undefined赋值给其他类型的变量，设置false以后null和undefined可以赋值给注解为其他类型的变量
      
    // 项目配置
    "baseUrl": "./", // 这个ts项目的根路径
  }
}
```

配置是可选的，需要的配置添加进tsconfig.json即可。





## 编译命令

编译是指将.ts文件转换为js引擎能执行的js文件

`tsc`	根据tsconfig.json配置将根目录下所有ts文件编译为js文件

`tsc -w`  每当ts文件修改就自动重新打包编译一次

`tsc demo.ts`	将指定ts文件编译为js文件（不会按照tsconfig.json的配置编译），如果想使用tsconfig.json编译指定文件应该配置`include`字段以后使用`tsc`命令打包

`tsc demo.ts -t es5` 	将制定ts文件编译为es5及以上的版本，有些高级特性必须编译为es5以上的版本时就需要运行这个命令编译（如get/set关键字）

`ts-node demo.js`	使用tsconfig.json配置直接运行ts文件



## 配置自动编译

在package.json中配置

```
"script": {
  "build": "tsc -w"
}
```

开启一个命令行工具运行 npm run build 打包，之后每次ts文件改变就会立刻编译刷新js文件

然后安装nodemon，专门用于自动执行node端的js文件

```
npm install nodemon
```

在package.json中配置，再开启另外一个命令行工具运行`npm run start`，每当js文件改变后就会自动重新运行

```json
"script": {
  "build": "tsc -w",
  "start": "nodemon ./build/demo.js"
}
```

`tsc -w`和nodemon组合起来，每次ts改变，就会重新生成js文件，js文件每次重新生成nodemon检测到以后就会重新运行。



这样需要同时运行两个命令行工具有些繁琐，可以使用一个并行执行工具concurrenly

```
npm install concurrently -D
```

然后再package.json中配置

```json
"script": {
  "build": "tsc -w",
  "start": "nodemon ./build/demo.js",
  "dev": "concurrently npm run build & npm run start"
}
```

运行`npm run dev` 就会同时运行`build`和`start`两个命令





# .d.ts

d.ts大名叫TypeScript Declaration File，存放一些声明，类似于C/C++的.h头文件（`#include <stdio.h>`）。

没有`.d.ts`文件不会影响编译之后的JS结果，但是在使用IDE编写代码时很多地方会报错，因为ts不能直接识别js库中定义的属性，需要使用`.d.ts`进行辅助识别。可以在`.d.ts`中将库中的属性和方法以ts语法进行注册。

1）当编写的ts文件编译为js文件后，可以对外生成一个`.d.ts` 文件用于记录ts中的类型信息

`.ts` 表示你的代码就是用 ts 写的。但这种代码最后会编译成 `.js` 的 js 代码，供他人使用。直接使用js文件，类型信息就丢失了。所以 ts 编译器会自动根据 `.ts` 中的信息，生成对外的 `.d.ts` 文件，和生成的 js 文件搭配使用。其中，js 文件是给js引擎运行使用的，而 `.d.ts` 文件是给写ts代码时进行语法提示用的。

2）在ts文件中引入js库

另一种情况是，库是用js写的，希望能ts中使用时能有类型信息作为辅助语法提示，不让IDE报错，就需要编写`.d.ts`文件用于加载ts类型



js的库在ts中不能直接引入使用，如果官方提供了`.d.ts`才能引入，否则IDE会有很多语法报错，下载库的`.d.ts`的命令为

```
 npm i @types/XXX
```

这个并不能代替XXX库本身的安装。可以理解成@types只是为js库提供了index.d.ts 这样的带有类型注解的导出声明而已。如果js库类似于是.c的话，@types就类似于给加了一个.h文件，不能代替.c本身，还是需要`npm install XXX`下载库的JS代码。



## 编写.d.ts

```html
<!--index.html-->
<script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.js"></script>
<script src="./index.ts"></script>
<!--使用parcel打包可以直接引入ts-->
```

```typescript
// index.ts
$(function() {
  $('body').html('<div>hello,world</div>')
  new $.fn.init()
})
```

因为jQuery是js库，ts中没有类型声明无法直接使用，IDE会报错（但是编译为js后依然能正常使用）

所以我们需要手动添加`.d.ts`



## declare

- declare  声明

declare声明一个ts的全局变量并对这个变量进行注解，declare可以向TypeScript域中引入一个变量，在编写代码的时候就能够实现语法提示的功能。

例如想在ts中使用jQuery，但是ts并不能识别jQuery.js中的$，需要将`$`在`.d.ts`中声明为一个全局变量，让IDE不报错。



`.d.ts`中声明函数可以直接用function声明，也可以var一个变量来指代声明函数

`.d.ts`中声明一个全局对象是声明将对象为一个命名空间，内部包含属性和方法

```typescript
// Jquery.d.ts

// $(function(){})将$注解为一个全局函数，需要传入一个返回void的函数参数，$函数最后也返回void
// declare var $: (param: ()=>void) => void 定义一个全局变量$，变量代表一个函数，作用也一样
declare function $(param: ()=>void): void



// 对$函数进行重载
// $('body').html('<div>hello,world</div>') 表示$允许传入一个字符串并返回一个含有html方法的对象
// 创建一个Jquery对象接口，接口中包含html方法
interface JqueryInstance {
  html: (html: string) => JqueryInstance
}
declare function $(params: string): JqueryInstance



// new $.fn.init() 表示$对象调用init对象下的init类的构造函数
// 先将$声明为一个对象，fn声明为$的属性对象，init声明为fn的一个子类
declare namespace $ {
    namespace fn {
        class init {}
    }
}
```



上面的函数重载写法有些繁琐，重载可以通过interface重载简写为以下格式

```typescript
// Jquery.d.ts
interface JqueryInstance {
  html: (html: string) => JqueryInstance
}
// 使用interface重载
interface JQuery {
  (readyFunc: () => void): void;
  (selector: string): JqueryInstance;
}
// 声明全局变量，让其他ts文件能使用理解$函数是什么
declare var $: JQuery
```



## 模块化

推荐使用模块化的方式编写`.d.ts`文件

以模块化的方式来编写`.d.ts`，让其他文件引入模块中导出的声明，这样能够让代码更利于阅读，能够追踪引入的全局变量是在哪个`.d.ts`文件中进行声明的



下载jquery库并引入

```
npm install jquery --save
```

import `jquery.d.ts` 中导出的全局变量$

```typescript
// page.ts
// 将jquery.d.ts导出的$导入
import $ from 'jquery'

$(function() {
  $('body').html('<div>hello,world</div>')
  new $.fn.init();
})
```

以ES6模块化的方式写`jquery.d.ts`，将全局变量export出去

```typescript
// jquery.d.ts
// ES6模块化的形式
declare module 'jquery' {
  interface JqueryInstance {
    html: (html: string) => JqueryInstance
  }
  
 //$声明成一个混合类型，可以是函数，也可以是对象
  function $(readyFunc:() => void): void;
  function $(selector: string): JqueryInstance;
  namespace $ {
    namespace fn {
      class init{}
    }
  }
  // 将$导出出去
  export = $
}
```





