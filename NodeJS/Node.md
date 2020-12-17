# Node
Node是一个基于Chrome V8引擎的js代码运行环境，浏览器是js运行环境，Node也是js运行环境，是前端人员适合使用的后台开发环境，为js提供了访问数据库，文件读写等能力。

**在 Node 应用程序中，执行异步操作的函数将回调函数作为最后一个参数， 回调函数接收错误对象err作为第一个参数。**

Node单线程事件驱动，异步执行，提高IO速度和效率。

node三大特点：

- 事件驱动

  node本身提供了事件对象，帮助我们快速订阅者模式以及观察者模式，或者事件模式

  事件绑定：`event.on(eventName,callback)`

  事件触发：`event.emit(eventName)`

- 非阻塞IO模型（异步）

  Async 异步

  Sync   同步

- 轻量和高效



## node可以用来干嘛

- 做中间层对接前后端
- 做项目构建工具，例如webpack，vue-cli
- 做一些小型网站的后端



## Nodejs和javascript的区别

**JavaScript**：
 dom和bom是浏览器为操作页面提供的api

- `ECMAScript`(语言基础，如：语法、数据类型结构以及一些内置对象)
- `DOM`（一些操作页面元素的方法）
- `BOM`（一些操作浏览器的方法）

**Nodejs**：

- `ECMAScript`(语言基础，如：语法、数据类型结构以及一些内置对象)

- `os`(操作系统)

- `file`(文件系统)

- `net`(网络系统)

- `database`(数据库)

ECMAscript是js语言核心

而Node.js是由ECMAscript及Node环境提供的一些附加api组成的（包括文件，网络，路径等api）



## 命令行中node环境下执行js文件

  在命令行中进入js文件的工作目录

  命令行中运行命令：`node [fileName]`  ，就能在命令行中运行js文件

  例如运行index.js文件：`node index.js`





# 命令行

## PASH系统环境变量
命令行工具运行命令时会先去工作目录下寻找应用，没有的话会去系统环境变量pash中去查找应用，pash的作用是存储系统中的目录，在命令行中执行命令的时候系统会自动去这些目录中查找命令的位置。

简而言之，将应用的目录（文件地址）放入系统环境变量pash中，就能直接在命令行工具中运行该应用，不用进入该文件的工作目录中再运行。



## 命令行命令

```html
cd 进入
./当前目录	
../上级目录
cd ../  进入上级目录
在目标文件夹中按住shift再点击鼠标右键，选择打开命令行窗口
则当前命令行的工作窗口就在当前文件夹目录下了
在命令行中输入文件前几个字符再按tap键会自动补全文件名
按方向上键，切换到上一次命令
命令clear，会清空当前窗口的输出
```


## 常见node命令

1. `npm -v`：查看npm版本。
2. `npm init`：初始化后会出现一个`package.json`配置文件。可以在后面加上`-y` ，快速跳过问答式界面。
3. `npm install`：会根据项目中的`package.json`文件自动下载项目所需的全部依赖。
4. `npm install 包名 --save-dev`(`npm install 包名 -D`)：安装的包只用于开发环境，不用于生产环境，会出现在`package.json`文件中的`devDependencies`属性中。
5. `npm install 包名 --save`(`npm install 包名 -S`)：安装的包需要发布到生产环境的，会出现在package.json文件中的`dependencies`属性中。
6. `npm list`：查看当前目录下已安装的node包。
7. `npm list -g`：查看全局已经安装过的node包。
8. `npm --help`：查看npm帮助命令。
9. `npm update 包名`：更新指定包。
10. `npm uninstall 包名`：卸载指定包。
11. `npm config list`：查看配置信息。
12. `npm 指定命令 --help`：查看指定命令的帮助。
13. `npm info 指定包名`：查看远程npm上指定包的所有版本信息。
14. `npm config set registry https://registry.npm.taobao.org`： 修改包下载源，此例修改为了淘宝镜像。
15. `npm root`：查看当前包的安装路径。
16. `npm root -g`：查看全局的包的安装路径。
17. `npm ls 包名`：查看本地安装的指定包及版本信息，没有显示empty。
18. `npm ls 包名 -g`：查看全局安装的指定包及版本信息，没有显示empty。







# 安装Node.js

在官网下载node安装后，node会自动加入系统环境path中。

- LTS稳定版，具有长期支持
- Current 实验版，具有最新特性
```html
安装错误2502，2503 ：权限不足
解决：需要用以管理员身份打开命令行工具运行命令：msiexec/package node安装包全路径
node-v后出现错误：node安装目录写入环境变量失败
解决：重新写入node系统环境变量
```



## 启动Node

1.打开命令行工具
2.输入命令`node`，该命令作用是查看node版本(version-版本)</br>
3.输出node版本号则证明安装node成功



# nvm

nvm，nodejs版本管理工具，可以切换多个nodejs版本

下载安装后根据命令控制node版本



## nvm命令

nvm list      	查看当前所有的node版本



nvm install v10.13.0 	安装指定版本的node



nvm use --delete-prefix  8.12.0   切换node版本 







# CommonJS

CommonJS是node支持的模块化语法规范

CommonJS：module.exports，exports，require

## module.exports/exports

module.exports才是最终导出模块对象
exports只是module.exports导出模块对象的一个别名指针
导出单个属性的时候两者作用完全相同
但exports不能用于导出对象，否则就是修改了原本指针指向，系统就找不到导出模块
导出文件文件最终导出的对象永远是module.exports
但module.exports={}只能导出一个对象
如果需要导出多个对象不能再来一次module.exports={}(上一个导出对象会被覆盖)

`module.exports.对象名={}`，这样可以用于导出多个对象而不怕被覆盖

```javascript
var flag = true;
function sum(num1,num2){
    return num1+num2;
}
let a = 123
let b = 234
console.log(a)
//属性分开各自导出，exports不能导出对象，只可以导出单个属性
exports.a = a
module.exports.b = b;

//module.exports还可以直接导出一个对象，包含所有导出属性
//exports不能导出对象
//注意module.exports导出对象会直接覆盖之前的所有导出属性的导出
module.exports={
       flag:flag,
       sum:sum
}
```

## 导入：require

```javascript
第一次执行导入时会先运行一次导出文件用于初始化
后续再发生导入该文件操作时不会再次运行导出文件

var a = require('导出文件路径');
//打印123
//第一次导入会运行一次导出文件，所以会打印一次123
var flag = a.flag;
var sum = a.sum;
//var {flag,sum} = require('导出文件路径')
//语法糖写法，一步当以上三步
```

## commonjs特点

