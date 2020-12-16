

# 环境搭建

https://reactnative.cn/docs/getting-started#yarn

- Ios

安装依赖：node.js，watchman，xcode， Cocapods

Watchman是 facebook 的一个开源项目，它开源用来监视文件并且记录文件的改动情况，当文件变更它可以触发一些操作,例如执行一些命令等等。在使用React Native时，官方推荐推荐安装watchma，否则可能会遇到一个Node.js监视文件系统的BUG。

- Android

必须安装的依赖有：Node、Python2、JDK1.8 和 Android Studio。



# 创建项目

```
npm install react-native-cli

npx react-native init RNdemo // 创建普通RN项目

npx react-native init RNdemo --template react-native-template-typescript
// 创建ts模板的RN项目
```

运行安装模拟器

```
yarn android
# 或者
yarn react-native run-android
```



连点两次R：模拟器刷新界面

ctrl+M： 点击debug，会打开一个浏览器控制台，可以查看代码中的的console.log调试信息

RN中封装了Fetch API，也内置了ajax的XHR对象，也支持WebSocket



**多环境**

使用react-native-config插件来配置多环境

```
yarn add react-native-config
```





# 组件

RN的运行环境不是浏览器，所以react编写的web应用不同，不是使用div，span等标签，而是使用RN官方提供的组件，例如View，Text等组件来搭建页面，使用组件之前需要从react-native中导入

```jsx
import React from 'react';
import { View, Text } from 'react-native';

class App extends React.Component {
  render() {
    return (
      <View>
        <Text>Hello,World</Text>
      </View>
    )
  }
}

export default App;
```



## 基础组件

### View

类似div，但是View中不能直接放字符串



### Text

显示文本内容的组件，Text组件中只能放字符串或Text组件，且Text组件中的多个Text组件不会自动换行，超出屏幕宽度才会自动换行

```jsx
<Text>
  <Text>hello,</Text>
  <Text>word</Text>
</Text>
// hello,world

<View>
  <Text>hello,</Text>
  <Text>word</Text>
</View>
// hello,
// world
```



### Image

显示图片内容的组件



### TextInput

文本输入框组件



### StyleSheet

提供类似CSS样式表的样式抽象层



### ScrollView

可滚动的容器视图组件。在html中，如果元素需要滚动，要设置`overflow: scroll`，RN中需要使用ScrollView或FlatList组件，并且RN中一旦当前页面超出了屏幕高度或宽度，也不会自动出现滚动条，也需要使用ScrollView让页面可以滚动。

ScrollView会简单粗暴地把所有子元素一次性全部渲染出来，所以存在性能问题，`FlatList`组件会惰性渲染子元素，只在它们将要出现在屏幕中时开始渲染。但惰性渲染逻辑要复杂很多，因而 API 在使用上也更为繁琐。此外`FlatList`组件还可以方便地渲染行间分隔线，支持多列布局，无限滚动加载等等。

ScrollView 必须有一个确定的高度才能正常工作，一般直接设置高度不太合适，一般都是设置`flex: 1`。



**组件属性：**

- `horizontal`

当此属性为 true 的时候，所有的子视图会在水平方向上排成一行，而不是默认的在垂直方向上排成一列。默认值为 false。



- `alwaysBounceVertical`

bool值，当此属性为 true 时，垂直方向即使内容比滚动视图本身还要小，也可以弹性地拉动一截。当`horizontal={true}`时默认值为 false，否则为 true。



- `keyboardDismissMode`

`'none'` （默认值），拖拽时不隐藏软键盘。

`'on-drag'`，当拖拽开始的时候隐藏软键盘。

`'interactive'`，仅ios可用，软键盘伴随拖拽操作同步地消失，并且如果往上滑动会恢复键盘。安卓设备上不支持这个选项，会表现的和`none`一样。



### FlatList

FlatList组件是可滚动组件，会惰性渲染子元素，只在它们将要出现在屏幕中时开始渲染，如果需要分组/类/区（section），需要使用SectionList组件。

FlatList具有以下优点：

​	支持水平布局模式

​	行组件显示或隐藏时刻配置回调事件

​	支持单独的头部，尾部组件

​	支持自定义行间分隔线

​	支持下拉刷新

