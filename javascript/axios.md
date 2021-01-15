#  Axios

axios是前端最流行的ajax请求库

- 基于promise的异步ajax请求库
- 浏览器端/node端都可以使用
- 支持请求/响应拦截器
- 支持请求取消
- 请求/响应数据转换
- 批量发送多个请求

**axios返回一个promise对象，使用then，catch处理结果**

```javascript
axios.get('www.baidu.com').then((res)=>{
	console.log(res)
})
```

```
axios(config)
axios.request(config)
axios.get(url[, config])
axios.delete(url[, config])
axios.head(url[, config])
axios.post(url[, data[, config]])
axios.put(url[, data[, config]])
axios.patch(url[, data[, config]])
```



axios支持自动返回promise，所以可以直接链式调用then()

```javascript
1.安装axios
npm install axios --save

2.在main.js或组件中使用基础的axios(config)提交请求
config是axios的网络配置对象
最基础的包括
{
    url:'url'
}
其他还有一些配置属性
method请求方式
timeout超时时间
catch异常抛出等

import axios from 'axios'
axios({
    url:'http://123.207.32.32:8000/home/multidata',
    method:'get',
    timeout:5000,
    catch:'失败'
}).then((res)=>{
    console.log(res);
})
//axios支持返回promise，所以可以用then()
//method属性是请求方式get或post，不写的话默认为get请求
//也可以用最开始写的方式之一的axios.get直接调用get请求

```



## axios功能特点

- 在浏览器中发送 XMLHttpRequests 请求

- 在 node.js 中发送 http请求

- 支持 Promise API

- 拦截请求和响应

- 转换请求和响应数据

  

# axios请求

- **axios(config)**

- axios.get(url[, config])
- axios.post(url[, data[, config]])

- axios.delete(url[, config])
- axios.request(config)

- axios.head(url[, config])

- axios.options(url[, config])

- axios.put(url[, data[, config]])

- axios.patch(url[, data[, config]])

```javascript
axios({})
.then((response)=>{

})
```



# config配置请求对象

配置对象属性

```javascript
{
  // `method` 是请求的方法get,post,head等
  method: 'get', // 默认值get

  // `url` 是请求的接口地址
  url: '/user',

  // URL参数。必须是一个纯对象或者 URL参数对象
  // 会跟url属性拼接为最终的url
  params: {
    ID: 12345
  },

  // 如果url不是绝对路径，那么会将baseURL和url拼接作为请求的接口地址
  // 用来区分不同环境，建议使用
  baseURL: 'https://some-domain.com/api/',
  
      
  // 设置请求头
  headers: {
	'Content-Type': 'application/json'
  }
   
  // 请求体数据,只有当请求方法为'PUT', 'POST',和'PATCH'时可用
  data: {
    firstName: 'Fred'
  },

  // 请求超时时间（毫秒）
  timeout: 1000,

  // 是否携带cookie信息
  withCredentials: false, // 默认false

  // 响应格式,可选项 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // 默认值是json

  // 设置http响应内容的最大长度
  maxContentLength: 2000,

  // 定义可获得的http响应状态码
  // return true，则promise将resolved,否则将rejected
  validateStatus: function (status) {
    return status >= 200 && status < 300; // default
  },

  // 最大重定向次数
  maxRedirects: 5, // default

  // `httpAgent` and `httpsAgent` define a custom agent to be used when performing http
  // and https requests, respectively, in node.js. This allows options to be added like
  // `keepAlive` that are not enabled by default.
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // 代理
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  }

}

```



# parmas参数拼接

axios的parmas{key:value}对象属性，跟get请求相对应（拼接url），可以将get请求的url后面的参数放入params对象储存，自动拼接为get请求url完整字符串

```javascript
//原url：http://123.207.32.32:8000/api/hy/home/data?type=sell&page=1
axios({
  url:'http://123.207.32.32:8000/api/hy/home/data',
  params:{
    type:'sell',
    page:1
  }
}).then((res)=>{
  console.log(res);
})
```





# response

拿到的响应的对象结构

```javascript
axios.get('/user/12345')
  .then(function(response) {
    console.log(response.data);
    console.log(response.status);
    console.log(response.statusText);
    console.log(response.headers);
    console.log(response.config);
  });
```