1、CommonJs导出的是变量的一份拷贝，ES6 Module导出的是变量的绑定(export default是特殊的)
2、CommonJs是单个值导出，ES6 Module可以导出多个
3、CommonJs是动态语法可以写在判断里（即最终可能并不加载运行），ES6 Module静态语法只能写在顶层，前者属于编译时加载，即静态加载，在编译时就能够确定模块的依赖关系，以及输入和输出的变量；后者属于运行时加载，都只有在代码运行时才能确定这些东西。
4、CommonJs的this是当前模块，ES6 Module的this是undefined

5、import()属于异步加载，require()属于同步加载。







# Node.js模块化开发

js存在两大弊端，文件依赖不明确和文件依赖的命名冲突

模块化开发中有明确表明文件依赖关系，模块化开发文件与文件之间是半开放状态，一个功能就是一个模块，多个模块可以组成完整应用

抽离其中一个模块不会影响其他功能，形成低耦合高内聚



## Node.js模块开发规范

node环境只支持CommonJS模块化规范，不支持ES6模块化

```javascript
//a.js
let version = 1.0;
const sayHi = name => `hello,${name}`
exports.sayHi = sayHi;
exports.version = version;  
//将属性或方法封装进exports对象，成为它的属性或方法，exports对象的属性（方法）名随便起
//exports是module.exports对象的别名（地址引用），导出对象最终以module.exports对象为准

//b.js
let a = require('./a.js');
//引入a模块导出的内容封装进a对象，.js后缀可以省略
console.log(a.version);
Console.log(a.sayHi('小明')); 
```



# npm

第三方模块又叫包，第三方模块具有两种存在形式，以js文件形式存在，提供实现项目具体功能的api接口，或者以命令行工具形式存在，辅助项目开发

npmjs.com：第三方模块存储和分发仓库网站，国内下载地址 npm.taobao.org

npm(node package manager): node集成的第三方模块管理工具



## npm下载

命令行工具一般用全局安装，所有项目都能使用，库文件一般是本地安装，只有当前目录使用。

- -g

全局安装： 	`安装命令 + -g`

开发时依赖则局部安装： `安装命令 + --save-dev`

```
下载包命令：
npm install [name] -g(默认下载到命令行当前工作目录下)

删除包：
npm uninstall [name] -g
```



- -s

```
即--save（保存）
包名会被注册在package.json的dependencies里面，在生产环境下这个包的依赖依然存在
```

npm insatll -s 就是npm install --save 安装到生产环境 如 vue ,react 等



- -d

```
即--dev（生产）
包名会被注册在package.json的devDependencies里面，仅在开发环境下存在的包用-D，如babel，sass-loader这些解析器
```

npm install -d 就是 npm install --save-dev 安装到开发环境 例如 gulp ，babel，webpack 一般都是辅助工具



- 啥也不写

```
包名不会进入package.json里面，因此别人不知道你安装了这个包
```



- 下载指定版本

```
npm i 包名@版本号
```





## 导入第三方模块或文件

下载第三方模块或文件后需要在用require导入
例如：`const fs= require('fs');  ` 导入文件操作模块

1.require('第三方包名')优先加载该包的文件同级目录下的node_modules目录下去查找相关包

2.找到该第三方包中的pageage.json文件，并找到该报里面的main属性对应的入口模块加载该包

3.如果该包的pageage.json中没有main属性的话则会默认加载该包的index.js文件

4.如果在加载第三方模块的文件同级目录下没找到node_modules目录，则会向上一级文件夹中寻找

5.如果到磁盘根路径下都没找到，则会报错：can not find module xxx





## npm淘宝镜像

也可以使用npm淘宝镜像来加快下载速度

```
npm install -g cnpm --registry=https://registry.npm.taobao.org

linux系统需要在npm前加$符号
```

切换淘宝镜像之后命令npm变为cpn

```
cnpm install [name]
```



## 发布一个npm包

1. 创建包文件夹

2. npm包的初始化

```
npm init
```

3. npm包信息的设置

```JavaScript
//package.json
{
  "name": "lcfs",
  "version": "0.1.0",
  "description": "将原生的fs模块进行promise封装，可以快速的使用async_await模式",
  "main": "lcfs.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "fs",
    "promise封装"
  ],
  "author": "wtw",
  "license": "ISC"
}
```

4. 注册NPM官网账号

5. NPM官网账号需要邮箱验证

6. 本机登陆NPM

```
npm login
```

7. 发布NPM包

```
npm publish
```



# node工具

## nodemon命令行工具

nodemon是一个命令行工具，辅助项目开发，在node.js中每次修改文件都要在命令行工具都要重新执行文件

使用nodemon后，nodemon会监控文件的保存操作，文件修改保存后会自动在命令行重新执行该文件
	使用步骤：

1. 使用命令 `npm install nodemon-g` 下载nodemon
2. 运行文件时用   `nodemon [fileName]` 替代  `node [fileName] `
3. ctrl+c键可以中止nodemon操作



## nrm命令行工具

nrm下载地址切换工具，因为npmjs.com在国外下载速度较慢,使用nrm切换为国内的下载地址加快下载速度。

​	使用步骤

1. 使用命令下载nrm命令：`npm install nrm-g`
2. 查询可用下载地址列表命令： `nrm ls`
3. 切换npm下载地址命令： `nrm use 下载地址名称`
4. 运行卡死时清除缓存命令: `npm clean cache -force`







# 常用模块

一、HTTP模块

作用：处理网络客户端的请求



二、URL模块 

作用：处理客户端请求过来的url



三、Query Strings模块

作用：处理客户端通过get/post请求传递过来的参数 



四、File System模块

作用：在服务端操作文件,可能是需要将浏览器上传的图片保存到服务器,也可能是需要将服务器的资源读取之后返回给浏览器。



五、Path模块

作用:操作文件的路径,为文件操作服务

常用的几个函数: path.join(第一个路径,第二个路径) : 拼接路径



六、Global模块

作用:全局共享的,不需要导入模块即可以使用
Global模块常用的属性:

​    `__dirname` : 文件所在的文件夹路径

​    `__filename` : 文件所在的路径

​    `require()` :  导入模块

​    `exports` :  导出模块

​    `module` :    自定义模块时用到





# 读写模块

node运行环境提供的，模块化的方式开发出来的自带api

但只有引入系统模块以后才可以使用相应的api

## 文件操作：fs模块

引入fs系统模块:

```const fs= require('fs');
const fs= require('fs');
```
```JavaScript
path是目标文件的地址

mode是读取模式，一般为默认值0o666,可以不写

options是缓冲区对象
   options:{flag:'flags',encoding:'encoding'}

			flag是当前操作类型
        		'a': 打开文件用于追加，如果文件不存在将创建该文件
                //append
                
				'r': 打开文件用于读取，如果文件不存在将出现异常
                //read
                
				'r+': 打开文件用于读取或写入，如果文件不存在将出现异常
                
				'w': 打开文件用于写入，如果文件不存在将创建该文件，如果文件存在则截断文件
                //write
                
			encoding是文件编码
        		'utf-8'：通用编码
```





