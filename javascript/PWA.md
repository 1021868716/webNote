# PWA

Progressive Web App   渐进式网络应用



## PWA组成技术

- Service Worker

  Service Worker（以下简称sw）是基于WEB Worker而来的，Service Worker与Web Worker类似，但拥有更多的特性，sw便是在web worker的基础上增加了离线缓存的能力。可以在控制台Application下查看Service Workers。

  例如

  - 独立于页面常驻内存运行
  - 可以代理网络请求
  - 依赖https，另外有一点需要注意的是，出于对安全问题的考虑，**Service Worker 只能被使用在 https 或者localhost 环境下**。

  

- Promise



- fetch

  ajax不但发送请求很麻烦，还需要解析返回值，fetch用于解决这个问题，fetch比XMLHttpRequest更简洁，并且是Promise风格的api。但fetch也有不足。



- caches

  浏览器中往cookie和local storage里注入数据都属于内存数据，对于资源例如css，图片等没办法做持久缓存。
  
  caches API依赖Service Worker拦截网络请求，可以拦截资源请求并返回cache中缓存的数据，这使得web页面在没有网络的情况下也能打开。可以通过控制台的Application下查看Cache Storage。





- Notification API

  依赖Service Worker常驻内存，Notification API可以模仿原生App向用户发送消息推送





# Service Worker

sw是由**事件驱动**的，具有**生命周期**，并通过生命周期事件的形式提供交互手段。sw可以拦截处理页面的所有网络请求(fetch)，也可以访问cache和indexDB，并支持推送，可以让开发者自己控制管理缓存的内容以及版本。这使离线弱网环境下的 web 的运行提供了可能，让 web 在体验上更加贴近 native，拥有主动推送，全屏等native特性。

sw可以把应用里的所有静态动态资源根据不同策略缓存起来，在用户下次打开时不再需要去服务器请求，这样一来就减少了网络耗时，使得web应用可以秒开，并且在离线环境下也变得可用。

在离线情况下，Service Worker可以将请求拦截下来，通过自身的缓存去渲染页面，达到离线使用。因为js和页面渲染共用一个线程，所以js不能做大规模的计算，避免页面卡顿，但Service Worker可以独立主线程之外进行计算，然后和主页面通信通知计算结果，可以实现大规模处理数据。

需要首先在页面中使用script标签使用window.navigator.serviceWorker下的register方法注册一个sw线程，导入sw脚本文件并控制作用域。

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>LEARN PWA</title>
</head>
<body>
  <script>
    if(window.navigator.serviceWorker) {
      // 创建一个Service Worker线程
    window.
    navigator.
    serviceWorker.
    register('./sw.js', {scope: '/',}) // 第一个参数为Service Worker脚本文件，第二个参数为脚本可以控制的页面的路径,'/'表示当前目录下所有页面，register返回peomise
    .then(
      registration => {
        console.log('hello,Service Worker')
        console.log(registration)
      },
      err => {
        console.error(err)
      })  
    }
  </script>
