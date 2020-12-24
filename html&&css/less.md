# less

​	Less是一门css预处理语言，他扩展了css语言，增加了变量、Mixin、函数等特性，使css更容易维护和扩展。他不是一个直接使用的语言，而是一个生成css的语言。Less可以运行在Node或浏览器端。

## 运行时编译less

```html
<style type="text/less">
less代码
</style>
...
<!--引入less支持-->
<script src="less.js"></script>
```

运行时编译less性能较低，所以我们需要提前将less转换为css文件，然后让html文件引用这个css文件

所以叫Less为css预处理语言，他还要转化为css使用

## 预编译less

```html
 <link href="CSS文件路径"  rel="stylesheet"  type="text/css"/>
```

less文件转为css后需要用外链引入css

### 使用软件编译less

使用考拉软件或者vs code的easy less插件可以编译less成css文件

考拉软件需要手动编译生成css文件

安装easy less插件后less文件保存后会自动生成一个css文件

### 使用Node端编译less

先下载less编译器

```
npm install less -g
```

将less文件编译后的css代码在控制台输出查看

```
lessc lessFail.less
```

将 less文件编译为 css文件的命令行命令

```
lessc lessFail.less cssFile.css
```



# 注释

用//的注释不会编译进css，开发人员才能看的注释

用/**/的注释会编译进css，用户可以看的注释



# 嵌套结构

## 子集嵌套

css中不能嵌套，我们只能用选择器来选中子级，但是当css代码多了以后就很不利于阅读和书写

```css
#wrap{

}
#wrap .center{


}
```

less中可以书写嵌套关系，让代码更利于书写与阅读，不用使用选择器

子集嵌套会被编译为如上代码

```less
#wrap{
    
	//利用less的嵌套来选择子级的center盒子
	.center{

	}
}
```

## 伪类嵌套

`:hover`是元素的进入的伪类选择器，书写时嵌套进元素中，但直接嵌套编译为css时`元素:hover`会被编译为子集嵌套，`元素: hover`

两者多了一个空格，less编译器就会识别为子集选择器，就发生错误了

所以规定，状态嵌套需要在嵌套前加 `&`  符号来表明这是元素的一个伪类，而不是一个子级元素

```less
#wrap{
    
	//伪类嵌套用&提示编译器不加空格
	&:hover{

	}
}
```

****

less编译为css后如下

```css
#wrap{

}
#wrap:hover{
   	
}
```



# 变量

用@来声明一个变量 

 ```
@变量名:变量值
 ```

将属性值定义为变量，直接：`@变量名 ` 使用即可

如果变量为属性名，URL，选择器，或者在字符串中使用变量，使用时都需要用{ }包裹变量名：`@{变量名}`

不这样做定义变量和使用变量就会混淆，但一般不推荐将选择器和属性名定义为变量。

```less
@color1:pink;
//将颜色属性值pink变为变量

@m:margin;
@p:padding;
//将margin,padding属性名变为变量

@selector:#wrap;
//将id选择器定义为变量

*{
    //属性值定义为变量，直接：@变量名使用即可
	background:@color1;
    //将属性名或者选择器定义为变量，需要用{ }包裹变量名
    @{m}: 0;
    @{p}: 0;
}
@{selector}{
    position: relative;
    width: 300px;
    height: 300px;
    margin: 0 auto;
    border:1px solid;
    background: #000;
}
```

```less
@images: "http://www.kaifaxueyuan.com";
.myclass {
  background : url("@{images}/images/birds.jpg");
  width:700px;
  height:440px;
}
```

## 变量的延迟加载

less变量同样具有作用域，一般在一对{ }内

局部变量优先级大于上一级传递过来的变量

而且变量具有延迟加载的特性，会将整个less文件读取完后再开始为将变量替换为值，也就是同样优先级的情况下后定义的变量会覆盖前面定义的同名变量

```less
@var:0;
.box1{
@var:1
	.box2{
		@var:2;
		three:@var;//属性值为3
		@var:3;//延迟加载覆盖之前定义的变量var，且局部变量优先级大于上一级的var
	}
	one:@var;//1
}
```





# 混合

当两个css代码块具有大量重复代码时，可以将重复代码写在一个类里，在需要的地方调用这个代码块，less编译器自动将代码复制进css文件的调用代码块内



## 普通混合

**所以一般都不使用普通混合**

普通混合代码是将混合的代码设置成一个普通的css类

但普通混合在编译为css文件时，混合这段代码会留下来，因为编译器看到普通类会把它当做普通的less代码处理，混合和普通类不能区分，遇到使用普通类却没有加{ }，编译器就会去寻找该类并替换掉这段调用代码，但`.mixins`这个类也会被编译进css文件，而`.box1`内有存在一段相同代码，造成代码冗余

