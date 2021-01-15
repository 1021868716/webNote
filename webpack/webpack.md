# Webpack

最开始的前端开发每一个html页面都需要引入许多不同的js文件，会多出大量的http请求并且文件结构不清晰，难以进行修改。所以当时的做法是设置一个入口JS文件，html只引入这个入口JS文件，但是这样还是有很多问题，例如在入口文件中导入其他文件的顺序必须符合依赖关系否则会报错，例如浏览器对es6兼容不好，我们还需要手动写es5语法等等诸多问题。

而使用webpack对这个入口文件进行打包后，会帮我们进行依赖管理（原本的依赖关系可能是混乱的，html导入无法运行），由webpack统一管理后进行es6转es5等各种操作，最后生成一个最终的`main.js`文件，html引入这个js文件就可以了。webpack支持打包使用Module和CommonJs模块化的js文件。

## 常见概念

- bundler

webpack的定义是一个bundler，即模块打包工具。模块化的js文件就是一个模块，若干个js模块会打包成一个总的js文件（bundle），这个总的js文件称作bundle。



- bundle

bundle是由webpack打包出来的文件，webpack最后生成浏览器可以直接运行的bundle



- chunk

chunk是指webpack在进行模块的依赖分析的时候，代码分割出来的代码块（代码片段），即是间接引入的模块，webpack 处理时是 chunk，当我们写的 module 源文件传到 webpack 进行打包时，webpack 会根据文件引用关系生成 **chunk** 文件，webpack 会对这个 chunk 文件进行一些操作。

例如我们写的代码（module）被webpack引入后就变为了chunk，chunkId就是产出时给每个文件一个唯一标识id，chunkhash就是文件内容的md5值，name就是在entry中指定的key值。

bundle只有一个，浏览器最终执行的是bundle，而chunk可能会有多个，webpack处理的文件都是chunk，这些chunk最终会合成bundle。



- module

对于一份同逻辑的代码，当我们手写下一个一个的文件，它们无论是 ESM 还是 commonJS 或是 AMD，他们都是 **module** 

`module`，`chunk` 和 `bundle` 其实就是同一份逻辑代码在不同转换场景下的取了三个名字：

我们直接写出来的代码是 module，进入webpack 处理时是 chunk，最后生成浏览器可以直接运行的 bundle。



- loader

loader是模块转换器，用于模块内容的转换，使wenbpack拥有加载和解析非js文件的能力，举个例子：js类型的文件和css文件需要不同的loader来处理。最常用的加载器是eslint-loader和babel-loader。



- plugins

**plugins**用于扩展webpack的功能，在构建流程中监听特定的事件来做一些处理，相比着loader更加灵活，不用指定文件类型。常用的plugins有三个，html-webpack-plugin、commonChunkPlugin和ExtractTextPlugin



- entry：入口

- output：输出

- filename 指列在 entry 中，打包后输出的文件的名称
- chunkFilename 指未列在 entry 中，却又需要被打包出来的文件的名称



## 安装webpack

1、初始化项目

```
npm init
```

生成`package.json`文件，这个文件记录了这个项目所需要的依赖以及版本，和一些项目信息，npm自定义命令等。



2、安装webpack

webpack-cli这个是用来支持webpack打包命令的，不安装`npx webpack index.js`或`webpack index.js`这两个命令不能运行

项目局部安装

`--save-dev `只用于开发环境，`--save `开发生产环境都需要

```
npm install webpack-cli --save-dev
npm install webpack --save
```

全局安装（不推荐，建议项目局部安装）

```
npm install webpack webpack-cli -g
```



3、使用webpack对需要打包的入口文件index.js进行打包，生成一个默认的main.js

**使用局部的webpack打包命令：**

```
npx webpack index.js
```

使用全局的webpack打包命令:

```
webpack index.js
```

webpack优先使用全局的版本，为了解决这个问题，就可以使用npx，这是npm自带的工具，npx是以当前项目的webpack而不是全局的webpack运行打包，npx 会帮你执行**依赖包**里的二进制文件。 

npx 会自动查找当前依赖包中的可执行文件，如果找不到，就会去 PATH 里找。如果依然找不到，就会帮你安装。



每次执行打包会先输出四条信息

```
Hash: 67c5564b81b7eb44a512
Version: webpack 4.43.0
Time: 72ms
Built at: 2020-05-17 23:14:44
```

Hash带表本次打包唯一的hash值，Version是使用的webpack版本，Time表示打包事件，Build at表示打包时间

然后会输出每个打包文件的文件名（Asset），大小（Size），文件对应的标记id值（Chunks），文件对应的标记name值（Chunk Names）



webpack就完成了一次简单的打包，原本的项目文件夹下多出一个dist文件夹，dist文件夹下生成了一个main.js文件，这个文件就是webpack打包处理后的文件，html中只需要引入这个main.js就可以了。









# 构建流程

`webpack` 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

1. 初始化参数：从配置文件和 `Shell` 语句中读取与合并参数，得出最终的参数；
2. 开始编译：用上一步得到的参数初始化 `Compiler` 对象，加载所有配置的插件，执行对象的 `run` 方法开始执行编译；
3. 确定入口：根据配置中的 entry 找出所有的入口文件
4. 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理；
5. 完成模块编译：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系；
6. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 `Chunk`，再把每个 `Chunk` 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；
7. 输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

在以上过程中，`webpack` 会在特定的时间点广播出特定的事件，plugin在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 `webpack` 提供的 API 改变 `webpack` 的运行结果。





# 配置文件

webpack的默认配置文件是`webpack.config.js`，该文件导出一个对象，对象内设置了webpack的各种配置，如果想使用其他文件作为webpack的配置文件则运行命令，将webpack的配置文件进行指定

```
npx webpack --config 文件名
```

```javascript
const path= require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  devtool: 'cheap-module-source-map', // 开启代码映射
  mode:'production', //打包模式，表示打包后的js文件是否压缩
    // production 压缩    development 不压缩
	entry: './src/index.js', // 打包的入口文件
    //设置入口之后打包代码可以省略入口文件名，打包命令简化为 npx webpack
	output: {
		// 打包后的js文件名
		filename: 'main.js',
		// 在当前目录下新建一个dist文件夹，将main.js放进去
		path: path.resolve(__dirname, 'dist')
	},
  	module: {  // loader模块配置
    	rules: [
      	 {
        	test:/\.jpg$/, //正则匹配以.jpg结尾的文件形式
        	use: {
          		loader: 'file-loader' // 如果匹配则使用file-loader对文件进行打包
       	 	}
         }
       ]
    },
    plugins: [
       new HtmlWebpackPlugin({
        template: 'src/index.html'  //模板文件地址
    })  
    ]
}
```





# 配置分离

可以分别创建两个配置文件`webpack.dev.js`和`webpack.prod.js`，分别对应开发和线上的配置文件，然后公用代码抽离出来创建一个`webpack.common.js`。

然后在线上和开发的配置文件中引入`webpack-merge`这个模块将公共配置合并进去，并导出一个完整的配置

```
npm install webpack-merge -D
```

```javascript
//webpack.common.js
module.exports = {
	entry: './src/index.js', 
	output: {
		filename: 'main.js',
		path: path.resolve(__dirname, 'dist')
	},
}
```

 在两个配置文件中使用merge引入webpack.common.js，然后合并导出

```javascript
const merge = require('webpack-merge')
const commonConfig = require('./webpack.common.js')
const devConfig = {
  mode: 'development'
  ...
}
module.exports = merge(commonConfig, devConfig)
```

```javascript
const merge = require('webpack-merge')
const commonConfig = require('./webpack.common.js')
const prodConfig = {
  mode: 'production'
  ...
}
module.exports = merge(commonConfig, prodConfig)
```



可以在根目录下创建一个build文件夹专门放置webpack配置文件，将三个配置文件移入build文件夹，然后在package.json中进行配置，并分别命名打包命令

```json
"script": {
	"dev": "webpack-dev-server --config ./build/webpack.dev.js",
    "build": "webpack --config ./build/webpack.prod.js"
}
```

配置文件移入build文件夹后跟dist文件夹有关的配置都需要改变

```javascript
output: {
    filename: 'main.js',
    // path: path.resolve(__dirname, 'dist')
    path: path.resolve(__dirname, '../dist')
 }
```



## 分离打包

有些配置是开发时需要用到的（例如本地服务器devserver），有些配置是上线时需要用到的配置（例如压缩js插件ugluglifyjs），所以可以将配置文件进行抽离，根据不同环境使用不同配置

1. **在项目根目录下创建一个build文件夹专门放置配置文件**

在build下创建三个配置js文件 base.config.js，prod.config.js， dev.config.js，分别对应开发上线都需要的配置，生产环境的配置和开发环境的配置

- base.config.js：开发上线都要使用的公共配置放这里



- prod.config.js：生产时使用的配置放这里（例如 uglifyJsPlugin），上线时将prod和base合并