### buffer缓冲区(了解)

buffer是一个内存缓冲区（将数据内容以二进制进行存储到内存缓冲区）,类似数组
buffer会在内存开辟固定大小，连续的空间存放数据
但普通数组不能进行二进制数据的操作而且js数组不像其他语言数组效率高
所以node为了提高数组性能提供了buffer缓冲区，可以直接操纵内存

```javascript
Buffer.alloc(size[,fill[,encoding]])
//开辟一块空间并把原来的数据清空，拿到一块空白的内存空间存放数据
Buffer.allocUnsafe(size) //Unsafe 不安全的
//开辟一块空间，原来的内容不清空，可以直接操纵原本空间内的数据

var str = 'hello,world'
let buf = Buffer.from(str)//将字符串转为buffer对象
console.log(buf)//buf对象显示时是16进制，因为二进制太长了
console.log(buf.tostring)//将buffer对象转换为字符串

//开辟一个新的buffer空间BUffer.alloc()
let buf1 = Buffer.alloc(10) //10个字节大小的buffer
buf1[0] = 10 //将10存入缓冲区第一个位置


//Buffer.allocUnsafe() 直接操作原本存在内存空间读写数据，性能更高，但容易出现内存泄漏等问题
let buf2 = Buffer.allocUnsafe(10)
console.log(buf2)
```



### 读取操作原理(了解)

```javascript
node中读取操作也有同步和异步接口。（读写都推荐异步）

open接口用于取文件放进内存，但只放进内存后我们并不能读取到文件内容。
	fs.open()/fs.openSync()
read接口用于读文件，再把文件从内存中读取出来。
	fs.read()/fs.readSync()
因为太过繁琐，所以一般直接使用封装好的readFile接口


异步取接口：fs.open()
同步取接口：fs.openSync(path[,flags,mode])
var fd = fs.openSync('./1.txt','r') //创建一个取接口的对象


异步读接口：fs.read()
同步读接口：fs.readSync(fd,buffer,offset,length,position)
fd			取接口对象

buffer      缓冲器对象，内存中开辟出来的缓冲区，类似数组存放数据,缓冲区的数据访问速度较快。
			用Buffer.alloc()创建该对象。
			Buffer.alloc(size[,fill[,encoding]])

offset      buffer写入的偏移量

length     （integer）指定文件读取字节数长度

position   （integer）指定文件读取的起始位置，如果该项为null，将从当前文件指针的位置开始读取数据。
```



### 读取操作：fs.readFile()

传统的读取操作需要open又需要read才能读取文件，十分繁琐，所以提供了封装好的读取api

fs.readFile  		   异步读取
fs.readFileSync	 同步读取

```javascript
fs.readFileSync(path[,options])

var content = fs.readFileSync('1.txt',{flag:'r',encoding:'utf-8'})
console.log(content)
```

```javascript
fs.readFile(path[,options], callback)
//异步读取有一个回调，读取成功后的操作需要在回调中执行
//callpack是回调函数，获取文件读取的结果，文件编码如utf-8等
//fs.readFile的回调函数中有两个参数err和data
//也可以fs.readFile('读取文件的相对路径','[文件编码]',callback);
//如果文件读取发生错误，参数err的值就返回错误对象，否则err的值为null
//data参数时读取到的文件内容
fs.readFile('1.txt','utf-8',(err,data)=>{
	if(err){
		Console.log(err);
	}else{
        Console.log(data);
    }
})
```



```javascript
将读写api封装为一个方法返回promise，避免太多回调
function FsRead(path){
	return new Promise(function(resolve,reject){
		fs.readFile(path,'utf-8',(err,data)=>{
			if(err){
        reject(err);
			}else{
        resolve(data);
      }
		})
	})
}

读取文件操作
var w1 = FsRead('1.txt')
w1.then(function(res){
	console.log(res) //打印文件内容
})

封装的好处在于有顺序读取多个文件时避免频繁回调
```



### 写入文件：fs.writeFile()

```javascript
写入文件api:
fs.writeFile(path,data[,options],callback);  	异步写入
fs.writeFileSync(path,data[,options]);  		同步写入
     
//flag为w时每次写入都会覆盖之前的内容，改为a则变为追加
//fs.writeFile的callback只有一个err参数
//如果写入成功，err等于null，如果失败err返回一个对象包含错误信息
//向index.html写入const里的文字
const content = '<h3>使用fs.writeFile 写入文件<h3>';
fs.writeFile('index.html',const,{flag:"a",encoding:"utf-8"},err=>{  
	if(err != null)
    {
    	console.log(err,'写入出错');
    	return;
	}else{
		console.log('写入成功');
	}
})

```
```javascript
因为fs.writeFile是异步写入，每次写入操作之间没有顺序，如果想要顺序的写入需要在回调中再写入
但频繁的回调写入就会造成回调地狱，所以推荐将写入操作封装返回promise方便调用
function writefs(path,content){
    return new Promise(function(resolve,reject){
        fs.writeFile(path,const,{flag="a",encoding="utf-8"},err=>{  
			if(err != null)
   		 	{
    			reject(err);
			}else{
                resolve(err);
			}
		})
    })
}

此后有顺序的写入大量内容时就可以调用writeFs方法
async function write(){
    await writefs('index.html','今天吃什么/n');
    await writefs('index.html','今天吃烧烤/n');
}
write();
```



### 删除文件：fs.unlink()

```javascript
fs.unlink(path,callback)

path：文件路径
callback：删除后的回调
```



### 读取目录：fs.readdir()

```javascript
fs.readdir(path,callback)
可以读取出该目录下的文件在回调函数中操作

fs.readdir("../fs",function(err,files){
	if(err){
		return console.log(err)
	}
	//将文件数组遍历成单个文件并输出
	files.forEach(function(file){
		console.log(file)
	})
})

回调函数callback有两个参数错误对象err和文件夹下文件组成的数组files
```



### 删除目录：fs.rmdir()

```javascript
fs.rmdir(path,callback)
删除该目录

fs.rmdir("../fs",function(){
	console.log('删除成功')
})
```



### 重命名：fs.rename()

```
fs.rename(oldPath,newPath,callback)
```



### 读取文件状态：fs.stat(path,callback)

