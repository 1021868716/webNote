# 常见元素

html用于描述文档的结构，有区块和大纲

- 不出现页面上

  meta，title，style，link，script，base

  

- 出现在页面上

  区域块级元素：div，section，artilcle，aside，header，footer

  段落：p

  行内元素：span，em，strong

  表格：table，thead，tbody，tr，td

  列表：ul，ol，li，dl，dt，dd

  超链接：a

  表单：form，input，select，textarea，button





# 元素嵌套规则

块级元素可以包含行内元素

块级元素不一定能包含块级元素，例如p不能包div

行内元素一般不能包含块级元素，a除外，因为a是透明元素，计算嵌套合法性的时候会默认忽略a标签。例如p>a>div，嵌套合法验证时忽略a，变为p>div，不合法，div会被挤到外面去。





# 兼容性问题

浏览器兼容性问题的解决方案有：css hack，可以区分不同浏览器针对性的加class











# HTML/XHTML/H5

HTML属于SGML语法。

XHTML属于XML，是html进行XML严格化的结果

HTML5不属于SGML或XML，属于独立的规范，新增了语义化，表单增强，新的api（离线缓存，音视频，图形canvas/svg，websocket，webstorage等）



# 块级元素/行内元素

块级元素有：div，p，h1-h6，ul，ol，dl，li，header，footer，aside，section，article，form，table

行内元素有：span，img，button，input，b，q，i，a，em，label

块级元素行内元素的本质区别：

行内元素设置width，height属性无效，块级元素设置width，height属性有效

行内元素边距起作用的只有：margin-left/right，padding-left/ringht。



# h5格式

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
  </head>
  <body>
	<script>
	</script>
  </body>
</html>
```



# h5语义化

语义化优点

- 开发者容易理解
- 机器容易理解，爬虫等
- SEO

| 标签     | 描述                             |
| -------- | -------------------------------- |
| header   | 定义了文档的头部区域             |
| footer   | 定义了文档的尾部区域             |
| nav      | 定义文档的导航                   |
| section  | 定义文档中的节（section、区段）  |
| article  | 定义页面独立的内容区域           |
| aside    | 定义页面的侧边栏内容             |
| detailes | 用于描述文档或文档某个部分的细节 |
| summary  | 标签包含 details 元素的标题      |
| dialog   | 定义对话框，比如提示框           |



 w3c  手册中文官网     :   http://w3school.com.cn/
ie9及以上浏览器能够识别html5标签

```
  <header> 语义 :定义页面的头部  页眉</header>
  <nav>  语义 :定义导航栏 </nav> 
  <footer> 语义: 定义 页面底部 页脚</footer>
  <article> 语义:  定义文章</article>
  <section> 语义： 定义区域</section>
  <aside> 语义： 定义其所处内容之外的内容 侧边</aside>
  ...