​	支持上拉加载

​	支持跳转到指定行（ScrollToIndex）

​	支持多列布局

**组件属性**

- data

  存储列表渲染的数组



- renderItem

  renderItem是一个函数，需要返回jsx

  会对data数组中每一项遍历执行该函数并将该jsx render渲染到屏幕上

```jsx
<FlatList
	data= {[{name: 'a'}, {name: 'b'}]}
	renderItem = {
	  {item} => {
	    <Text>{item.name}</Text>
	  }
	}
/>
```



- keyExtractor

  keyExtractor是一个函数，会遍历执行data每一项，并将返回值作为每个item的key值

  

```react
import React from 'react';
import { FlatList, Text } from 'react-native';

class App extends React.Component {
  constructor(props){
    super(props);
    this.state = {
        data:[{name:'fan'}, {name:'chen'}, {name:'liu'}, {name:'kan'}]
    }
  }
  render() {
    return (
      <>
        <FlatList
          keyExtractor={(item, index) => item.name}
          data= {this.state.data}
          renderItem = {
            ({item}) => 
              <Text key={item.name}>{item.name}</Text>
            }
        />
      </>
    )
  }
}

export default App;
```



**组件方法**

1. scrollToEnd: 滚动到底部触发。如果不设置getItemLayout属性的话，可能会比较卡。
2. scrollToIndex:滚动到指定index的item触发，如果不设置getItemLayout属性的话，无法跳转到当前可视区域以外的位置。
3. scrollToItem: 滚动到指定item触发，如果不设置getItemLayout属性的话，可能会比较卡。
4. scrollToOffset: 滚动指定距离触发



## Tableable类组件

html中，可以直接给div绑定一个点击事件来响应用户的点击或者触摸，RN的button组件不能自定义样式，并且RN的View组件中虽然有一系列的触摸处理函数，但是想要监听到用户的操作是点击，还是不方便的。

所以RN提供了一系列的Touchable组件，专门为用户处理点击事件。例如`TouchableOpacity`，`TouchableNativeFeedback`，``TouchableHighlight`等。用这些特定组件来代替View创建需要触发点击事件的组件。

- `TouchableHighlight`组件

  高亮触摸，用户点击时，会产生高亮效果。

  组件属性

  - `activeOpacity`：在触压时的空间的透明度

  - `underlayColor`：触压的时候的背景变化的颜色

  

- `TouchableOpacity`组件：透明触摸。

  用户点击时，点击的组件会出现透明效果。

  组件属性

  - `activeOpacity`：在触压时的空间的透明度

  

- `TouchableWithoutFeedback`组件：无反馈点击。

  用户点击时无任何视觉效果。这是一个具备Android原生样式的组件



### 事件

Touchable组件组件都可以监听以下事件：

- onPress：  触摸操作结束时调用
- onPressIn：  手指按下时就调用，在onPress前调用

- onPressOut：  手指抬起触摸释放之后立即调用，在onPress前调用
- onBlur：失去焦点事件
- onFocus：得到焦点事件
- onLayout：当加载或布局改变的事件
- onLongPress：长按事件







# StyleSheet

React-Native编写的组件样式不是靠css来实现的，而是依赖js来添加样式。RN中所有组件都同组件的style属性来接收样式，样式属性命名基本遵循了驼峰化的css属性名（属性值不用驼峰化，直接写成字符串）。

但是RN中不能给除了Text以外的组件设置文本样式，例如颜色字体等，所以不能给父组件设置文本属性继承给子组件使用。

RN中的尺寸都是无单位的，表示的是与设备像素密度无关的逻辑像素点（例如设置了50，那么在所有手机上，它显示的大小都是一样的），默认值为auto，也可以使用百分比来设置宽高，除了数字外，其他属性值（颜色，百分比等）应该设置为字符串。



RN组件可以使用行内样式。

```
<View style={{width: 50, height: 50}}></View>
<View style={{width: '20%', height: 50}}></View>
```

但更常用的情况是使用StyleSheet，需要从rn中引入StyleSheet，使用StyleSheet.create()方法来创建样式对象，然后通过style属性为组件添加样式。

```jsx
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

