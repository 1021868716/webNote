# 页面

admin 管理页面

edit 编辑页面

login 登陆

detail 博客详情

index 首页

new 新建页面



# cross-env

运行跨平台设置和使用环境变量的脚本

```
npm install --save-dev cross-env
```



# nodemon

自动重启node文件

```
npm install --save-dev nodemon
```



# 接口设计

| 描述         | 接口             | 方法 | url参数                            | 备注                     |
| ------------ | ---------------- | :--: | ---------------------------------- | ------------------------ |
| 获取blog列表 | /api/blog/list   | get  | author：作者 ，keyword：搜索关键字 | 参数为空则不进行过滤查询 |
| 获取blog内容 | /api/blog/detail | get  | id                                 |                          |
| 新增blog     | /api/blog/new    | post |                                    | postData中携带新增内容   |
| 更新blog     | /api/blog/del    | post | id                                 | postData中携带更新内容   |
| 删除blog     | /api/blog/del    | post | id                                 |                          |
| 登录         | /api/user/login  | post |                                    | postData中携带用户名密码 |



# 原生实现路由

app.js是总的路由，引入客户端传入的路由信息，但总路由不适合有大量的路由代码处理，将具体功能拆分成User路由和blog路由，最后在app.js中进行合成总路由

例如User路由

```javascript
const handleUserRouter = (req, res, url) => {
  // 与User相关的接口
  const method = req.method // 获取请求方式

  // 登陆
  if (method === 'POST' && url.pathname === '/api/user/login'){
    return {
      msg: '登陆接口'
    }
  }  
}

module.exports = handleUserRouter
```

还有blog路由

...

将blog路由和User路由导入到总路由/src/app.js下

总路由接收到请求，将请求信息req传入blog/User的处理函数中，然后再将返回值写入处理结果res中，最后res返回给客户端

```javascript
let murl = require('url')
const handleBlogRouter = require('./src/router/blog')
const handleUserRouter = require('./src/router/User')

// 总路由，将blog请求分流到blog路由，User请求分流到User路由，未命中路由返回404
const serverHandle = (req, res) =>{
  // 将响应头中的Content-type设置为JSON格式
  res.setHeader('Content-type', 'application/json')

  const url = murl.parse(req.url,true)

  // 处理blog路由
  const blogData = handleBlogRouter(req, res, url)
  if(blogData){
    res.end(JSON.stringify(blogData))
    return
  }
  
  // 处理User路由
  const UserData = handleUserRouter(req, res, url)
  if(UserData){
    res.end(JSON.stringify(UserData))
    return
  }

  // 如果没有命中路由返回404
  res.writeHead(404,{"Content-type": "text/plain"})
  res.end('404,not found')
}

// 导出总路由
module.exports = serverHandle
```



# HTTP服务与路由分离

在根目录文件夹下创建一个bin文件夹，建立www.js文件封装http模块处理http请求

http模块的具体路由路径处理就是总路由app.js，所以需要引入

将www.js（HTTP模块）和app.js（HTTP里的路径判断逻辑）拆分开的话，如果后续有重构主需要修改app.js总路由和下属的路由就行

```javascript
const http = require('http')

const PORT = 8000
const serverHandle = require('../app')

const server = createServer(serverHandle)
// createServer中直接写回调等同于监听request事件在执行回调
server.listen(PORT)
```



# 数据查询与路由分离

将路由中对数据库进行查询的操作单独抽离到一个controller文件夹中，路由引入查询函数，通过解析url和请求拿到的请求数据，给查询函数传入参数，然后获取结果



# 启动服务

将路由绑定到www.js上后我们就可以启动这个http服务器了

通过nodemon启动这个服务，每次修改文件，就会自动刷新

```
nodemon .bin/www.js
```

通过postman通过测试跳转路由获取路由返回的response

```
http://localhost:8000/xxx
```



# 成功/失败对象

路由中返回一个对象，这个对象的具体要求我们用一个专门的类（SuccessModel，ErrorModel）来生成。

