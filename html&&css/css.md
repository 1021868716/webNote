# css



# 浏览器与内核

Wekbit是一个开源的Web浏览器引擎，也就是浏览器的内核。Apple的Safari, Google的Chrome, Nokia S60平台的默认浏览器，Apple手机的默认浏览器，Android手机的默认浏览器均采用的Webkit作为器浏览器内核。Webkit的采用程度由 此可见一斑，理所当然的成为了当今主流的三大浏览器内核之一。另外两个分别是Gecko和Trident，大名鼎鼎的Firefox便是使用的Gecko 内核，而微软的IE系列则使用的是Trident内核。

三大内核

- Wekbit   ------》绝大部分浏览器

- Gecko

- Trident    -----》ie

  

## 浏览器前缀

常用的解决H5和C3 的兼容解决文件

| 浏览器前缀 | 浏览器                                 |
| ---------- | -------------------------------------- |
| -webkit-   | Google Chrome, Safari, Android Browser |
| -moz-      | Firefox                                |
| -o-        | Opera                                  |
| -ms-       | Internet Explorer, Edge                |
| -khtml-    | Konqueror                              |

```css
.div{
  position:relative;
  -moz-background-size:50%;
  -ms-content-zoom-limit-max:50%;
  -o-box-shadow:5px10px20px#f0f;
}
```





# 层叠样式表CSS

行内样式

在标签内添加style="属性1:值；属性2:值；..."



内部样式

在head标签内添加

```html
<style>
    对象{
        属性:值；
    }
    ...
</style>
```



外部样式

将所有样式放在一个以css为扩展名的外部样式表文件中。

```html
<head>
  <link href="CSS文件的路径"  rel="stylesheet" type="text/css"/>
</head>
```

在html中添加link标签关联css文件，link是一个单标签，须注明三个属性

```
href：定义所链接外部样式表文件的URL，可以是相对路径，也可以是绝对路径。
type：定义所链接文档的类型，在这里需要指定为“text/CSS”，表示链接的外部文件为CSS样式表。
rel：定义当前文档与被链接文档之间的关系，在这里需要指定为“stylesheet”，表示被链接的文档是一个样式表文件。
```





# 引入CSS

```html
<link href="img/divcss5.css" rel="stylesheet"/>
```

```css
@import url("img/divcss5.css")
```

引入css外部文件的两种方法为在html页面通过link，src属性（注意空链接时的陷阱！），再者是通过CSS文件本身通过@import url()引入，虽然这两种方式都可以加载进来CSS文件，可是link和@import也存在着细微的差别。 隶属上的差别 link属于XHTML标签，而@import完全是CSS提供的一种方式。link标签除了可以加载CSS外，还可以做很多其它的事情，比如定义RSS，定义rel连接属性等，@import就只能加载CSS了。此处注意浏览器的link src为空时候时会导致页面加载次数增多。



# 层叠性

层叠性是浏览器处理样式冲突的能力
1.权重相同时样式冲突采取就近原则，权重高则应用权重高的样式
2.样式不冲突采取叠加原则



# 继承性

子标签会继承父标签的能复用的样式，想设置一个可继承的属性，将其指定给父标签即可
一般来说跟文本相关的样式是可复用样式，宽，高等样式不可复用
行高，文字颜色，字体等都会继承



# 权重

通配符选择器或者继承的权重为：0，0，0，0
每个元素的权重是： 0，0，0，1
类和伪类的权重是：  0，0，1，0
id的权重是：0，1，0，0
行内样式表的权重：  1，0，0，0

!important的权重无限大，例如`color：red!imortant`

权重是可以叠加的，选择器的组成会叠加权重，每位权重单独相加，而且权重不会进位





# CSSReset

重置css默认样式





# em/rem

在css中单位长度用的最多的是px、em、rem，这三个的区别是：

1. px是固定的像素，一旦设置了就无法因为适应页面大小而改变。

2. em和rem相对于px更具有灵活性，他们是相对长度单位，意思是长度不是定死了的，更适用于响应式布局。

对于em和rem的区别一句话概括：**em相对于父元素的font-size，rem相对于根元素（html）的font-size。**



## em

- **子元素字体大小的em是相对于父元素字体大小**

- 元素的width/height/padding/margin用em的话是相对于该元素的font-size

  ```css
  div {
    font-size: 40px;
    width: 10em; /* 400px */
    height: 10em;
    border: solid 1px black;
  }
  p {
    font-size: 0.5em; /* 20px */ 
    width: 10em; /* 200px */
    height: 10em;
    border: solid 1px red;
  }
  span {
    font-size: 0.5em;  
    width: 10em;
    height: 10em;
    border: solid 1px blue;
    display: block;
  }
  cs
  ```



## rem

rem是全部的长度都相对于根元素，根元素是谁？`<html>`元素。通常做法是给html元素设置一个字体大小，然后其他元素的长度单位就为rem。

```css
html {
    font-size: 10px;
    }
div {
    font-size: 4rem; /* 40px */
    width: 30rem;  /* 300px */
    height: 30rem;
    border: solid 1px black;
}
p {
    font-size: 2rem; /* 20px */
    width: 15rem;
    height: 15rem;
    border: solid 1px red;
}
span {
    font-size: 1.5rem;
    width: 10rem;
    height: 10rem;
    border: solid 1px blue;
    display: block;
} 
```



# css画三角形

利用border，边框交界处是斜切的，每个边框各占一半，可以利用这特性来画三角形。

```scss
div {      
  width: 0;      
  height: 0;      
  border: 40px solid;      
  border-color: transparent transparent red transparent; 
  //上右下左
}
```



# 产生不占空间的边框

box-shadow阴影模拟边框

outline

ie盒模型



# 盒子模型

总宽度 = margin-left + border-left + padding-left + width + padding-right + border-right + margin-right



IE盒模型总宽度 = margin-left + width + margin-right

`margin(外边距)` - 清除边框外的区域，外边距是透明的。
`border(边框)` - 围绕在内边距和内容外的边框。
`padding(内边距)` - 清除内容周围的区域，内边距是透明的。
`content(内容)` - 盒子的内容，显示文本和图像



标准盒模型定义时的width、height所定义的简单来说就是定义了content部分的宽、高；当定义了你的盒子宽、高后你所定义的padding、border、margin部分的宽高则会累加在content的宽高上，最终显示出上图所示的整个盒子元素的总体宽高是上述四个属性的和

**标准盒模型，我们定义的width，height就是内容的宽高，设置padding，border会在外面叠加把盒子撑大**



IE盒模型，也称怪异盒模型，它定义时的盒子的width、height与标准盒模型所不同的是它定义的是border框起来的部分的宽、高，当你定义好盒模型的宽高属性后，再定义padding属性时会向内挤压content部分，倒置最终content这一部分内容的显示可能无法达到你预期的效果，而最终显示出整个盒子元素的总体的宽是width + margin（left、right），高则是height + margin（top、bottom）
**IE盒模型，我们定义的width，height就是内容+padding+border的宽高，我们设置padding和border会向里面挤压，盒子不会被border和padding撑大。**



## 盒模型的切换

切换盒模型，即定义盒模型的相关属性box-sizing

box-sizing：content-box； // 标准盒模型

box-sizing：border-box； // IE盒模型





# 布局

## 静态布局

最原始的布局方案，内容居中,使用固定的宽(高)度,超出部分用滚动条查阅。

居中布局，所有样式使用绝对宽度/高度(px)，设计一个Layout，在屏幕宽高有调整时，使用横向和竖向的滚动条来查阅被遮掩部分，这样在移动端需要单独设计一个布局，并使用m等域名导流



## table布局

很早以前使用table来布局



## 流式布局

也叫百分比布局