class App extends React.Component {
  const mystyle = {width: '20%', height: 50, backgroundColor: 'red'}
  render() {
    return (
      <>
        <View style={mystyle}>
        	<Text>Hello,World</Text>
        </View>
        <View>
        	<Text style={styles.container} >Hello,World</Text>
        </View>
      </>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    backgroundColor: 'red',
    width: '50%',
    height: 50
  }
})

export default App;
```



## 布局

RN中主要使用flexBox进行布局，使用flexDirection，alignItems和justifyContent三个样式属性就能满足大多数布局需求。父盒子不需要设置`display:flex`，直接使用flex的属性即可。

```jsx
const styles = StyleSheet.create({
  container: {
    backgroundColor: 'red',
    width: '50%',
    height: 50,
    flexDirection: 'col',
    justifyContent: 'center',
    alignItems: 'flex-end'
  },
  item1: {
  	flex: 1,
    backgroundColor: 'blue'
  },
  item1: {
  	flex: 1,
    backgroundColor: 'green'
  }
})
```





# 配置路径别名

web应用中使用webpack的resolve.alias配置别名实现设置绝对路径别名，RN中可以通过插件`babel-plugin-module-resolver`来实现这个功能。

```
yarn add babel-plugin-module-resolver
```

然后在`babel.config.js`中配置

```javascript
module.exports = {
  presets: ['module:metro-react-native-babel-preset'],
  plugins: [
    [
      'module-resolver',
      {
        root: ['./src'], // 设置从哪个路径为配置的根目录
        alias: { // 配置路径别名
          '@': './src',
          '@/pages': './src/pages',
          '@/utils': './src/utils'
        }
      }
    ]
  ]
};
```

但是ts文件依旧找不到路径，需要在tsconfig.json中修改，这样ts文件也能找到路径不会报错

```json
"baseUrl": "/src"
// 将根路径从./修改为./src
"path": {
  "@/*": ["*"],
  "@/pages/*": ["pages/*"],
  "@/utils/*": ["utils/*"],      
}
```





# react-navigation

导航器可以看做一个普通的react组件。可以用来定义app的导航结构，导航器还可以用来渲染通用元素

类似router，native中叫导航器，用于切换不同页面，常用的是第三方库react-navigation



**react-navigation导航器分类**

可以用以下api创建出导航器组件对象

- createStackNavigator：堆栈式导航器，屏幕上方会有返回上一级按钮和标题栏
- createBottomTabNavigator：底部导航器
- createDrawerNavigator：抽屉导航栏，侧边滑出
- createMaterialTopTabNavigator：屏幕顶部的材料设计主题标签栏



**两个与导航相关的概念**

- Screen navigation prop 屏幕导航属性

  可以通过导航器完成页面之间的调度的属性

  

- Screen navigationOptions 屏幕导航选项

  定制导航器显示的方式，样式，例如导航栏标题，选项卡标签，切换页面动画样式等

  

## 初始化

```
yarn add @react-navigation/native
```

还安装需要的依赖库：`react-native-gesture-handler`, `react-native-reanimated`, `react-native-screens` and `react-native-safe-area-context` and `@react-native-community/masked-view`

```
yarn add react-native-reanimated react-native-gesture-handler react-native-screens react-native-safe-area-context @react-native-community/masked-view
```

`react-native-reanimated`：动画库，组件式声明动画

`react-native-gesture-handler`：手势库，性能很好的跨平台的手势库

`react-native-screens` ：使用原生代码实现导航器，提高性能

 `react-native-safe-area-context` ：识别异型屏幕，获取哪些地方是安全的

`@react-native-community/masked-view`：堆栈式导航器依赖的库





ios上需要手动安装ios所需的一些依赖（必须在mac环境下）

```
cd ios
npx pod-install ios
```

android在RN0.6版本后会自动安装依赖



配置`react-native-gesture-handler`**手势库**: [官方文档](https://docs.swmansion.com/react-native-gesture-handler/docs/)

进入`android/app/src/main/java/com/[projectName]/MainActivity.java`文件，增加以下代码

```java
import com.facebook.react.ReactActivityDelegate;
import com.facebook.react.ReactRootView;
import com.swmansion.gesturehandler.react.RNGestureHandlerEnabledRootView;

