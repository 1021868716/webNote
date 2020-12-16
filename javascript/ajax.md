# AJAX

ajax是在不刷新页面的情况下更新部分网页的前端技术

## 发送ajax请求的过程
```javascript
1.创建一个异步对象
//XMLHttpRequest对象
let xhr = new XMLHttpRequest()

2.open()方法设置请求方式和请求地址
xhr.open(请求方式, url, 是否异步)

3.xhr.send()发送请求
post请求send方法中可以传入一个json化的参数对象 xhr.send(JSON.stringify(postData))

4.监听状态的变化
异步对象调用onreadystatechange事件

5.判断请求是否成功
异步对象的readyState和status属性

6.返回处理结果
```



## XMLHttpRequest对象

所有现代浏览器都支持XMLHttpRequest对象，除了ie5，ie6（他们使用ActiveObject），XMLHttpRequest对象用于后台与服务器在不刷新网页的情况下更新数据

​    **XMLHttpRequest对象属性**

- onreadystatechange()

  每当readyState属性改变时就会调用该事件

  

- readyState  当前ajax请求的

  0：请求未初始化
  1：已调用send方法，服务器连接已建立
  2：请求已接收
  3：请求处理中
  4：请求已完成且响应已就绪

- HTTP状态码status

  

- responseText  

  服务器响应的字符串

  

- responseXML 

   服务器响应的XML



​	  **XMLHttpRequest对象方法**

- abort()   停止当前请求



- getResponseHearders("首部属性名")     

  把响应的指定首部属性的值返回



- getAllResponseHearders() 

   把HTTP请求的所有响应首部作为键/值对返回



- open("method", "url", bool)   

  method为HTTP请求方式get/post/put，url为目标服务器地址，bool值为是否异步请求

  **调用send和setRequestHeader之前必须调用open方法**

  

- setRequestHeader(“请求头属性”,"value")    

  设置请求头的指定属性

  

- send()        向服务器发送请求

post请求send方法中可以传入一个json化的参数对象 xhr.send(JSON.stringify(postData))

​    

## 实现ajax
```javascript
    //1.创建一个异步对象
var xmlhttp=new XMLHttpRequest();
    
    //2.设置请求方式和请求地址
xmlhttp.open("GET", "url地址?t="+(new Date().getTime()), true);
    //请求方式method: get , post
    //同步异步async: ture(异步) , false(同步)
    //ajax是异步的，所以为true
    //因为ie有缓存机制，认为同一个url只有一个结果
    //ie获取一次ajax后不能持续刷新数据，会在缓存中读取
    //所以可以给文件名后加上"?t=随机数乱码"
    //根据时间获取随机数new Date().getTime()
    //这样访问的文件还是同一个，但url不同所以不会触发缓存机制
    
    //3.发送请求
xmlhttp.send();
    
    //4.监听状态的变化
xmlhttp.onreadystatechange = function(){
  if(xmlhttp.readyState === 4){ // 4表示请求已完成且响应已就绪
    //5.判断请求是否成功
    if(xmlhttp.status>=200 && xmlhttp.status<300||xmlhttp.status === 304){
    //6.处理返回的结果
       console.log("接收服务器数据成功");
       console.log(xmlhttp.responseText)
    }else{
       console.log("接收服务器数据失败");
    }
  }
}
```



## ajax服务器响应

获取XMLHttpRequest对象获取服务器响应，responseText 或 responseXML 属性

```javascript
alert(xmlhttp.responseText);
//获取从后台获得的字符串形式的响应数据并弹窗
```

​    属性
​    responseText	获得字符串形式的响应数据。
​    responseXML	    获得 XML 形式的响应数据。


​    



# fetch

fetch是一种HTTP数据请求的方式，是XMLHttpRequest的一种替代方案，是js原生的另外一种网络请求的方式。fetch不是ajax的进一步封装，而是原生js的window下的一个属性。Fetch函数就是原生js，没有使用XMLHttpRequest对象。

- 特点

  1. 可以使用Request配置对象和自定义的http配置对象（可以省略）

     也可以直接传入url和自定义的http配置对象（可以省略）

     ```javascript
     // Request 是js提供的配置对象，Object options是我们自定义的http配置对象
     fetch(url[, Object options]);
     fetch(Request[, Object options]);
     ```

     

  2. 使用了Promises来处理结果/回调

  

```javascript
// 使用自定义的配置对象发送fetch
let requestConfig = {
      credentials: 'include',//为了在当前域名内自动发送 cookie ， 必须提供这个选项
      method: 'GET',
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json'
      },
      mode: "cors",//请求的模式
      cache: "force-cache"
}
fetch('url', requestConfig).then(response => response.json())
.then(data => console.log(data))
.catch(e => console.log("error：", e))


// 使用Request对象发送fetch
var req = new Request('url', {method: 'GET', cache: 'reload'}); // cache设置为不缓存响应的结果
fetch(req).then(
  function(response) {
  	console.log(data)
	},
  function(e) {
    console.log("error：", e)
  }
)
```







## fetch和ajax 的主要区别

- fetch()返回的promise将不会拒绝http的错误状态，即使响应是一个HTTP 404或者500，只有网络故障或者请求被拒绝时才会进入promise的reject状态

- 在默认情况下，fetch不会接受或者发送cookies，必须手动设置credentials
- fetch兼容不如ajax