元素的宽高用百分比做单位，元素宽高按屏幕分辨率调整，布局不发生变化

缺点屏幕尺度跨度过大的情况下，页面不能正常显示。

网页中主要的划分区域的尺寸使用百分数，并使用max-width/min-width属性等限制最大宽高防止严重拉伸，并且文字大小等往往是用px定义的，所以不会跟着拉伸造成不匹配



## 浮动+margin布局

利用float来布局，但是浮动会有父元素高度塌陷的问题



## flex布局

flex布局

弹性布局是CSS3引入的强大的布局方式，简单、方便、快速

缺点CSS3新特性,浏览器兼容性不好。而且手机浏览器对flex的支持也不是很理想。



## 响应式布局(Responsive layout)

采用自适应布局和流式布局的综合方式(流式布局+弹性布局，再搭配媒体查询技术使用。)，为不同屏幕分辨率范围创建**流式布局**

利用**媒体查询**(@media )可以检测到屏幕的尺寸(主要检测宽度)，并设置不同的CSS样式，就可以实现响应式的布局。

移动端可以将某些元素隐藏或者点击弹出。



主要属性有：rem/viewport/media query(媒体查询)

- viewport

```html
<meta name="viewport" content="width=device-width,initial-scale=1.0,user-scalable=no">
```

width=device-width表示根据屏幕尺寸进行自定宽度

- rem

- media query(媒体查询)

```css
<!--这段样式只在屏幕最大宽度为640px的设备上生效-->
@media (max-width: 640px) {

}
```

缺点：

1）媒体查询是有限的，也就是可以枚举出来的，只能适应主流的宽高。

2）要匹配足够多的屏幕大小，工作量不小，设计也需要多个版本。



## Grid网格布局

`display: grid`

它将网页划分成一个个网格，可以任意组合不同的网格，做出各种各样的布局。





## 具体布局方案

- 单列水平居中布局

- 一列定宽一列自适应布局

```
1) 父盒子设置 display: flex;
定宽子列直接设置宽度
自适应子列设置 flex: 1

2) 利用定位拉伸
给父盒子设置position:relative
定宽子列设置定宽
自适应子列设置绝对定位，利用定位拉伸来自适应
例如定宽子列定宽200
自适应子列定位 left:200 right:0  这样这个自适应子列就被拉伸了

3) 固定定位+padding
父盒子设置高度
定宽子列设置定宽定高，然后固定定位left:0
自适应子列不设置定宽，设置padding-left为定宽，right完全填充父盒子，因为padding会留下定宽子列的空间，让right不会被覆盖
```



- 两列定宽一列自适应布局

```
父盒子设置 display: flex;
左右定宽子列直接设置宽度
中间自适应子列设置 flex: 1
```



- 两侧定宽中间自适应三列布局：圣杯布局和双飞翼布局

  **圣杯布局**

html布局一目了然

但css需要使用相对定位

```html
<div id="header"></div>
<div id="wrapper">
  <div id="center" class="one"></div>
  <div id="left" class="two"></div>
  <div id="right" class="three"></div>
</div>
<div id="footer"></div>
```

```css
.wrapper{
    padding: 0 100px
    /*为two盒子留出空间*/
}
.one{
    width: 100px;
    height:100px;
    background-color: lightblue;
    float:left;
}
.two{
    width: 100px;
    height: 100px;
    background: lightgreen;
    float:left;
    margin-left: -200px;/*左移200px*/
}
.three{
    width: 100px;
    height: 100px;
    background: yellow;
    float:left;
}
```



**双飞翼布局**

html布局一目了然

但css无需使用定位

```
<div id="header"></div>
<div id="wrapper">
  <div id="center" class="one"></div>
</div>
  <div id="left" class="two"></div>
  <div id="right" class="three"></div>
<div id="footer"></div>
```



# 垂直居中

1) 使用定位

这个方法使用绝对定位的 `div`，把它的 `top` 设置为 `50％`， `margin-top` 设置为负 `content` 高度的一半。

```
<div class="content"> Content goes here</div>
```

```css
#content {
	position: absolute;
	top: 50%;
	height: 240px;
	margin-top: -120px; /* negative half of the height */
}
```



2)使用margin

margin: auto只能水平居中还需要配合定位实现水平垂直

```html
<div id="content"> Content here</div>
```

```css
#content {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  margin: auto;
  height: 500px; /*其余就是margin：auto占据了*/
  width: 500px; /*剩下就是margin：auto占据了*/
  background-color: lightblue;
}
```



3）flex居中对齐

```css
{
	display: flex;
	justify-content: center;
}
```



4）文本居中

这个方法只能将单行文本置中。只需要简单地把文本 `line-height` 设置为等于对象的 `height` 值就可以使文本在那个元素中居中了



5)  translateY



6）display：table-cell





# 水平居中

1）`margin: 0 auto;`



2）绝对定位导致`margin: 0 auto;`失效

绝对定位下margin:auto会失效，这时设置`left:50%`，然后设置margin-left的值为层的宽度的一半
如果是要垂直居中，也是一样的方法，即设置`top:50%`，然后设置margin-top的值为层的高度的一半

```css
{
	position:absolute;
	left:50%;
	top:50%;
	margin-top:-13px;
	margin-left:-58px;
	width:116px;
	height:25px;
	z-index:999999;
}
```



3）translateX



4）flex

```css
{
	display: flex;
	flex-direction: column;
	justify-content: center;
}
```





# BFC

块级格式化上下文，他决定了与其他元素的关系。

BFC形成一个完全封闭不会影响外部的空间，触发方式为脱标

- float不为none

- position不为relative（绝对定位）和static

- overflow为auto  、scroll  和hidden

- display为`table-cell`，`inline-block`，`flex`等

  

解决的问题

1）清除浮动

​	给父元素添加overflow：hidden，display：table-cell，position：fixed，让父元素也添加float属性等等

​	触发了BFC的父盒子为了不影响页面中其他元素，计算高度时会把其他元素也计算进来



2）防止浮动后其他文字环绕



3）解决外边距垂直方向重合

兄弟元素如果垂直方向上，下外边距和上外边距接触以后会合并，给其中一个兄弟元素设置一个div包裹并给这个div触发BFC能解决这个问题







# 选择器

将CSS样式应用于特定的html需要用选择器先找到该元素。
样式显示效果与html元素中类名先后顺序没有关系，受css样式书写上下顺序影响。
- TagName

标签选择器，把某一类标签全部选出来
```
标签名{
    属性:值;
}
```
- .class

类名选择器，css中.name{属性:值;...}调用，在html元素中class="name"命名



- 多类型选择器

在一个div里只能有一个class，但可以一个clss命名多个类名，中间用空格隔开。



- #class

id选择器，大多数html元素都有id属性，可以用该id属性定位该元素，但id具有唯一性，但类选择器的类名不存在唯一性。
```
#id{
    属性:值;
}
```



- *

通配符选择器*号表示，表示所有html元素全部选中。
```
*{
    属性:值;
}
```



- .class1 .class2 

  .class1>.class2

  子代后代选择器

后代选择器每个选择器用空格隔开  会选取所有的后代选择元素
子代选择器每个选择器用>号隔开  >只选取子代选择元素



- .class1.class2

  .class1.class2的意思是且，代表两个类名必须都具备



- 交集并集选择器

交集选择器：选择器之间用点隔开，a选择器.class选择器{ }，就能选中a和class选择器之交集的元素
并集选择器：多个选择器用逗号隔开并集选择器可以集体声明为统一样式



- 属性选择器

可以选择满足某些特定条件的元素

```html
元素[条件]{
}
```