```javascript
//response响应对象
{
  // 服务端返回的数据
  data: {},

  // 服务端返回的状态码
  status: 200,

  // 服务端返回的状态信息
  statusText: 'OK',

  // 响应头
  // 所有的响应头名称都是小写
  headers: {},

  // axios请求配置
  config: {},

  // 请求
  request: {}
}  
```



# 全局配置axios.defaults

全局默认配置

```javascript
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

因为很多url前缀都是重复的，很多参数也是重复的，所以可以将重复的信息抽离出来，或者利用axios的全局配置

常见配置选项
请求地址：`url: '/user'`
请求类型：`method: 'get'`
根路径：`baseURL: 'http://www.mt.com/api'`
自定义的请求头：`headers:{'x-Requested-With':'XMLHttpRequest'}`
URL查询对象：

```
params:{ id: 12 },
request body
data: { key: 'aa'}
```

超时设置：`timeout: 1000`
跨域是否带Token：`withCredentials: false`

设置语法 axios.defaults.配置选项 = 配置值
例如
`axios.defaults.baseURL ='' 设置默认url前缀`
`axios.defaults.timeout = 5000  设置超时时间`

```javascript
import axios from 'axios'
axios.defaults.baseURL = 'http://123.207.32.32:8000/api/hy';
axios.defaults.timeout = 5000
//设置url前缀全局url和超时时间配置
axios.all([
    //设置全局配置后可以省略前缀
    //超时时间也自动配置上了
    axios({url:'/home/multidata'}),
    axios({
        url:'/home/data',
        params:{
        type:'sell',
        page:1
        }})
]).then((res)=>{
    //res是两个url返回对象组成的数组
    console.log(res);
    console.log(res[0]);
    console.log(res[1]);
})
```



# axios.create()

创建一个新的axios

```javascript
let instance = axios.create([config])
// config里配置instance的baseURL等信息
```

```javascript
instance({
  url: '/xxx'
})
```



这个方法创建的axios没有取消请求和批量发送请求的功能，其他语法与普通axios一致

**为什么需要这个语法：**

​		如果项目中有部分接口与其他接口需要的配置不一样，就可以创建新的axios，每个都具有自己独特的配置，分别应用于不同要求的接口请求





# axios.all()

类似promise.all，批量执行多个异步请求，全部成功才执行回调

```
axios.all(promise)
```

有的业务需要同时并发发送多个网络请求，并且只有所有请求都成功才执行结果，可以使用axios.all([])，传入axios请求组成一个数组

```javascript
axios.all([
    axios({url:'http://123.207.32.32:8000/api/hy/home/multidata'}),
    axios({
        url:'http://123.207.32.32:8000/api/hy/home/data',
        params:{
        type:'sell',
        page:1
        }})
]).then((res)=>{
    //res是两个url返回对象组成的数组
    console.log(res);
    console.log(res[0]);
    console.log(res[1]);
})


也可以用axios.spread()方法将res展开
axios.all([
    axios({url:'http://123.207.32.32:8000/api/hy/home/multidata'}),
    axios({
        url:'http://123.207.32.32:8000/api/hy/home/data',
        params:{
        type:'sell',
        page:1
        }})
]).then(axios.spread((res1,res2)=>{
    console.log(res1);
    console.log(res2);
}))


```



# 取消请求

想要取消这个请求必须引入CancelToken类，并且在请求的config配置里对取消令牌属性（cancelToken）进行配置为CancelToken对象

取消请求是在发送请求之后，拿到请求结果之前调用的，例如发送了一个get请求，如果服务端设置了定时器间隔一段时间发送结果，我们在定时器触发之前调用了取消请求，那这个结果就拿不到了，peomise进入reject流程，取消函数cancel，cancel可以传入一个字符串参数，而reject中传入的err对象会被替换为一个cancel对象，这个对象内有一个message属性，可以看到这个字符串参数

`axios.isCancel(obj)`    判断对象是否是取消函数对象传入的cancel对象

```javascript
var CancelToken = axios.CancelToken;
var cancel;  // 用于保存取消请求的函数c

