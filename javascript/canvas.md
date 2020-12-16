# canvas

Echarts是基于canvas的，canvas是一个H5标签，在JS中通过操作这个dom当做画布，然后再创建一个对象进行画图。

html中的canvas标签相当于画布，js中的CanvasRenderingContext2D对象相当于画笔

canvas是基于像素的，而svg则是基于对象模型的，svg的特点是高保真



## canvas画圆

```HTML
<body>
  <canvas id="myCanvas">
  </canvas>
  <script>
    // 获取canvas画布
    var canvas = document.getElementById('myCanvas');
    // 创建一个 CanvasRenderingContext2D 对象
    var ctx = canvas.getContext('2d');
    // 开启一个新的绘制路径
    ctx.beginPath();
    // 设置弧线的颜色为蓝色
    ctx.strokeStyle = "blue"
    var circle = {
    	x: 100, // 圆心的x轴坐标
    	y: 100, // 圆心的y轴坐标
    	r: 50 // 圆的半径
    };
    // 沿circle设置的属性进行顺时针方向绘制绘制弧线
    ctx.arc(circle.x, circle.y, circle.r, 0, Math.PI * 2, false)
    // 安装指定路径绘制会先
    ctx.stroke()
  </script>
</body>
```