类生成的实例对象有一个errno属性（决定这次请求成功（0），失败（-1）），一个data属性（存放返回给客户端的数据）,message存放描述信息属性

创建一个model文件夹，创建resModel.js文件，在文件类创建一个类用于生成返回给客户端的对象，数据装入成功/失败对象的data中，描述信息装入message中，**最后将这个成功/失败对象JSON化后返回给客户端**。





# 处理post请求

```javascript
// 判断请求是否为post请求，请求体是否为JSON格式，是则resolve对象，不是则resolve空
const getPostData = (req) =>{
  return new Promise((resolve, reject) => {
    if(req.method !== 'POST') {
      resolve()
      return 
    }
    if(req.headers['content-type'] !== 'application/json') {
      resolve({})
      return
    }
    // 处理post请求体中的数据
    let postData = ''
    req.on('data', chunk => {
      postData += chunk.toString()
    })
    req.on('end', ()=>{
      if(!postData) {
        resolve({})
        return
      }
      // 将请求体中的json转化为对象
      resolve(JSON.parse(postData))
    })
  })
}

```



# 统一存放配置

新建conf文件夹将一些配置信息存放在这里统一管理

新建db.js存放操作数据库的配置并导出

```javascript
const env = process.env.NODE_ENV // 引入系统环境参数

let MYSQL_CONF = {}
// 根据系统的环境判断当前是什么情况，采取哪种配置
// 开发环境配置
if(env === 'dev'){
  MYSQL_CONF = {
    host: 'localhost',
    user: 'root',
    password: '123456',
    port: '3306',
    database: 'myblog'
  }
}
// 生产环境配置
if(evn === 'production'){
  MYSQL_CONF = {
    host: 'localhost',
    user: 'root',
    password: '123456',
    port: '3306',
    database: 'myblog'
  }
}

module.exports = {
  MYSQL_CONF
}

```





# 自定义工具连接数据库

src下新建一个db文件夹，存放操作数据库的js文件

新建mysql.js文件

```javascript
const mysql = require('mysql')
const { MYSQL_CONF } = require("../conf/db") // 引入mysql配置

// 创建连接对象
const con  = mysql.createConnection(MYSQL_CONF)

// 开始连接
con.connect()

// 创建函数执行sql语句
function exec(sql) {
  return new Promise((resolve, reject)=>{
    con.query(sql, (err, result) => {
      if(err) {
        reject(err)
      }
      resolve(result)
    })
  })
}

module.exports = {
  exec
}
```



# 封装redis存放session

在db文件夹下新建redis.js文件用于连接redis，并提供get/set方法查询或设置redis数据库数据 

```javascript
const redis = require('redis')
const { REDIS_CONF } = require('../conf/db')

const redisClient = redis.createClient(REDIS_CONF.port,REDIS_CONF.host)
redisClient.on('error', err=>{
  console.log(err)
})

function set(key, val) {
  if(typeof val === 'object'){
    //如果val是一个对象先转化为字符串形式，redis中存储的键值对必须为字符串类型
    val = JSON.stringify(val)
  }
  redisClient.set(key, val, redis.print)
}

function get(key) {
  return new Promise((resolve, reject) => {
    redisClient.get(key, (err, val)=>{
      if(err){
        console.error(err)
        return
      }
      // 没找到这个key，val为null，则将null给resolve回去
      if(val == null) { 
        resolve(null) 
        return 
      }

      // 尝试将拿到的字符串，转回为对象，无法转换就直接将字符串resolve
      try{
        resolve(JSON.parse(val))
      } catch {
        resolve(val)
      }
    })
  })
}

module.exports = {
  get,
  set
}
```



# 前后端联调

登陆验证功能使用cookie携带sessionId

cookie跨域不共享所以需要前后端同域，需要使用nginx做代理，让前后端同域

例如前端跑在8001端口，后端放在8000端口，前端就无法向后端发送cookie，需要使用nginx做反向代理