axios.get('/user/12345', {
// cancelToken是config里的一个属性，用于取消本次请求
// 属性值必须为axios.CancelToken的实例，所以先要引入axios.CancelToken
cancelToken: new CancelToken(function executor(c) { cancel = c; })
    // executor 函数会默认传入一个参数c，
    // c是一个函数，用于取消当前请求，参数c赋值给外部定义的变量cancel
    // 外部调用cancel即可取消请求
}).then(
    res => {
    // cancel只能拦截本次请求
    // 如果请求已经成功了还没执行cancel进行拦截，那cancel没有意义了，可以null等垃圾回收了
 	cancel = null
    console.log('请求成功',res.data)
  },
 	err => {
    //axios.isCancel(obj) 判断对象是否是取消函数对象传入的cancel对象
    if(axios.isCancel(err)){
     // err是cancel对象，则证明本次请求被取消
     cancel = null 
     //cancel函数没用了，因为这个axios.get请求已经结束了，cancel没有意义了
    }
 	console.log('请求失败',err.message) // 请求失败 取消请求
    // 取消请求之后err实际是取消函数传入的cancel对象，内含一个message属性
    // message就是取消函数cancel函数传入的参数字符串
 });  


// 发送请求之后，拿到结果之前外部执行cancel方法，取消请求
// 执行cancel之后，promise会进入失败的流程，err.message会变味cancel内的提示文本
// 取消请求之后在then中要将cancel = null避免内存泄漏
function cancelReq() {
if(typeof cancel == 'function'){
    // cancel可以传入一段提示文本，执行promise的reject流程时传入一个cancel对象当做err 
    cancel('取消请求');
  }else {
    console.log('没有可取消的请求')
  }
}

```







# axios.create创建实例

开发中可能会用到很多不同的url前缀或其他配置，这样全局配置就不是很合适了
这时可以使用axios.create()创建一个单独的实例，然后用这个实例发送请求，每个实例都可以拥有自己的配置

```javascript
//创建两个实例instance1，instance2
const instance1 =  axios.create({
    baseURL : 'http://123.207.32.32:8000/api/hy',
    timeout : 5000
})

const instance2 =  axios.create({
    baseURL : 'http://123.207.32.32:8000/api/hy',
    timeout : 10000
})

//分别用两个实例发送请求，一个实例可以发送多次请求
instance1({
    url:'/home/multidata',
}).then(res => console.log(res))

instance1({
    url:'/home/data',
    params:{
        type:'sell',
        pag:1
    }
}).then(res => console.log(res))


instance2({
    url:'/home/data',
    params:{
        type:'sell',
        pag:1
    }
}).then(res => console.log(res))


```



# 拦截器interceptors

**拦截器，通过请求拦截器和响应拦截器来进行数据处理**，在请求或响应被 `then` 或 `catch` 处理前拦截它们。

```js
// 在封装好的request.js文件中添加拦截器
import axios from 'axios'
export function request(config) {
  //创建实例
  const instance = axios.create({
    baseURL:'http://123.207.32.32:8000/api/hy',
    timeout:5000
  })
}
```



1.请求拦截：interceptors.request.use
axios.interceptors是全局请求拦截器，也可以单独拦截创建的axios.creat实例，`实例名.interceptors`就是请求实例拦截器
拦截器参数为两个函数：一个是拦截函数，执行拦截后的操作，一个是发送失败函数（很少被用到，是指还没拦截发送就失败了）。
拦截函数拿到的参数是发送给服务器的config网络配置，拦截函数会将config拦截了，我们还需要发送给服务器
所以最后必须将config发送服务器return config

```js
instance.interceptors.request.use(
  config=>{
    return config
    //console.log(config);
    //拦截函数
    //比如config的一些信息不符合要求
    //比如每次发送请求时都希望在界面中有加载动画
    //比如某些网络请求（比如登陆），必须携带一些信息（token等）
    //就需要在这里拦截
    //这里执行拦截的一些函数
    //执行完操作后继续向服务器发送网络配置       
  },err=>{
    //发送失败函数
    //很少到这里，比如请求超时等
    //可以在这里跳转超时页面
    console.log(err);
  })
