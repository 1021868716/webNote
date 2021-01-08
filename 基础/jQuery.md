# jQuery
1. 下载jQuery并在本地文件中引入jQuery

```html
<script src="jQuery文件地址"></script>
<script>
    //代码
</script>
```

2. 使用CDN

```html
<script src="http://code.jquery.com/jquery-migrate-1.2.1.min.js"></script>
<script>
    //代码
</script>
```



## 入口函数

所有jQuery代码都要写在入口函数中，否则可能操作失败。

ready：页面基本元素加载后就触发，所有代码应该写在这个事件里面，如果在文档没有完全加载之前就运行函数，操作可能失败。`$(document).ready()`等同于`$(function(){})`。

```javascript
$(document).ready(function(){			
    console.log("ok"); 		
})
//或者
$(function(){ 			
    console.log("ok"); 		
}); 

/*
$等同于jQuery
jQuery(document).ready(function(){		
    console.log("ok"); 		
}) 
*/	
```



load：利用dom的页面加载事件：页面全部加载完毕才触发（标签，文字，图片，引入文件）

```
$(window).load(function (){
    console.log("ok");
});
```



## 原生js和jQuery的区别  

- 原生js会等到dom元素加载完毕，并且图片加载完毕才会执行，jQuery会等到dom元素加载完毕，但不会等到图片加载完毕就会执行 
- 原生js如果编辑多个相同入口函数（事件），后面的事件函数会覆盖前面的函数，jQuery编辑多个相同入口函数（事件），后面的事件函数不会覆盖前面的函数




## 访问符号$冲突
当$符号与其他代码或者框架冲突时，可以释放jQuery中$的使用权，用单词jQuery代替$来使用或者自定义访问符号 。自定义访问符：`jQuery.noConflict() 	`

```javascript
var jQ = jQuery.noConflict(); //自定义访问符号jQ 
jQ(function(){
    console.log("ok");
});
```



## 核心函数$()

jQuery核心函数为$()，接收各种参数调用，$等同于jQuery
- 参数为函数时为入口函数ready
- 参数为字符串形式的选择器，返回一个jQuery对象，保存了选择器找到的dom元素
- 参数为字符串形式的dom代码片段，返回一个jQuery对象，保存了创建的dom元素
- 参数为JS元素选择器，返回一个jQuery对象，保存了该dom元素
```javascript
//第一个核心函数$()参数为函数
$(function(){
//第二个核心函数参数为字符串形式的选择器
//返回一个jQuery对象，保存了选择器找到的dom元素
  var $box1 = $(".box1");//class选择器 
  var $box2 = $("#box2");//id选择器
  var $div = $("div");//标签选择器
  console.log($box1);
  console.log($box2);  
  console.log($div);
            
  //第三个核心函数$()参数为字符串形式的dom代码片段会创建这个dom对象 		
  //返回一个jQuery对象，保存了创建的dom元素 
  var $p = $("<p>this is a p</p>");
  console.log($p);
        
 //第四个核心参数$()为JS元素选择器
 //返回一个jQuery对象，保存了该dom元素
  var span = $(document.getElementByTagName("span")[0]);
  console.log(span);
});
```



# jQuery顶级对象

jQuery顶级对象是jQuery或者$，jQuery所有属性方法全部需要用顶级对象来使用,$和jQuery能互相替代使用

## 包装集
jQuery对象是一个伪数组(包装集)，有length属性，有0到length-1的下标。

```javascript
jQuery对象:   jQuery.fn.init(length)
jQuery获取的对象都是一个包装集，具有length属性

案例判断元素是否存在
if($("#dv").length!=0){
//if($("#dv"))无法判断该元素是否存在
    console.log("该元素存在");
}else{
    console.log("该元素不存在");
}
```



## DOM对象与jQuery对象

dom对象并不能调用jQuery对象的方法，反之亦然，所以常常需要在dom对象和jQuery对象中相互转换。

jQuery调用事件一般去掉on，jQuery对象才能调用jQuery方法，dom对象不能使用jQuery方法，jQuery对象不能使用on事件等原生js写法

```javascript
var btn1 = document.getElementById("btn") ; //dom对象
var btn2 = $("#btn");    //jQuery对象
console.log(btn1==btn2); //false，两者并不相等
```

```javascript
$() // dom对象转jQuery对象

//jQuery对象转dom对象
jQueryobj[0]
jQueryobj.get(0)  
```



# jQuery选择器