```

- datalist   标签定义选项列表。请与 input 元素配合使用该元素

  ```
  <input type="text" value="请输入明星" list="star"/>
  	<datalist id="star">
  		<option value="刘德华">刘德华</option>
  		<option value="刘若英">刘若英</option>
  		<option value="刘晓庆">刘晓庆</option>
  		<option value="戚薇">戚薇</option>
  		<option value="戚继光">戚继光</option>
  	</datalist>
  ```

- fieldset 元素可将表单内的相关元素分组，打包    与legend 搭配使用

  ```
  <fieldset>
      		<legend>用户登录</legend>  标题
      		用户名: <input type="text"><br /><br />
      		密　码: <input type="password">
  </fieldset>
  ```











# html和dom的关系

html是静态的，dom是html解析来的，是动态的。

JS可以维护dom



# doctype

文档最开头的doctype的意义是

- 使ie浏览器以默认的标准w3c盒子模型进行渲染
- 让浏览器知道元素的合法性

```html
<!DOCTYPE html>
```



# meta

表示字符集

```
<meta charset="utf-8">
```



表示窗口控制，控制窗口能否缩放，窗口大小等

```
<meta name="viewport" content="width=device-width,initial-scale=1.0,user-scalable=no">
```

width：控制 viewport 视口的大小，可以指定的一个值，如果bai 600，或者特殊的值，如 device-width 为屏幕的宽度（单位为缩放为 100% 时的 CSS 的像素）。
height：和 width 相对应，指定高度。
initial-scale：初始缩放比例，也即是当页面第一次 load 的时候缩放比例。
maximum-scale：允许用户缩放到的最大比例。
minimum-scale：允许用户缩放到的最小比例。
user-scalable：用户是否可以手动缩放。



`<meta> `元素可提供有关页面的元信息（meta-information），比如针对搜索引擎和更新频度的描述和关键词。
`<meta> `标签位于文档的头部，不包含任何内容。`<meta> `标签的属性定义了与文档相关联的名称/值对。

```html
<meta name="viewport" content="width=<device-width>, initial-scale=1.0">
```

**content属性值：**

| Value           | 可能值                               | 描述                                                         |
| :-------------- | :----------------------------------- | :----------------------------------------------------------- |
| `width`         | 一个正整数或者字符串 `device-width`  | 以pixels（像素）为单位， 定义viewport（视口）的宽度。        |
| `height`        | 一个正整数或者字符串 `device-height` | 以pixels（像素）为单位， 定义viewport（视口）的高度。        |
| `initial-scale` | `一个0.0` 到`10.0之间的正数`         | 定义设备宽度（纵向模式下的设备宽度或横向模式下的设备高度）与视口大小之间的缩放比率。 |
| `maximum-scale` | `一个0.0` 到`10.0之间的正数`         | 定义缩放的最大值；它必须大于或等于`minimum-scale`的值，不然会导致不确定的行为发生。 |
| `minimum-scale` | 一个`0.0` 到`10.0`之间的正数         | 定义缩放的最小值；它必须小于或等于`maximum-scale`的值，不然会导致不确定的行为发生。 |
| `user-scalable` | 一个布尔值（`yes`或者`no`）          | 如果设置为` no`，用户将不能放大或缩小网页。默认值为` yes`。  |





# link

`<link> `标签定义文档与外部资源的关系。link标签最常见的用途是rel="stylesheet"，链接样式表。

```
<link href="img/divcss5.css" rel="stylesheet"/>
```







# 空格标记&nbsp

html中，无论有多少个空格间隔，渲染出来都只会占一个空白位置，如果要使用多个空白，需要用`&nbsp`空白符进行占位





# h5基本结构

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    
</body>
</html>
```





## 水平线标签

```
<hr/>
```



## 段落标签

```
<p></p>
```



## 标题标签

```
<hn></hn>   n=1~6,逐级减小
```



## 字符集

```
<meta charset="UTF-8"/>
```



## 换行标签

```
<br/>
```



## div,span标签：布局盒子

```
<div></div>     一行只能放一个
<span></span>   一行可以放多个
```



## 文本格式化标签

```
<b></b> <strong></strong>  文本加粗
<i></i> <em></em>          将文字斜体显示
<s></s> <del></del>        将文字加删除线显示
<u></u> <ins></ins>        将文字加下划线显示
第二列四个语义较强，建议XHTML使用
```



# em和i区别

都是斜体标签

em是语义化标签，表示强调

i是纯样式的标签，已被废弃



# img标签

```
<img src="URL"/>
属性
1.src="URL"
2.alt="图片无法显示时的文本"
3.title="鼠标悬停时的的文本"
4.width，height 宽高，单位px
5.border="图像边框的宽度，单位数字"
```

img默认也是按照基线对齐，所以在图片底部会留下底线与基线的3px缝隙，去除方法如下
          1. 给img的 vertical-align:middle/top/bottom等等。  让图片不要和基线对齐。
          2. 给img添加display：block;转换为块级元素。





# a标签

```
<a herf="跳转链接" target="self(原页面刷新)/blank(建立新窗口打开)">文本或图像</a>
在head中可以添加<base targer="self/blank"/>来实现整个页面的跳转方式
超链接必须写http://
```

href属性：超链接地址

target属性 ：self为当前窗口打开，_blank为新建窗口打开

css属性 text-decoration  控制下划线，a标签自带下划线，把这个css属性设置为none即可取消



## 锚点定位

```
<a herf="#id"> </a>
id为需要跳转的html元素id，可实现同页面跳转
```



## 列表标签