```javascript
const uglifyJsPlugin = require('uglifyjs-webpack-plugin');
module.exports={
  ...
  mode: 'production', // 生产环境代码压缩
  plugins:[
    new uglifyJsPlugin()
  ]
}
```



- dev.config.js：开发使用的配置放这里（例如devServer），开发调试时将dev和base合并

```javascript
module.exports={
  ...
  mode: 'development', // 开发环境代码不压缩
  devServer:{
    contentBase: './dist',//选择服务的文件夹
    inline: true
  }
}
```



2. **下载分离合并插件webpack-merge**

```
npm install webpack-merge
```



3. **配置**

在prod.config.js中导入merge插件和base配置文件

```JavaScript
const uglifyJsPlugin = require('uglifyjs-webpack-plugin');
const webpackMerge = require('webpack-merge');
const baseConfig = require('./base.config');
module.exports = webpackMerge(baseConfig,{
   mode: 'production',
   plugins:[
     new uglifyJsPlugin()
]})
```



在dev.config.js中导入merge插件和base配置文件

```JavaScript
const webpackMerge = require('webpack-merge')
const baseConfig = require('./base.config')
module.exports=webpackMerge(baseConfig,{
  mode: 'development',
  devServer:{
     contentBase: './dist',
     //选择服务的文件夹
     inline: true
   }
})
```



删除根目录下原webpack.config.js配置文件，在package.json中脚本里修改配置文件地址，对"scripts"下"build"和"dev"字段进行修改

```json
//"build":"webpack"
"build":"webpack --config ./build/prod.config.js"
//"dev":"webpack-dev-server"
"dev":"webpack-dev-server --open --config ./build/dev.config.js"
```

运行build命令会使用prod配置和base配置，运行dev使用dev配置和base配置



4. **修改base配置文件中出口打包地址**

base.config.js中将output的path属性修改为` path:path.resolve(__dirname,'dist')`
原来的打包地址为配置文件同目录下创建dise文件夹，现在配置文件放入build文件夹了，所以需要修改出口地址

```javascript
//enter属性不用修改，enter以package.json为准
output: {
  path:path.resolve(__dirname,'../dist') 
}
```







## 环境变量打包

env是webpack的环境变量对象

原本的配置分离是两个文件同时引入webpack.common.js，然后分别不同的命令打包

使用环境变量打包是，webpack.common.js引入生产和开发配置，根据打包时的环境变量来进行导出不同的配置

webpack.dev.js和webpack.prod.js无需再引入merge和commonConfig，在webpack.common.js引入这两个配置，打包时根据环境变量判断所处打包环境分别使用不同的配置。

```javascript
import merge = require('webpack-merge')
import devConfig = require('./webpack.dev.js')
import prodConfig = require('./webpack.prod.js')

const commonConfig = {
 // 公共配置
}
  
module.exports = (env) => {
  if(env && env.production) {
    // env环境变量存在则为线上环境
    return merge(commonConfig, prodConfig)
  }else {
    // 否则为开发环境
    return merge(commonConfig, devConfig)
  }
}
```

修改package.json

```json
"script": {
	"dev": "webpack --config webpack.common.js",
	"build": "webpack --env.production --config webpack.common.js"
}
```

执行dev打包时，默认不输入环境变量，env就是空，则导出开发环境的配置打包

当执行build打包时，传入环境变量env.production，则导出生产环境的配置执行打包





# 打包命令

使用项目局部的webpack进行打包的默认命令为`npx webpack index.js`

wenpack配置文件设置入口entry后打包命令简写为`npx webpack`（这样还是有些繁琐并且需要用到npx这个工具）

在`package.json`中可以对终端代码设置自定义命令，为命令配置别名使用`npm run`运行自定义命令

```json
"script": {
  "build": "webpack"
}
```

添加进script的命令会优先从依赖包中寻找依赖，解决了webpack优先从全局找的问题，所以不用配置为`"npx webpack"`，设置之后打包代码就从`npx webpack` 简化为`npm run build`



# mode

控制打包后的js文件是否压缩

```javascript
mode:'production', //打包模式，表示打包后的js文件是否压缩
```

属性值  

- production	压缩    

- development    不压缩



# entry

配置src文件夹中的项目用于打包的入口文件

```
entry: './src/index.js', // 打包的入口文件
```

如果具有多个入口文件或者需要将入口文件反复打包两次，则需要将entry配置为对象然后分别进行配置。

```javascript
entry: {
	main: './src/index,js',
  sub: './src/sub,js'
}
```



# output

如果没有配置这个对象，打包后文件名默认为main.js，可以在output对象中对打包文件名和路径进行修改

```javascript
output: {
  // 打包后的js文件名
  filename: 'bundle.js',
  // 在当前目录下新建一个dist文件夹，将打包后的bundle.js放进去
  path: path.resolve(__dirname, 'dist')
}
```

但如果在entry中打包了两个js文件，使用上述output就会报错，因为输出的js文件都叫bundle.js就会报错，所需需要将filename进行动态命名

```javascript
output: {
  filename: '[name].js',
  path: path.resolve(__dirname, 'dist')
}
```

[name]是动态命名，根据entry中配置的属性名进行动态命名，例如上面的entry就会被打包为main.js和sub.js



output还有一个属性是publicPath，用于获取cdn上的js文件，例如

```javascript
output: {
  publicPath:'http://cdn.com.cn',
  filename: '[name].js',
  path: path.resolve(__dirname, 'dist')
}
```

这样在使用html-webpack-plugin自动生成html后，引入js的script标签上src就不是引用本地路径了，而是自动加上一个网络路径前缀，去cdn服务器中获取这个js文件

```html
<script src="http://cdn.com.cn/main.js"></script>
<script src="http://cdn.com.cn/sub.js"></script>
```



chunkFilename是给不是入口文件，但是被间接打包出来的文件命名，例如被代码分割出来的chunks。

 ```javascript
output: {
  filename: '[name].js',
  chunkFilename: '[name].chunk.js',
  path: path.resolve(__dirname, 'dist')
}
 ```





# module/loader

*loader* 让 webpack 能够去处理那些非 JavaScript 文件（**webpack 自身只理解 JavaScript**）。loader 可以将所有类型的文件转换为 webpack 能够处理的有效模块。然后就可以利用 webpack 的打包能力，对它们进行处理。

Loader就是将非JS文件处理成webpack能打包的形式。webpack只能打包js文件，其他格式的文件（css，图片，vue等）则需要使用相应的loader辅助进行打包。

loader用于对模块源码的转换，loader描述了webpack如何处理非javascript模块，并且中引入这些依赖。loader可以将文件从不同的语言（如TypeScript）转换为JavaScript，或者将内联图像转换为data URL。比如说：CSS-Loader，Style-Loader等。

loader的配置在webpack.config.js中配置对象的module对象下的rules数组内进行配置。

**loader的执行顺序是从下到上，从右到左。**一个匹配规则中可以配置使用多个 loader，即一个模块文件可以经过多个 loader 的转换处理，执行顺序是从最后配置的 loader 开始，一步步往前。**loader 的应用顺序在配置多个 loader 一起工作时很重要。**

```javascript
module: {
  rules: [
    {
      test:/\.jpg$/, //正则匹配以.jpg结尾的文件形式
      use: {
        loader: 'file-loader' // 如果匹配则使用file-loader对图片进行打包
      }
    }
  ]
}
```

在webpack中Loader有两个目标：

​	使用test属性识别出应该被对应的loader进行转换的文件。
​	使用use属性转换这些文件，从而使其能够被添加到依赖图中（并最终添加到bundle中）。



## 常用loader
**babel-loader**：可以将ES6代码转化为ES5代码

**css-loader**：加载css，支持模块化，压缩，文件导入

**style-loader**：将css代码注入到js中，并通过dom加载css

**file-loader**：文件的解析，在代码中通过相对url引入文件

**url-loader**：与file-loader类似，但是能在文件很小的情况下以 base64 的方式把文件内容注入到代码中去



## file-loader

这个loader是处理静态文件格式（例如图片，文本等文件格式）的loader。

```
npm install file-loader -D
```

这个loader会把文件重新命名成随机字符串后移动到dist文件夹，然后将**对应的文件地址**返回给dist的main.js让他引用。

打包后的默认文件会被随机命名并放在根目录，如果需要对打包文件的命名，存放地址等进行规定则需要添加配置对象options。

options.name属性通过设置特定的占位符（Placeholders）为打包后的文件名进行配置。具体语法官方文档上可以查询。

options.outputPath属性是配置打包后文件存放在哪个文件夹下

```javascript
module: {
  rules: [
      {
       test:/\.(jpg|png|gif)$/, //正则匹配以.jpg，png，gif结尾的文件形式
       use: {
         loader: 'file-loader', // 如果匹配则使用file-loader对文件进行打包
       	 options: { // file-loader配置
            name: '[name].[ext]', //该项语法为使用原始文件名和原始后缀
            outputPath: 'images/' //新建一个images文件夹存放文件
           }
        }
      },
      {
  		test:/\.css$/,
  		use: ['style-loader','css-loader']
	  }
    ]
}
```