选择器selector，将抓取dom元素并转化为jQuery对象
- id选择器

```javascript
$("#id值")
```



- 标签选择器

```js
$("TageName")
```


- 属性限定符[]

```javascript
[]//限定属性
$("input[type='button']")
//限定只能选择type=button的input标签
```



- 类选择器

```js
$("className")
```



- 交集并集选择器
  - 交集选择器（标签+类）：标签名.类样式名
  - 并集选择器：每个选择器用逗号隔开



- 子代后代选择器
  - 子代选择器，用>隔开，只选取子代的符合元素
  - 层次选择器也叫后代选择器，用空格隔开，所有的后代元素中被符合的元素全部选中



- 兄弟元素选择器
  - 用~隔开,选取这个元素之后的兄弟元素中的全部符合元素                                                                       
  - 用+号隔开，选取这个元素后面紧跟的兄弟元素



- 奇偶选择器
  - :odd  可以选出选择器元素组中索引下标奇数元素
  - :even 可以选出选择器元素组中索引下标偶数元素

```javascript
	例如$(ul>li:odd)/ $(ul>li:even)
```



- 索引选择器
  - :eq(index)  可以选出索引值为index元素
  - :lt(index)  选中所有索引小于index的元素
  - :gt(index)  选中所有索引大于index的元素

```javascript
//所有的li中选中索引为2的li
$("ul>li:eq(2)").css("backgroundColor","green");

//所有的li中索引小于5的li
$("ul>li:lt(5)").css("backgroundColor","deeppink");

//所有的li中索引大于5的li
$("ul>li:gt(5)").css("backgroundColor","hotpink");

//选中索引5之后的三个元素6，7，8
$("ul>li:gt(5):lt(3)").css("backgroundColor","blue");
```



- :selected

  选取被鼠标选中的元素

```javascript
$("ul>li:selected").css("backgroundColor","green"); 
```



# 选取元素

选取元素并返回对象的方法，selector参数：字符串类型的选择器

- .parent()   返回父级元素



- .last()     返回最后一个子元素



- .first()    返回第一个子元素



- .children() 返回子级元素

  可添加selector选择器参数，可以限定选择直接子元素

```JavaScript
.children("li")
//将元素的所有li直接子元素选取
```


- .find(selector)     返回指定子元素

  具有selector选择器参数，可以限定选择所有后代元素

```JavaScript
.find("li")  //将元素的所有li后代元素选取
```


- .prev()     返回上一个兄弟元素

可选selector选择器参数，可以限定选择



- .prevAll()  返回前面所有兄弟元素

可选selector选择器参数，可以限定选择



- .next()     返回下一个兄弟元素

可选selector选择器参数，可以限定选择



- .nextAll()  返回后面所有兄弟元素

可选selector选择器参数，可以限定选择



- .siblings() 返回被选元素的其他所有兄弟元素

  可选selector选择器参数，可以限定选择

```JavaScript
.siblings("li")  //限定只选取调用元素的li兄弟元素
```


- .eq()方法   返回索引值为index元素

```JavaScript
.eq(index)
//可以选出索引值为index元素
//类似于选择器中的:eq(index)
```




# jQuery中的this
因为只有jQuery对象才能调用jQuery方法，所以在jQuery中js对象this也必须转化成jQuery对象后才能使用jQuery方法

```javascript
$("#btn").click(funtion(){
$("this").val("ok");
//将btn中的value值变为ok
//.val()是jQuery方法，js对象无法调用，需要转为jQuery对象
});
```



# jQuery事件

## 事件参数对象e
jQuery中也有事件参数对象e,也可以写作arguments[0],但该对象变成了jQuery对象,e具有判断用户触发事件时的行为的属性
- e.altKey

布尔类型，判断用户触发事件时是否同时按下alt键



- e.shiftKey



- e.ctrlKey



- 键值e.keyCode

触发事件时，e会记录按下键盘的键值保存在keycode属性中



## 事件的绑定
 **这种方式只能为调用时页面已经存在的元素添加事件调用后添加的元素无法获得事件**
- .事件名(function)
```
   部分事件jQuery没有实现，则无法用这种方式绑定，要使用.on()绑定
```

- .bind("事件名",function);
```
.bind({"事件名1":function,
	   "事件名2":function,
		  ...
	 });
//以对象为参数为一个元素添加多个事件
```

