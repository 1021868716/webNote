# blog项目

# styled-components

style样式管理插件

```
npm install --save styled-components
```

将css文件换为js文件

```javascript
import { createGlobalStyle } from 'styled-components'

export const GlobalStyled = createGlobalStyle`
  body {
    margin: 0;
    padding: 0;
    font-family: sans-serif;
  }
`
```

组件使用样式

```react
import { GlobalStyled } from './style.js';
function App() {
  return (
    <div className="dell">
      <GlobalStyled/>  //放在根目录下，dell下组件都能使用这个样式
      hello,world
    </div>
  );
}

export default App;
```



这个插件可以通过模拟标签来生成UI组件，我们可以很快速的生成UI组件

styled模拟标签，可以在标签名后通过attrs加属性

```javascript
import styled from 'styled-components'

// 模拟一个HeaderWrapper无状态组件，本质是一个div标签
export const HeaderWrapper = styled.div.attrs({id:"box"})`
  height: 56px;
  background: red;
`
// &.class会自动识别组件上的class样式，并根据对应的样式进行调整
export const NavItem = styled.div`
  &.left {
    float: left;
  }
  &.right {
    float: right;
  }
`
```

在style.js中我们模拟了一个div标签组件并且引出了，在使用时直接把这个组件当div用就行

```react
// 顶部导航栏组件
import React, { Component } from 'react'
import { HeaderWrapper } from './style.js'

class Header extends Component {
  render() {
    return (
      <HeaderWrapper>header</HeaderWrapper>
      <NavItem class="right"/>
    )
  }
}

export default Header
```







# reset.css初始化样式

使用reset.css初始化样式，将css复制进GlobalStyled，全局使用



# 目录划分

## 组件内目录划分

index.js  组件入口文件

style.js/css  组件样式文件

store文件夹   拆分reducer，组件的action处理代码写在组件文件夹内，最后store再统一引入

组件将redux中的reducer拆分出来，防止src/store/reducer文件代码冗余，redux中的reducer只需要引入其他组件的reducer就可以了

```javascript
import { combineReducers } from 'redux'
import headerReducer from '@/common/header/store/reducer'

export default combineReducers({
  header: headerReducer
})
```

组件中react-redux的映射方式也要搜索改变

```javascript
const mapStateToProps= (state) => {
  return {
    focused: state.header.focused
    // 原本为focused: state.focused
  }
}
```





# immutable

immutable.js是一个库，immutable是不可改变的意思，可以把一个JS对象转化为immutable对象。



# axios

使用axios进行网络连接