```javascript
import photo1 from './photo1.jpg'

var img = new Image()
img.src = photo1  // file-loader获得的是文件地址

var root = document.getElementById('root')
root.append(img)
```



## url-loader

url-loader也能用来加载静态文件，url-loader不会将图片放入dist文件夹，他会将图片以base-64的格式直接存储进js文件中，可以减少一次http请求，但是base64一般用来加载小图片小图标等，所以url-loader还可以设置一个limit值，大于这个值就会像file-loader一样把文件放入dist文件夹下，使用引用地址来存储文件。url-loader在option.limit中配置一个最大bit数，超过就不会使用base-64加载图片，会将图片移入dist文件夹使用地址引用。

```
npm install url-loader --save-dev
```

```javascript
{
  test:/\.(jpg|png|gif)$/,
  use: {
    loader: 'url-loader', 
    options: { 
       name: '[name].[ext]',
       outputPath: 'images/',
       limit: 10240 //大于10kb则将文件放入dist文件夹使用地址引用
    }
  }
}
```

```javascript
import photo1 from './photo1.jpg'

var img = new Image()
img.src = photo1  // url-loader打包获得的是base64字符串图片

var root = document.getElementById('root')
root.append(img)
```



## ts-loader

打包typescript的loader，配置ts-loader

```
npm install ts-loader --save-dev
```

```javascript
{
  test: /\.ts$/,
  use: 'ts-loader',
  exclude: /node_modules/,
}
```



## 样式相关loader

### css-loader/style-loader

项目中加载css文件需要使用两个loader：css-loader和style-loader

css-loader负责解析css文件与处理css文件之间的依赖关系并生成一个最终的css样式，style-loader负责将这个css样式挂载到页面的head部分

```
npm install style-loader css-loader -D
```

因为需要使用两个loader所以use必须是一个数组，将两个loader配置放进去

```javascript
{
  test:/\.css$/,
  use: ['style-loader','css-loader']
}
```

如果loader需要配置则use数组内添加loader对象

```javascript
{
  test:/\.css$/,
  use: [
    'style-loader',
     {
       loader:'css-loader',
       options: {
         importLoaders: 2
       }
     }
  ]
}
```



### scss-loader

加载sass需要sass-loader和node-sass进行编译，也需要style-loader进行挂载

```
npm install scss-loader node-sass --save-dev
```

```javascript
{
  test:/\.scss$/,
  use: ['style-loader','css-loader','scss-loader']
}
```



### postcss-loader

PostCSS 本身是一个功能比较单一的工具。它提供了一种方式用 JavaScript 代码来处理 CSS。它负责把 CSS 代码解析成抽象语法树结构（Abstract Syntax Tree，AST），再交由插件来进行处理。

插件基于 CSS 代码的 AST 所能进行的操作是多种多样的，比如可以支持变量和混入（mixin），增加浏览器相关的声明前缀，或是把使用将来的 CSS 规范的样式规则转译（transpile）成当前的 CSS 规范支持的格式。

从这个角度来说，PostCSS 的强大之处在于其不断发展的插件体系。目前 PostCSS 已经有 200 多个功能各异的插件。开发人员也可以根据项目的需要，开发出自己的 PostCSS 插件。

**PostCSS 的主要功能只有两个：第一个就是前面提到的把 CSS 解析成 JavaScript 可以操作的 AST，第二个就是调用插件来处理 AST 并得到结果。**

postcss-loader是用来将css解析为js能操作的AST并启动各种PostCSS插件的loader



例如想给一些特殊的css样式加上浏览器前缀可以使用autoprefixer插件

安装postcss-loader

```
npm install postcss-loader -D
```

安装css插件autoprefixe，用于插入浏览器前缀

```
npm install autoprefixer -D
```

创建一个postcss-config.js文件配置，导入插件

```javascript
modele.exports = {
	plugins: [
		require('autoprefixer')
	]
}
```

```javascript
{
  test:/\.scss$/,
  use: [
    'style-loader',
     {
       loader:'css-loader',
       options: {
         importLoaders: 2
       }
     },
    'scss-loader',
    'postcss-loader'
  ]
}
```

webpack打包之后autoprefixe插件会自动给一些特殊的css样式（例如transform）自动加上浏览器前缀例如` -webkit- `。

css-loader需要配置`option.importLoaders : 2`是因为在scss文件中可能会import引入其他sass文件，但引入的sass文件可能不会被`'scss-loader'`, `'postcss-loader'`两个loader给识别到，设置`importLoaders : 2`就是在sass引入其他sass文件时还必须重新经过前两个loader处理才行。



### css module

为了让一个文件引入的css不影响其他文件，必须要将css模块化，所以必须给css-loader进行配置options.modules为true

```javascript
{
  test:/\.scss$/,
  use: [
    'style-loader',
     {
       loader:'css-loader',
         options: {
             importLoaders: 2,
             modules: true //开启css模块化
         }
     },
    'scss-loader',
    'postcss-loader'
  ]
}
```

配置之后css就是独立的模块化，不会相互影响（在*vue*组件中,在*style*标签上添加scoped属性,以表示它的样式作用于当下的*模块*,很好的实现了样式私有化的目的）



### 字体

字体文件使用file-loader进行打包（把字体文件的移动到dist文件夹引用）

```javascript
{
  test:/\.(eot|ttf|svg)$/,
  use: {
    loader: 'file-loader'
  }
}
```







# Plugins插件

loader 被用于转换某些类型的模块，而插件（plugin）则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能极其强大，可以用来处理各种各样的任务。

想要使用一个插件，只需要 在配置文件中`require()` 引入它，然后new一个它的实例添加到 `plugins` 数组中（loader是添加进module对象中）。多数插件可以通过选项(option)自定义。

plugin会在webpack运行到某个时刻的时候做一些事情（类似生命周期函数）。每个plugin运行的时刻不同，例如

HtmlWebpackPlugin就是在js文件打包完成后再执行生成html模板引入打包js文件。



## 常用plugins
**HtmlWebpackPlugin**：简化了HTML文件的创建；使用模板，通过webpack生成html文件，并为html文件引入外部资源

**CleanWebpackPlugin**：在进行打包之前清空原来文件的打包内容

**MiniCssExtractPlugin**：CSS文件压缩插件

**webpack.HotModuleReplacementPlugin**：热更新插件

**DLLPlugin**：第三方库打包成静态文件，下次就无需再次打包了



## html-webpack-plugin

这个插件用于自动在dist文件夹中根据模板文件生成一个html文件，无需手动创建，并且自动引入打包的js文件