```javascript
fs.stat("./wenjian.txt",function(err,stats){
    console.log(err);
    console.log(stats);
//    获取文件的大小；
    console.log(stats.size);
//    获取文件最后一次访问的时间；
    console.log(stats.atime.toLocaleString());
//    文件创建的时间；
    console.log(stats.birthtime.toLocaleString());
//    文件最后一次修改时间；
    console.log(stats.mtime.toLocaleString());
//    状态发生变化的时间；
    console.log(stats.ctime.toLocaleString())
//判断是否是目录；是返回true；不是返回false；
    console.log(stats.isFile())
//    判断是否是文件；是返回true、不是返回false；
    console.log(stats.isDirectory())
})
```





### 文件流：Stream

文件写入流输出流也属于fs模块，主要用于写入读取大文件

如果文件过大，内存放不下就无法进行普通的文件读取写入，此时就需要文件写入流读取流来对文件进行写入或读取

#### 文件写入流

使用写入流：

	1. 引入fs模块
 	2. 创建写入流对象，语法：`fs.createWriteStream(path[,options])`
 	3. 监听并执行事件：`事件类型：open,finish,close`
 	4. 文件流式写入：`写入流对象.write(data,callback)`
 	5. 最后需要关闭写入流进程：`写入流对象.end(callback)`

```javascript
let fs = require('fs')

//创建一个写入流，写入1.txt
let writeStream = fs.createWriteStream('1.txt',{flags:'w',encoding:'utf-8'})

let data = 'hello,world'
//回调有一个错误对象，如果没出错err为null
writeStream.write(data,function(err){
    if(err){
        console.log(err.stack)
    }else{
        console.log('内容流入完成')
    }
	
})

//关闭写入流进程并执行回调
writeStream.end(function(){
    console.log('文件写入流关闭')
})


//打开文件事件
writeStream.on('open',function(){
	console.log('打开文件')
})


//写入完成事件
writeStream.on('finish',function(){
	console.log('写入完成')
})

//关闭文件事件
writeStream.on('close',function(err){
	console.log('写入完成，关闭文件')
})

//这句代码最先执行，因为流是异步执行的
console.log('程序执行完毕')

执行结果：
程序执行完毕
打开文件
内容流入完成
文件写入流关闭
写入完成
写入完成，关闭文件
```

#### 文件读取流

文件读取流是监听data事件在回调中一点一点的向内存分批次读取大文件

data事件的回调参数chunk就是这次事件拿到的部分数据

```javascript
let fs = require('fs')
let rs = fs.createReadStream('1.txt',{flags:'r',encoding:'utf-8'})

rs.on('open',function(){
    console.log('读取文件已经打开')
})

rs.on('close',function(){
    console.log('读取文件流结束')
})

rs.on('data',function(chunk){
    console.log('流入一批数据,并输出：',chunk)
    console.log('这批数据字节大小：',chunk.length)
    //chunk是我们拿到的部分文件
})
```

#### 管道流

如果需要拷贝复制一个文件，我们需要先调用读取流读取一部分文件，然后调用写入流写入一部分文件，再反复读取写入，太过繁琐，所以提供了一个管道（pipe）流封装省略了频繁使用流的过程

```javascript
let fs = require('fs')
let rs = createReadStream('1.txt',{flags:'r'})  //将文件读取出来
let ws = createWriteStream('2.txt',{flags:'w'}) //创建一个写入流
rs.pipe(ws) //用管道流将写入流与读取流串联
```





## 标准输入输出：readline模块

readline模块创建实例对象以后会创建标准输入输出流(stdin,stdout)，之后可以在控制台进行输入输出操作

readline接口的实例对象是通过各种事件来操作输入输出

1. 引入readline模块

2. 创建readline接口实例对象提供输入输出进程
3. 操作实例对象的事件获取输入输出
4. 关闭输入输出进程

```javascript
//引入readline模块
let readline = require('readline');

//创建一个readline接口实例对象r1：创建了控制台才有输入输出流
let r1 = readline.createInterface({
	input: process.stdin,
	output: process.stdout
})


//question：提问事件
//提问事件会在输出问题后在控制台生成一个输入流让用户输入
r1.question('你的名字是',function(answer){
	console.log('我叫'+answer);
	//调用close事件关闭输入输出流，否则输入输出进程不会结束
	r1.close();
})

//监听close事件结束输入输出进程
r1.on('close',function(){
	//调用close事件后退出这个进程
    //0是退出的时候不做任何操作，类似c++中的return 0
	process.exit(0);
})
```

```javascript
因为这个接口是异步的，当需要顺序调用时会造成重复的回调地狱
所以建议将question事件封装
//1.引入readline模块
let readline = require('readline');

//2.创建实例对象：创建输入输出流进程
let r1 = readline.createInterface({
	input: process.stdin,
	output: process.stdout
})

//将question事件封装
function lcQuestion(question){
	return new Promise(function(resolve,reject){
		r1.question(question,function(answer){
			resolve(answer)
		})
	})
}

//创建函数：使用封装好的事件来在控制台输入输出
async function createPackage(){
    let name = await lcQuestion('你的包名叫啥')
    let description = await lcQuestion('你的包名如何描述')
    let main = await lcQuestion('你的包主程序入口文件是什么')
    let author = await lcQuestion('作者是谁')
    r1.close();
}

//调用事件
createPackage()

//关闭输入输出流进程
r1.on('close',function(){
	process.exit(0);
})
```







# 路径操作：path模块

操作文件的路径

导入path模块：解析文件字符串路径，可以不用自己写正则表达式等去匹配字符串

```javascript
let path = require('path');
```

## 内置变量

_dirname：获得执行文件所在目录的完整目录名
_filename：获得执行文件的绝对路径名
process.cwd()：获得当前执行node命令的文件夹目录名

## 获取文件拓展名：path.extname()

```javascript
let path = require('path');
let strPath = '1.txt'
//获取路径信息拓展名
let info = path.extname(strPath)

console.log(info)//.txt
```

## 路径拼接：path.resolve()

resolve()是把给定的参数从左到右进行一次解析，直到构成一个绝对路径

```javascript
let arr = ['/a','b','c']
let info = path.resolve(...arr)
console.log(indo)
//D:\a\b\c
//因为会自动解析成绝对路径所以会加上盘符
```

## 路径拼接：path.join()

join()是用平台特定的分隔符将参数拼接成一个路径

```javascript
不同操作系统平台路径分隔符不统一，Windows系统是\ /两种，Linux是/
路径拼接api语法： path.join('路径1','路径2'…);
以下几个特殊路径名
_dirname  	获得执行文件所在目录的完整目录名
_filename	获得执行文件的绝对路径名
process.cwd()  获得当前执行node命令的文件夹目录名

const path = require('path');
//导入path模块
let finialPath = path.join(_dirname,'itcast','a','b','c'); //路径拼接
console.log(finialPath);//输出D:\itcast\a\b\c
```

## path.parse()