- .on("事件名",funcion);
```javascript
.on()方法可以为自己或者为子元素添加事件
.delegate()和.bind()的js内部实现就是调用了.on()方法
.on()和.delegate()作用参数顺序不同
.on()方法jQuery和JS都有
        
可以为自己添加事件：
例如
    $("#btn").on("click",funcion(){
    		...
    });
    	
也可以为子元素委托绑定事件：
    jQueryobj/JSobj.on("事件名","子元素选择器",function);
    .on()的事件处理函数function中this指向子元素
```

## 事件委托绑定
**事件委托绑定后，父元素(间接父元素也能委托绑定)会监听子元素，如果新增了符合要求的子元素就会自动绑定事件上去，也就是说：父元素代理添加事件,最终事件绑定在子元素上即使元素在事件添加之后再创建也能获得这个事件**
```
父元素.delegate("子元素选择器","事件名",function);        
  
父元素.on("事件名","子元素选择器",function);
```

## 事件委托绑定的区别
```javascript
.事件名(function);
.bind(事件数组);
//前两种方式只能为已经存在的元素添加事件
//调用后创建的元素没有事件

父元素.delegate("子元素选择器","事件名",function);
父元素.on("事件名","子元素选择器",function);
//父元素调用方法为子元素代理添加事件
//在调用之后创建的子元素也能获得该事件
//推荐使用.on()绑定委托事件,因为.delegate()内部实现也是.on()
```


​    
## 事件的隐式迭代
jQuery事件操作为jQueryobj.事件(事件函数),jQuery会隐式迭代，一个对象包含多个元素时会自动循环给每个元素操作事件
```javascript
例如点击事件
$(".btn").click(function(){ 
//隐式迭代，无需再循环
//所有class值为btn的元素全部赋予点击事件
    console.log("click");
});
```



## .each()遍历jQ对象

.each()方法是用来遍历jQuery对象的，但是里面的每个对象都是dom对象，想使用jQuery方法得转换为对应对象。
```javascript
$(selector).each(function(index,element){
//使用jQuery方法需要转换对象$(element)
//对每个对象都执行一次function
});

//参数index为每个对象的下标位置,可以直接使用
//参数element为当前的元素,为dom对象
//element对象想要使用jQuery方法需要转换对象在使用
```



## .unbind()解绑事件
```javascript
.unbind()可以解绑.bind()和.事件名()绑定的事件
.unbind("事件名1 事件名2 ...");
//所有的参数事件全部移除
```



## .undelegate()解绑事件

```javascript
解绑为子元素代理绑定的事件
父级元素.undelegate();//移除所有为子元素绑定的事件

父级元素.undelegate("子元素","事件名1 事件名2 ...");
//为指定子元素解绑参数事件
```



## .off()解绑事件

```javascript
父级元素.off();
//父级元素和子级元素所有事件全部解绑

父级元素.off("事件名1 事件名2 ...");
//父级元素和子级元素参数事件全部解绑

父级元素.off("事件名1 事件名2 ...","子元素");
//为指定子元素解绑参数事件

父级元素.off("事件名1 事件名2 ...","**");
//为所有子元素解绑参数事件

元素.off("事件",functionName);
//为指定事件移除指定函数
```



## 阻止事件冒泡
```javascript
在对象的事件处理函数结尾加入 return false; 在该对象外层的事件就不会冒泡
加入 event.stopropagatin(); 也能实现组织事件冒泡

<div id="dv1">
    <div id="dv2"></div>
	//在dv2的事件处理函数最后加入return false;dv1的相同事件就不会冒泡
</div>
```



## 阻止浏览器默认行为

在函数结尾中加入 `return false` 或者 `event.preventDefaul()` 就能事件自动阻止触发浏览器默认行为。
例如给a标签添加点击事件，加入阻止语句，就不会自动跳转到新网页。



## .trigger()/.triggerHandler()

```javascript
第一种方式触发器直接触发事件
.事件名();
.trigger("事件名");
//这种方式可以触发事件，也能触发浏览器默认行为，并且会事件冒泡
//a标签有所不同，给a标签添加.trigger()触发器，触发事件以后不会再跳转页面
//解决需要在给a标签中再加一个盒子，用盒子添加触发器，给盒子添加事件，然后a才会跳转

第二种触发器触发事件
.triggerHandler("事件名");
//这种方式自动触发事件，不会触发浏览器行为并且会阻止事件冒泡
```



## 事件命名空间

事件命名空间必须通过.on()绑定，可以添加给事件名点后缀并且只能通过触发器选择指定事件触发

