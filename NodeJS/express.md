# Express

- 直接安装express

安装 Express 并将其保存到依赖列表中：

```
$ cnpm install express --save
```

以上命令会将 Express 框架安装在当前目录的 **node_modules** 目录中， **node_modules** 目录下会自动创建 express 目录。以下几个重要的模块是需要与 express 框架一起安装的：

body-parser - node.js 中间件，用于处理 JSON, Raw, Text 和 URL 编码的数据。

cookie-parser - 这就是一个解析Cookie的工具。通过req.cookies可以取到传过来的cookie，并把它们转成对象。

multer- node.js 中间件，用于处理 enctype="multipart/form-data"（设置表单提交文件）的表单数据。

```
$ cnpm install body-parser --save
$ cnpm install cookie-parser --save
$ cnpm install multer --save
```

安装完后，我们可以查看下 express 使用的版本号：

```
$ cnpm list express
/data/www/node
└── express@4.15.2  -> /Users/tianqixin/www/node/node_modules/.4.15.2@express
```



```javascript
// 创建index.js作为express的入口文件
const express = require("express");
const bodyParser = require("body-parser");
const cookieParser = require('cookie-parser'); 
// 创建服务
const app = express();

// 使用 body-parser中间件处理post请求的body
// bodyParser.urlencoded则用来解析form表单提交的数据
// 也就是请求头中包含这样的信息：Content-Type: application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: true }));
// bodyParser.json是用来解析json数据格式的。
app.use(bodyParser.json());

// 注册cookie模块，在路由中可以使用req.cookie访问cookie
app.use(cookieParser());

// 创建路由
app.post("/login", function (req, res) {
    console.log(req.body);
    res.send(req.body);
});

// 监听服务
app.listen(3000, function () {
    console.log("server start 3000");
});
```







- 使用express-generator脚手架

express-generator是express常用的脚手架工具，

```
cnpm install express-generator -g
```

创建一个express项目

```
express projectName
```

express-generator目录

1. bin/www.js  

   开启http服务的配置文件，例如启动http服务，绑定端口号等，开启服务执行此文件，`npm start`启动应用，其实就是调用`node ./bin/www`。

2. routes         路由文件夹

3. app.js          express入口文件，负责分发路由

4. public          静态文件目录文件夹，前后端不分离时使用，例如images，css，js等

5. views           html模板文件夹，前后端不分离时需要在后端集成html



# app.js

使用脚手架安装的express项目的路由配置app.js如下

```javascript
var createError = require('http-errors'); // http错误模块
var express = require('express');
var path = require('path'); // path模块
var cookieParser = require('cookie-parser'); 
// cookie解析模块，提供api：req.cookie可以直接访问cookie
var logger = require('morgan'); // 日志记录模块

// 引用routes中的路由文件
var indexRouter = require('./routes/index');
var usersRouter = require('./routes/users');

var app = express(); //初始化app，生成本次http请求的实例

// 注册view组件，view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');

//use进行模块和中间件注册
app.use(logger('dev'));  //注册日志模块
app.use(express.json()); // 解析post请求请求体，路由中通过req.body获取post中键值对格式的数据
app.use(express.urlencoded({ extended: false })); // 解析post中其他数据（请求头）
app.use(cookieParser()); // 注册cookie模块，在路由中可以使用req.cookie访问cookie
app.use(express.static(path.join(__dirname, 'public'))); // 返回访问的静态文件

// 引用routes中的路由文件后还需要注册路由，进行路由分发
app.use('/', indexRouter);
app.use('/users', usersRouter);

// 以上路由都没有结果则注册404页面
app.use(function(req, res, next) {
  next(createError(404));
});

// 注册异常处理抛出函数
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  // 开发环境抛出错误对象，生产环境抛出空对象，防止用户获取到错误对象
  res.locals.error = req.app.get('env') === 'dev' ? err : {};

  // 异常抛出http500状态码
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app;
```





# routes

routes文件夹下的文件是对后端路由进行配置

解析get请求是router.get()

第一个参数是子路径，router.get的子路径是`/`，参数路径会跟前面的路由注册的路径进行拼接，app.js中注册的`app.use('/', indexRouter)`，最终路径就是`/+/`（还是/），例如`app.use('/users', usersRouter)`，则最终路径时`/users+/`（还是/users）

第二个参数是回调函数，回调函数中的第一个参数req是发送过来的get请求的信息，第二个参数res是我们返回的response的信息，第三个参数next是一个函数，调用后会访问下一个命中的子级路由