****

```less
//普通混合
//混合类会被编译进css
.mixins{
	margin:0 auto;
}

.box1{
	.mixins;
}
```

编译为css后如下

```css
.mixins{
	margin:0 auto;
}
.box1{
	margin:0 auto;
}
```



## 不带参数的混合

将混合代加上`()`，这样编译器会识别为`.mixins(){}`混合并且不编译进css文件中

```less
.mixins(){
	margin:0 auto;
}

.box1{
	.mixins;
}
```

****

编译为css代码如下

```css
.box1{
	margin:0 auto;
}
```



## 带参数的混合

可以给混合传入参数，可以让每次混入都不同，参数还可以带默认值，如果形参与实参数量不匹配时，则有默认值的形参优先使用默认值，**带默认值的参数只能放在形参最后，不然会报错**。

多个参数用`,`隔开，但当参数中自带有`,`时，参数间的分隔符应由`,`变为`;`

```less
.mixins(@h, @c, @w:50px){
//@w:50px是带默认值的混合，如果只传入了两个参数，则@w不占用参数，直接使用默认值
	width:@w;
	height:@h;
	background:@c;
}
.box1{
    //形参实参数量不匹配，@w有默认值，先使用默认值
	.mixins(100px,pink)
}
.box1{
    //形参实参数量匹配，@w不使用默认值
	.mixins(200px,200px,deeppink)
}
```



## 命名参数

如果形参与实参数量不匹配，会使用默认值的情况下，也可以传入命名参数指定将参数给那个形参，让他不使用默认值

```less
.mixins(@w:50px,@h:100px,@c:blue){
//@w:50px是带默认值的混合，如果只传入了两个参数，则@w不占用参数，直接使用默认值
	width:@w;
	height:@h;
	background:@c;
}
.box1{
	.mixins(100px,100px,pink)
}
.box1{
    //传入命名参数，指定将这个值给@c
	.mixins(@c:deeppink)
}
```



## 匹配模式

设置同名的混入附上上一个标识符，可以根据标识符选择不同的同名混入，类似于面向对象的多态

```less
.h(A,@c){
	height: 100px;
	background:@c
}

.h(B,@c){
	height: 50px;
	background:@c
}

.h(C,@c){
	height: 60px;
	background:@c
}

//当输入标识不为ABC时满足default()函数条件进入这个混入，此时传入的标识符将作为变量@d使用
.h(@d,@c) when (default()){
    height: 200px;
    tag:@d;
	background:@c;
}

//然后在使用混入类的时候会根据所带的标识符来确定取用哪个混入
.box1{
    //取用标识符为B的混入
	.h(B,blue);
}

.box2{
    //D不属于ABC进入default
    //D将作为变量传入形参@d
	.h(D,blue);
}
```

****

编译为css后如下

```css
.box1 {
  height: 50px;
  background: blue;
}
.box2 {
  height: 200px;
  tag: D;
  background: blue;
}
```



## arguments

实参列表@arguments

```less
.border(@w:1px,@s:soild,@c:red){
	//我们编写时就不用把@w，@s，@c全部写下来
	//@arguments代表了传入的实参列表
	border:@arguments
}

.box{
	.border(2px)
}
```

****

编译结果如下

```css
.box {
  border: 2px soild red;
}

```



## 混合返回值

混合用于运算，并返回结果生成变量

```less
.num(@num1,@num2){
	@sum:(@num1+@num2);
	@ave:((@num1+@num2)/2);
}

.box{
	.num(10px,20px);
    //传入参数进行运算，并将结果生成两个变量
	padding:@sum;
	margin:@ave;
}

```

编译css结果

```css
.box {
  padding: 30px;
  margin: 15px;
}
```



# 运算

less中任何数字，颜色，变量都可以进行运算，less会自动加上单位，不一定每个运算都必须添加单位

颜色运算时会将颜色值转化为rgb模式，然后再转换为16进制的颜色值并返回，颜色运算不能使用英文名进行计算

```less
.box1{
	height:50px+50
	//结果为100px
}
```



# 函数

less中提供能很多用于转换颜色，处理字符串和进行算数运算的函数，这些函数使用起来非常简单

rgb()  函数：编译时将rgb模式转换为16进制数

```less
.bgc{
	background: rgb(0,133,0);
	//编译为css时会转化为16进制的数
}
```

```css
//编译为
.bgc {
  background: #008500;
}
```



# 命名空间

命名空间将多个混入嵌套到一个混入内，然后使用时用类似css子级选择器的方式把想用的混用选择出来

通过嵌套关系然后`>`来指定调用哪个混入，大于号也可以省略为空格