@Override
protected ReactActivityDelegate createReactActivityDelegate() {
   return new ReactActivityDelegate(this, getMainComponentName()) {
     @Override
     protected ReactRootView createRootView() {
      return new RNGestureHandlerEnabledRootView(MainActivity.this);
     }
   };
}
```



在根目录index.js中导入库

```javascript
import 'react-native-gesture-handler';
```



在根路径下的index.js中将首页修改为导航器主页

```javascript
import {AppRegistry} from 'react-native';
// import App from './App';
import App from './src/navigation/index';
import {name as appName} from './app.json';
import 'react-native-gesture-handler';

AppRegistry.registerComponent(appName, () => App);
```



## 导航器组件对象

**`NavigationContainers`是管理所有导航器和所有导航状态的组件，从`@react-navigation/native`中导入，所有导航器都需要放在这个组件中**

```tsx
// src/navigation/index.js
import Home from '@/pages/Home'
import Detail from '@/pages/Detail'
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

function App() {
   return (
     <NavigationContainer>
       <Stack.Navigator>
         <Stack.Screen name="Home" component={Home} />
           <Stack.Screen name="Detail" component={Detail} />
       </Stack.Navigator>
     </NavigationContainer>
   );
}

export default App
```





## navigation对象

当导航器中的屏幕被打开时，页面组件的props中会被注入一个navigation对象和route对象，props.navigation具有以下属性

- navigate：用于跳转到其他页面
- state：已废除的属性，当前页面的state信息，现在被替换为props中的route对象
- setParams：改变当前页面导航器的route params，例如更新页面顶部标题，返回按钮等
- goBack：返回上一页或者路由栈指定的页面
- dispatch：向路由发送一个action
- addListener：订阅导航生命周期的更新
- isFocused：true表示屏幕获取了焦点
- dangerouslyGetParent：返回父导航器

注意：一个navigation有可能没有navigate，setParams以及goBack，只有state和dispatch，所以使用navigate进行跳转之前需要先判断，如果没有这个属性可以通过navigation对象dispatch一个新的action来完成页面之间的跳转

- dangerouslyGetState
- removeListener
- canGoBack
- setOptions



如果当前的navigation是stack navigation（堆栈式导航器）时，navigation上还有一些额外附加功能

- push：导航到堆栈中的一个新路由
- pop：返回堆栈中的上一个页面
- popToTop：跳转到堆栈中最顶层的页面
- replace：使用新路由替换当前路由
- reset：擦除导航器状态并将其替换为多个操作的结果

  



### navigate方法

navigation.navigate方法是在页面之间进行跳转

```
navigation.navigate({routeName, params, action, key})
或
navigation.navigate(routeName, params, action)
```

- routeName：要跳转页面的路由名，也就是Screen组件的name属性
- params：要传递给下一个界面的参数对象
- action：如果当前页面是一个navigator的话，将允许这个sub-action
- key：要导航到的路由的可选标识符，如果已存在，将后退到此路由

```tsx
class Home extends React.Component {
	render() {
	  const { navigation } = this.props
      return (
      	<View>
       	<Button 
          onPress = {() => {navigation.navigate('Detail', {age: 18})}}
          title = 'go tot Detail'
        />  
        </View>
      )
	}
}
```





### setParams

`navigation.setParams`方法用来改变当前页面的route params，例如更新页面顶部标题，返回按钮等，如果需要改变其他页面的route params可以通过`NavigationActions.setParams`完成

```jsx
class Home extends React.Component {
	render() {
	  const { navigation } = this.props
      return (
      	<>
       	<Button 
          onPress = {
            () => {
          		navigation.setParams({name: 'lucy'})
          	}
          }
          title = 'set title name to lucy'
        />  
        </>
      )
	}
}
```



### goBack

goBack方法可以返回上一页或者路由栈指定的页面

```
navigation.goback(key)
```

其中key表示要返回到的页面的标识，不是routeName，可以通过指定页面的navigation.state.key获取页面标识。也可以传入null

```
<Button 
   onPress = {() => {navigation.goBack()}} // 返回上一页