如 div[class]可以选出所有带有class属性的div，div[class=demo]选择calss为demo的div，元素可以省略，则不限定元素得进行选择
选择公式：
`=`  选择为该条件的属性，`div[class=demo]`选择calss为demo的div
`^=` 选择以该条件开头的属性，`div[class^=demo]`选择class以demo开头的div
`$=` 选择以该条件结尾的属性，`div[class$=demo]`选择class以demo结尾的div
`*=` 选择具有该条件的属性，`div[class*=demo]`class名中含有demo的div（demo可以在名字开头中间结尾都可以）





# 伪类/伪元素

- 伪类选择器

伪类选择器用于改变元素本身的状态

`元素:link {}`    未访问过的链接状态
`元素:visited {}`  已访问过的链接
`元素:hover {}` 鼠标经过的状态
`元素:active {}`  鼠标按下时的状态
lvha顺序不能调换，但可以省略



- 伪元素选择器

伪元素选择器用于创建一个元素放在页面上

`::first-letter`
文本的第一个单词或字；



`::first-line`
文本第一行；



`::selection`
可改变选中文本的样式；



`::before`/`::after`

 `::before`和`::after`是在元素盒子里面插入一个盒子,可以用来减少代码中的元素盒子，优化代码结构。

```css
 元素::before{
   content:" "<!--必须拥有content属性，可以为空可以添加文字，该文字会添加在元素内容之前-->
 }
 /*在元素内部插入一个行内盒子在元素内容之前，before创造的盒子不算一个元素但拥有元素特性，所以叫伪元素，注意该盒子初始拥有的是行内元素特性。*/


元素::after{
  content:" "<!--必须拥有content属性，可以为空也可以添加文字，该文字会添加在元素内容之前-->
}   
/*在元素内部插入一个行内盒子在元素内容之后*/
```





- 结构（位置）伪类选择器

可以选择固定位置的子元素。

```
- 子元素:first-child{}
选取属于其父元素的首个子元素的指定选择器
- 子元素:last-child{}
选取属于其父元素的最后一个子元素的指定选择器
- 子元素:nth-child(x){}
匹配属于其父元素的第X个子元素，不论元素的类型
- 子元素:nth-last-child(x){}
选择器匹配属于父元素的第X个子元素的每个元素，不论元素的类型，从最后一个子元素开始计数。

  括号内的x可以是数字、关键词或公式，如
  even 偶数
  odd  奇数
  xn   x为数字，表示选取x倍数的子元素
  xn+1 x为数字，表示选取x倍数+1的子元素
  ...
```







# width/height

**width和height单位可以是像素，也可以是%，但是以百分号为盒子的宽高单位时，其父盒子一定要指定宽高，否则属性不生效**

注意：

1、宽度属性width和高度属性height仅适用于块级元素，对行内元素无效（ img 标签和 input除外）。

2、计算盒子模型的总高度时，还应考虑上下两个盒子垂直外边距合并的情况。

3、**如果一个盒子则会和父亲一样宽 占满父亲的宽度， 如果此盒子没有给定宽度 则padding 不会影响本盒子大小**。

4、需要考虑ie盒子模型和标准盒子模型



考虑盒子模型的稳定性，按照 优先使用  宽度 （width）  其次 使用内边距（padding）    再次  外边距（margin）。 



# resize 调整元素大小

是否可以用户手动调整元素大小，规定可以由用户调整 div 元素的大小

属性值
none	用户无法调整元素的尺寸。
both	用户可调整元素的高度和宽度。
horizontal	用户可调整元素的宽度。
vertical	用户可调整元素的高度。



# list-style 列表样式

```
list-style：none;
```

在li标签里添加该属性可以取消列表自带的小点





# background

- background-color  盒子背景颜色

- background-image  url 盒子背景图片

```
background-image:url(url地址)
```




- background-repeat 背景图是否平铺

```
repeat:背景图在纵向和横向上平铺
no-repeat:背景图不平铺
repeat-x:x轴向平铺
repeat-y:y轴向平铺
```




- background-position 背景图在盒子中的位置

1.x,y为方位名词：top bottom上下 right left左右 center居中
可以写作background-position:top  center；表示水平居中，垂直向上

2.x,y为像素单位:  xpx  ypx 分别控制图像跟盒子左和上的距离
两者可以混合使用或者省略，但只要有数值（px）则书写顺序一定是先x轴向再y轴向。

```
background-position:x  y；
```




- background-attachment

```
scroll：背景图随滚轮滑动
fixed：背景图不随滚轮滑动，被固定住
```




- 背景连写，background

```
background：color url repeat attachment position
顺序没有具体要求，但建议按照上述顺序写
```



- background-size

  background-size 属性规定背景图像的尺寸。

```
background-size: length|percentage|cover|contain;
```

| 值         | 描述                                                         |
| :--------- | :----------------------------------------------------------- |
| *length*   | 设置背景图像的高度和宽度。第一个值设置宽度，第二个值设置高度。如果只设置一个值，则第二个值会被设置为 "auto"。 |
| percentage | 以父元素的百分比来设置背景图像的宽度和高度。第一个值设置宽度，第二个值设置高度。如果只设置一个值，则第二个值会被设置为 "auto"。 |
| cover      | 把背景图像扩展至足够大，以使背景图像完全覆盖背景区域。背景图像的某些部分也许无法显示在背景定位区域中。 |
| contain    | 把图像图像扩展至最大尺寸，以使其宽度和高度完全适应内容区域。 |



## 背景透明

```
rgba(r, g, b, alpha)
```

alpha在0~1之间，rgba归属在color属性下
一般r=g=b=0，然后再取a的值，意思是黑盒子取透明度



## 背景渐变

背景渐变兼容性差
```css
background:-webkit-linear-gradient(渐变的起始位置， 起始颜色， 结束颜色)；
background:-webkit-linear-gradient(left, green, red)；
或者去掉前缀，位置可以改为移动方向
background:linear-gradient(45deg, green, red)；
```
第二种写法

```css
background:linear-gradient(移动方向， 颜色 位置， 颜色 位置....)；
background:linear-gradient(135deg, red 0, green 50%, blue 100%)；
```



## 背景缩放

**通过background-size设置背景图片的尺寸,常用于移动端**
a) 可以设置两个长度单位(px)或百分比分别代表宽高（设置百分比时，参照盒子的宽高）

b) 设置为cover时，会自动调整缩放比例，保证图片始终填充满背景区域，如有溢出部分则会被隐藏。我们平时用的cover 最多

c) 设置为contain会自动调整缩放比例，保证图片始终完整显示在背景区域



## 多背景(CSS3)

以**逗号分隔**可以设置多背景，可用于自适应布局
- 一个元素可以设置多重背景图像。 
- 每组属性间使用逗号分隔。 
- 如果设置的多重背景图之间存在着交集（即存在着重叠关系），前面的背景图会覆盖在后面的背景图之上。
```
    background:color url repeat attachment position,
               color url repeat attachment position,
               color url repeat attachment position,
               ...
```



## 雪碧图

为了防止因为图像太多而重复频繁的访问服务器下载图像。为了有效地减少服务器接受和发送请求的次数，提高页面的加载速度，出现了CSS精灵技术。

```
    CSS 精灵其实是将网页中的一些背景图像整合到一张大图中（精灵图），
    然后精确定位到精灵图中的某个小图，
    使用background属性（背景简写）进行背景定位，
    其中最关键的是使用background-position属性精确地定位。
    其中background-position的x，y坐标属性一般为负值（精灵图中的相应x，y坐标加负号）或0。
```



## 滑动门原理

为了使各种特殊形状的背景能够自适应元素中文本内容的多少，出现了CSS滑动门技术。它从新的角度构建页面，使各种特殊形状的背景能够自由拉伸滑动，以适应元素内部的文本内容，可用性更强。 最常见于各种导航栏的滑动门。