```
path.parse(path)
path <string>
返回: <Object>
path.parse() 方法返回一个对象，其属性表示 path 的重要元素。

参数path是完整的目录
经过path.parse(path)处理返回对象pathObj后
返回的pathObj对象将具有以下属性：
dir  <string> 	该文件路径的目录（不包含文件名和文件后缀）
root <string> 	该路径的根目录
base <string>	该文件路径的文件名和文件后缀（不包含目录）
name <string>	该文件路径的文件名
ext  <string>	该文件路径的文件后缀
```







# 事件：events模块

Node.js 是单进程单线程应用程序，但是因为 V8 引擎提供的异步执行回调接口，通过这些接口可以处理大量的并发，所以性能非常高。

Node.js 几乎每一个 API 都是支持回调函数的。

Node.js 基本上所有的事件机制都是用设计模式中观察者模式实现。

Node.js 单线程类似进入一个事件循环，直到没有事件观察者再退出，每个异步事件都生成一个事件观察者，如果有事件发生就调用该回调函数。

也就是说node是单进程单线程的，但通过事件做到了异步。



##  事件驱动程序

Node.js 使用事件驱动模型，当web server接收到请求，就把它关闭然后进行处理，然后去服务下一个web请求。

当这个请求完成，它被放回处理队列，当到达队列开头，这个结果被返回给用户。

这个模型非常高效可扩展性非常强，因为 webserver 一直接受请求而不等待任何读写操作。（这也称之为非阻塞式IO或者事件驱动IO）

在事件驱动模型中，会生成一个主循环来监听事件，当检测到事件时触发回调函数。

整个事件驱动的流程就是这么实现的，非常简洁。有点类似于观察者模式，事件相当于一个主题(Subject)，而所有注册到这个事件上的处理函数相当于观察者(Observer)。

Node.js 有多个内置的事件，我们可以通过引入 events 模块，并通过实例化 EventEmitter 类来绑定和监听事件



## 基本使用

```javascript
var events = require('events');
var eventEmitter = new events.EventEmitter();

// 监听器 #1
var listener1 = function listener1() {
   console.log('监听器 listener1 执行。');
}

// 监听器 #2
var listener2 = function listener2() {
  console.log('监听器 listener2 执行。');
}

// 绑定 connection 事件，处理函数为 listener1 
eventEmitter.addListener('connection', listener1);

// 绑定 connection 事件，调用一次，处理函数为 listener2
eventEmitter.once('connection', listener2);

// 处理 connection 事件 
eventEmitter.emit('connection');

// 处理 connection 事件 
eventEmitter.emit('connection');
```





## 事件驱动原理（自定义事件）

在底层自定义事件的实现原理

```javascript
//node中事件驱动的原理
//在底层如何自定义一个事件

//创建一个存放自定义事件并为其绑定回调的对象events
//为events创建三个属性event，on，emit
//其中event是对象属性，on和emit是函数
//events.event属性是事件队列，存放自定义事件
//events.on(eventName,eventFn)方法为对队列里的事件添加回调
//events.emit(eventName)方法是触发某一个自定义事件
let events = {
  event:{
    //event是事件队列，所有自定义事件都在这个队列里
    //因为一个事件可能不止执行一个函数，所以事件被存放进来的时候是一个数组类型
    //自定义事件数组里存放触发事件时执行的回调函数
  },
    
   //on事件函数监听
   //外部调用这个来给事件队列里的自定义事件数组绑定回调
   //eventName参数是绑定的事件名，eventFn是为该函数准备的回调
  on:function(eventName,eventFn){
    //先判断一下这个自定义事件是否已经存在
    if(this.event[eventName]){
      //存在，则直接往数组里加函数
      this.event[eventName].push(eventFn)
    }else{
      //不存在就先创建，再加函数
      this.event[eventName]=[]
      this.event[eventName].push(eventFn)
    }
  },
    
  //emit是事件触发后执行函数
  emit:function(eventName){
    if(this.event[eventName]){
      //将自定义事件数组遍历处理并依次执行
      this.event[eventName].forEach(itemFn => {
        itemFn()
      });
    }
  }
}

//.on是添加自定义事件并绑定回调
//这里添加了两个自定义事件eat和helloworld
events.on('eat',function(){
  console.log('触发事件eat，执行回调1')
})

events.on('eat',function(){
  console.log('触发事件eat，执行回调2')
})

events.on('helloWorld',function(){
  console.log('触发事件helloWorld，执行回调')
})


//执行eat事件
events.emit('eat')
//执行helloWorld事件
events.emit('helloWorld')
```



## EventEmitter()类

自己写事件驱动（自定义事件）太过繁琐

node提供了自定义事件的api：EventEmitter()类

```javascript
// 引入 events模块
let events = require('events');

// new一个events事件对象
let eventEmitter = new events.EventEmitter();

// eventsObj.on绑定事件名eventName与回调eventHandler
eventEmitter.on('eventName', eventHandler);

// eventsObj.emit触发事件
eventEmitter.emit('eventName');
```







# os模块

os模块可以主要是获取操作系统和硬件的信息

```javascript
let os = require('os')

//获取每个cpu的信息和占用率等各种参数
console.log(os.cpu())

//获取内存信息
console.log(os.totalmem())

//获取cpu架构信息：比如arm，x64等
console.log(os.arch())

//获取闲置内存量
console.log(os.freemem())

//查看系统平台
console.log(os.platform())
```







# url模块

url一共提供了三个方法，分别是url.parse();　　url.format();　　url.resolve();

## url.parse()

快速的解析url地址将其转化为对象

**url.parse(urlString[,boolean])**

```
let url = require('url')
```

```javascript
 1 url.parse("http://user:pass@host.com:8080/p/a/t/h?query=string#hash");
 2 /*
 3 返回值：
 4 {
 5   protocol: 'http:',
 6   slashes: true,
 7   auth: 'user:pass',
 8   host: 'host.com:8080',
 9   port: '8080',
10   hostname: 'host.com',
11   hash: '#hash',
12   search: '?query=string',
13   query: 'query=string',  //第二个参数设置为true后query变为对象键值对
14   pathname: '/p/a/t/h',
15   path: '/p/a/t/h?query=string',
16   href: 'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'
17  }

```

 没有设置第二个参数为true时，query属性为一个字符串类型，设置为true后query从字符串转变为一个对象

`query: { query: 'string' }`

## url.resolve()

url.resolve()解析相对于基url的目标url，第一个参数基url，第二个参数目标url

用于快速拼接url地址，可以去`./`无用信息

```javascript
let tragetUrl='https://juejin.im/'
let httpUrl = './user/5e70a422f265da57515584e6'

let newUrl = url.resolve(tragetUrl,httpUrl)
console.log(newUrl)
//https://juejin.im/user/5e70a422f265da57515584e6
```