/> 
```



### dispatch

给当前页面设置action，会代替原来的跳转，回退等事件

```jsx
const resetAction = StackActions.reset({
    index: 0,
    actions: [
        NavigationActions.navigate({
            routeName: 'HomePage',
            params: {
                theme: theme,
                selectedTab: selectedTab
            }
        })
    ]
})
navigation.dispatch(resetAction)
```

如果你想用dispatch指定react-navigation的导航行为,你应使用react-navigation中提供的action creators





## route对象

route对象由三个属性组成

```javascript
{
  params: {...}, // setParams() 或 navigation.navigate() 传递的参数
  key: 'id-xxxxxxx-x', //当前页面唯一标识
  name: 'Home'
}
```

```jsx
// page1
<Button 
   onPress = {() => {navigation.navigate('page2', {age: 18})}}
/> 

// page2
const { route } = this.props
console.log(route.parmas.age) //18
```

可以通过route.params来获取通过setParams() 或 navigation.navigate() 传递的参数

可以通过指定页面的route.key获取页面标识







## NavigationActions

所有Navigation Actions所有方法都需要传入一个对象，可以使用`navigation.dispatch()`方法把对象发送到路由器，并执行相关操作，并使用Navigation Actions的结果来更新当前state

NavigationActions有如下方法

+ Navigate：导航到其他页面
+ Back：返回到上一层页面
+ Set Params：设置页面的Params，例如标题等
+ Init：如果页面没有state时初始化一个state



### Navigate

用来跳转页面的NavigationActions

```
NavigationActions.navigate({routeName, params, action, key})
```

outeName - *String* - 必需 - 已在应用路由器中路由名称

params - *Object* - 可选 - 参数

action - *Object* - 可选 - （高级）如果屏幕是导航器，则在子路由器中运行的子操作。 本文档中描述的任何一个操作都可以设置为子操作。

key：页面的标识

```jsx
import { NavigationActions } from 'react-navigation'

const navigateAction = NavigationActions.navigate({
    routeName: 'Profile',
    params: {},
    action: NavigationActions.navigate({ routeName: 'SubProfileRoute' })
})

this.props.navigation.dispatch(navigateAction) // 执行action
```



### back

key为null回退到上一个页面

```
NavigationActions.back(key)
```

```jsx
import { NavigationActions } from 'react-navigation'

const backAction = NavigationActions.back()
this.props.navigation.dispatch(backAction) // 执行action
```



### setParams

`navigation.setParams`只能修改当前页面的params信息，但是通过NavigationActions的setParams可以去修改其他页面的params信息，并且navigation中可能只有state和dispatch，所以此时只能使用`dispatch(setParamsAction)`的方式来修改页面params信息



```
NavigationActions.setParams(params, key)
```

params：对象，必须参数，将会合并到已经存在于页面中的Params中

key：字符串，必选参数，页面的key

```jsx
import { NavigationActions } from 'react-navigation'

const setParamsAction = NavigationActions.setParams({
 	params: {title: 'Home'},
	key: 'id-xxxxx-x'   
})
this.props.navigation.dispatch(backAction) // 执行action
```





## StackActions

堆栈式导航器才有的一些actions

### reset

重置当前的state到一个新state，这个action将删掉所有的navigation state并且使用这个actions的结果来代替。

```
StackActions.reset({
	index: 数组，必选，表示navigation state中route数组中激活route的index,
	actions: 数组，必选，接收Navigation Actions数组，将会代替navigation state,
	key: 页面key值，可选，如果设置具有给定key的导航器将重置，如果为null，根导航器将会重置
})
```

```javascript
import { NavigationActions, StackActions } from 'react-navigation'

const resetAction = StackActions.reset({
  // 将路由的首页从欢迎页换成首页
  index: 0,
  actions: [
    NavigationActions.navigate({ routeName: 'Home' })
  ]
})

this.props.navigation.diapatch(resetAction)
```

使用场景：例如进入app首页后的欢迎页将不再使用，可以使用reset重置它，不重置的话在首页点击返回，将会回到欢迎页。





### replace

使用另一个路由替换指定路由

- key：string，被替换的路由的key
- newKey：string，用于替换的路由的key
- routeName：string，routeName用于替换路由
- params：obj，要传入替换路由的参数
- action：obj，可选的子动作



### push

在堆栈顶部添加一个页面，然后跳转到该页面，与navigate的区别在于，如果有已经加载的页面，navigate将跳转到已经加载的页面，而不是重新创建这个页面，而push永远都会创建新页面并放在栈顶，一个页面可以被创建多次。

- routeName：string，新建页面的routeName
- params：obj，合并到目标路由的this.props.navigation.state.params中

```jsx
import { StackActions } from 'react-navigation'