```
滑动门结构：
    <li>
        <a href="#">
            <span>导航栏内容</span>
        </a>
    </li>
    1. a 设置背景左侧，padding-left撑开合适宽度。    
    2. span 设置背景右侧， padding-right撑开合适宽度 剩下由文字继续撑开宽度。
    3. 之所以a包含span就是因为 整个导航都是可以点击的。
    4.a和span需转换为行内块元素，不然无法设置高度
```





# 文本属性

- color:颜色

```
1.预定义颜色：red，green...<br/>
2.十六进制：#FF0000<br/>
3.RGB代码：rgb(x%,y%,z%)或rgb(a,b,c)，abc在0~255之间
```



- line-height 行高

行高决定文字上下的多余高度



- text-align 水平对齐方式

left，right，center 
**让文字水平对齐，而不是盒子**



- text-indent:首行缩进

单位em(一个字的距离)，首行缩进相应字符距离



- text-decoration: 文字装饰属性

  a标签文字装饰默认为下划线，设置为none可以取消

```
none 无装饰
blink 闪烁
underline 下划线
line-through 管穿线
overline 上划线
```




- text-shadow:文字阴影

```css
text-shadow:水平位置 垂直位置 模糊距离 阴影颜色;
前两项是必须写的。  后两项可以选写。
```



# resize: none防止拖拽文本域

resize：none  

这个属性可以防止火狐，谷歌等浏览器随意的拖动文本域。

```
该文本域右下角不可以拖拽： 
<textarea  style="resize: none;"></textarea>
```



# font 字体

- @font-face  引入外部字体文件

```
指定名为"myFirstFont"的字体，并指定在哪里可以找到它的URL：

@font-face
{
font-family: myFirstFont;
src: url('Sansation_Light.ttf'),
     url('Sansation_Light.eot'); /* IE9 */
}
```



- font-size  字号大小

```
默认16px；
px：像素
em：相对于当前对象内文本的字体大小
绝对长度单位：说明
```



- font-family  字体

字体大小14px以上,尽量使用偶数字号
**中文字体或字体名包含特殊符号,字体名需要加引号**
需要设置英文字体时，英文字体名必须在中文之前
各种字体名之间用英文逗号隔开
尽量使用系统默认字体
字体显示顺序为字体名顺序，没有浏览器该字体会跳过
可以用字体的Unicode编码代替字体名

**单位**

```
"宋体"
"微软雅黑"或"microsoft yahei"
...
```



- font-weight  字体加粗

可以使用strong,b标签和css来实现字体加粗，但css没有语义

```
font-weight后单位有
normal（正常宋体）
bold（粗体）
bolder（特粗体）
lighter（细体）
数字100~900(没有单位)
```

其中数字400=normal,700=bold,但数字表示更明确。



- font-style  字体斜体

**单位：**

```
normal：正常字体
italic：斜体，对于没有斜体变量的特殊字体，将应用oblique
oblique：倾斜的字体
```



- 字体连写

```
选择器{font:font-style font-weight font-size/line-height font-family}

font-size/line-height：可以写14px/10px  表示14px字体，10px行高
style和weight可以省略(font-size和font-family必须保留)，但不能打乱顺序，如
h1{
    font: normal normal  40px  "宋体";
}
```



- 行高：line-height

行高是指两行文字基线与基线直接的距离

```
    一个盒子由上边距下边距和内容高度组成
    当行高=盒子高度时，可以实现文字垂直居中对齐
    行高>盒子高度文字会偏下
    行高<盒子高度文字会偏上
    行高如果不带单位为数字，表示行高等于该数字*字体大小
```



- text-decoration  下划线

a标签自带下划线，把这个属性设置为none即可取消





# 溢出文字隐藏

white-space:是否在一行显示所有文本

```
white-space: 设置或检索对象内文本显示方式。通常我们使用于强制一行显示内容，防止跳行
normal : 　默认处理方式，超出范围后跳行显示
nowrap : 　强制在同一行内显示所有文本，直到文本结束或者遭遇br标签对象才换行。
```



text-overflow:文字溢出添加省略标记

```
text-overflow : clip | ellipsis
设置或检索是否使用一个省略标记（...）标示对象内文本的溢出
clip : 　不显示省略标记（...），而是简单的裁切,超出范围的不显示 
ellipsis : 　当对象内文本溢出时显示省略标记（...）
注意一定要首先强制一行内显示，再次和overflow属性  搭配使用
```



超出显示范围文本替换为省略号的代码

```css
p:{
  overflow: hidden;  <!--超出显示部分进行隐藏-->
  white-space: nowrap;  <!--强制在一行显示文本，防止跳行-->
  text-overflow: ellipsis; <!--溢出部分用省略标记进行代替--> 
}
```





# 文本折行

overflow-wrap 是否保留单词

word-break 换行时是否打断单词

white-space 空白处是否断行



# vertical-align 对齐方式

以通过vertical-align 控制行内块元素图片和文字的垂直关系。 默认的图片会和文字基线对齐。

~~~
vertical-align:baseline |top |middle |bottom 
baseline: 图片和文字基线对齐（默认，会留下白色缝隙）
top:      图片和文字顶部对齐
middle:   图片和文字垂直居中对齐
bottom:   垂直居下对齐
~~~

vertical-align 不影响块级元素中的内容对齐，它只针对于 行内元素或者行内块元素，特别是行内块元素， **通常用来控制图片/表单与文字的对齐**。



img默认也是按照基线对齐，所以在图片底部会留下底线与基线的3px缝隙，去除方法如下
       1. 给img的 vertical-align:middle/top/bottom等等。  让图片不要和基线对齐。
       2. 给img添加display：block;转换为块级元素。





# display 显示模式

**不渲染在页面上  none**

元素不渲染在页面上，不出现在dom树上。display:none会引起回流和重绘。

display:none不会被子元素继承，但是父元素都不在了，子元素自然也就不会显示了。



**块级元素 block**

独自占一整行或多整行，可以设置宽高，对齐等属性，常用于网页布局，如div，h，p等，div适合网页布局，所以叫css+div布局,文字类块级元素不要放其他块级元素。块级元素如果没有设置宽度会通栏显示。

块级元素有：div，p，h1-h6，ul，ol，dl，li，header，footer，aside，section，article，form，table



**行内元素  inline**

不占独立区域，仅靠自身字体大小和图像尺寸来支撑结构，一般不可以设置宽高，对齐等属性，常用于控制文本样式。

1.和相邻元素在同一行
2.宽高无效，但可以用padding和margin设置水平方向，垂直无效
3.行内元素只能容纳文本和行内元素，a除外，a里面不能再放链接，但可以放块级元素

行内元素有：span，img，button，input，b，q，i，a，em，label



**行内块元素 inline-block**

对内是block可以有宽高，对外是inline，可以于其他盒子同处一行

```
<img/>   <input/>   <td>等
一行可以放多个，也能设置宽高等，但是元素与元素直接会有缝隙，不设置宽高不会通栏显示，会直接隐藏。
```

inline-block的元素会产生一些空白间隙，原因是字符间距，解决方案是font-size: 0



显示模式转换

```
display: none
display: inline 将块元素转换为行元素
display: block 将行元素转换为块元素
display: inline-block 将元素转换为行内块元素
```







# visibility 可见性

设置hidden对象隐藏,隐藏之后，继续保留原有dom树位置，只是页面显示上看不见了。

元素上绑定的事件也无法触发，与display的区别在于隐藏之后是否占据dom树位置，visibility:hidden会引起重绘，但不会引起回流。

```
visible: hidden;
```

visibility:hidden 会被子元素继承，可以通过设置子元素visibility:visible 使子元素显示出来





# opacity 透明度