## url.format()

将url对象拼接为原本的字符串

```javascript
url.format({
  protocol: 'https',
  hostname: 'example.com',
  pathname: '/some/path',
  query: {
    page: 1,
    format: 'json'
  }
});

// => 'https://example.com/some/path?page=1&format=json'
```







# querystring模块

```
const querystring = require("querystring");
```

querystring是node自带的模块，一般是对http请求所带的数据进行解析

例如将键=值&键=值以&分隔，然后在数据部分里用 = 判断键值对，并转化为一个对象



## querystring.parse()

**querystring.parse(str,separator,eq,options)**

parse这个方法是将一个字符串反序列化为一个对象。

参数：str指需要反序列化的字符串;

　　　separator（可省）指用于分割str这个字符串的字符或字符串，默认值为"&";

　　　eq（可省）指用于划分键和值的字符或字符串，默认值为"=";

　　　options（可省）该参数是一个对象，里面可设置maxKeys和decodeURIComponent这两个属性：

　　　　　　maxKeys：传入一个number类型，指定解析键值对的最大值，默认值为1000，如果设置为0时，则取消解析的数量限制;

```javascript
const querystring = require('querystring')
let str = 'a=123&b=456&c=789'
let strs = querystring.parse(str)
console.log(strs)
// { a: '123', b: '456', c: '789' }
```



## querystring.stringify()

**querystring.stringify(obj,separator,eq,options)**

stringify这个方法是将一个对象序列化成一个字符串，与querystring.parse相对。

参数：obj指需要序列化的对象

　　　separator（可省）用于连接键值对的字符或字符串，默认值为"&";

　　　eq（可省）用于连接键和值的字符或字符串，默认值为"=";

　　　options（可省）传入一个对象，该对象可设置encodeURIComponent这个属性：

　　　　　　encodeURIComponent:值的类型为function，可以将一个不安全的url字符串转换成百分比的形式，默认值为querystring.escape()。

```javascript
let query = querystring.stringify(strs)
console.log(query)
// a=123&b=456&c=789
```



## querystring.escape()

**querystring.escape(str)**

escape可使传入的字符串进行编码



## querystring.unescape()

**querystring.unescape(str)**

unescape方法可将含有%的字符串进行解码







# http模块

http模块是node的核心模块之一，用于搭建node的http服务器，接收客户端发送的请求并返回服务器发送的消息。

Node.js 的 HTTP API 都非常底层。 它仅进行流处理和消息解析。 它将消息解析为消息头和消息主体，但不会解析具体的消息头或消息主体。

http分为客户端和服务端，客户端最常见的就是浏览器。

1. 引入http模块

   ```javascript
   let http = require('http')
   ```

2. 创建一个服务器实例server

   ```javascript
   let server = http.createServer()
   ```

3. `server.on('request',callback)`   监听request请求事件

   ​	当服务器接收到客户端的HTTP请求就触发request事件并执行回调

   ​	在回调中有request和response两个参数

   ​	`res.write()`后必须用`res.end()`来结束这次响应否则客户端会一直等待

   ​	

4. `server.listen([number][,callback])`在本地启动服务器并占据相应端口,不写端口，默认80端口



```javascript
let http = require('http') //引入http模块
//http.createServer()创建一个http服务器实例
let server = http.createServer()

//res.write()方法是用来给客户端发送一个响应数据(只能是二进制或者字符串)
//write可以调用多次但最后必须用response.end()来结束这次响应否则客户端会一直等待

//如果只调用一次write可以直接在res.end()中添加参数
//res.end()中添加参数就会断开之前执行一次response.write()发送一次数据
//例如  res.end('创建服务器成功')  先执行write再执行一次end

//当服务器接收到客户端请求就触发request事件并执行回调
//每刷新一次网页就向服务器发送两条请求(普通请求和/favicon.ico请求)，然后就会执行一次回调

//request  请求对象 请求对象可以获取客户端的一些请求信息例如请求路径
//response 响应对象 响应对象可以用来给客户端发送响应消息

server.on('request',function(req,res){
    console.log('收到一次客户端请求，请求路径为'+req.url)
    
    //设置http头部,Content-type响应内容类型，声明这次返回的消息是html格式，编码utf-8
    res.writeHead(200,{"Content-type":"text/html;charset=UTF-8"})
    
    switch(req.url){
        //根据请求地址返回不同消息
        case '/': 
            res.end('index');
            break;
        case '/login': 
            res.end('login');
            break;
        case '/favicon.ico': //网站图标的请求，每次刷新都会有一次这个请求
            res.end('favicon.ico');
            break;
        default: 
            res.end('404');
    }
})



//server.listen()启动一个监听绑定3000端口号

server.listen(3000,function(){
    //服务器启动成功，可以通过http://127.0.0.1:3000/或者localhost来访问
    console.log('服务器启动成功')
})

//打开 http://127.0.0.1:3000/
//不加3000指定端口，http请求默认访问80端口
//实际业务都是80端口
//输出 创建服务器成功
```

## request请求对象属性

`server.on('request',function(req,res){}) `

监听请求中回调的res请求对象携带的请求信息

req.url   			`<string>`获取请求地址，请求地址只会返回域名之后的`/xx`这部分内容

req.method      `<string>`获取响应方法`'GET'/'POST'`

req.headers      `<object>`HTTP请求头内容

request.httpVersion  获取协议的版本



## response响应对象方法

1）`	res.writeHead(state,{key:value})`  该方法设置返回的HTTP响应头部信息

第一个参数是返回的HTTP状态码，第二个参数时返回的请求头属性的key-value键值对

```javascript
res.writeHead(200,{"Content-type":"text/html;charset=UTF-8"})
```



2）`res.setHeadr(key,value)`  该方法也是设置响应体信息，但属性值省略了状态码且只能添加一个请求头属性

该方法设置的属性将传给 `response.writeHead()`方法设置的响应头一起合并

```javascript
res.setHeader('Content-Type', 'text/html')
```



3）`	res.write()`  方法是返回的HTTP响应体的数据（只能是二进制或者字符串）

```javascript
res.write('请求成功')
//向客户端发送消息
```



4）`res.end()`   方法结束这次HTTP响应

`res.write()`后必须调用用`res.end()`方法来结束这次响应否则客户端会一直等待

如果只调用一次write可以直接在res.end()中添加参数
res.end()中添加参数就会断开之前执行一次response.write()发送一次数据

```javascript
res.end('创建服务器成功')
//先执行write再执行一次end结束连接
```

## 封装http