```javascript
$("#btn").on("click.zs"，funcion (){//zs点击事件
	alter("zs");	
})
$("#btn").on("click.ls"，funcion (){//ls点击事件
	alter("ls");
})

//点击btn两个事件都能触发
$("#btn").trigger("click.ls")//添加触发器后只会自动触发ls事件
```

如果子元素和父元素具有相同命名空间的事件，用触发器.trigger()触发子元素的带命名空间的事件，父元素具有相同命名空间的事件也会触发不带命名空间的事件都会触发，如果用触发器.trigger()触发子元素不带命名空间的事件，那么子元素所有相同类型的事件和父元素所有相同类型的事件（带不带命名空间都算相同类型）都会冒泡触发





# 事件类型type

- click点击事件

    jQueryobj.click(function);



- mousedown鼠标按下事件



- keydown键盘按下事件



- mouseenter/mouseleave  鼠标进入/离开事件

```javascript
jQueryobj.mouseenter(function);// 鼠标进入事件
jQueryobj.mouseleave (function);// 鼠标离开事件
//可以链式调用，就能只获取一次jQueryobj实现两个事件
jQueryobj.mouseenter(function).mouseleave (function);
```


- .hover() 鼠标移入移出事件

```javascript
.hover(function1,function2);
function1是移入事件函数
function2是移出事件函数
.hover的内部实现还是mouseenter/mouseleave事件
```



- focus焦点获取事件



- change 失去焦点或属性改变事件

触发条件
	a）当前对象属性改变，并且是由键盘或鼠标事件激发的（脚本触发无效）

　b）当前对象失去焦点(onblur)

　c）只适用于文本域（text field），textarea以及select元素
当用于文本域或textarea标签时必须要元素失去焦点时才会触发，与select使用时，会在选择某个选项时被触发



- propertychange（ie）和input事件

input是标准的浏览器事件，一般应用于input元素，当input的value发生变化就会发生，无论是键盘输入还是鼠标黏贴的改变都能及时监听到变化

input事件：用于监听input表单内容（value）的改变，改变就会触发
propertychange事件：只要当前对象属性发生改变就会触发。



## 自定义事件

自定义事件只能通过on绑定，且只能通过.trigger()触发器自动触发
```javascript
$("#btn").on("myClick"，funcion (){
	alter("ok");
})

$("#btn").trigger("myClick");
```




# jQuery方法
## 动态方法与静态方法
js里静态方法通过类名调用，实例方法通过实例调用(实例方法添加在原型里)

```javascript
function AClass (){}//添加静态方法
AClass.staticMethod = function (){
console.log("staticMethod");
}

AClass.staticMethod();//调用静态方法

//通过原型添加实例方法
AClass.prototype.instanceMethod = function(){
console.log("instanceMethod");
} 
var obj = new AClass();
a.instanceMethod();//调用实例方法
```


## 链式调用
`对象.方法().方法.方法().方法();`
这样的书写方式叫链式调用，只要方法的返回值还是该对象就可以继续链式调用，在jQuery中，一般情况，对象调用的方法是设置属性，那么返回来的几乎都是当前的对象，可以链式调用该jQuery对象的方法或属性




### 链式编程原理
链式编程必须方法返回值还是对象，一般来说方法用于设置返回值还是当前对象。
```javascript
设置和查看是一个函数的内部实现
function theValue(setvalue){
    if(setvalue){//有值传入为设置
        //设置代码
        ...
        //返回值为对象
        return this
    }else{//无值传入为查询
        //查值代码
        ...
    }
}
```


​    
### .end()方法修复断链
断链:对象调用方法之后,返回的已经不是当前的这个对象了,此时再链式调用方法,就出现了断链

```javascript
.end()
//在断链处调用修复断链,恢复断链之前的状态
//但出现了断链就不推荐使用链式编程
```



## .val()

`.val()`：表示获取该jQuery对象的value值
`.val(string)`：表示设置该jQuery对象的value值



## .css()

`.css("css属性名","值")`：设置css属性值

```javascript
// 参数也可以写成对象传入
.css({"属性名"：值，"属性名"：值，...})  
```



`.css("css属性名")`：获取css属性值：
设置对象的css属性，值为空时表示取消该css属性，属性名可以为dom属性名如backgroundColor，也可以为css属性名如background-color



## .text()