```
npm install html-webpack-plugin -D
```

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin')
// 这个插件用于自动在dist文件夹中根据模板文件生成一个html文件，无需手动创建，并且自动引入打包的js文件

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  plugins: [
    new HtmlWebpackPlugin({
        template: 'src/index.html'  //模板文件地址
    }) 
  ]
}
```



## clean-webpack-plugin

每次打包前清空dist文件夹

```
npm install clean-webpack-plugin -D
```

```javascript
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: 'src/index.html'
    }),
    new CleanWebpackPlugin() // 每次打包之前清空上次打包的dist文件夹
  ]
}
```

clean-webpack-plugin更新新版本之后引入方式变为

```
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
```

且配置插件的构造函数无需传入配置（旧版本需要传入打包文件夹地址），可以自动清空上次打包的文件夹





# resolve解析

## alias 别名

配置全局的路径别名

```javascript
const path = require('path')
module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  resolve: {
    alias: {
      "@":path.resolve(__dirname, 'src')
    }
  }
}
```

在文件中都可以使用@来代替src了





# SourceMap代码溯源 

如果打包后的js代码出错，直接在浏览器中查看的错误会提示在打包好的dist文件夹下的js文件中，但排错时我们需要知道在src中源代码的错误在哪。

**SourceMap是一个用于建立源代码与打包代码之间映射的工具**，可以将dist文件夹中打包的js文件中每一行代码对应src中的js文件中的哪一行给映射出来。开启这个选项导致打包变慢。

在webpack配置导出对象中加一个属性devtool，开启映射设置为`source-map`，关闭映射设置为`none`。开启映射后，报错信息会显示错误代码在src源代码中的位置，而不是dist中已经打包好的js文件中。



如果将devtool设置为`'SourceMap'`映射后会在dist文件中存储一个js.map文件，其中存储了代码的映射关系

如果将devtool设置为`'inline-source-map'`，则不会单独生成一个map文件保存映射，它会将映射关系保存进打包好的js文件中。

如果将devtool设置为`'cheap-inline-source-map'`，是将映射精度减小到列，而不是精准到字符，用于提升打包性能。并且这个选项只会映射用户写的代码，第三方模块的代码是不做映射的。

如果将devtool设置为`'cheap-module-inline-source-map'`，就会对第三方模块也做映射。

如果将devtool设置为`'eval'`是最快的映射方式，不会生成映射文件，而是在代码上加上eval函数来建立映射关系，是性能最后的打包方式，但是代码较为复杂时，提示错误可能不精确。

在开发环境（development）中性能和映射精度取舍比较适合的是将devtool设置为`'cheap-module-eval-source-map'`

在线上生产环境（production）中比较合适的是将devtool设置为`'cheap-module-source-map'`

```javascript
module.exports = {
    devtool: 'cheap-module-source-map', 
    mode:'production', 
	entry: './src/index.js',
	output: {	
		filename: 'main.js',
		path: path.resolve(__dirname, 'dist')
	}
}
```



# DevServer自动打包

提供自动打包，代理转发等功能，一共有三种方案实现



## watch

使用`webpack --watch`命令来打包，当src修改时，dist就会自动去重新打包

在package.json中修改命令别名

```JSON
"script":{
  "build": "webpack",
	"wacth": "webpack --watch"
}
```

此时`npm run watch`打包，之后修改了src中的源码，dist也会自动重新打包。

但是每次修改就自动build在磁盘中重新打包，性能太低，所以不推荐这样使用



## WebpackDevServer

```
npm install --save-dev webpack-dev-server
```

webpack提供一个基于node.js的本地服务器，内部使用express框架，可以实现想要的让浏览器自动刷新显示修改后的结果。**启动服务器是因为前端需要发送http请求，如果只是打开一个html页面是无法发送http请求的。所以vue/react的脚手架工具都需要开启服务器。**

webpack-dev-server 主要提供两个功能：

- 为静态文件提供服务
- 自动刷新和热替换(HMR)

他可以监听一个文件夹，如果文件夹内代码发生改变他会自动编译，放入内存测试，无需像watch一样频繁访问磁盘，速度更快，但因为本地服务器是放入内存的，不会放入磁盘，用于修改代码测试，修改完成后还是需要保存并重新打包才会输入磁盘。安装之后使用命令`webpack-dev-server`即可使用devServer自动打包。

devServer具有很多webpack配置：
	**contentBase**：为哪一个文件夹提供服务，默认是根文件夹(修改为./dist)

​	**port**：指定服务端口号

​	**inline**：页面是否实时刷新

​	**historyApiFallback**：在SPA页面中，依赖HTML5的history模式

​	**open**：自动打开浏览器并进入服务器

​	**proxy**：配置转发代理

​	**hot/hotOnly**：hot 和 hotOnly 的区别是在某些模块不支持热更新的情况下，前者会自动刷新页面，后者. 不会刷新页面，而是在控制台输出热更新失败

```javascript
module.exports = {
    mode:'production', 
	entry: './src/index.js',
	output: {	
		filename: 'main.js',
		path: path.resolve(__dirname, 'dist')
	},
	devServer: {
		contentBase: './dist',
        open: true,
        port: 8080,
        hot: true,
        hotOnly: true,
        historyApiFallback：true,
        proxy: { 
          //转发代理，访问/api会被重定向到http://localhost:3000
		  "/api":"http://localhost:3000",
          // 从/getjson请求header.json文件时会被转发到localhost:3000请求demo.json
          '/getjson': {
             target: 'http://localhost:3000',
             pathRewrite: {
               'header.json': 'demo.json'
             }
           }
        }
	}
}
```

在package.json中进行配置，使用npm run start即可启动devServer进行调试，调试完成后再build完成实际打包

```json
"script":{
    "build": "webpack",
	"start": "webpack-dev-server"
}
```







## 实现一个devServer

模仿devServer实现启动一个服务器

1.  **在package.json中进行配置，自定义一个命令用于启动服务器**

```json
"script":{
    "build": "webpack",
	"start": "webpack-dev-server",
    "server": "node server.js"
}
```



2. **安装express和webapck-dev-middleware**

webapck-dev-middleware是一个用于webpack开发的中间件

```
npm install express webapck-dev-middleware
```



3. **在根目录创建一个server.js用于创建服务器**

webpack可以在node中使用，也可以在命令行中使用，在node中使用需要引入webpack库并传入配置对象

```javascript
const express = require('express')
const webpackDevMiddleware = require('webpack-dev-middleware')
const webpack = require('webpack') //引入webpack库
const config = require('./webpack.config.js') //引入webpack配置对象
const complier = webpack(config) // 这是在node中使用webpack
// webpack库传入配置对象后返回一个编译器
// 执行这个编译器，就会重新使用webpack根据配置打包代码

const app = express()
app.use(webpackDevMiddleware(complier, {}))

app.listen(3000, () => {
  console.log('server is running')
})
```

之后使用npm run server即可启动自己创建的服务器，每当src改变，就会重新打包，进入`localhost:3000`即可查看到页面





# HMR热模块替换 

Hot Module Replacement 热模块替换（HMR）

热模块替换是指，当只有src中的样式文件改变时，我们不刷新浏览器，只动态的改变样式。避免为了去看到样式而做很多重复操作。用于在开发过程中，实时预览修改后的页面，无需重新加载整个页面。

- 保留在完全重新加载页面时丢失的应用程序状态。
- 只更新变更内容，以节省宝贵的开发时间。
- 调整样式更加快速 - 几乎相当于在浏览器调试器中更改样式。

在webpack配置文件的devServe的配置中进行开启hot，（hotOnly开启后即使XHR出现问题无法正常执行也不自动刷新页面）。在引入webpack自带的一个插件webpack.HotModuleReplacementPlugin()，开启HMR（热模块替换）功能

```javascript
const webpack = require('webpack')
module.exports = {
  mode:'production', 
	entry: './src/index.js',
	output: {	
		filename: 'main.js',
		path: path.resolve(__dirname, 'dist')
	},
	devServer: {
		contentBase: './dist',
      open: 'true',
     	port: 8080,
     	hot: true,
     	hotOnly: true
	},
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ]
}
```

配置完成后样式文件发生改变不会刷新浏览器，而是直接去改变样式进行热模块更新

js文件也可以引入热模块更新，每当监听的文件发生改变就回去执行对应的回调函数。

除了上面的webpack配置外还需要在index.js入口文件中进行使用module.hot.accept监听文件变化。

```javascript
import a from './a.js'
import b from './b.js'

a()
b()