// 创建一个新的home页面并跳转
const pushAction = StackActions.push({
  routeName: 'Home',
  params: {
	myUserId: 100
  }
})

this.props.navigation.diapatch(pushAction)
```



### pop

当前页面出栈，返回上一层页面

- n：number，可以指定返回的层数

```jsx
import { StackActions } from 'react-navigation'

const popAction = StackActions.pop({
  n: 1
})

this.props.navigation.diapatch(popAction) // 返回两层
```



### popToTOP

跳转到堆栈最顶层页面，并销毁其他页面，功能上与`pop({n: currentIndex})`相同

```jsx
import { StackActions } from 'react-navigation'

this.props.navigation.diapatch(StackActions.popToTop)
```





## 堆栈式导航器

堆栈式导航栏会有返回上一层按钮和一个标题栏，页面堆积在一个栈中，先进后出。

```
yarn add @react-navigation/stack
```

**`NavigationContainers`是管理所有导航器和所有导航状态的组件，所有导航器都需要放在这个组件中**

`createStackNavigator`是创建堆栈式导航器的函数，这个函数返回一个对象Stack，这个对象含有Navigator，Screen两个React组件，Navigator组件是总的导航器组件。Screen组件放在Navigator组件内，用来定义路由展示组件页面。

```tsx
import { createStackNavigator } from '@react-navigation/stack'
const Stack = createStackNavigator()
```

Stack.Screen绑定页面组件后，会向该组件的props中注入navigation对象

默认情况下createStackNavigator提供了转场过渡效果，但是安卓和ios上效果不同，安卓默认是从底部淡入，ios是从右部滑入，可以使用配置让安卓也是右部滑入转场。

`Stack.Navigator`组件

- headerMode属性：string，切换页面时标题栏的动画，设置为“none”，安卓端默认标题栏会有重置动画，设置为“float”会统一为ios的样式，标题栏不会重置。

- screenOptions属性：可以统一设置子路由标题栏的样式，子路由Stack.Screen中也有options属性单独设置标题显示（两者拥有的属性一样）。

- initialRouteName
  首次加载的页面的名字，不设置默认安装包裹的第一个子组件显示

- keyboardHandlingEnabled
   如果为false，则导航到新屏幕时，屏幕键盘不会自动关闭。默认为true

- mode
   定义渲染和过渡转场的样式

  属性值

  ​	 card：使用标准的iOS和Android屏幕过渡。这是默认值.
  ​	 modal：这有两件事：设置headerMode到screen堆栈，除非指定使屏幕从iOS底部的底部滑入，这是一种常见的iOS模式

- headerMode
  指定标题的呈现方式
  float：渲染停留在顶部的单个标题，并在更改屏幕时进行动画处理。iOS上的常见模式。
  screen：每个屏幕都有一个附加的标题，标题随屏幕一起淡入和淡出。Android上的常见模式。
  none ：没有标题。




`Stack.Screen`组件

screen绑定子组件后会向组件的props中注入navigation对象，子组件中可以通过调用`navigation.navigate(name)`的方式完成页面的跳转

- name属性：默认的导航栏的标题，也是`navigation.navigate()`跳转路径传入的参数
- component属性：默认显示的页面
- options属性：可用于配置导航器内的各个屏幕导航栏样式
  - headerTitle属性：手动设置导航栏标题
  - headerTitleAlign属性：设置标题位置，‘left’，‘right’，‘cengter’
  - headerShown
    是显示还是隐藏屏幕标题。默认情况下显示标题，除非将headerMode其设置为none。设置为 false隐藏标题。在特定屏幕上隐藏标题时，您可能还需要将headerModeprop 设置为screen。

- title：头部标题
  

### 案例

新建路由，导航器自带一些页面切换动画和标题栏样式风格，可以统一设置为ios风格

```javascript
// src/navigation/index.tsx
import React from 'react'
import { NavigationContainer } from '@react-navigation/native'
import { createStackNavigator, 
         StackNavigationProp, 
         HeaderStyleInterpolators, 
         CardStyleInterpolators } from '@react-navigation/stack'