```
无序列表
<ul>
    <li>选项1</li>
    <li>选项2</li>
    ...
</ul>
有序列表
<ol>
    <li>选项1</li>
    <li>选项2</li>
    ...
</ol>
自定义列表
<dl>
    <dt>名词1</dt>
        <dd>名词1解释</dd>
    ...
</dl>
一个dt下可以有多个dd
```



# 表格table标签

```
<table></table>
属性
1.width，height 宽，高
2.cellpadding   单元格内容与边框的间距
3.cellspacing   单元格之间的距离
4.border        边框宽度
5.align="left/center/right" 对齐方式
```



## 行标签

**tr子标签**

```
<tr></tr>
```



## 单元格标签

**td子标签**

```
<td></td>
```



## 表头标签 

**th标签替代td标签,这个单元格会加粗显示**

```
<th></th>
```



## 表格标题标签

**caption子标签** 

```
<caption>表格标题</caption>
```

**标签内存表格的标题，仅存table标签内且仅有一个**





# 表格结构

## thead标签和tbody标签

**方便理清表格的标题和内容,没有实际作用**

```
<thead></thead>
<tbody></tbody>
```

## colspan合并单元格

**两个属性都写在td标签内**

合并顺序自上到下，自左到右，合并以后会把多的单元格挤到右边一列去

跨列向右合并:   colspan="合并单元格个数" 

跨行向下合并:   rowspan="合并单元格个数"





# input标签

用于上传信息

## input控件标签

**input是一个单标签**

```
    <input type=" "  value=" ">
```



### type表单类型

text 单行文本框

psaaword 密码框

radio 单选按钮,每个按钮都单独放进一个input控件，且同一组按钮需要有相同的name

checkbox 多选按钮

button 普通按钮，配合value标签显示名称

submit 提交按钮，配合value标签显示名称

reset 重置按钮，配合value标签显示名称

image 图片形式的提交按钮，src="url"属性为图片地址

file 文件域，用于上传文件



### input属性

```
 type=" " 表单类型
 
 value=" "属性为表单显示默认值

 name=" "属性为控件名称

 size=" " input的宽度

 checked="checked"   input组里的默认选项

 maxlength=" "       input内允许输入字符的最大值

 disabled="disabled" 禁用一个 input 元素
 
 placeholder=" "    默认的提示信息
```



## label标签

```
<label><input></label>
```

为input元素定义标注
**用label标签包裹的input可以点击文字域并进入输入栏**

当label内有多个input标签时可以用 for="id"。

for 属性规定 label 与哪个表单元素绑定。

属性来定位input的id属性指定该input的文字域可以被点击。



## textarea控件标签

text：文本框，只能写一行文本
但textarea文本域控件可以输入大量文字信息且可换行
属性：cols="一行的字符数"    rows="显示的行数"
css属性resize:none用于控制不能拖动文本框大小

```
<textarea cols="" rows="" style="resize:none"></textarea>
```





## select下拉菜单控件

```
<select>
    <option selected="selected" value="1">选项一</option>
    <option>选项二</option>
    ...
</select>
```

select标签为下拉菜单标签
selected="selected"属性表示下拉菜单的默认选项





## option子标签

在select标签范围中用于显示子选项

```
<option value="1">选项1</option>
```





## form表单域

包裹所有表单标签

在HTML中，form标签被用于定义表单域，即创建一个表单，以实现用户信息的收集和传递，form中的所有内容都会被提交给服务器。创建表单的基本语法格式如下：

```html
<form action="表单提交url地址" method="提交方式(GET/POST...)" name="表单名称">
  各种表单控件
</form>
```



## h5表单新属性

| 属性               | 用法                                             | 含义                                                         |
| ------------------ | ------------------------------------------------ | ------------------------------------------------------------ |
| **placeholder=""** | `<input type="text" placeholder="请输入用户名">` | 占位符  当用户输入的时候 里面的文字消失  删除所有文字，自动返回 |
| **autofocus**      | `<input type="text" autofocus>`                  | 规定当页面加载时 该input 元素应该自动成为焦点                |
| **multiple**       | `<input type="file" multiple>`                   | 多文件上传                                                   |
| **autocomplete**   | `<input type="text" autocomplete="off">`         | 规定表单是否应该启用自动完成功能  有2个值，一个是on 一个是off      on 代表记录已经输入的值     autocomplete 首先需要提交按钮 , 这个表单您必须给他名字 |
| **required**       | `<input type="text" required>`                   | 必填项  内容不能为空                                         |
| **accesskey=""**   | `<input type="text" accesskey="s">`              | 规定激活（使元素获得焦点）元素的快捷键   采用 alt + s的形式  |