if(module.hot) {
    // 本地服务器运行过程中，a文件发生变化就去执行回调
    module.hot.accept('./a'), () => {
		console.log('执行热模块更新')
    }
}
```

每次a.js文件改变就会执行对应的回调





# babel

babel用于ES6转ES5

babel 总共分为三个阶段：解析，转换，生成。babel 本身不具有任何转化功能，它把转化的功能都分解到一个个 plugin 里面。因此当我们不配置任何插件时，经过 babel 的代码和输入是相同的。



**安装babel-loader和Babel核心库babel/core**

核心库可以把ES6语法转化为AST抽象语法树，babel-loader用于提供语法转换的接口（loader仅仅提供文件与翻译规则之间的接口，而不是靠loader翻译）

```
npm install --save-dev babel-loader @babel/core
```

配置babel-loader

```javascript
module: {
  rules: [
    { 
      test: /\.js$/,
      exclude: /node_modules/, 
      //如果文件是第三方的依赖文件这不需要使用loader转ES5，只有src文件转
      loader: "babel-loader"
    }
  ]
}
```

但是光安装loader是无法进行翻译的，因为还没有安装翻译规则，我们需要在options.presets里安装babel的翻译规则。



## babel原理

**1.解析**

解析步骤接收代码并输出 AST。 这个步骤分为两个阶段：词法分析（Lexical Analysis） 和 语法分析（Syntactic Analysis）。

​	词法分析：词法分析阶段把字符串形式的代码转换为 令牌（tokens） 流。

​	语法分析：语法分析阶段会把一个令牌流转换成 AST 的形式。 这个阶段会使用令牌中的信息把它们转换成一个 AST 的表述	结构，这样更易于后续的操作。



简单来说，解析阶段就是

```
code(字符串形式代码) -> tokens(令牌流) -> AST（抽象语法树）
```

Babel 使用 @babel/parser 解析代码，输入的 js 代码字符串根据 ESTree 规范生成 AST（抽象语法树）。Babel 使用的解析器是 babylon。



2.转换

转换步骤接收 AST 并对其进行遍历，在此过程中对节点进行添加、更新及移除等操作。 这是 Babel 或是其他编译器中最复杂的过程。

Babel提供了@babel/traverse(遍历)方法维护这AST树的整体状态，并且可完成对其的替换，删除或者增加节点，这个方法的参数为原始AST和自定义的转换规则，返回结果为转换后的AST。



3.生成

代码生成步骤把最终（经过一系列转换之后）的 AST 转换成字符串形式的代码，同时还会创建源码映射（source maps）。

代码生成其实很简单：深度优先遍历整个 AST，然后构建可以表示转换后代码的字符串。

Babel使用 @babel/generator 将修改后的 AST 转换成代码，生成过程可以对是否压缩以及是否删除注释等进行配置，并且支持 sourceMap。





## presets预设

安装babel/preset-env依赖模块，这个模块包含了大部分ES6转ES5的翻译规则

```
npm install @babel/preset-env --save-dev
```

将翻译规则加载到loader的options.presets中

```javascript
module: {
  rules: [
    { 
      test: /\.js$/,
      exclude: /node_modules/, 
      loader: "babel-loader",
      options: {
		  	presets: ["@babel/preset-env"]
	  }
    }
  ]
}
```

presets只处理语法上的问题，不处理api逻辑的相关问题，所以一些新的api还需要polyfill来翻译



## polyfill

babel/preset-env配置完成后打包后的ES6语法全部就会按照babel/preset-env中的规则转化为ES5

Babel默认只转换新的JavaScript语法（syntax），如箭头函数等，而不转换新的API，比如Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign、Array.from）都不会转码；因此我们需要polyfill来转码这些

```
npm install --save @babel/polyfill
```

在入口文件index.js引入babel/polyfill，babel/polyfil会把剩余的es6转化为es5。因为引入babel/polyfil包后会在window下绑定一些全局变量（例如Promise转化规则就是引入window.Promsise这个全局变量）。不设置按需引入会将所有语法规则全部引入，所以打包后的main.js文件会变得特别大，所以推荐在webpack中配置polyfill按需引入

```
import "@babel/poly-fill";
```



## polyfill按需引入

例如我们的文件中只有Promise这也一个ES6语法，但是引入babel/polyfil包会把所有ES6语法规则都一起注入，会特别占空间，因此在babel-loader的预设中设置按需引入useBuiltIns属性为usage，只有文件中使用到了的ES6语法才会被引入，精简了文件大小，且无需手动在入口文件index.js中手动`import "@babel/poly-fill"`，webpack会自动帮我们引入需要的语法规则。

```javascript
module: {
  rules: [
    { 
      test: /\.js$/,
      exclude: /node_modules/, 
      loader: "babel-loader",
      options: {
		  presets: [
            [
                "@babel/preset-env", 
                {
                  useBuiltIns: 'usage'
                }  // 按需引入
            ]
        ]
	  }
    }
  ]
}
```

presets中还可以配置target属性，判断浏览器的版本，如果高于指定版本则无需转换语法，因为浏览器支持ES6。

```javascript
presets: [
   [
     "@babel/preset-env", 
     {
       "targets": {
         "edge": "17",
         "firefox": "60",
         "chrome": "67",
         "safari": "11.1",
       },
       useBuiltIns: 'usage' // 按需引入
     }
   ]
]
```

设置按需引入以后，无需手动在入口文件index.js中引入`import "@babel/polyfill";`，webpack会在打包时自动引入polyfill。





## babel插件

我们在入口文件中`import "@babel/poly-fill";`引入了babel/polyfill用于语法转换，但是这样语法规则会通过window全局变量的方式直接注入JS文件，在给其他人使用库项目中（例如编写一个react类似的UI库）这样容易污染全局环境，可以采取使用插件的形式注入语法，普通的业务代码可以直接import引入。



使用插件babel/plugin-transform-runtime和babel/runtime这个模块解决库代码全局变量污染问题

```
npm install --save-dev @babel/plugin-transform-runtime
```

```
npm install --save @babel/runtime
```

在babel-loader的配置中使用插件

```javascript
module: {
  rules: [
    { 
      test: /\.js$/,
      exclude: /node_modules/, 
      loader: "babel-loader",
      options: {
		  "plugins":
         [
    	    [ 
            "@babel/plugin-transform-runtime",
      	    {
        	  "corejs": 2, // 采用哪种核心库
        	  "helpers": true,
        	  "regenerator": true,
        	  "useESModules": false
      		  }
    	    ]
 	    ]
	  }
    }
  ]
}
```

安装配置中的核心库babel/runtime-corejs2

```
npm install --save @babel/runtime-corejs2
```





# TreeShaking按需引入 

我们在入口文件中引入其他js文件时，有可能只需要一个导出模块，但是webpack打包的时候会把整个js文件所有的导出模块一起引入，造成打包文件过大。

Tree Shaking是webpack自带的功能，Tree Shaking是将导出模块变成一颗树状结构，能够在导入文件时按需导入，但是Tree Shaking只支持静态引入的ES6 module形式，不支持动态导入的CommonJS。

在webpack配置对象中开发环境下需要配置optimization属性，开发环境下（development）`optimization.usedExports`设置为true，在线上环境（production）下不用设置optimization属性

```javascript
constpath= require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  mode:'development',
  devtool: 'cheap-module-source-map',
	entry: './src/index.js',
	output: {
		filename: 'main.js',
		path: path.resolve(__dirname, 'dist')
	},
  optimization: {
		usedExports: true 
  }
}
```

在package.json中配置一个`"sideEffects"`字段，设置Tree Shaking将不按需引入`babel/poly-fill`文件和样式文件（*.css表示所有css文件），而是完全引入这些文件。

因为这个文件我们没有使用任何模块，但我们需要引入这个文件用于编译。不设置排除Tree Shaking就会忽略这个文件。

```json
{
  "sideEffects":[
    "@babel/poly-fill",
    '*.css'
   ]
}
```

配置好Tree Shaking后，webpack就会采取Tree Shaking的方式进行打包，如果引入了一个文件只会引入他想导入的模块，而不是整个文件一起被导入进来。









# CodeSplitting代码分割 

Code Splitting是一个概念，指对一个js文件中的代码代码拆分成多个js文件，提高下载带宽利用率，提高性能。



## 什么是代码分割

lodash是一个一致性、模块化、高性能的 JavaScript 实用工具库，提供了比原生更多的api可以调用（例如数组api，字符串api等）。假设我们的项目需要引入这个库。

安装lodash

```
cnpm install lodash --save
```

在main.js中引入库并写入业务代码

```javascript
import _ from 'lodash';
console.log(_.join(['a','b','c'],'***')) //a***b***c
```



假设loadsh库有1mb，我们的业务代码也有1mb，那main.js就有2mb大小。

webpack打包是将这个index.js打包为一个js文件，这个文件会很大（2mb），加载时间很长，并且上线之后，假设对其中一句业务代码进行了修改，用户就要重新加载这个很大的js文件才能正常显示页面。



**所以我们需要进行Code Splitting**

新建一个lodash.js

我们将lodash库导出的对象挂载到window上，这样打包后对象在全局上，其他js文件可以直接使用

```javascript
// lodash.js
import _ from 'lodash';
window._ = _
```

```javascript
//index.js
console.log(_.join(['a','b','c'],'***')) //a***b***c
```

需要将两个js文件分别打包，这样有了两个入口文件需要对webpack配置进行更改

```javascript
// entry: './src/index.js'
entry: {
   lodash: './src/lodash.js',
    main: './src/index.js'
  }, 