import Home from '@/pages/Home'
import Detail from '@/pages/Detail'
import { StyleSheet, Platform } from 'react-native'
// Platform用于判断当前平台，例如 Platform.OS == 'android' 是判断是不是安卓手机


const Stack = createStackNavigator()

class Navigator extends React.Component {
  render() {
    return (
      <NavigationContainer>
        <Stack.Navigator
          initialRouteName="Home"     //作为初始化页面、不写的话默认第一个screen为初始化页面
          headerMode='float' // 所有页面都显示标题栏
          screenOptions={{
            headerTitleAlign: 'center', // 标题栏文字居中
            headerStyleInterpolator: HeaderStyleInterpolators.forUIKit,
            cardStyleInterpolator: CardStyleInterpolators.forHorizontalIOS,
            gestureEnabled: true, // 开启安卓端手势系统，但默认上下滑动切换页面
            gestureDirection: 'horizontal', // 修改为左滑返回上一层页面
            headerStyle: { // 设置标题栏样式
              ...Platform.select({ // 使用Platform筛选平台
                android: { //只对安卓端生效的标题栏样式
                  // 消除切换页面的动画阴影
                  elevation: 0,
                  borderBottomWidth: StyleSheet.hairlineWidth
                }
              })
            }
          }}
        >
          <Stack.Screen 
            name="Home" 
            component={Home} 
            options = {{
              headerTitle: 'H' // 单独设置标题
            }}/>
          <Stack.Screen name="Detail" component={Detail}/>
        </Stack.Navigator>
      </NavigationContainer>
    )
  }
}

export default Navigator
```





在页面中设置按钮跳转

```jsx
import React from 'react'
import { View, Text, Button } from 'react-native'
import { RootStackNavigation } from '@/navigation/index'


class Home extends React.Component {
  onPress = () => {
    const {navigation} = this.props
    navigation.navigate("Detail", {
      id: 100
    }) // 跳转到Detail，携带一个对象,
  }
  render() {
    return (
      <View>
        <Text>Home</Text>
        <Button title='跳转Detail' onPress={this.onPress}/>
      </View>
    )
  }
}

export default Home
```



Detail页面中可以通过route.params拿到参数对象

```jsx
import React from 'react'
import { View, Text } from 'react-native'
import { RouteProp } from '@react-navigation/native'
import { RootStackParamList } from '@/navigation/index'

class Detail extends React.Component {
  render() {
    const {route} = this.props
    return (
      <View>
        <Text>Detail</Text>
        <Text>传入参数: {route.params.id}</Text>
      </View>
    )
  }
}

