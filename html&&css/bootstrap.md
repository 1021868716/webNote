# Bootstrap

bootstrap是一个css框架，bootstrap提供了基础样式，布局，常用组件和js插件（例如弹出框等），bootstrap4使用flex布局。重置样式抛弃了Nomalize.css，提供了reboot.css来重置样式。

下载后使用bootstrap.css，则包含了所有功能。



官网下载源码后引入

```html
<link rel="stylesheet" href="bootstrap/css/bootstrap.css">
```



优点：css代码结构合理，使用方便

缺点：定制较为繁琐，体积大



# 网格布局/自适应

Bootstrap 提供了一套响应式、移动设备优先的流式网格系统，随着屏幕或视口（viewport）尺寸的增加，系统会自动分为最多 12 列。



- clo

类似于antd的网格布局，bootstrap将一行分为了12份，使用class名`col-n`来控制一个容器占几份

```html
<h2 class="col-6">hello,world</h2>
<!--占据6份-->
```



- row 将容器内部重新划分至十二份

```html
<div class="col-6">
	<div class="row"> <!--将父节点重新划分为12份-->
		<p class="col-10"></p>
	</div>
</div>
```



## 网格类

原理：使用media query媒体查询设置不同分辨率的class

Bootstrap 4 网格系统有以下 5 个类:

- .col- 针对所有设备
- .col-sm- 平板 - 屏幕宽度等于或大于 576px
- .col-md- 桌面显示器 - 屏幕宽度等于或大于 768px)
- .col-lg- 大桌面显示器 - 屏幕宽度等于或大于 992px)
- .col-xl- 超大桌面显示器 - 屏幕宽度等于或大于 1200px)



可以添加多个网格类以自适应不同屏幕

大于992px的屏幕占三份，其余的屏幕独占12份

```html
<h2 class="col-12 col-lg-3">hello,world</h2>
```





# 定位

元素在容器中所在位置

```html
<h2 class="col-6 offset-3">hello,world</h2>
<!--元素在容器所在的6份中居中显示-->
```





# 样式

给元素加上class即可给普通html元素使用bootstrap的样式

- btn-primary   按钮

```html
<button class="btn-primary">btn</button>
```



- 提示 alert





# js组件

交互组件，例如下拉框（dropdown），弹窗（modal）等。bootstrap的js组件基于jQuery，Popper.js（bootstrap自带，无需引入），需要引入才能使用js组件。使用js组件需要引入源文件中的bootstrap.js。

使用方法

- 基于html的`data-* `属性
- 基于js api





# 定制

- css同名类覆盖重写样式
- 修改bootstrap的sass源文件并重新编译css文件

- 在sass文件中直接引用bootstrap的sass源文件，把bootstrap当作一个mixin或者变量使用