该属性值为0到1，对应不同的透明度

opacity: 0，只会引起页面重绘，元素依然可以交互可以触发事件

opacity: 0 ，也会被子元素继承，但是不能通过设置子元素opacity: 0使其重新显示





# overflow

超出容器部分的显示

visible      超出部分撑出父容器显示  

hidden     超出部分直接隐藏

scroll        超出部分生成滚动条允许滚动

auto		 超出部分生成滚动条允许滚动，但滚动条根据高度是否超出父容器决定显示与否





# border

边框交界处是斜切的，每个边框各占一半，可以利用这特性来画三角形。

- border  盒子边框

- border-width 边框宽度

- border-style  边框样式

```
none   无边框
solid  实线边框
dashed 虚线边框
dotted 点线边框
```



- boder-color:边框颜色

- 边框连写

    border:width style color
    顺序没有规定



- 边框拆分

```
border-top/bottom/left/right:可以单独设置边框四个边的属性值
```



- border-image 边框图片

  ```
  border-image: url('') 图片大小 repeat;
  ```

  repeat:背景图是否平铺

  

- 表格边框

合并边框border-collapse:collapse;

```
table{ border-collapse:collapse; }  
collapse 单词是合并的意思，border-collapse:collapse; 表示相邻边框合并在一起。
```



## border-radius 圆角边框

- border-radius:圆角边框

radius 半径（距离）

```
可以写像素或者百分比，正方形50%为圆形
语法格式：border-radius: 50%;
```

可以拆分为四个角分别设置圆角

```
还是顺时针的方式排序
一个值设置的是四个角的圆角
两个值左上右下一组，左上和右下一组（对角线形式）
三个值左上，右上和左下，右下
四个值分别是左上右上右下左下
```





# padding/margin

## 内边距（padding）

padding属性用于设置内边距。  是指 边框与内容之间的距离。
```
padding-top/right/bottom/left:拆分设置四边内边距
内边距简写：
padding：a b c d;
只写a代表上下左右
只写a，b分别代表上下边距与左右边距
写三个值a，b，c分别代表上边距，左右边距与下边距
abcd写齐则代表上右下左边距（顺时针）
```

## 内边距和边框会撑大盒子
```
盒子高=border-top + padding-top +height + padding-bottom + border-bottom
盒子宽=border-left + padding-left + weight + border-right + padding-right
```



## 外边距（margin）

不同浏览器标签默认有一个外边距，需要用通配符选择器或者并集选择器手动清除，margin属性用于设置外边距。 

 设置外边距会在元素之间创建“空白”， 这段空白通常不能放置其他内容。margin可以为负表示移动。

```
margin-top/right/bottom/left:拆分设置四边外边距
margin简写顺序跟内边距相同
```


### 外边距实现盒子居中

可以让一个盒子实现水平居中，需要满足一下两个条件：

1. 必须是块级元素。    

2. 盒子必须指定了宽度（width）

然后就给**左右的外边距都设置为auto**，就可使块级元素水平居中。

实际工作中常用这种方式进行网页布局，示例代码如下：

~~~css
.header{ width:960px; margin:0 auto;}
~~~

文字盒子居中图片和背景区别

1.  文字水平居中是  text-align: center
2.  盒子水平居中  左右margin 改为 auto 

~~~css
text-align: center; /*  文字居中水平 */
margin: 10px auto;  /* 盒子水平居中  左右margin 改为 auto 就阔以了 */
~~~

3. 插入图片 我们用的最多 比如产品展示类
4. 背景图片我们一般用于小图标背景 或者 超大背景图片

~~~css
section img {  
		width: 200px;/* 插入图片更改大小 width 和 height */
		height: 210px;
		margin-top: 30px;  /* 插入图片更改位置 可以用margin 或padding  盒模型 */
		margin-left: 50px; /* 插入当图片也是一个盒子 */
	}

aside {
		width: 400px;
		height: 400px;
		border: 1px solid purple;
		background: #fff url(images/sun.jpg) no-repeat;
	
		background-size: 200px 210px; /*  背景图片更改大小只能用 background-size */
		background-position: 30px 50px; /* 背景图片更该位置 我用 background-position */
	}
~~~



## 外边距合并
使用margin定义块元素的垂直外边距时，可能会出现外边距的合并。



### 相邻块元素垂直外边距的合并

**当上下相邻的两个块元素相遇时**，如果上面的元素有下外边距margin-bottom，下面的元素有上外边距margin-top，**则他们之间的垂直间距不是margin-bottom与margin-top之和，而是两者中的较大者**。这种现象被称为相邻块元素垂直外边距的合并（也称外边距塌陷）。
```
解决方案：
1. 触发BFC，为其中一个元素添加父元素并触发父元素BFC
2.用padding替代margin
3.给父元素添加隐形边框border: 1px solid transparent
4.给父元素或者子元素添加浮动或者定位absolute
```





### 嵌套块元素垂直外边距的合并

对于两个嵌套关系的块元素，如果父元素没有上内边距及边框，**则父元素的上外边距会与子元素的上外边距发生合并，合并后的外边距为两者中的较大者**，即使父元素的上外边距为0，也会发生合并。
```
解决方案：
1. 可以为父元素定义1像素的上边框或上内边距。
2. 可以为父元素添加overflow:hidden。
```





# 阴影box-shadow

box-shadow:水平阴影 垂直阴影 模糊距离(虚实) 阴影尺寸（影子大小）阴影颜色 内/外阴影；

1. 前两个属性是必须写的。其余的都可以省略。
2. 外阴影 (outset) 不能写 ，省略最后一个值就是外阴影，外阴影设置为inset

h-shadow  水平阴影，单位px，必写
v-shadow  垂直阴影，单位px，必写
blur      模糊距离，单位px
spread    阴影的尺寸，单位px
color     阴影颜色
inset     内阴影写inset，外阴影省略
例如  box-shadow: 0 15px 30px  rgba(0, 0, 0, 0.4);







# 浮动float
css有三种定位机制：标准流，浮动和定位。

**浮动可以让多个块级元素在一行显示，便布局**，最重要的是要怎么排列，浮动元素不占据位置会把位置让给后面的标准流盒子。

标准流：块级元素一行显示一个，行级元素一行显示多个

所有元素经过**浮动**变为**行内块元素**，并且会形成BFC

```
选择器{float:属性值;}
left向左浮动
right向右浮动
none不浮动
```



**浮动后元素脱离文档流但是不脱离文本流，文本还是会被他排挤，不会被遮盖。所以可以用来做图文混排。**浮动元素脱离文档流，如果后续元素为浮动元素，则依此排列；如果使文本节点，则围绕浮动元素；否则，会被浮动元素覆盖，布局忽略浮动元素。



浮动盒子不占据位置，但只会影响它之后的标准流(写在它之前的标准流不会受影响)，会找离他最近的父级元素对齐，但不会超出内边距的距离（padding）也不会贴着边框（border）

一个父盒子里面的子盒子，如果其中一个子盒子浮动，那么其他子盒子都需要浮动才能在一行显示



## 对父元素的影响

高度塌陷：父盒子若没有设置高度且子盒子浮动时，此时父盒子高度会变为0，不会撑开父盒子。

解决办法清除浮动





# 清除浮动

**浮动元素无法撑起父盒子，让父盒子高度坍塌的问题**

- 触发BFC，给父元素添加float属性，让父元素一起浮动。或添加overflow: hidden等
- 给父元素添加固定宽度

- 给浮动元素之后加一个空元素，并设置css属性{ clear:both }清除浮动

- 为浮动的元素添加一个伪元素，类似于第三种办法

  ```
  ::after{
  	clear:both
  }
  ```

  