output: {
    filename: '[name].js',
    path: path.resolve(__dirname, '../dist')
}
```

打包后dist文件夹下会有main.js和lodash.js两个js文件，并且html-webpack-plugin插件生成的html会同时引入这两个js文件。

这样就将一个大的main.js文件（2mb）拆分成了两个js文件main.js和lodash.js，各1mb大小。浏览器下载文件的线程是多线程的，这样就利用了带宽同时下载2个文件，加快了加载速度。并且之前修改一句业务代码，需要重新下载整个大的js文件，现在我们可以对两个js文件进行缓存，哪个文件修改了代码就只重新请求该文件即可。



## optimization

我们手动进行Code Splitting十分繁琐，考虑的问题很多，webpack原生提供了SplitChunksPlugin插件进行代码分割。

webpack4之后，production模式下，SplitChunksPlugin插件是默认被启用的，optimization是插件的配置项，但是配置默认只分割异步的chunks（chunks默认为async），需要分割同步chunks需要将chunks设置为all。

```javascript
// webpack.config.js
constpath= require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
  mode:'production',
	entry: './src/index.js',
	output: {
		filename: 'main.js',
		path: path.resolve(__dirname, 'dist')
	},
  // SplitChunksPlugin插件配置optimization
  optimization: {
    splitChunks: {
       chunks: 'all'
    }
  }
}
```

webpack 在处理import这样同步的引入方式的时候，需要修改optimization配置，然后才会自动分析文件并进行代码分割。

而异步的引入方法，例如import()，无需做optimization配置，webpack在打包时会自动进行代码分割，并且还可以进行魔法注释，指定代码分割后的文件名称。

```javascript
import(/* webpackChunkName: "lodash" */'lodash')
/* webpackChunkName="lodash" */为魔法注释，指webpack代码分割后指定分割后的js文件名称
```



## es6 import()

在js中import需要放在顶部，import命令会被 JavaScript 引擎静态分析，先于模块内的其他模块执行。import()方法类似于 Node 的require方法，区别主要是前者是异步加载，后者是同步加载。

es6具有动态导入的语法import()方法（可以使用魔法注释为chunk命名），可以**异步**导入模块，通过返回promise对象处理结果

引入的变量需要用`{default: 变量名}`进行赋值，default表示引入的文件的导出对象，变量名表示接收对象，此后使用变量即可。

```javascript
// 定义一个getStr方法，调用这个函数，异步加载loadsh库并执行then函数
function getStr() {
  return import( /* webpackChunkName: "lodash" */ 'lodash').then(
    ({ default: _ }) => {
      console.log(_.join(['a','b','c'],'***')) //a***b***c
    }
  )
}
getStr()
```

`import()`的适用场景有：

（1）按需加载。

`import()`可以在需要的时候，再加载某个模块。

（2）条件加载

`import()`可以放在`if`代码块，根据不同的情况，加载不同的模块。

（3）动态的模块路径

`import()`允许模块路径动态生成。

```javascript
import(f()).then(...);
// 上面代码中，根据函数f的返回结果，加载不同的模块。
```

### lazy loading

模块懒加载，利用import()这样的异步加载语法配合代码分割，提高js加载速度。



## SplitChunksPlugin

webpack4之后，production模式下，SplitChunksPlugin插件是默认被启用的（因为chunks默认设置为async，所以import()可以自动代码分割，同步引入需要手动开启插件的配置项chunks修改成all），插件的默认配置如下：

```javascript
optimization: {
splitChunks: {
    chunks: "async",
    minSize: 30000,
    minChunks: 1,
    maxAsyncRequests: 5,
    maxInitialRequests: 3,
    automaticNameDelimiter: '~',
    name: true,
    cacheGroups: {
        vendors: {
            test: /[\\/]node_modules[\\/]/,
            priority: -10
        },
    	default: {
            minChunks: 2,
            priority: -20,
            reuseExistingChunk: true
        }
    }
}
}
```

optimization常见配置项有：

**chunks**: 可填 `async`, `initial`, `all`. async针对异步加载的chunk做切割，initial对同步加载进分割，all针对所有chunk都进行分割。

**minSize**: 我们切割完要生成的新chunk要>30kb，否则不生成新chunk。

**maxSize**：如果新生产的chunk大于maxsize，webpack会尝试进行二次代码拆分

**minChunks**: 共享该module的最小chunk数，也就是这个模块被chunks的引用（import）次数，如果被引用次数小于minChunks，则不进行代码分割

**maxAsyncRequests**：最多有5个异步加载请求该module，例如引入了10个类库，只有前五个会被分割，防止同时请求次数过多

**maxInitialRequests**： 初始化的时候最多有3个请求该module.

**automaticNameDelimiter**：生成js文件时组名和文件名中间的间隔符

**name**：chunk的名字，如果设成true，会根据被提取的chunk自动生成。

**cacheGroups**：我们要切割成的每一个新chunk（每一个模块）都是一个cache group缓存组。

符合条件的分割文件会被打包到同一个文件里

​	vendors是每一个组名，可以修改，最终打包出来的js文件名为`组名~文件名.js`

```javascript
vendors: {
  test: /[\\/]node_modules[\\/]/,
  priority: -10
},
```

​	test是指，发现模块是从node_modules中引入的就进行代码分割，并划入vendors组，文件名为`vendors~文件名.js`



- **test**：这个组的要求，用来决定提取哪些module，可以接受字符串，正则表达式，或者函数，函数的一个参数为module，第二个参数为引用这个module的chunk(数组).
- **priority**：优先级高的chunk为被优先选择,优先级一样的话，size大的优先被选择，例如一个文件同时满足两个组的test要求，选择优先级高的组进行命名。
- **reuseExistingChunk**: 当这个模块以及被拆分保存过，是否可以复用之前分割的chunk.





# css代码分割

```javascript
// index.js
import './style.css'
import './style1.css'

const element = document.createElement('div')
element.innerHTML = 'Wang Taiwei'
document.body.appendChild(element)
console.log('hello,world')
```

打包后dist文件夹内并没有css文件，因为webpack在打包时，默认会把css文件打包进js文件中



如果想要将css文件单独打包，就需要使用插件进行css代码分割，webpack中代码分割是由一个插件MiniCssExtractPlugin完成的，这个插件可以对引入的css文件进行代码分割。

```
npm install --save--dev mini-css-extract-plugin
```

在webpack配置文件中引入插件，loader在处理css时最后一步不再使用style-loader，使用插件给提供的loader

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module: {  
  rules: [
    {
      test:/\.css$/,
      use: [
        //"style-loader"
        MiniCssExtractPlugin.loader,
        'css-loader'
      ]
    }
  ]
},
plugins: [
  new MiniCssExtractPlugin()
]
```

配置完成后打包，Index.js在引入多个css文件时，MiniCssExtractPlugin插件默认将多个css文件合并成一个css文件并打包





# 打包分析工具

analyse 是webpack自带的打包分析工具

将打包命令修改为

```json
"build": "webpack --profile --json > stats.json --config webpack.config.js"
```

增加的`--profile --json > stats.json`字段是webpack在打包时生成一个json文件（stats.json）记录打包过程



可以将stats.json上传到以下网址进行分析打包过程

```
https://webpack.github.com/analyse
```

```
https://alexkuz.github.io/webpack-chart/
```

```
https://webpack.jakoblind.no/optimize/
```

使用webpack-bundle-analyzer插件（推荐）





# 预加载

浏览器控制台Coverage选项，点击监控按钮，可以监控代码的覆盖率。如果文件中有大量代码下载下来，却没有使用，代码覆盖率就低，异步的事件处理代码应该尽量分离成单独文件以提高代码覆盖率

例如，index.js打包后，开启网页就会整个index.js下载下来，但是可能并没有触发click事件，因此代码覆盖率就低了。

```javascript
// index.js
document.addEventListener('click', () => {
  const element = document.createElement('div')
    element.innerHTML = 'Wang Taiwei'
    document.body.appendChild(element)
})
)
```



提高代码利用率的思想是懒加载，将index.js中的异步事件分离为两个文件click.js和index.js，触发事件时再将事件处理函数import()进来，只有出发click事件的时候才会import下载click.js提高了代码利用率

```javascript
// click.js
function handleClick() {
    const element = document.createElement('div')
    element.innerHTML = 'Wang Taiwei'
    document.body.appendChild(element)
}

export default handleClick
```

```javascript
// index.js
document.addEventListener('click', () => {
  import('./click.js').then(({default: func}) => {
     func()
  })
})
```

但是用户触发事件的时候才去加载文件，可能会造成卡顿，所以需要进行Preloading和prefetching 进行预加载，当带宽空闲的时候就去下载所需文件，触发事件时就能直接调用了。



## Preloading/Prefetching

prefetching    n.预取; 预提取; 预加载; 预取技术; 预先抓取;

preloading     n. 初始负载(预加载)

预加载还是要使用魔法注释来开启，在import()内填写，开启后，用户浏览时一旦带宽有空闲，就会立刻去请求import的文件，达到预加载的效果



**Prefetch和Preload的区别**

**Prefetch**：会等待核心代码加载完成后，页面带宽空闲后再去加载prefectch对应的文件

```
/* webpackPrefetch: true */ 
```

**Preload**：和主文件一起去加载，不会等待带宽空闲再去加载

```
/* webpackPreload: true */ 
```

一般都是使用Prefetch，可以用prefetch来解决懒加载的用户体验问题



```js
// index.js
document.addEventListener('click', () => {
  import(/* webpackPrefetch: true */ './click.js').then(({default: func}) => {
     func()
  })
})
```





# Caching浏览器缓存

浏览器会自动缓存一些资源（如图片等），浏览器默认的缓存策略可能使浏览器不去请求最新更新的文件而是去使用缓存中的文件，所以上线时给打包文件名加上哈希值，这样浏览器每次都回去获取最新的文件

只有代码发生改变contenthash值才会发生改变，这样就避开了浏览器的缓存策略。

```javascript
output: [
  filename: '[name].[contenthash].js',
  chunkFliename: '[name].[contenthash].js',
]
```



 

# Shimming全局变量

`webpack` 编译器(compiler)能够识别遵循 ES2015 模块语法、CommonJS 或 AMD 规范编写的模块。然而，一些第三方的库(library)可能会引用一些全局依赖（例如 `jQuery` 中的 `$`）。这些库也可能创建一些需要被导出的全局变量。这些“不符合规范的模块”就是 *shimming* 发挥作用的地方。

webpack进行打包的时候每个文件都是一个模块，模块与模块之间是独立不耦合的，所以，一个模块就是一个作用域，模块不能直接使用其他模块作用域中定义的变量。

每次使用一个文件中的变量都需要手动import这个文件来导入，webpack提供了一个插件webpack.ProvidePlugin，可以自动导入变量，如果有一个文件使用了这个变量，就会去自动导入变量的模块，这样就实现了全局变量。

```javascript
// webpack.config.js
const webpack = require('webpack')

plugins: [
  new webpack.ProvidePlugin({
    $: 'jquery',
    _: 'lodash'
  })
]
```

配置好$为全局变量后，我们使用$就无需手动`import $ from 'jquery'`了，后台遇见$会自动帮我们在这个文件里引入jquery