```javascript
// routes/index.js
var express = require('express')
var router = express.Router()

// 子路径/会和app.js中注册这个路由的路径进行拼接
router.get('/', function(req, res, next) {
    res.render('index', {title: 'Express'})
})

module.exports = router
```



## 新建路由

```javascript
// routes/blogs
var express = require('express')
var router = express.Router()

router.get('/list', function(req, res, next) {
    let data = {
        errno: 0,
        data: 'ok'
    }
    // 将data进行json化，然后返回给客户端
    res.json(data)
})

router.get('/detail', function(req, res, next) {
    let data = {
        errno: 0,
        data: 'wtw'
    }
    res.json(data)
})

// app.js中使用了app.use(express.json());模块
// 所以可以使用req.body直接获取post请求体，不用自己解析
router.post('/login', function(req, res, next) {
    const { username, password } = req.body
    let data = {
        errno: 0,
        data: 'success'
    }
    res.json(data)
})

module.exports = router
```

```javascript
// app.js
// 引入路由文件
var blogRouter = require('./routes/blogs');
// 注册路由
app.use('/api/blog', blogRouter)
```

客户端访问`/api/blogs/list`，就会拿到blogs路由中json化的data对象`{"errno": 0, "data":"ok"} `，访问`/api/blogs/detail`，就会拿到`{"errno": 0, "data":"wtw"}`





# 中间件

在app.js中有很多app.use，并且在路由的回调函数第三个参数是next函数，这些都跟中间件有关。中间件就是app.use()中的回调函数。express中间件函数必须有req，res，next三个参数。

app.use()是监听路由并执行中间件。通过这种方式处理路由和脚手架中通过引入文件在app.use()的方式结果是一样的。

**app.use()中的中间件通过next进行串联**，回调函数的第三个参数next函数，就是去执行下一个命中的app.use()，如果没有执行next，这个请求就到此为止了，不访问下一个命中的app.use()开始返回数据了。如果next接收一个参数，则代表抛出一个错误，参数为错误文本，抛出错误以后，后面的中间件将不再执行，直到发现一个错误处理函数为止。

```javascript
function uselessMiddleware(req, res, next) {
  next('出错了！'); // 抛出错误，后面的next不再执行
}
```



```javascript
const express = require('express')

// 创建本次http请求的实例app
const app = express()

// 访问所有路由首先进入这个回调，所有路由都会命中没路径参数的app.use()路由
app.use((req, res, next) => {
    console.log('请求开始', req.method, req.url)
    req.cookie = {
        name: 'abc'
    }
    setTimeout(() => {
        next()// 500ms后执行下一个命中的app.use()
    }, 500) 
})

// 访问/api的所有请求(get,post)执行这个回调
app.use('/api', (req, res, next) => {
    console.log('/api')
    next()
})

// 访问/api的get请求执行这个回调
app.get('/api', (req, res, next) => {
    console.log('get请求访问/api')
    next() // 访问下个命中的app.use()
})

app.get('/api/get', (req, res, next) => {
    res.json({
        errno: 0,
        data: 'success'
    })
    console.log('get请求访问/api/get')
})


// 访问/api的post请求执行这个回调
app.post('/api', (req, res, next) => {
    console.log('post请求访问/api')
    // 没有调用next()，所以访问/api/post时，请求到这里就返回了，不会去访问下个命中的app.use()
})

// 这个app.use()不会被命中，因为父路由没有调用next()
app.post('/api/post', (req, res, next) => {
    console.log('post请求访问/api/post')
})


// 最后需要配置404路由
// 如果请求运行到最后的404路由，说明上一个路由函数还有next()没有返回数据，并没有找到模板路由
// 例如get请求访问/api/abc，最后一次命中的路由/api回调中还有next()
// 但没找到/api/abc，因为所有路由都会命中没路径参数的app.use()，最后运行到这里命中了，需要返回404
app.use((req, res, next) => {
    console.log('404')
    res.json({
        errno: 0,
        data: '404'
    })
})

app.listen(3000, () => {
    console.log('服务在3000端口运行')
})
```



中间件函数通过next串联，一个app.use()中可以插入多个中间件函数

```javascript
function loginCheck(req, res, next) {
  setTimeout(() => {
    next()
  }, 1000)
}

function login(req, res, next) {
  res.json({
        errno: 0,
        data: 'success'
    })
}

app.get('/login', loginCheck, login)
```



# 中间件原理

- app.use()来注册中间件，将中间件收集起来
- 遇到http请求，根据path和method来命中
- 实现next机制，触发下一个命中的中间件