```javascript
let http = require('http')
let path = require('path')
let url = require('url')
class httpServer{
	constructor(){
    this.server = http.createServer()
    //httpServer对象.on方法是根据路径选择对应事件触发
    //将路径与对应回调以key-value的形式存放绑定
    //路径就变为了事件对象里的函数名
    this.reqEvent={
      "/":function(){},

    }
    //监听http请求
    this.server.on('request',(req,res)=>{
      let pathObj = url.parse(path.url)
      //箭头函数的this指向上一层的this
      //如果request请求事件的回调用命名函数就不能用this
      //pathObj.dir是提取该文件的目录
      if(pathObj.dir in this.reqEvent){
        //如果路径在事件对象里能找到就执行对应方法
        this.reqEvent[pathObj.dir](req,res)
      }else{
        //路径找不到则向客户端返回404
        res.writeHeader(200,{"Content-type":"text/html;charset=UTF-8"})
        res.end('<h1>404,页面找不到</h1>')
      }
    })
  }

  on(url,fn){
    //.on方法是将url路径与回调函数存入事件对象，调用类似于绑定事件
      this.reqEvent[url] = fn;
  }

  run(port,callback){
    //run方法是添加监听端口号，启动回调
      this.server.listen(port,callback)
  }
}
//导出
module.exports = httpServer
```

```javascript
let server = require('httpServer.js')
let app = new  server()

app.on('/',(req,res)=>{
	res.writeHead(200,{"Content-type":"text/html;charset=UTF-8"})
	res.end('<h1>访问主页</h1>')
})

app.run(80,()=>{
	console.log('开启服务器成功，监听80端口')
})
```



已经有很多网络框架封装过了http例如express，只需要了解以上的原理，以后调用库就行了

## 梳理http模块流程

**1 浏览器发送请求**

​	1.1用户输入网址

​	1.2 浏览器根据请求转变为http请求包

**2 服务器收到请求**

​	2.1 服务器监听请求事件，每次请求都会触发请求事件

​    2.2  将http的请求包转化为req的请求对象，并传入到请求事件触发的函数中

​	2.3  生成res响应对象

**3 解析请求路径，根据路径渲染不同的页面**

​    3.1   正则匹配路径

​	3.2   以匹配到的正则字符串作为KEY，找到需要调用的渲染函数

​	3.3   如果匹配路径为静态路径，则是请求静态文件（如图片等），那么就按照静态文件输出

**4 RES响应对象将最终转化为http响应包**

**5 浏览器解析响应包，渲染页面**

## 解析get请求

通过url模块和querystring模块来解析get请求url中携带的数据

## 解析post请求

post请求数据量较大不能一次接收，就要使用数据流，收到一点写入一点

```
req.on('data', chunk => {})
这个事件指收到数据就触发，chunk是收到的数据流
```

```javascript
server.on('request',(req,res)=>{
  if(req.method === 'POST'){
    console.log('content-type', req.headers['content-type'])
    let postData = ''
    // 每次接收到数据一点，就存入chunk中，接收完毕后最后将chunk数据块toString后加入字符串中
    req.on('data', chunk => {
      console.log('here')
    //只会运行一次here，因为存入chunk不会触发回调，chunk不再接收新数据后触发回调
    //将post请求请求体里的数据转化为字符串保存起来
      postData += chunk.toString()
    })
    // 确认接收数据完毕打印字符串
    req.on('end', ()=>{
      console.log(postData)
      res.end('hello world')
    })
  }
})
```





# 	Puppeteer 自动化测试库

Puppeteer是一个Node库，可以通过 Chrome DevTools Protocol 协议控制 Chromium/Chrome 浏览器的行为。

Puppeteer主要用于前端的自动化测试。

作用：

- 生成页面PDF
- 抓取SPA并生成预渲染页面
- 自动提交表单，进行ui测试，表单输入等
- 捕获网站的timeline trace，进行性能分析

```
npm install puppeteer
下载安装Puppeteer
```

```javascript
const puppeteer = require('puppeteer')

async function test(){
  let options = {
    //defaultViewport是配置浏览器视窗大小
    defaultViewport:{
      width:1400,
      height:800
    },
    //headless是控制开启headless模式
    headless:false
  } 
  let browser = await puppeteer.launch(options);
  //puppeteer.launch()可以创建一个浏览器对象
  //options配置里可以关闭headless模式，headless是无界面浏览器
  //浏览器对象可以控制浏览器做出操作
  //无界面浏览器性能更高，有界面一般用于调试开发

  //创建一个page实例，一个browser实例可以有多个page实例
  let page = await browser.newPage()
  //用页面对象访问百度
  await page.goto('https://www.dytt8.net/')
  //对页面截图并保存为screenshot.png
  // await page.screenshot({path:'screenshot.png'})
  
  //获取页面内容page.$$eval(选择器,callback)并执行回调
  //$$eval方法 抓取选择器选中的内容并在浏览器（不是node）中执行回调，选中元素默认为第一个参数
  //所以这里的输出在浏览器控制台
  page.$$eval('#menu li a',(elements)=>{
    console.log(elements)
    elements.forEach(function(item){
      console.log(item.innerHTML)
    })
  })
  
  //console输出事件，request请求事件，requestfinished请求完成事件
  //具体事件种类见文档
    //监听浏览器的控制台的输出事件，然后在node中执行回调
  page.on('console',function(...args){
    console.log(args)
  })
    
  //还可以模拟鼠标
}

test()
```

## 选中元素

`pageObj.$(selector)`

此方法在页面内执行 `document.querySelector`。如果没有元素匹配指定选择器，返回值是 `null`。

`page.$$(selector)`

此方法在页面内执行 `document.querySelectorAll`。如果没有元素匹配指定选择器，返回值是 `[]`。



`page.$eval(selector, pageFunction[, ...args])`

执行 `document.querySelector`，将选中的元素作为回调第一个参数，在浏览器中执行回调

`page.$$eval(selector, pageFunction[, ...args])`

执行 `document.querySelectorAll`，将选中的元素数组作为回调第一个参数，在浏览器中执行回调

## 请求拦截器

启用请求拦截器

```
pageObj.setRequestInterception(true); //打开拦截器
```

拦截器可以对请求进行拦截，放弃当次请求

```
interceptedRequest.abort();  //拦截本次请求
interceptedRequest.continue(); //不拦截本次请求
```

```javascript
//例如拦截图片请求
await page.setRequestInterception(true);
//监听页面的请求事件并向回调中传入拦截器
pageObj.on('request', interceptedRequest => {
    //判断请求类型：以jpg/png结尾就拦截这次请求
    if (interceptedRequest.url().endsWith('.png') || interceptedRequest.url().endsWith('.jpg'))
      interceptedRequest.abort();
    else
      interceptedRequest.continue();
  });
```

## 等待函数