## H5 input type

 html5提供表单验证功能，验证表单是否输入正确

| **类型**     | **使用格式**              | **含义**           |
| ------------ | ------------------------- | ------------------ |
| **email**    | `<input type="email">`    | 输入邮箱格式       |
| **tel**      | `<input type="tel">`      | 输入手机号码格式   |
| **url**      | `<input type="url">`      | 输入url格式        |
| **number**   | `<input type="number">`   | 输入数字格式       |
| **search**   | `<input type="search">`   | 搜索框，体现语义化 |
| **range**    | `<input type="range">`    | 自由拖动滑块       |
| **time**     | `<input type="time">`     | 小时 分钟          |
| **date**     | `<input type="date">`     | 年月日             |
| **datetime** | `<input type="datetime">` | 时间               |
| **month**    | `<input type="month">`    | 月年               |
| **week**     | `<input type="week">`     | 星期 年            |



# iframe标签

iframe标签用于创建一个内联框架被用来在当前 HTML 文档中嵌入另一个文档。

也就是当前网页中嵌入一个窗口浏览另外的网页

| 属性     | 作用                                         |
| -------- | -------------------------------------------- |
| height   | 规定 iframe 的高度。                         |
| name     | 规定 iframe 的名称。                         |
| seamless | 规定` <iframe> `看上去像是包含文档的一部分。 |
| src      | 规定在 iframe 中显示的文档的 URL。           |
| srcdoc   | 规定在` <iframe> `中显示的页面的 HTML 内容。 |
| width    | 定义 iframe 的宽度。                         |



# 多媒体标签

## iframe标签内嵌视频

iframe标签用于创建一个内联框架被用来在当前 HTML 文档中嵌入另一个文档。

也就是网页中嵌入一个另外的网页

因为视频太占资源，所以一般小型网站采用内嵌视频的方式上传视频文件，可以节约服务器资源。

```
    <iframe height="" weight="" src=''></iframe>
```

src可以放其他视频网站的分享地址，实现网站内嵌视频。可以先将视频放置视频门户网站上，分享得到src地址后内嵌即可。



## embed标签插入多媒体

embed可以用来插入各种多媒体，格式可以是 Midi、Wav、AIFF、AU、MP3等等。url为音频或视频文件及其路径，可以是相对路径或绝对路径。
因为兼容性问题，一般用于插入网络视频，H5用audio播放音频，用video播放视频。 



## audio标签插入音频

html5用audio元素播放音频。一般来说mp3和wav格式在浏览器中兼容性较好

```html
    <audio src=" " aotoplay controls loop>
        <source src=" ">
    </audio>
    
    属性值
    aotoplay     自动播放
    controls     添加控制控件
    loop         循环
    source单标签 <source src=" ">
    提供替换音频，可以放置备用格式音频，不支持audio内的格式文件时可以播放此标签内音频
    audio标签中可以添加文本，音频无法播放时会显示。
```



## video标签插入视频

video标签支持三种格式，ogg，mp4，WebM,后两种兼容性较好,video标签通过source子单标签放置视频文件

```html
    <video aotoplay controls loop>
        <source src=" ">
    </video>
    
    属性值
    aotoplay     自动播放
    controls     添加控制控件
    loop         循环
    source单标签 <source src=" ">
```





# logo

## logo代码结构

```
    <div class="logo">
        <h1>
            <a href="#">logoname</a>
        </h1>    <!--h1高权重-->
    </div>
```

## 隐藏logoname

```
    logo框内的文字在搜索引擎里权重很高，所以不能直接省略，而是应该通过隐藏让其不显示。
    首先对文字首行缩进text-indent: -9999ex使其移出logo框外，然后通过overflow: hidden; 隐藏
```