```javascript
// import $ from 'jquery'
const element = $('<div>')
element.html(_.join(['wang','taiwei','hello'],'***'))
$('body').append(element)
$('body').css('background','red')
```





# 库打包

我们如果写了一个js库需要打包，但是库的打包配置和业务代码有些许不同，因为库代码需要被别人的业务代码引入，需要考虑引入方式等配置问题



## libraryTarget

建立了一个库需要打包，外部引用可能有多种方式，例如commonjs，es6 module，amd等，我们如果想让库兼容各种形式模块化引入就需要`output.libraryTarget`配置为umd，umd（通用）表示兼容各种模块化导入的方式。

配置`output.library`。如果以script以标签src的形式引入，但script标签引入无法为导出对象命名，library配置项会生成一个全局变量。用户可以在全局环境下通过这个全局变量使用库。

```javascript
output: {
  filename: 'library.js',
  path: path.resolve(__dirname, '../dist'),
  libraryTarget: 'umd', // 兼容各种模块化方式
  library: 'root' // script引入时，生成一个库导出的全局变量root
}
```

library和libraryTarget也可以配合使用将库代码挂载到某一个变量上，不再支持各种模块化导入，而是通过script标签引入，在通过变量拉取，例如将库浏览器环境下挂载到window上，node环境挂载到global上

```javascript
libraryTarget: 'global', // 将全局变量root挂载到global上
library: 'root'
```



## externals

如果我们的库代码中引入了其他第三方库(例如lodash)，用户引入了我们的库后，如果又引入了相同的第三方库，这个第三方库就会被打包两次在用户的源码中，可以设置配置对象中的externals属性，我们的库的源码中将不再打包lodash，需要用户在自己的业务代码中单独引入lodash库进行依赖，这样可以保证lodash只被引入一次。

external配置可以是数组也可以是对象，对象形式可以有更多配置项

```javascript
externals: {
  lodash: 'lodash' 
// 任何引入方式下，lodash库变量名必须叫'lodash'，防止用户引入的库后自定义变量名，我们的源码无法使用第三方库
  /*
  lodash: {
      commonjs: 'lodash' 
//该配置要求如果lodash库以commonjs方式被引入，lodash库变量名必须叫'lodash'，防止用户引入的库后自定义变量名，我们的源码无法使用第三方库
  }
  */
}
```

```javascript
module.export = {
  mode: 'production',
  entry: './src/index.js',
  output: {
    filename: 'library.js',
    path: path.resolve(__dirname, '../dist'),
  },
  externals: ["lodash"] // 第三方库不被打包，需要用户单独安装依赖
}
```



## 上传npm

在库代码打包后我们需要修改package.json中的main字段，将其修改为dist下已经打包好的入口文件，而不是src中的入口文件

然后在npm官网申请一个账号，在命令行中运行`npm adduser`，然后运行`npm publish`上传库

之后别人使用库就可以`npm install`就可以了，库的名字就是package.json中的name字段





# ESlint配置

安装eslint

```
npm install eslint --save-dev
```

eslint配置初始化

```
npx eslint --init
```

初始化后会自动一个eslint配置文件.eslintrc.js

```javascript
module.export = {
  "extends": "airbnb", // 以airbnb公司的js规范进行约束
}
```

官方默认解析器（parser）为Espree，现在流行的是babel-eslint解析器

```
npm install babel-eslint --save-dev
```

配置parser

```javascript
// eslintrc.js
module.export = {
  "extends": "airbnb",
  "parser": "babel-eslint"
}
```

检测语法命令：通过eslint工具检测src文件夹下的代码

```
npx eslint src
```

通过命令行执行eslint工具，错误会在命令行中显示，需要一行一行去找，所以可以在编辑器中下载eslint的插件，错误会直接在代码上飘红。



以上是不借用webpack使用eslint的方式，webpack中也可以配置eslint

安装eslint-loader

```
npm install eslint-loader --save-dev
```

配置eslint-loader

```javascript
{
  test: /\.js$/,
  exclude:/node_modules/,
  use: [
     'babel-loader',
      {
		    loader: 'eslint-loader',
        options: {
          fix: true, //自动修复一些小错误
        }
      }
    ] 
}
```





# 性能优化

1）升级版本（node，npm，webpack，yarn等）



2）在尽可能少的模块上应用loader

利用exclude，include等方式来降低loader执行频率，提高打包速度



3）尽可能少的使用plugin，并保证plugin的精简可靠



4）使用DllPlugin

DllPlugin可以将特定的类库提前打包然后引入。这种方式可以极大的减少打包类库的次数，只有当类库更新版本才有需要重新打包，并且也实现了将公共代码抽离成单独文件的优化方案



5）控制包大小

例如引入了包没有使用也没有配置Tree shaking，导致产生了很多冗余无用的代码，尽量减少这样的无用包的引入

然后利用代码分割将一个大包拆分成多个小包，加快打包速度



6）webpack默认通过node单线程打包，可以通过thread-loader，parallel-webpack（单页面应用打包时，可以同时打包多个页面），happypack来进行多进程打包，这些工具可以利用node开启多进程，打开多个cpu同时进行打包。



7）合理配置SourceMap



8）利用打包分析工具产生stats.json文件进行性能分析



9）开发环境下利用DevServer进行打包，在内存中打包速度远大于在硬盘中打包。



10）开发环境无用的插件剔除，例如开发环境下去压缩代码，导致速度变慢





# 多页面打包配置

整个应用中只有一个html文件的应用叫单页面应用，vue，react都是单页面应用，webpack绝大多数场景都是对单页面应用进行打包，如果想webpack打包生成多个html，需要对html-webpack-plugin进行额外配置。

template：html模板

filename：打包生成html的文件名

chunks：打包后html需要引入chunks数组

```javascript
enter: {
  main: './src/main.js',
  login: './src/login.js'
  runtime: './src/runtime.js'
},
output: {
  filename: 'library.js',
  path: path.resolve(__dirname, '../dist') // 生成chunks
}
plugins: [
  new HtmlWebpackPlugin({
    template: 'src/index.html',
    filename: 'index.html',
    chunks: ['runtime','main'] // 这个html需要引入的chunks
  }),
  new HtmlWebpackPlugin({
    template: 'src/login.html',
    filename:'login.html',
    chunks: ['runtime','list']
  })
]
// new了两次，每次都会根据相应的html模板打包生成一个html文件，分别引入不同的chunks
```





# 手撕loader

Loader是一个函数，会接收源码并返回源码，可以对源码进行修改来达到想实现的目的，loader函数中this是核心参数，this中携带了loader文件需要接收的webpack配置项参数和大量webpack提供的方法。

例如

源文件

```javascript
console.log('hello,world')
```

编写loader使打包后源码为

```javascript
console.log('hello,wtw')
```



## this.query

在根目录下新建一个loaders文件夹，新建replaceLoader.js

loader函数接收一个参数souce，代表了被处理文件的源码，将源码中world字符串替换为wtw

loader函数中this.query附带了webpack配置项中传递的options对象

```javascript
module.exports = function (souce) {
  // loader函数不能使用箭头函数，因为会有this指向问题,this是webpack配置项中传递的参数
  // 参数souce是引入的文件的源代码
  return souce.replace('world', this.query.name)
}
```

将自己编写的loader进行配置

```javascript
module: {
  rules: [
    {
      test: /\.js/,
      use: [
        {
          loader: path.resolve(__dirname,'./loaders/replaceLoader.js'),
          options: {
            name: 'wtw'
          }
        }
      ]
    }
  ]
},
```

借助resolveLoader配置项，可以省略path模块来配置loader路径

```javascript
resolveLoader: {
  module: ['node_modules','./loaders']
  // 引入loaders时先去node_modules找，找不到再去./loaders里找
},
module: {
  rules: [
    {
      test: /\.js/,
      use: [
        {
          loader: 'replaceLoader'),
          options: {
            name: 'wtw'
          }
        }
      ]
    }
  ]
}
```



## loader-utils

帮助loader获取query的工具库，防止this.query传递过程出现问题，它会自动分析this.query

```
npm install loader-utils --save-dev
```

```javascript
const loaderUtils = require('loader-utils')

module.exports = function (source) {
  const options = loaderUtils.getOptions(this)
  return source.replace('world', options.name)
}
```



## this.callback

loader只能返回源码，如果想让返回的源代码携带一些类似sourceMap的信息，需要使用this.callback替代return，可以把额外的参数返回出去。

this.callback()可以加入四个参数

第一个是err错误信息，第二个是源码，第三个是sourceMap信息，第四个参数可以放置一些其他额外任意信息

最后两个参数可以省略

```javascript
this.callback(
	err: Error | null,
	content: string | Buffer,
	sourceMap?: SourceMap,
	meta?: any
)
```

```javascript
const loaderUtils = require('loader-utils')

module.exports = function (source) {
  const options = loaderUtils.getOptions(this)
  const result = source.replace('world', options.name)
  this.callback(null, result)
}
```



## this.async

loader中一般不能写异步代码来返回source（例如定时器中返回源码等），会导致报错 