</body>
</html>
```



sw生命周期如下

![sw生命周期](https://upload-images.jianshu.io/upload_images/6201114-187180a283f20add.png?imageMogr2/auto-orient/strip|imageView2/2/w/702/format/webp)

- No ServiceWorker（初始化状态）

  网页初始为没有sw的状态

  

- Installing（安装状态）

  第二步网页会进入安装sw的状态，这一步会返回一个promise状态，失败进入Error，不支持sw，成功安装进入Activated（激活状态）



- Activated（激活状态）

  sw开始运行，这一步可以开始监听事件，由事件驱动sw进行计算，拦截请求，与主页面通信等操作。



## sw检测

chrome上有两条命令进行sw检测

- chrome://inspect/#service-workers   检测当前正在运行的sw线程
- chrome://serviceworker-internals/     检测曾经在chrome上注册过的sw线程

也可以在chrome浏览器控制台Application下查看service workers





# self

在Service Worker的控制脚本上下文中不能访问dom，也不能访问window，local storage等对象，Service Worker脚本具有全新的上下文，拥有一个全新的全局对象self，在sw脚本文件中（sw.js）使用self。self是Service Worker脚本的全局作用域对象，可以监听self上的事件来操作Service Worker。

self有三种主要生命周期事件，分别是 `install` ， `activate `， `fetch`，需要给self对象绑定这三个事件并传入默认的事件处理参数event。Service Worker编程就是在与这三个生命周期打交道。

在一个新的Service Worker脚本被下载后触发install事件，install 事件中, 可以对抓取资源进行缓存

当一个资源install后被正式加载时会触发activate事件，activate 事件中，一般进行遍历缓存, 清除过期的资源

进行网络请求时会触发fetch事件，fetch 事件中, 一般进行捕获网络请求, 查询缓存或者网络, 返回请求的资源

```javascript
self.addEventListener('install', event => {
  console.log('install: ', event)
})
self.addEventListener('activate', event => {
  console.log('activate: ', event)
})
self.addEventListener('fetch', event => {
  console.log('fetch: ', event)
})
```



## event.waitUntil

event.waitUntil是生命周期函数参数event的自带函数，需要传入一个promise，直到promise返回成功后这个生命周期才真正的完成，这意味着event.waitUntil()可能会推迟下个生命周期的执行（这个生命周期完成后才能去执行下个生命周期）

```javascript
self.addEventListener('install', event => {
  console.log('install: ', event)
  event.waitUntil(new Promise(resolve => {
    setTimeout(resolve, 5000) // 延迟五秒之后install才算完成，资源才能开始activate
  }))
})
self.addEventListener('activate', event => {
  console.log('activate: ', event)
  event.waitUntil(new Promise(resolve => {
    setTimeout(resolve, 5000) // 延迟五秒之后activate才算完成
  }))
})
```



## self.skipWaiting()

self.skipWaiting()，skipWaiting()用于强制停止Service Worker，并激活新的Service Worker。

self.skipWaiting()一般和event.waitUntil配合使用，当Service Worker脚本有更新，刷新页面都会强制自动卸载旧的并激活新的Service Worker。

```javascript
self.addEventListener('install', event => {
  event.waitUntil(self.skipWating())
})
```





## self.clients.claim()

self.clients代表sw控制的所有页面，调用self.clients.claim()事件可以让页面首次加载也受到sw的控制，而默认情况下首次加载页面是不受sw控制的。

```javascript
self.addEventListener('install', event => {
  console.log('install: ', event)
  event.waitUntil(self.skipWating())
})
self.addEventListener('activate', event => {
  console.log('activate: ', event)
  event.waitUntil(self.ClientRectList.claim())
})
```





# caches

缓存，控制sw往浏览器缓存中增加资源

在fetch生命周期拦截资源网络请求，先在缓存区中遍历，找到了返回缓存，没找到进行在进行请求

```javascript
// sw.js
self.addEventListener('install', function (e) {
  e.waitUntil(
    // caches.open()开启缓存app-v1，这个方法返回promise
  	caches.open('app-v1').then(
      function (cache) {
    		console.log('open cache')
        // 返回cache.addAll方法往app-v1缓存区中添加的文件
        return cache.addAll([
          './index.html',
          './index.css',
          './add.js'
        ])
    	}
    )
  )
})

// fetch事件中设定当有网络请求发送时先去缓存中找
self.addEventListener('fetch', function (e) {
  event.respondWith(
    // event.request是判断当前请求的是什么文件，使用caches.match方法遍历缓存区caches
  	caches.match(event.request).then(
    	function (res) {
        if(res) {
          // 如果缓存中找到了对应文件就无需发送请求,直接返回缓存
          return res
        } else {
          // 缓存中没找到就使用fetch发送请求
          fetch(url).then( res => {})
        }
      }
    )
  )
})
```





# 页面通信

页面js线程和sw线程进行相互通信，可以实现同一个sw管理下的两个不同的html页面传递消息



- 页面向sw发送消息

`window.navigator.serviceWorker.controller.postMessage(value)`

- 页面从sw接收消息

`navigator.serviceWorker.addEventListener('message',func`)

```javascript
// html1.js 
// 页面js线程发送消息
window.navigator.serviceWorker.controller.postMessage('hello')

// html2.js
// 页面接收sw发送的消息
window.navigator.serviceWorker.addEventListener('message', function (event) {
  console.log(event.data.message) //展示消息
})
```

```javascript
// sw.js
self.addEventListener('message', function (event) {
  var promise = self.clients.matchAll().then(
    // 分析所有sw管理的页面，除了发送消息的页面其他都应该接受消息
  	function (clientList) {
       var senderID = event.source ? event.source.id : 'unknow'
       clientList.forEach(
       	function(client) {
          // 判断遍历到的页面是不是发送消息的页面，不是则将消息转发到该页面
        	if(client.id === senderID) {
            return
          } else {
            // client.postMessage向接收页面发消息对象
            // 接收页面通过navigator.serviceWorker.addEventListener监听message事件来接收，event.data.message就是发送页面client.postMessage发送的消息对象
            client.postMessage({
              client: senderID,
              message: event.data
            })
          } 
        }
       )
    })
  event.waitUntil(promise)
})
```