export default Detail
```



## 底部导航器

```
yarn add @react-navigation/bottom-tabs
```



**Tab.Navigator的配置**

1. initialRouteName
    导航首次加载时要渲染的路线的名称。
2. screenOptions
    导航中用于屏幕的默认选项。
3. backBehavior
    后退按钮处理的行为。
   - initialRoute： 返回初始标签
   - order： 返回上一个标签页（按照标签页中显示的顺序）
   - history： 返回上次访问的标签页
   - none：不处理后退按钮
4. lazy
    默认为true。如果为false，则所有选项卡都将立即呈现。如果为true，则仅在首次使选项卡处于活动状态时才显示它们。注意：选项卡不会在后续访问时重新呈现。
5. tabBar
    返回React元素以显示为选项卡栏的函数
6. tabBarOptions
    包含选项卡栏组件的道具的对象。它可以包含以下属性：
   - activeTintColor -活动标签的标签和图标颜色。
   - activeBackgroundColor -活动标签的背景颜色。
   - inactiveTintColor -非活动标签的标签和图标颜色。
   - inactiveBackgroundColor -非活动标签的背景颜色。
   - showLabel -是否显示标签标签，默认为true。
   - showIcon -是否显示标签图标，默认为true。
   - style -标签栏的样式对象。
   - labelStyle -标签标签的样式对象。
   - labelPosition-在何处显示与标签图标相关的标签标签。可用值为beside-icon和below-icon。默认为beside-icon。
   - tabStyle -标签的样式对象。
   - allowFontScaling -标签字体是否应缩放以符合“文本大小”辅助功能设置，默认为true。
   - adaptive-标签图标和标签对齐方式是否应根据屏幕尺寸而改变？true对于iOS 11 false，默认值为。如果，标签图标和标签始终垂直对齐。当时true，标签图标和标签在平板电脑上水平对齐。
   - safeAreaInset-覆盖forceInset道具。默认为{ bottom: ‘always’, top: ‘never’ }。可用的键top | bottom | left | right随值一起提供’always’ | ‘never’。
   - keyboardHidesTabBar-默认为false。如果true在键盘打开时隐藏标签栏。



Tab.Screen属性

**options属性 可用于配置导航内的各个屏幕。支持的选项有：**

1. title
    通用标题可以用作备用headerTitle和tabBarLabel。
2. tabBarVisible
    true或false显示或隐藏标签栏（如果未设置），则默认为true。
3. tabBarIcon
    给定的函数{ focused: boolean, color: string, size: number }返回一个React.Node，以显示在选项卡栏中。
4. tabBarLabel
    显示在选项卡栏中的选项卡的标题字符串或给定的函数将{ focused: boolean, color: string }返回React.Node，以显示在选项卡栏中。未定义时，使用场景title。
5. tabBarButton
    该函数返回一个React元素以呈现为选项卡按钮。它包装图标和标签并实现onPress。TouchableWithoutFeedback默认情况下渲染。tabBarButton: props => <TouchableOpacity {…props} />会TouchableOpacity改为使用。
6. tabBarAccessibilityLabel
    选项卡按钮的辅助功能标签。当用户点击选项卡时，屏幕阅读器会读取该内容。如果您没有标签的标签，建议您进行设置。
7. tabBarTestID
    在测试中找到此选项卡按钮的ID。
8. unmountOnBlur
    离开该屏幕时是否应卸载该屏幕。卸载屏幕将重置屏幕中的任何本地状态以及屏幕中嵌套导航器的状态。默认为false。





### 案例

新建src/navigation/BottomTabs

```jsx
import React from 'react'
import { NavigationContainer } from '@react-navigation/native'
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs'
import Home from '@/pages/Home'
import Detail from '@/pages/Detail'


const Tab = createBottomTabNavigator()

class BottomTabs extends React.Component {
  render() {
    return (
      <>
        <NavigationContainer>
          <Tab.Navigator tabBarOptions = {{
            activeTintColor: 'red' // 底部导航栏选中状态的字体颜色
          }}>
            <Tab.Screen name='Home' component = {Home} options={{tabBarLabel: '主页'}}/>
            <Tab.Screen name='Detail' component = {Detail} options={{tabBarLabel: '详情页'}}/>
          </Tab.Navigator>
        </NavigationContainer>
      </>
    )
  }
}

export default BottomTabs
```



将src/index.tsx中的路由改为底部导航器

```jsx
// import Navigator from '@/navigation/index' 堆栈式路由
import Navigator from '@/navigation/BottomTabs'

export default Navigator
```



## 抽屉导航器

DrawerNavigator导航

```
npm install @react-navigation/drawer
```

```jsx
import 'react-native-gesture-handler';
import React, { Component } from 'react';
import { NavigationContainer } from '@react-navigation/native';
//导入
import { createDrawerNavigator } from '@react-navigation/drawer';
//引入页面模块
import LoginScreen from './app/views/LoginScreen';
import HomeScreen from './app/views/HomeScreen';

const Drawer = createDrawerNavigator();

function App() {
  return (
    <NavigationContainer
      drawerStyle={{
        backgroundColor: '#c6cbef',
        width: 200,
      }}
    >
      <Drawer.Navigator initialRouteName='Home'>
        <Drawer.Screen name='Home' component={HomeScreen} />
        <Drawer.Screen name='Login' component={LoginScreen} />
      </Drawer.Navigator>
    </NavigationContainer>
  );
}

export default App;
```





# dva-core

```
yarn add dva-core react-redux
```

dva使用步骤

1.创建实例

2.加载model对象

3.启动dva

4.导出dva数据