**父盒子若没有设置高度且子盒子浮动时，此时父盒子高度会变为0，不会撑开父盒子。清除浮动后父盒子会随着子盒子的高度自动撑开高度**，不是所有浮动都需要清除，没有影响布局不需要清除，清除浮动语法格式如下：

```
    选择器{clear:属性值}
    left  不允许左侧有浮动元素（清除左侧浮动影响）
    right 不允许右侧有浮动元素（清除右侧浮动影响）
    both  同时清除两侧浮动影响
```
额外标签法

```
    通过在最后一个浮动子元素的末尾添加一个空的元素标签清除浮动
    如<div clear="both"></div>
    优点：通俗易懂，书写方便
    缺点：会添加无意义的标签，结构化变差
```



给父盒子添加 overflow:hidden

触发BFC实现清除浮动效果





使用clear： both之后，会把浮动元素的边界拉下来到标准流，从而把对应标准流的位置撑开。

`::after{clear: both;}`伪元素清除浮动

```
    <!--正常浏览器清除浮动，可以放进css初始化中-->
    选择器::after{
        content:"";
        display: block;
        height: 0;
        clear: both;
        visibility: hidden;
    }
    建议选择器取名clearfix，再为需要清除浮动的元素添加clearfix多类名
    <!--这个方法是为ie6清除浮动，在任何元素前加*，其他浏览器都不会读取，ie6或者ie7才会读取-->
    .clearfix{
        *zoom: 1;
     }
```



before和after双伪元素清除浮动

```
.clear::before,
.clear::after{
    content:"";
    display: table;
}
.clear::after{
    clear: both;
}
.clearfix{
    *zoom: 1;
}
```





# 定位position
定位属性包括**定位模式**和**边偏移**两个部分，定位要求其父盒子有宽高，才能正确的定位

position：定位模式

```
position: static  静态定位，无定位，所有元素默认静态定位
position: relative  相对定位，相对于其文档流进行定位
position: absolute  绝对定位，相对于其上一个已经定位的父元素进行定位
position: fixed     固定定位，相对于浏览器窗口进行定位
```



边偏移

单位都是像素或%，表示元素距离该边的位置，上与下，左与右在固定宽高时不能同时写，不固定时会自动拉伸。
优先级先左后右先上后下，不受层叠性影响，所以不能依靠层叠性来层叠相反属性改变边偏移。

```
top:
bottom:
left:
right:
```

```css
书写格式
position: static/relative/absolute/fixed;
top/bottom/left/right: 属性值(px或%);
```



**static**   静态定位

 所有元素默认都是静态定位，静态定位下无法通过边偏移属性来改变元素的位置，唯一的作用是取消元素的定位。



**relative** 相对定位

每次以自己左上角为基准进行边偏移移动，但原来的位置继续占有



**absolute** 绝对定位

脱离文档流，不会对其他元素造成影响，边偏移以最近的relative/absolute为基准

绝对定位以当前屏幕显示范围为基准点对齐，绝对定位完全脱标，完全不占有位置，类似浮动
若父元素没有定位则绝对定位元素以页面为基准点对齐，可以超出父元素的范围（跟漂浮不同）
若父元素有定位，则根据最近的已经定位的父元素左上角进行定位



**fixed**  固定定位，脱离文档流，不会对其他元素造成影响，边偏移以浏览器页面为基准

固定定位的盒子会一直停留在屏幕固定位置，不会随滚动条而移动，固定定位脱标，不占有位置，与绝对定位不同，固定定位不管父盒子有没有定位依都以浏览器页面为基准进行定位。因为固定定位不占有位置，所以排版时应留出足够距离防止固定定位元素遮盖内容。



## 子绝父相

子级元素元素是绝对定位，父级元素一定要加定位，且用相对定位更合适，因为相对定位占有位置，不会漂浮，可以防止下面的元素占有其位置，适合布局。



## 定位的盒子居中对齐

加了定位和浮动的盒子 margin：0 auto;因为定位漂浮与其属性冲突而无法居中，可以用如下方法

```css
position: absolute；
left: 50%   <!--因为是左上角水平移动父盒子的一半，所以不会正好走到中间-->
margin-left: (-50%*width)px;  
<!---50%*width需计算出结果，曲线救国。让元素自己往左移动自身的50%即可实现水平居中对齐-->
<!--垂直居中对齐同理-->
```




## sticky

    position: sticky;
    top: 44px
    当达到设置的默认值高度之前position值默认为static，达到之后默认立刻变为fixed
    这是一个新的position属性
    推荐在移动端使用，ie不支持



## 模式转换

跟浮动一样，元素添加了绝对定位和固定定位后，**元素模式会默认转换为行内块模式，行内块元素宽高与内容有关，默认没有宽高，需要手动设置宽高，背景图片无法实现宽高**，插入图片可以。



## z-index叠放次序

定位比标准流默认高一级，会优先显示，但当对多个元素添加定位时可能发生重叠，此时显示优先级就由叠放次序决定
```
如果元素都有定位，后来者居上（与漂浮相反），但可以添加z-index属性（默认0，没有单位）控制，
属性值越高，显示优先级越高。该属性只有定位的盒子才有，属性值为数字，表示显示优先级。
z-index: 属性值；
```





# 鼠标样式cursor

 设置或检索在对象上移动的鼠标指针采用何种系统预定义的光标形状。 

```
cursor :  default  小白箭头 | pointer  小手  | move  移动  |  text  文本
```





# outline  轮廓

 是绘制于元素周围的一条线（一般用于表单），位于边框边缘的外围，点击表单后轮廓线亮起，可起到突出元素的作用。

~~~
 outline : outline-color ||outline-style || outline-width 
~~~

 一般来说轮廓线是去掉的

最直接的写法是 outline: 0;或者outline: none;











# 变形 transform

transform是CSS3中具有颠覆性的特征之一，**可以实现元素的位移、旋转、倾斜、缩放，甚至支持矩阵方式**，配合过渡和即将学习的动画知识，可以取代大量之前只能靠Flash才可以实现的效果。

```
使用语法 transform:translate scale rotate skew
transform连写按属性书写顺序变形
```

变形不是动画，但可以和动画配合



## 2D变形

```css
transform: translateX(100px) translateY(100px) rotate(45deg);
```



### 移动平移 translate(x, y)    

```
transform: translate(x,y)属性使元素在x轴向上平移x像素，y轴向上平移y像素，可以为负值
translateX(x)仅x方向移动
translateY(Y)仅y方向移动
```





### 缩放 scale(x, y) 

```
    transform:scale(X,Y)使元素x方向缩放x，y方向缩放y
    scaleX(x)元素仅x方向缩放
    scaleY(y)元素仅y方向缩放
    x,y取值为数字，默认为1，0.01~0.99缩小，大于则1放大，只写一个值默认x与y轴上等比例缩放
```



### 旋转 rotate(deg)

```
    transform:rotate(deg);
    单位为度数(deg)，如旋转45度则填入45deg，正值为顺时针，负值为逆时针；
```



### transform-origin:调整元素转换变形的原点

```css
    语法transform-origin:原点方位/原点坐标
    原点为四个顶点角则为原点方位方位，为内部某一点则为原点坐标
    例 div{transform-origin: left top;
           transform: rotate(45deg); 
		}  
 /* 改变元素原点到左上角，然后进行顺时旋转45度 */
 
     div{
           transform-origin: 10px 10px;
           transform: rotate(45deg);
		}  
/* 改变元素原点到x 为10  y 为10，然后进行顺时旋转45度 */
 
```



### 倾斜 skew(deg, deg)

```css
    transform:skew(x deg,y deg);
    可以使元素按一定的角度进行倾斜，可为负值，第二个参数默认为0，可以省略。
    x为水平方向倾斜xdeg，y为水平方向倾斜ydeg，单位为deg(度)
```