```less
#bgcolor(){
	background: #fff;
	.a{
		color:red;
	}
	.b{
		color:blue;
	}
}

.box1{
    //调用#bgcolor混入里的.a混入
	#bgcolor>.a;
    //省略大于号换为空格同样生效
    //#bgcolor .a;
}
.box2{
	#bgcolor>.b;
}
```

编译结果如下

```css
.box1 {
  color: red;
}
.box2 {
  color: blue;
}
```



# 引入

在一个less文件中可以`@import`需要的css文件和less文件

```less
@import "library.less";
@import "typo.css";
//可以加上()输入参数
//reference  引用less文件，但是不输出
//inline  将less文件原样输出到css中
//less   无视拓展名，都当做less文件
//css	 无视拓展名，都当做css文件
//multiple	允许引入多次相同的文件
//once   默认，一个文件只允许引入一次
//例如
@import(inline)'less1.less'
```



# 条件表达式

根据布尔值来判断是否往css文件中输出这个混入

```
when  类似于if用于条件判断

判断条件结果应为bool类型
mixinName(参数)when(判断条件){
	//传入参数调用混入时，判断条件结果为真才往css中渲染这个混入
}

比较运算符：>,>=,<,<=,=,true

函数库中的一些函数
light(@c) 		输出这个颜色的亮度
iscolor(@c)		判断参数是否是颜色
isnumber(@n)	判断参数是否是数字
ispixel(@p)		判断参数是否是像素
ispercentage(@pe)	判断参数是否是百分比
isem(@e)		判断参数是否是em
```

```less
//lightness亮度
//判断参数@a亮度大于50%，将背景颜色设置为黑色
.mixin(@c)when(lightness(@c)>=50%){
	background:black;
}
//判断参数@a亮度小于50%，将背景颜色设置为黑色
.mixin(@c)when(lightness(@c)<50%){
	background:white;
}
.mixin(@c){
	color:@a;
}

.box1{
    //字体颜色#ddd亮度大于50%
    //box1背景颜色将设置为黑色
    .mixin(#ddd)
}
.box2{
    //字体颜色#555亮度小于50%
    //box2背景颜色将设置为白色
    .mixin(#555)
}
```

编译css后结果如下

```css
.box1 {
  background: black;
  color: #ddd;
}
.box2 {
  background: white;
  color: #555;
}
```



# 递归

利用条件表达式递归调用混入

```less
.loop(@counter)when(@counter>0){
    .h@{counter}{
    //选择器变量要加{}
    //每次循环根据counter生成选择器和padding属性值
        padding:10px*@counter;
    }
    //@counter和-1之间应该有空格隔开，不然编译器会被@counter-1当做一个变量识别
    .loop(@counter - 1); //递归调用loop，每次调用counter-1，知道不满足条件counter>0
}
    
.box{
    .loop(3);
    //counter初值为3，数字类型
}
```

编译css后如下

```css
.box .h1{
	padding: 30px;
}
.box .h2{
	padding: 20px;
}
.box .h3{
	padding: 10px;
}
```



# 合并属性

存在同名属性时，具有合并标识的属性合并将合并在一起

```
合并标识
+:以逗号合并属性值
+_:以空格合并属性值

合并是两个同名属性为一组来进行合并的
如果两个同名属性的合并标识不一，以后出现的属性的标识为准
```

```less
.mixin(){
	box-shadow+:inset 0 0 10px #555;
}
.box{
    .mixin();
	box-shadow+:0 0 20px black;
}
```

编译结果，混入和box的属性合并了并且以逗号隔开

```css
.box {
  box-shadow: inset 0 0 10px #555,0 0 20px black;
}
```

****

```less
.mixin(){
    background+_:#f60;
    background+_:url('./a.jpg');
}
.box{
    .mixin();
}
```

编译结果，混入内有两个属性，他们合并了并且以空格隔开

```css
.box {
  background: #f60 url('./a.jpg');
}
```



# 函数库

less函数大全：https://blog.csdn.net/weixin_44198965/article/details/90075475

## 常用函数

light(@c) 				   输出这个颜色的亮度
iscolor(@c)				判断参数是否是颜色
isnumber(@n)		  判断参数是否是数字

isstring(@s)					判断参数是否为字符串

ispixel(@p)				判断参数是否是像素
ispercentage(@pe)		判断参数是否是百分比
isem(@e)				   判断参数是否是em

## length()

**length()**
功能：返回集合中值的个数

```css
n:length(1px solid #0080ff);
编译后：n:3
```

## unit()

less中unit()函数用于转换单位

```
unit(变量,指定单位)
将变量的单位重置为指定单位，没有指定单位将移除本身的单位
```

```less
@num: 10px;
 
div {
  //将变量@num的单位从px重置为rem
  height: unit(@num, rem);
}
```

输出css结果如下

```css
div {
  height: 10rem;
}
```