text方法类似于dom中的innertext属性，操作元素中的文本属性

    .text()//获取该元素文本内容
    .text("属性","值") //设置该元素的文本内容



## .html()

```javascript
.html()
//相当于dom中的innerHTML
//设置或获取对象中的的html内容,设置内容时会完全覆盖掉原有内容
```



## .index()

```javascript
//该方法可以获取元素在所有兄弟中的索引下标
var index = $(this).index();
//获取索引
```



## .append()/.appendTo()

append()是父级元素调用的，appendTo()是子级元素调用的，用于转移元素，但是用这个方法用于转移已有的元素时，被转移的子级元素会消失并转移进目标元素中的最后

```javascript
//append()是在调用对象（父级元素）中追加一个元素
//例如在dv中创建追加一个input元素
$("#dv").append($("<input type='button' value='btn'/>"));
//将创建的子级元素追加进父级元素dv
$("<input type='button' value='btn'/>").appendTo($("#dv"));
```



```javascript
//案例
<div id="dv1">
  <p>我是快乐的</p>
</div>
<div id="dv2"></div>

//p元素由dv1直接转移进dv2后dv1中的原p标签会消失
$("#dv2").append($("#dv1>p"));
$("#dv1>p").appendTo($("#dv2"));
$("#dv1>p").clone().appendTo($("#dv2"));
//克隆一份再转移，原属性就不会消失
    
//创建表格案例
var array=["风斯托罗斯基","小苏坨萝斯基","助教坨萝斯基","尼古拉斯凯奇赵四","小胖妞","杰森斯坦森班主任","小明","小红","小李","小白","小绿","小黑"];
$("#btn").click(function () {
  //先创建ul加入到div中
  var ulObj=$("<ul></ul>").appendTo($("#dv"));
  //创建li标签,循环加入到ul中
  for(var i=0;i<array.length;i++){
    $("<li>"+array[i]+"</li>").appendTo(ulObj)
  });
}   
```


## .prepend()/.prependTo()

这两个方法作用域.append()和.appendTo()类似,但是将子元素追加进父元素最前面,其他用法相同



## .after()/.before()

这两个方法是追加兄弟元素,用法与追加子元素相同

`.after()`：将兄弟元素追加在调用元素之后
`.before()`：将兄弟元素追加在调用元素之前



## .clone() 

.clone()方法用于复制元素



## .addClass()/.removeClass()

为调用对象添加或移除类样式或移除类样式
```javascript
$("#dv").addClass("cls").addClass("cls2");
//console.log($("#dv").addClass("cls"));
//同时添加cls和cls2类样式

$("#dv").removeClass("cls").removeClass("cls2");
//移除类样式
```



## .toggleClass()

toggleClass()是切换类样式方法

```javascript
$("#dv").toggleClass("cls");
//如果有cls样式则移除，没有则添加
```



## .hasClass()

判断元素是否应用了参数类样式，返回值为布尔类型

```javascript
if($("#dv").hasClass("cls cls2")){
    console.log("应用了");
}else{
    console.log("没有应用");
}
```



## .attr()

.attr()方法主要操作html属性（特别是自定义属性），方法设置或返回被选元素的属性值。但是这个方法操作选中属性checked时比较麻烦，操作checked属性推荐使用prop方法

    .attr("属性")       查询属性值
    .attr("属性","值")  更改和创建属性值



## .removeAttr()

```javascript
//移除自定义属性
.removeAttr("自定义属性")
```



## .prop()

**.prop()方法不仅可以设置属性，还能设置属性节点，设置获取元素(一般为表单元素)的选中状态使用prop方法**

```javascript
.prop("checked")
//查看元素的选中状态
//返回选中为ture,不选中为false

.prop("checked",boolean)
//设置元素的选中状态

// 例如
.prop("checked",ture); 
//将表单元素的checked属性变为选中状态
```



## 清除元素

```javascript
.html('');  //清空该元素的子级元素
.empty();   //清空该元素的子级元素
.remove();  //把该元素连带子级元素一起清除
```



## .width()/.height()

```javascript
.width();/.height();         //获取调用元素的宽或高
.width("值")/.height("值")  //设置调用元素的宽或高
```



## .innerWidth()/.innerHeight()

该方法返回元素的包括内边距的宽/高



## .outerWidth()/.outerHeight()

```javascript
.outerWidth()/.outerHeight();
//不带参数返回元素包括内边距和边框的宽/高

.outerWidth(ture)/.outerHeight(ture);
//带ture或false参数返回内边距,边框,外边距的宽/高
```