## 3D变形

 x左边是负的，右边是正的

y 上面是负的， 下面是正的

z 里面是负的， 外面是正的



### rotateX/Y/Z(deg) 

`transform: rotateX/Y/Z(deg) `

```
rotateX()
rotateY()
rotateZ()
```

 就是沿着 X/Y/Z 轴立体旋转.



### translate3d(x,y,z)

`transform: translate3d(x,y,z)`

```
translateX()
translateY()
translateZ()
```

其中，x和y可以是长度值，也可以是百分比，百分比是相对于其本身元素水平方向的宽度和垂直方向的高度和；但z轴只能设置长度值



### perspective

景深，单位px，越大越不立体

```
perspective: 200px;
```



### backface-visibility: hidden 

 属性定义当元素不面向屏幕时是否可见。不是正面对象屏幕，就隐藏



## 配合动画

```
#box:hover {
	transform: translateZ(-100px) rotateX(90deg) rotateY(90deg)
}
```







# 动画

## 补间动画 transition

过渡动画：是从一个状态通过计算机计算（不是我们手动添加）渐渐的过渡到另外一个状态

帧动画：通过一帧一帧的手动添加的画面按照固定顺序和速度播放。如电影胶片

~~~css
注意如果有伪类选择器等注意过渡必须写在本身上，而不是写在伪类选择器等其他上。
transition-property:要过渡的属性名称如width，height等，所有变化属性都应用则写all
                 
          -duration:花费时间，单位s或者ms
          
          -timing-function:运动曲线
          linear匀速/ease渐缓(默认)/ease-in/ease-out/ease-in-out 
          
          -dela:何时开始(延时),默认0;
~~~



过渡属性可以连写，后两个属性可以省略，多个属性过渡组连写用逗号隔开

```
transition:  property duration timing-function dela
```

以下属性都可以使用补间动画计算

- 位移：left，top，margin，transform位移倾斜，background-posituon等
- transform旋转
- 缩放大小：transform缩放，width，height等
- opacity透明度
- transform线性变换



```javascript
#box {
	width: 100px;
	background: green;
	transition: width 1s, background 2s;
}
#box:hover {
    width: 500px;
	background: red;
}
```







## 关键帧动画 keyframe

和animation配合执行动画

百分号（指时间）或者像素也能写作from和to

```css
@keyframes 动画名称 {
   0%{ 开始位置样式 }  
   100%{  结束位置样式  }  
}
```



```css
#box {
	width: 100px;
	animation: run 1s;
}

@keyframes run {
   0%{ 
     width: 100px;
   }  
   100%{  
     width: 800px;
   }  
}
```





## animation

动画是CSS3中具有颠覆性的特征之一，可通过设置多个节点来精确控制一个或一组动画，常用来实现复杂的动画效果。配合@keyframe执行动画

```css
animation:动画名称 花费时间 运动曲线 何时开始 播放次数 是否反方向;
```



```css
.animation{
   animation-name:动画名称;
   animation-iteration-count: infinite;  infinite无限循环播放    默认是1次
   animation-direction: alternate;   alternate动画应该轮流反向播放    默认是 normal
   animation-play-state: paused;   paused暂停动画，可以用js控制
   animation-fill-mode: forwards;  forwards动画执行完后保持最后一帧，backwrads回到最初状态
}
```








# flex弹性布局

CSS3在布局方面做了非常大的改进，使得我们对块级元素的布局排列变得十分灵活，适应性非常强，其强大的伸缩性，在响应式开发中可以发挥极大的作用。

**开启了flex布局元素叫flex container 内部子元素叫flex items**

**主轴（main-axis）：Flex容器的主轴主要用来配置Flex项目，默认是水平方向，主轴开始方向叫main-start，主轴结束方向叫main-end，主轴长度较main-size**

**侧轴（交叉轴，cross-axis）：与主轴垂直的轴称作侧轴（交叉轴），默认是垂直方向的，交叉轴开始方向cross start，结束方向叫cross end，交叉轴长度较cross-size**

方向：默认主轴从左向右，侧轴默认从上到下，主轴和侧轴并不是固定不变的，通过flex-direction可以互换。

```css
    开启父盒子flex布局css属性
    .box{
        display: flex;         
        /*flex container为块级元素*/
        display: inline-flex;  
        /*flex container为行内元素*/
    }
```



## flex container属性

设置在flex container上的css属性



1.flex-direction 

调整主轴方向（默认为水平方向从左到右）

```css
flex-direction: row;
从左(start)到右(end)为主轴→
    
flex-direction: row-reverse;  
从右(start)到左(end)为主轴←

flex-direction: column;
从上(start)到下(end)为主轴↓
    
flex-direction: column-reverse;
从下(start)到上(end)为主轴↑
```



2.flex-wrap 

调整一行放不下时是否换行（默认nowrap）

```css
flex-wrap: nowrap;
默认情况下所有items都会在一行显示，放不下时就会开始收缩

flex-wrap: wrap;
当一行显示不完items时会换行显示

flex-wrap: wrap-reverse
当一行显示不完items时会换行显示并且第一行从cross-end方向开始排布
这个属性用的很少
```



3.flex-flow

flex-flow是flex-direction与flex-wrap的结合缩写属性
其中任意一个属性值都可以省略且顺序随意

```
例如flex-flow: row-reverse wrap
```



4.justify-content 

调整items在主轴上的对齐方式（默认为flex-start）

```css
justify-content: flex-start;
items在main-start方向上正序排列
    
justify-content: flex-end;
items在main-end方向上倒序排列
    
justify-content: center;
items于主轴上居中对齐
    
justify-content: space-between;
flex items之间距离相等
items与main-start和main-end两端对齐
    
justify-content: space-evenly;
flex items之间距离相等
items与main-start,main-end之间距离等于items之间的距离
    
justify-content: space-around;
flex items之间距离相等
items与main-start,main-end之间距离是items之间的距离的一半
```



 5.align-items 调整items在cross-axis上的对齐方式（默认stretch）

```css
align-items: normal;
在弹性布局中等同默认值stretch
    
align-items: stretch;
items在交叉轴方向的size为auto或未设置时，会自动拉伸到最大长度
    
align-items: flex-start;
items在交叉轴上的对齐方式为从start到end排列开对齐（默认）
    
align-items: flex-end;
items在交叉轴上的对齐方式为从end到start排列开对齐
    
align-items: center;
items在交叉轴上的对齐方式为在中心点上对齐
    
align-items: baseline;
items在交叉轴上的对齐方式为所有items的第一行文本的基线对准对齐
```



6.align-content

align-content决定了设置了align-items后
调整为多行的flex-items以每一行为单位在cross-axis上的对齐方式
用法与justify-content类似

```css
align-content: flex-start;
items行在cross-start方向上正序排列
    
align-content: flex-end;
items行在cross-end方向上倒序排列
    
align-content: center;
items行在cross-axis上居中对齐
    
align-content: space-between;
items行之间距离相等
items与cross-start和cross-end两端对齐
    
align-content: space-evenly;
items行之间距离相等
items与cross-start,cross-end之间距离等于items行之间的距离
    
align-content: space-around;
items行之间距离相等
items与cross-start,cross-end之间距离是items行之间的距离的一半
```





## flex items属性

应用于flex items的属性

- flex-grow

  可以设置任意非负数（0，正小数，正整数）.
  items占不满父元素主轴宽度时剩下的宽度会分为items的grow属性值之和等份。
  根据值进行膨胀，值越大膨胀越多。items成长后最终size不能超过父元素的宽高
  当所有items的flex-grow属性值之和小于1则剩余宽度不会完全被瓜分。瓜分的宽度是剩余宽度乘属性值之和

  ```
  flex-grow:1 主轴剩余宽度他占1份
  flex-grow:0.2 主轴剩余宽度他占0.2份
  ```

  