```javascript
const loaderUtils = require('loader-utils')

module.exports = function (source) {
  const options = loaderUtils.getOptions(this)
  setTimeout(() => {
    const result = source.replace('dell, options.name')
    return result
  }, 1000) // 报错
}
```

可以使用this.async()来异步的this.callback形式的返回，打包过程会多出1000ms等待异步。

```javascript
const loaderUtils = require('loader-utils')

module.exports = function (source) {
  const options = loaderUtils.getOptions(this)
  const callback = this.async()
  setTimeout(() => {
    const result = source.replace('dell', options.name)
    callback(null, result)
  }, 1000) // 成功返回
}
```



## loader案例

项目国际化

可以将项目中的文字信息替换为占位符，然后在loader中根据传入的环境参数编写替换

```javascript
module.exports = function (source) {
  let result
  if(this.query.language === 'chinese'){
     result = source.replace('{{documentTitle}}', '标题')
  } else {
     result = source.replace('{{documentTitle}}', 'title')
  }
  return result
}
```





# 手撕plugin

plugin是基于发布订阅的设计模式，代码与代码之间的执行是通过事件来驱动的。

loader是一个函数，但是plugin则是一个类（webpack配置中是new插件）

```javascript
class CopyrightWwebpackPlugin {
  constructor(options) { // options是new插件时传入的参数
    console.log('插件被使用了') // 打包时会在控制台显示
    console.log(options.name) // wtw
  }
  apply(compiler) {
    // apply传递compiler参数，compiler可以理解为webpack实例，可以监控webpack打包过程
    // hook是钩子，代表某一时刻会自动执行的函数，可以用来监控webpack的生命周期，tapAsync是执行异步任务
    // emit 在打包完成后，生成资源到 output 目录之前触发。
    // compilation参数存放的是跟本次打包所有相关内容，compilation.assets中存放了本次打包生成的内容
    // 在输出到optput之前，在资源里生成一个额外的文件copyright.txt
    compiler.hooks.emit.tapAsync('CopyrightWwebpackPlugin', (compilation,callback) => 	    {
         console.log(compilation.assets)
         compilation.assets['copyright.txt'] = {
          source: function() { // source是文件源码
            return 'hello,world'
          },
          size: function() { // size是文件大小
            return 21 
          }
         }
         callback()
      })
  }
}
module.exports = CopyrightWwebpackPlugin
```

```javascript
const path = require('path')
const CopyrightWwebpackPlugin = require('./plugins/copyright-webpack-plugin')
module.exports = {
  mode: 'development',
  entry: {
    main: './src/index.js'
  },
  output: {
    path: path.resolve(__dirname,'dist'),
    filename: '[name].js',
  },
  plugins: [
     new CopyrightWwebpackPlugin({
       name: 'wtw'
     })
  ]
}
```



## compiler.hooks

`Compiler` 支持可以监控文件系统的监听(watching)机制，并且在文件修改时重新编译。当处于监听模式(watch mode)时，compiler 会触发诸如 `watchRun`, `watchClose` 和 `invalid` 等额外的事件。通常用于开发环境中使用，也常常会在 `webpack-dev-server` 这些工具的底层之下调用，由此开发人员无须每次都使用手动方式重新编译。还可以通过 CLI进入监听模式。

## 相关钩子

以下生命周期钩子函数，是由 `compiler` 暴露，可以通过如下方式访问：

```js
compiler.hooks.Hook名.tap(插件类名, func)
compiler.hooks.Hook名.tapAsync(插件类名, func)
compiler.hooks.Hook名.tapPromise(...)
```



同步hook使用tap执行回调，异步使用tapAsync执行回调，异步的tapAsync的回调函数参数中需要传入第二个参数，callback函数，并在回调结束之后执行。

回调参数由当前hook的类型决定

```javascript
apply(compiler) {
  compiler.hooks.compile.tapAsync('CopyrightWwebpackPlugin', () => {
    console.log('compile执行')
  })
  compiler.hooks.emit.tapAsync('CopyrightWwebpackPlugin', (compilation,callback) => {
    console.log('emit执行')
    console.log(compilation.assets)
    callback()
  })
}
```



钩子信息官网查询



# 手撕bundler

安装@babel/parser帮助分析js文件的语法，他可以帮助将js转化为AST抽象语法树，将入口文件从代码转化为对象

```
npm install @babel/parser --save
```

安装@babel/traverse遍历所有的ast节点

```
npm install @babel/traverse --save
```

安装babel核心模块和@babel/preset-env对文件转es5，核心模块提供了transformFromAst方法，可以把ast的抽象语法树转化为浏览器可以运行的es5 

```
npm install @babel/core --save
npm install @babel/preset-env --save
```

主要思路：

babel将js文件转换为ast

根据文件的依赖关系循环解析生成Dependencies Graph依赖图谱

返回经过处理后的代码字符串，这就是最终bundler处理之后的代码



```javascript
// bundler.js
const fs = require('fs')
const path = require('path')
const parser = require('@babel/parser')
const traverse = require('@babel/traverse').default
const babel = require('@babel/core')

const moduleAnglyser = (file) => { // 文件分析函数
  const content = fs.readFileSync(file, 'utf-8')
  const ast = parser.parse(content, {
    sourceType: 'module'
  }) // 获取AST树
  // console.log(ast.program.body) ast.program.body保存了所有ast节点

  const dependencies = {}

  traverse(ast, {
    ImportDeclaration({ node }) { 
      //抽取import节点并执行函数,参数node就是传入的ImportDeclaration节点

      // node.source.value是import的相对于入口文件的文件路径
      // 需要path模块转换为相对于bundler.js的相对路径
      const dirname = path.dirname(file)
      const newFile = './' + path.join(dirname, node.source.value)

      dependencies[node.source.value] = newFile 
      // 将原本的相对路径和相对于bundler的路径都保存下来
      // { './message.js': './src/message.js' }
    }
  })
  const { code } = babel.transformFromAst(ast, null, {
    presets: ["@babel/preset-env"]
  }) // 处理后的返回值的code字段就是转换es5以后的代码
  // console.log(code)

  return { // 返回的解析结果包含文件地址，文件依赖，和babel编译后的es5代码
    file, // 文件地址
    dependencies, // 文件依赖
    code // 文件es5代码
  }
}

// Dependencies Graph 依赖图谱，根据文件的依赖关系进行循环解析
// 这个函数根据文件的依赖关系进行循环解析生成依赖图谱
const makeDependciesGraph = (entry) => {
  const entryModule = moduleAnglyser(entry) // 分析入口文件
  const graphArray = [ entryModule ] // 创建依赖图谱数组，初始只有入口文件的分析结果
  for(let i = 0; i < graphArray.length; i++) {
    const item = graphArray[i]
    const { dependencies } = item; // 将依赖提取出来
    if(dependencies) {
      for(let j in dependencies) {
         graphArray.push(moduleAnglyser(dependencies[j])) 
         // 将分析结果添加进依赖图谱并继续循环分析依赖的文件
      }
    }
  }
  // console.log(graphArray)
  // 对依赖图谱的数据结构进行转化，从数组变为对象，键为文件相对bundler的地址，值为code和dependencies
  const graph = {}
  graphArray.forEach(item => { 
    graph[item.file] = {
      dependencies: item.dependencies,
      code: item.code
    }
  })
  return graph
}


// 这个函数传入入口文件，返回一段代码的字符串
// 并且返回的代码必须在一个闭包中执行，避免污染全局
const generateCode = (entry) => {
    // 生成依赖图谱并转化为json字符串，避免传入的图谱变成[object]这种显示方式
    const graph = JSON.stringify(makeDependciesGraph(entry))
    
    // 返回代码字符串
    return 
    `
		  (function (graph) {
			// 由于图谱代码中存在require方法和exports对象，但是浏览器中没有，所以需要我们定义
		    function require(module) {
			  // 创建用于将相对路径转化为绝对路径的方法localRequire
			  function localRequire (relativePath) {
				return require(graph[module].dependencies[relativePath])
			  }

			  // 创建exports对象
			  var exports = {}		

			  // 制造闭包执行传入的代码code（也就是图谱代码graph[module].code）
			  (function (require, code) {
				eval(code)
			  })(localRequire, exports, graph[module].code)
				
			  return exports // 导出代码执行结果
			}

		    require('${entry}')
		  })(${graph});
	`
}

// graghInfo就是经过bundler处理后的代码
const graghInfo = generateCode('./src/index.js') 
```





# webpack优化

webpack的优化

> 减少打包时间：
>
> - 优化Loader
> - Happypack
> - DllPlugin
> - 代码压缩
>
> 减少打包后的文件体积
>
> - 按需加载
> - Scope Hoisting
> - Tree Shaking



# webpack和glup的区别

> - gulp是基于流的构建工具：all in one的打包模式，输出一个js文件和一个css文件，优点是减少http请求，万金油方案。 
> - webpack是模块化管理工具，使用webpack可以对模块进行压缩、预处理、打包、按需加载等。