## .offset()

.offset()方法获取或者设置元素的距离窗口的偏移位left和top值,这个值会包含元素margin的值,但该方法无论是设置还是获取返回的都是一个对象,该对象有两个属性:left和top
    
```javascript
.offset()
//获取距离窗口的偏移位,并返回一个包含left和top值的对象
//left和top值会包含元素margin的值

.offset().left;
//获取距离页面的左偏移量
.offset().top;
//获取距离页面的上偏移量

.offset({"left":值,"top":值})
//设置距离页面的偏移属性
```



## .position()

.position()方法只能获取定位的偏移（不能设置设置用.css()方法）
	
```javascript
.position().left; //获取定位左偏移
.position().top;  //获取定位上偏移
```



## .scrollLeft()和.scrollTop()

```javascript
.scrollLeft()
//获取元素向左卷曲的距离
.scrollTop()
//获取元素向上卷曲的距离

传入数字参数可以设置默认的卷曲距离
.scrollTop(300);//页面刷新时调用元素自动卷曲到300px处

获取页面卷曲量
ie：$("body").scrollTop();
其他浏览器：$("html").scrollTop();
兼容代码：console.log($("body").scrollTop()+$("html").scrollTop());
//其他浏览器body调用为0，ie浏览器html调用为0

设置页面卷曲量
兼容代码：$("body,html").scrollTop(500);  //页面卷曲到500px
```





# 动画方法

动画方法内的时间参数time单位都为毫秒，也可以替换为slow，normal，fast
## .stop()/.delay()

停止动画队列

`.delay(time)`： time毫秒后在执行下一个动画

`.stop(boolean,boolean)`： 清除上一个动画并执行下一个动画
false   停止当前动画并执行下一个动画（同无参调用）
ture    停止当前动画不执行下一个动画
false, false   同false
false, ture    立刻完成当前动画并执行下一个动画
ture, false    同ture
ture, ture     立刻完成当前动画并不执行下一个动画



## .show()/.hide()
```javascript
//无参调用
$().show();
//显示元素内部实现 display='';
$()..hide();
//隐藏元素内部实现display='none';
```

这两个方法可以传入时间参数（毫秒）和函数参数，可以形成渐变效果，但是会有bug，可能上一次动画还没执行完，下一次动画又开始执行了，出现重复动画，可以加入stop()方法链式调用，清除上次一动画再开始下一次动画，而函数参数则在动画执行完毕后才会执行

```javascript
//先执行stop()函数清除未执行完的动画，防止出现重复的动画效果                 
$().stop().show(200,function(){
    alert("ok");//动画执行完毕后弹出窗口ok
}); 
$().stop().hide(200,function(){
    alert("ok");
});
```



## .slideUp()/.sildeDown()

可以传入时间参数控制渐变速度和函数参数（动画执行完后执行函数）

slideUp()：渐变收起，将元素隐藏
sildeDown()：渐变展开，将元素显示出来
slideToggle()：切换滑入滑出



## .slideToggle()
切换收起展开



## .fadeIn()/.fadeOut()
    fadeIn()淡入
    fadeOut()淡出
    fadeToggle()切换淡入淡出
    可以传入时间参数控制渐变速度和函数参数（动画执行完后执行函数）


##  .fadeToggle(time)

fadeToggle(time)方法：切换淡入和淡出，如果元素已被隐藏则淡入出来，如果元素已经显示则淡出隐藏，可以传入时间参数控制淡入淡出速度



## .fadeTo(time,opacity)

改变透明度，在time毫秒内逐渐变为opacity透明度，opacity取值0-1



## .animate(options, time)

.animate()参数  

1. 属性键值对对象options
2. 时间time（ms）
3. 动画节奏(必须写在time后)  "linear"匀速   "swing"缓动
4. 回调函数function

```javascript
//调用三段动画，以下代码也可以简写为链式调用
$("#im").animate({"width":"300px", "height":"300px", "left":"100px", "top":"100px"},300);
$("#im").animate({"width":"30px", "height":"30px", "left":"10px", "top":"600px"},300);
$("#im").animate({"width":"50px","height":"50px","left":"800px","top":"20px","opacity":0.5},200);

关键字
{
    width:"+=",//相当于width=width+width
    width:"hide",//隐藏,相当于.hide()方法
    width:"toggle"//如果该元素显示则隐藏，如果隐藏则显示
}
```