- flex-basis
      决定主轴方向上item的宽或高
      优先级：父元素的宽高限制>flex-basis>元素本身的width/height>内容宽高

      flex-basis: 100px
      item在主轴上的宽高变为100px
  
- flex-shrink
      可以设置任意非负数（0，正小数，正整数）
      不设置flex-warp换行时，items超过父元素宽度会收缩
      可以设置flex-basis值，会根据所有items的值进行收缩,值越大收缩越多
      item收缩后最小值不会小于本身设置的宽高



- order
      order可以设置任意整数，值越小，在items中就排在前面



- align-self
      items设置cross可以覆盖flex container设置的align-items
      `align-self: auto(默认值)`遵从父元素align-items的设置
    stretch，flex-start，flex-end，center，baseline效果与align-items一致，会覆盖align-items



- flex 是flex-grow，flex-shrink，fkex-basis的缩写属性
      一个无单位值：flex-grow
      一个有单位值：flex-basis
      两个值：
          第一个值必须无单位：flex-grow
          第二值有单位则是fkex-basis，无单位是flex-shrink
      三个值：
          第一个值无单位：flex-grow
          第二值无单位：flex-shrink
          第三个值有单位：fkex-basis 





# c3计算属性calc

用于动态计算长度值，任何长度值都可以用calc进行计算

支持加减乘除的运算

```
width: calc(100% - 10px);
```



# clip-path 容器裁剪

对容器进行显示上的裁剪，可以通过常见的集合图形或者自定义路径进行裁剪。容器的占位和大小不变，只是显示内容变了，被裁掉的地方变透明，但是还是占据位置。并且clip-path支持transition动画。



- inset(宽  高)   方形裁剪

```
clip-path: inset(100px 50px);
```



- circle(半径 in 宽 高)   圆形裁剪

在一片宽100px高px100区域有一个半径的为50px的圆

```
clip-path: circle(50px in 100px 100px);
```



- url()   自定义路径，可以使用svg

```
clip-path: url('');
```






# web字体
## 字体格式
1、TureType(.ttf)格式

.ttf字体是Windows和Mac的最常见的字体，是一种RAW格式，支持这种字体的浏览器有IE9+、Firefox3.5+、Chrome4+、Safari3+、Opera10+、iOS Mobile、Safari4.2+；

2、OpenType(.otf)格式

.otf字体被认为是一种原始的字体格式，其内置在TureType的基础上，支持这种字体的浏览器有Firefox3.5+、Chrome4.0+、Safari3.1+、Opera10.0+、iOS Mobile、Safari4.2+；

3、Web Open Font Format(.woff)格式

woff字体是Web字体中最佳格式，他是一个开放的TrueType/OpenType的压缩版本，同时也支持元数据包的分离，支持这种字体的浏览器有IE9+、Firefox3.5+、Chrome6+、Safari3.6+、Opera11.1+；

4、Embedded Open Type(.eot)格式

.eot字体是IE专用字体，可以从TrueType创建此格式字体，支持这种字体的浏览器有IE4+；

5、SVG(.svg)格式

.svg字体是基于SVG字体渲染的一种格式，支持这种字体的浏览器有Chrome4+、Safari3.1+、Opera10.0+、iOS Mobile Safari3.2+；


## 字体图标iconfont
图片会增加文件大小和很多http请求，所以有些地方可以用字体图标代替图片，字体图标可以跟图片一样旋转，改变透明度等，也能像文字一样，改变颜色，添加阴影等，几乎支持所有浏览器，也适用移动端开发。

## 字体资源
**icomoon字库**
http://www.iconfont.cn/


**阿里icon font字库**
http://www.iconfont.cn/


**fontello**
http://fontello.com/


**Font-Awesome**
[http://fortawesome.github.io/Font-Awesome/](http://fortawesome.github.io/Font-Awesome/)


**Glyphicon Halflings**
[http://glyphicons.com/](http://glyphicons.com/)


**Icons8**
[https://icons8.com/](https://icons8.com/)




## 字体引用
```
    1.建立fonts文件夹，放入字体图标压缩包解压后其中的4个文件.eot，.svg，.ttf，.woff
    
    
    2.在css中复制粘贴如下代码引入字体
    
     <!--声明字体图标-->
     @font-face {   
     font-family: 'icomoon';<!--''表示字体名，随意写-->
     src:  url('fonts/icomoon.eot?7kkyc2');
     src:  url('fonts/icomoon.eot?7kkyc2#iefix') format('embedded-opentype'),
       url('fonts/icomoon.ttf?7kkyc2') format('truetype'),
       url('fonts/icomoon.woff?7kkyc2') format('woff'),
       url('fonts/icomoon.svg?7kkyc2#icomoon') format('svg');
     font-weight: normal;<!--字体加粗正常-->
     font-style: normal;<!--字体倾斜正常-->
     }
     
     
     3.在使用字体图标的盒子css中声明字体
     span{
        font-family: "icomoon" ; <!--icomoon为css中的字体名-->
        }
     
     4.在使用字体图标的位置复制粘贴中的该字体图标编号
       图标编号一般在字体图标压缩包解压后的demo.html中,注意字体图标需要用盒子包起来引用。
       span::before{
           content: "\字体编号";
       }
       <span>口</span>
```



## 自定义字体图标

1.设计svg格式文件


2.在字体图标网站上传字svg文件


3.再下载一次，自动会转换为网页使用的字体格式，再按字体引用的方法使用



## 追加字体图标

1.选择原先字体压缩包中的selection.json文件

2.在字体图标网站上导入该文件

3.继续追加新的字体图标

4.重新下载字体图标压缩包



# ico图标
ico图标用于网站标题旁的logo图或者应用程序的快捷方式图，文件后缀名为.ico
```
    代码
    <link rel="shortcut icon" href="favicon.ico" type="image/x-icon">
    1.ico图标放在head标签里
    <!--代码放在title标签之后-->
    2.type="image/x-icon"属性可以省略

    3.为了兼容性，favicon.ico这个图标文件放在根目录下
    <!--所以一般网站的ico图标在  网址/favicon.ico  路径下-->
```
## 转换ico图标

我们可以自己做的图片，转换为 ico图标，以便放到我们站点里面。 http://www.bitbug.net/

1. 先切图 尽量透明图片  png 格式
2. 把图片转换为图标   http://www.bitbug.net/
3. 把ico图标放入 网站根目录下  之后 利用`<link rel="shortcut icon" href="favicon.ico" /> ` 引入到html里面





# PostCSS

PostCSS是一个用 JavaScript 工具和插件转换 CSS 代码的工具，类似于预处理器，是对css进行解析，然后可以使用各种插件修改css，例如合并模块化等。可以单独使用，也可以配置在webpack中使用。

postcss本身只做两件事，将css转换成css抽象语法树，可以简单的理解为将css转换成js；postcss做的第二件事就是调用插件来处理抽象语法树，通过插件实现对css的处理。



## 常用插件

- import 模块合并插件，合并css，减少http请求
- autoprefixier 自动加浏览器前缀
- caanano 压缩css代码，去除无用代码
- cssnext  让低版本浏览器使用新的css特性，类似babel

- precss     类似预处理器，使用变量，计算循环等。



## webpack配置

下载css-loader，postcss-loader处理css文件

```shell
//除了必备webpack webpack-cli打包工具必备的插件和处理模块
npm install less less-loader --save-dev  //处理less
npm install css-loader postcss postcss-loader cssnano postcss-cssnext autoprefixer --save-dev   //集成postcss
npm mini-css-extract-plugin --save-dev//生成css文件
```