```



2.响应拦截：interceptors.response.use
响应拦截也有两个函数，响应的成功拦截中，主要是对数据进行过滤
第一个一个函数的参数服务器返回的数据对象result，result是axios封装的返回对象，服务器发送的数据存在res.data内，对我们有用的只有res.data
响应拦截会拦截服务器返回的对象，拦截了我们就无法接收数据，所以还需要再将res.data返回回去，失败函数返回err错误信息（比如没有找到这个接口），响应的失败拦截中，可以根据status判断报错的错误码，跳转到不同的错误提示页面。

```js
instance.interceptors.response.use(
  res=>{
        //拦截到res先进行处理
        //最后再结果返回客户端
        //console.log(res)
    return res.data
  },
  err=>{
    console.log('响应的失败拦截')
    if(err&&err.response){
      switch(err.response.status){
        case 400:
          err.message='请求错误'
          break
        case 401
          err.message='未经授权的访问'
          break
        }
     }
     return err
  })
 //回到主页面发送网络请求
  return instance(config)
}
```



## 多个拦截器的执行顺序

**设置多个请求拦截器，先执行后设置拦截器，在执行之前的拦截器**

```javascript
axios.interceptors.request.use(
	config => {
        console.log('拦截到了成功请求1')
        return config //将处理后的请求继续向服务器发送
    },
    err => {
        console.log('拦截到了失败请求1') //较为少见
        return Promise.reject(err) //将处理后的请求返回给promise进行处理
    })  //请求拦截器
// 再设置一个请求拦截器，先执行后设置的第二个拦截器，在执行第一个拦截器
axios.interceptors.request.use(
	config => {
        console.log('拦截到了成功请求2')
        return config
    },
    err => {
        console.log('拦截到了失败请求2') 
        return Promise.reject(err)
    })  //请求拦截器2

//拦截到了成功请求2
//拦截到了成功请求1
```

**设置多个响应拦截器，先执行最先设置拦截器，在执行之后的拦截器**

```javascript
axios.interceptors.response.use(
    response => {
        console.log('拦截到了成功的处理结果1')
        return response //将处理后的请求返回
    },
    err => {
        console.log('拦截到了失败的处理结果1') //较为少见
        return Promise.reject(err) //将处理后的err结果返回给promise进行处理
    })  //响应拦截器


axios.interceptors.response.use(
    response => {
        console.log('拦截到了成功的处理结果2')
        return response 
    },
    err => {
        console.log('拦截到了失败的处理结果2') 
        return Promise.reject(err) 
    })  //响应拦截器2

//拦截到了成功的处理结果1
//拦截到了成功的处理结果2
```



# 封装axios

- 简单封装

根目录下创建一个network文件夹，文件夹内创建request.js文件用于封装axios，封装了一个request函数
参数config要求传入一个网络配置对象，最后返回axios.create的实例所返回的promise

```javascript
import axios from 'axios'
export function request(config) {
  //创建实例
  const instance = axios.create({
    baseURL:'http://123.207.32.32:8000/api/hy',
    timeout:5000
  })
  //instance(config)本身就会返回一个promise
  return instance(config)
}
```

这个文件导出的是一个request函数，这个函数就是我们封装的用于网络请求的api，在组件中调用时就使用request函数发送请求，向request传入网络配置对象，request返回的是promise，所以我们直接接上then和catch处理返回值即可

```javascript
import {request} from "../network/request";
//request返回的是promise所以能用then和catach
request({
  url:'/home/multidata'
}).then(res=>{
  console.log(res);
}).catch(err=>{
  console.log(err);
})
```



- ts+使用拦截器

```typescript
import axios, {AxiosInstance, AxiosResponse} from 'axios';

interface AjaxData<D=any> { // 与后端约定好的统一返回格式
  code: number;
  data: D;
  msg?: string,
  success?: boolean
}

// 创建axios实例instance
const instance: AxiosInstance = window.__axois__ = axios.create({
  timeout: 5000
});

// 使用拦截器拦截返回
instance.interceptors.response.use(
  async function success(response: AxiosResponse<AjaxData>) {
    // console.log('response：',response);
    // 成功返回todo
    return response;
  }, 
  async function onerror(error) {
    // console.log('error：',error);
    // 发生错误todo
    return Promise.reject(error);
  }
);

export default instance;
```

