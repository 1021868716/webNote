# uniapp

uniapp发布h5时完全支持vue语法，发布app和小程序时只能支持部分vue语法，某些特性无法使用例如vue-router等操作dom操作的特性。

uniapp中组件标签（view，image，text等）靠近小程序规范，接口能力（JS API）靠近微信小程序规范，拥有完整的小程序生命周期。uniapp项目pages文件下每一个vue文件都必须用一个同名文件夹包裹，并且在page.json中注册，以符合小程序的要求



# 导航栏

uniapp中不能使用`vue-router`，而是类似小程序在package.json中配置底部导航栏和顶部导航栏进行路由跳转。切换页面默认不重新渲染页面而是使用页面缓存，所以需要每次切换页面都要调用的方法应该写进onTabItemTap或者onShow生命周期中

onTabItemTap，每次点击tabbar就会触发该生命周期。生命周期默认含有一个参数e，改参数存储了这个tabbar按钮对应的pagePath，index，text信息。

onShow生命周期每次进入页面就会触发。

```json
{
	"pages": [ //pages数组中第一项表示应用启动页，参考：https://uniapp.dcloud.io/collocation/pages
		{
			"path": "pages/index/index",
			"style": {
				"navigationBarTitleText": "index"
			}
		},
    {
      "path" : "pages/about/about",
      "style" : {
        "navigationBarTitleText": "about"
      }
    }
  ],
  // 全局顶部导航栏配置，页面style可覆盖
  "globalStyle": { 
    "navigationBarTextStyle": "black",
	"navigationBarTitleText": "uni-app",
	"navigationBarBackgroundColor": "#F8F8F8",
	"backgroundColor": "#F8F8F8"
  },
  // 底部导航栏 
  "tabBar": {
    "color": "#666",
    "selectedColor": "#ff5a5f",
    "borderStyle": "black",
    "list": [
      {
        "pagePath": "pages/index/index",
        "text": "首页",
        // iconPath图片大小不大于40kb，尺寸建议81*81px
        "iconPath": "static/school.png",
        // 选中时图片
        "selectedIconPath": "static/school-active.png"
      },
      {
        "pagePath": "pages/about/about",
        "text": "关于",
        // iconPath图片大小不大于40kb，尺寸建议81*81px
        "iconPath": "static/school.png",
        // 选中时图片
        "selectedIconPath": "static/school-active.png"
      }
    ]
  }
}

```