```javascript
const http = require('http')

// 创建app的构造函数newExpress
class newExpress {
  constructor() {
    this.routes = {
      all: [], // 收集app.use中间件
      get: [], // 收集app.get中间件
      post: [] // 收集app.post中间件
    }
  }
  
  // 提取路径以及中间件函数的方法register
  // app注册中间件时第一个参数为路径（可省略），第二个参数为中间件函数
  register(path) {
    const info = {}
    if (typeof path === 'string') {
      // 第一个参数为字符串，说明传入了路径
      info.path = path
      // 将第二个参数中间件函数以及之后的参数中间件函数组合成数组存入info
      info.stack = Array.prototype.slice.call(arguments, 1)
    } else {
      info.path = '/'
      info.stack = Array.prototype.slice.call(arguments, 0)
    }
    return info
  }

  use() {
    const info = this.register.apply(this, arguments)
    this.routes.all.push(info)
  }

  get() {
    const info = this.register.apply(this, arguments)
    this.routes.get.push(info)
  }

  post() {
    const info = this.register.apply(this, arguments)
    this.routes.post.push(info)
  }

  // match方法是根据url和method过滤出需要访问的中间件函数
  match(method, url) {
    let stack = []
    if (url === '/favicon.ico') {
      return stack
    }

    // 获取all+method的中间件组成新数组curRoutes
    let curRoutes = []
    curRoutes = curRoutes.concat(this.routes.all) // all所有路由都命中
    curRoutes = curRoutes.concat(this.routes[method]) // 根据method判断命中get/post
  
    // 从curRoutes中判断命中的路由添加进stack中
    curRoutes.forEach(routeInfo => {
      // indexOf判断路径是否命中，命中后往中间件列表stack中添加
      if(url.indexOf(routeInfo.path) === 0) {
        stack = stack.concat(routeInfo.stack)
      }
    })
    return stack
  }

  // 模拟next机制
  handle(req, res, stack) {
    const next = () => {
      // 取出队首的中间件
      const middleware = stack.shift()
      if(middleware) {
        // 执行取出的中间件函数
        middleware(req, res, next)
      }
    }
    next() //执行定义的next函数
  }

  callback () {
      return (req, res) => {
      // 定义一个res.json方法返回客户端json对象
      res.json = (data) => {
        res.setHeader('Content-type', 'application/json')
        res.end(
          JSON.stringify(data)
        )
      }

      const url = req.url
      const method = req.method.toLowerCase()
      // match方法是根据url和method判断需要访问的中间件列表，然后依次执行
      const resultList = this.match(method, url)
      this.handle(req, res, resultList)
    }
  }
  // 监听端口号，开启http服务
  listen(...args) {
    const server = http.createServer(this.callback())
    server.listen(...args)
  }
}

// 工厂模式函数
module.exports = () => {
  return new newExpress()
}
```





# express-session

可以使用express-session中间件快速实现session校验和cookie配置，使用connect-redis连接redis存储session

中间件第一个参数是加密字符串密匙，第二个参数是cookie配置对象

配置中间件后可以在路由通过req.session来访问session

```
npm install express-session
```

```javascript
var session = require('express-session')

// 使用express-session中间件配置session和返回的cookie
app.use(session({
   secret: 'WJiol_8776#',
   cookie: {
       // path: '/', // 默认值/
       // httpOnly: true, // 默认值true
       maxAge: 24 * 60 * 60 * 1000 //24小时后cookie失效
   }
}))
// 配置之后可以通过req.session来访问session

// 然后使用seesion达到记录状态的目的
app.get('/api', (req, res, next) => {
    var session = req.session // 第一次访问肯定为空，初始化为0
    if(!session.num)
    {
        session.num = 0
    }
    session.num++
    res.json({
        num: session.num
    })
})
```



# 日志

access log记录，直接使用脚手架推荐的工具morgan

自定义日志使用console.log和console.log.error可以通过PM2工具写入文件

```javascript
// app.js中默认的日志配置
var logger = require('morgan');
app.use(logger('dev'));
```

修改为combined模式，可以修改配置将日志输出在文件中

```javascript
const logFilename = path.join(_dirname, 'logs', 'access.log') // 获取读取写入的文件路径
// 创建写入流
const writeStream = fs.createWriteStream(logFileName, {
    flags: 'a'
})
app.use(logger('combined', {
  // stream: process.stdout // 默认的配置，日志输出在控制台
  stream: writeStream // 用写入流将日志写入对应文件
}));
```





# express缺点

express中有大量的回调函数，异步回调带来了很多问题，Promise也不能解决所有问题

而koa2框架no callback，原生支持async/await语法，koa把 express 中内置的 router、view 等功能都移除了，使得框架本身更轻量化。