`pageObj.waitForSelector(selector[,options])`

等待某一个元素消失或者出现在页面中再继续下面的代码，如果调用此方法时已有匹配的元素立刻返回，如果超出options规定的最大时间就会报错。

```
selector <string> 选择器
options  <object>
	visible <boolean> 默认false,等待元素出现在dom中并且可以看到
					 （例如不能display:none/visibility:hidden）
	hidden  <boolean> 默认false,等待元素出现在dom中消失或看不见
					 （例如display:none/visibility:hidden）
	timeout <number>  最大等待时间，默认30000ms
```

例如有些元素是前端通过js代码渲染出来的，点击右键查看源代码看不到，这时我们想要选中这个元素就得等到前端把这个元素渲染出来才能选中它。

```javascript
//在一个异步函数中我们使用await整个函数会停下来等待这句代码执行
//所以我们在选中前端渲染出来的元素时就得使用等待函数等这个元素渲染完毕再选中它
await pageObj.waitForSelector('')
```

## 返回所有页面

`browserObj.pages()`

这个方法会返回一个数组，里面保存了所有当前打开的页面对象

## 选取元素的属性

`eleObj.getProperties()` 返回选中元素的所有属性键值对组成的对象

`eleObj.getProperties(propertyName)`返回选中元素指定属性的属性值









# 连接Mysql

1. 下载Mysql
2. 用mysql cline client输入账号密码进入数据库使用sql语句操作数据库
3. 安装navicat界面化操作数据库

**node连接Mysql**

1. 安装插件

   ```javascript
   cnpm install mysql
   ```

2. 创建mysql连接对象

   ```javascript
   //引入mysql模块
   let mysql = require('mysql')
   
   //mysql.createConnection(options)  创建连接数据库对象
   //保存连接的配置信息
   let options = {
       host     : 'localhost',
       user     : 'root', //默认root
       password : '123456',
       database : 'myblogs'  //database连接wtw数据库
       //port: 3306  修改端口号，不设置进入数据库默认端口号
   }
   
   //根据配置信息创建一个连接对象connection
   let connection = mysql.createConnection(options);
   ```

   3.连接数据库

   ```javascript
   //用连接对象连接数据库
   connection.connect((err)=>{
       if(err){
           console.log('连接失败',err)
       }else{
           console.log('连接成功')
       }
   });
   ```

   4.执行sql语句与回调函数

   ```javascript
   connectionObj.query(sqlStr,callback)
   ```

   ```javascript
   //执行sql语句并执行回调
   //常见的回调基本如下
   let sqlStr = 'select * from users;'
   
   connection.query(sqlStr,(err,result)=>{
   if(err){
     console.log(err)
   }else{
     console.log(result, '操作成功')
    }
   })
   ```
   
   5.关闭连接结束进程
   
   ```javascript
   connection.end()
   ```
   



**在执行新增和修改，删除的sql语句返回值是一个对象，包含了执行结果等信息**

```javascript
OkPacket {
  fieldCount: 0,
  affectedRows: 1,// 本次修改影响了几行，大于0则本次修改成功
  insertId: 0,
  serverStatus: 2,
  warningCount: 0,
  message: '(Rows matched: 1  Changed: 0  Warnings: 0',
  protocol41: true,
  changedRows: 0
}
```



# redis

session设计为js变量，放进nodejs进程内存中，进程内存有限，访问量过大会导致内存不足，而且正式上线运行时多进程，进程直接无法共享内存。

操作系统会限制一个进程的最大内存，nodejs不能占用太大的内存。正式上线时，nodejs会分为多个进程来跑，尽量利用内存。不同进程之间的内存不能共享，负载均衡后每次访问到的不是同一个进程，session就不能共享，将session存在nodejs进程中会导致很多问题。

redis时服务端常用的缓存数据库，数据存放在内存中，数据断电丢失。相比mysql（硬盘数据库），因为在内存中所以访问速度快，但是成本更高，可储存的数据量更少。

将session放进redis中存储可以解决很多问题，redis是一个单独的进程服务，不会导致nodejs内存过大。

**为什么不将session放入mysql？**

session访问频繁，对性能要求高，session可以不考虑断电丢失的问题，丢失了重新登陆即可，session数据量不会很大。

## redis命令

打开一个 **cmd** 窗口 使用 cd 命令切换目录到 **C:\redis** 运行：

```
redis-server.exe redis.windows.conf
```

如果想方便的话，可以把 redis 的路径加到系统的环境变量里，这样就省得再输路径了，后面的那个 redis.windows.conf 可以省略，如果省略，会启用默认的。

这时候另启一个 cmd 窗口，原来的不要关闭，不然就无法访问服务端了。

切换到 redis 目录下运行:

```
redis-cli.exe -h 127.0.0.1 -p 6379
```

设置键值对:

```
set Key value
```

获取键值对值:

```
get Key
```

取出所有键值对

```
keys *
```

删除键值对

```
del key
```



# node连接redis

```
npm insall redis --save
```

```javascript
const redis = require('redis')

const redisClient = redis.createClient(6379,'127.0.0.1')
// 第一个参数是端口，第二个参数是域名地址

// 监听异常事件进行异常处理
redisClient.on('error', err=>{
  console.log(err)
})

// set方法，这是个异步操作
redisClient.set('myname', 'zhangsan2', redis.print)

// get方法，查询值
redisClient.get('myname', (err, val)=>{
  if(err){
    console.error(err)
    return
  }
  console.log('val', val)

  redisClient.quit() // 退出本次查询
})
```



# nginx

高性能的web服务器，用于做反向代理（解决cookie跨域问题），负载均衡

nginx命令：cd到nginx文件夹

- nginx.exe -t   测试配置文件格式是否正确

- nginx.exe       启动nginx

- nginx.exe -s reload   重启nginx

- nginx.exe -s stop   停止

跨域配置

```
location / {
		  proxy_pass http://localhost:8082;
		
		}
		
		location /api/ {
		  add_header Access-Control-Allow-Origin "$http_origin";
		  add_header Access-Control-Allow-Methods GET,POST,PATCH,PUT,OPTIONS,DELETE;    
		  add_header Access-Control-Allow-Headers *;
		  add_header Access-Control-Allow-Credentials true;
		  proxy_pass http://localhost:8082;
		  proxy_set_header Host $host;
		}
```







# 上线服务器

花生壳 这个软件可以将私网服务器地址和端口号和公网固定的ip地址与端口绑定。

1. 注册花生壳

2. 实名认证

3. 免费获取域名和花生壳体验账号

4. 进入花生壳设置平台

5. 添加应用

   ```
   应用名称
   内网主机ip地址
   内网服务的端口号
   ```

6. 下载花生壳安装并启动

