## 小程序和普通网页开发的区别

1. 网页开发渲染线程和脚本线程是一个进程。而小程序是分来的，分别运行在不同的线程。
2. 网页开发者可以操作DOM和BOM。小程序缺少DOM和BOM方法。导致了例如JQ、zepto在小程序无法运行
3. 小程序的运行环境与nodejs环境也有不相同，所以一些npm的包在小程序中也是无法运行的。
   - 从小程序基础库版本                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           2.2.1开始支持使用npm安装第三方包

## 小程序的开发流程

## 小程序4种文件类型

1. js 逻辑文件
2. json 配置文件
3. wxml(weixin markup Language)，是小程序框架设计的一套标签语言，结合小程序的基础组件、事件系统，可以构建出页面结构
   - 与html的区别是他不能使用我们属性的Div、span、p等标签，而是使用小程序为我们提供的各种组件
4. wxss 小程序样式文件

## js

### 脚本的执行顺序

当app.js执行结束后，小程序会按照开发者在app.json中定义的pages的顺序，逐一执行

### 作用域

同浏览器中运行的脚本文件有所不同，小程序的脚本的作用域同nodeJS更为相似。在文件中声明的变量和函数只在该文件中有效，不同的文件中可以声明相同名字的变量和函数，不会互相影响

## WXSS

1. 小程序的尺寸单位rpx，可以根据屏幕宽度进行自适应。规定屏幕宽度750rpx。如在iphone6上，屏幕宽度在375px，共有750个物理像素，则750rpx=375px=750物理像素，1rpx=0.5rpx=1物理像素

2. 定义在app.wxss的样式为全局样式，它会被注入到每个小程序的每个页面。在page的wxss文件中定义的样式为局部样式，只作用在对应的页面，并会覆盖app.wxss中相同的配置（就近原则）

3. 样式导入

   在css中，开发者可以这样引用另一个样式文件：@import url('./test.css')

   在小程序中，我们依然可以实现样式的引用，样式的引用是这样写:@import './test.wxss'

   官方样式库：https://github.com/Tencent/weui-wxss

4. 选择器

5. 小程序目前支持的选择器有：（文档中是这样写到的，但实际支持的选择器不止这些，我们可以在开发中尝试使用一些其他选择器，不行再换一种方式）

## app.json 配置

app.json 配置项列表 https://developers.weixin.qq.com/miniprogram/dev/framework/config.html#全局配置

小程序有tabBar，不需要全部自己写，需要配置一下

app.json

```json
{
    "pages":[ // 页面路径列表
        "pages/index/index,
        "pages/my/my"    
    ],
    "window":{ //全局的默认窗口表现
        "navigationBarBackgroundColor":"#fff", //导航栏背景颜色
        "navigationTitleText":"我的小程序", // 导航栏标题文字
        "navigationBarTextStyle":"black", // 导航栏标题颜色，仅支持black/white
    },
    "tabBar":{
        "color":"#eee", //文字颜色
        "selectedColor":'red', //选中时的文字颜色
        //"position":"top", //位置，一般在下面
        "borderStyle":"black", // black/white 上边框颜色
        "list":[{ //tabBar选项数量2-5个
            "pagePath":'pages/home/home',
            "text":"首页",
            "iconPath":"/images/home2.png", //没被选中的icon展示
            "selectedIconPath":"/images/home.png"
        }]
    }
}
```

每一个小程序页面也可以使用`.json`文件来对本页面的窗口表现进行配置。



## image 组件

img自适应问题，在小程序中设置高度height='auto'是不起作用的：要灵活运用mode

```javascript
<image src='/images/weixin.png' mode='widthFix'></image>
```

页面加背景色

```css
page{ background:red}
```

open-type获取展示用户信息

```html
<view class='container'>
    <open-data type='userAvatarUrl'></open-data>
    <open-data type='userNickName'></open-data>
</view>
```

## 用户授权

得到用户信息，小程序绑定事件是bind。

授权过了就不要需要再授权，授权按钮也不在显示

wx:if wx:else wx:elif

```html
<view class='container' bindtap='onTap'>
    <block wx:if='{{!avatarUrl}}'> //实际渲染不会先出block标签
        <image src={{avatarUrl}} mode='widthFix'></image>
        <text>{{nickName}}</text>
    </block>

	<button wx:else open-type='getUserInfo' bindgetuserinfo='onGetUserInfo'>授权登录</button>
</view>
```

js

```javascript
Page({
    data:{
		avatarUrl:'',
        nickName:''
    },
    onTap(event)=>{
    	console.log(event)
	},
    onLoad:function(options){
        // 授权过了就不要需要再授权 如果要做登录还是要发给后台
        // 判断是否授权过了
        wx.getSetting({ // 获取用户的当前设置
            success:(res)=>{
                console.log(res)
                if(res.authSetting['scope.userinfo']){ // 如果授权过了
                    wx.getUserInfo({
                        success:(res)=>{
                            this.setData({
                                avatarUrl:res.userInfo.avatarUrl,
                                nickName:res.userInfo.nickName
                            })
                        }
                    })
                }else{
                    console.log('未授权')
                }
            }
        })

    },
    onGetUserInfo(event){
        console.log(event);
        this.setData({
            avatarUrl:event.detail.userInfo.avatarUrl,
            nickName:event.detail.userInfo.nickName
        })
    } 
})

```

## 数据绑定

```html
<view id='{{id}}'></view>
<view hidden="{{flag?true:false}}">hidden</view>
<view>{{'hello'+name}}</view> 
```

## 事件绑定及事件处理函数

事件是视图层到逻辑层的通讯方式，可以将用户的行为反馈到逻辑层进行处理

事件对象可以携带额外信息，如id,dataset

事件分类

- 冒泡事件 bind
- 非冒泡事件 catch

事件绑定

- 事件绑定的写法同组件的属性，以key、value的形式

  key以bind或者catch开头，然后跟上事件的类型，如bindtap、catchtap

  bind事件绑定不会阻止冒泡事件向上冒泡，catch事件绑定可以阻止冒泡事件向上冒泡

  

事件对象

- type 代表事件的类型

- timeStamp页面打开到触发事件所经过的毫秒数

- target触发事件的源组件

- currentTarget事件绑定的当前组件

  id 事件源组件的Id

  tagName 当前组件的类型

  dataset 事件源上有data-开头的自定义属性组成的集合

以data-开头，多个单词有连字符-连接，不能有大写（大写会自动转成小写）如data-element-type，最终在event.currentTarget.dataset中会将连字符转成驼峰elementType

```html
<image src={{avatarUrl}} data-info="{{avatarUrl}}" mode='widthFix'></image>

<image src={{avatarUrl}} data-info-avatarUrl="{{avatarUrl}}" mode='widthFix'></image>
/* infoAvatarurl */
```

## 页面跳转

wx.navigateTo 这个会有返回上一个页面的箭头功能，

```javascript
onTap(event){
    console.log(event);
    wx.navigateTp({
        url:'/pages/my/my',
    })
}
```

## swiper组件

高度在swiper上修改，给swiper-item设置高度不起作用。

home.wxml

```html
<swiper
  indicator-dots="{{indicatorDots}}"
  autoplay="{{autoplay}}"
  interval="{{interval}}"
  duration="{{duration}}"
>
  <block wx:for="{{imgUrls}}" wx:key="id">
    <swiper-item>
      <image src="{{item}}" class="slide-image" width="355" height="150" />
    </swiper-item>
  </block>
</swiper>
```

## 列表渲染 wx:for

在组件上使用wx:for控制属性绑定一个数组，即可使用数组中各项的数据重复渲染该组件。

默认数组的当前项下标变量名默认为Index，数组当前项的变量默认为item。

使用wx:for-item 可以指定数组当前元素的变量名

使用wx:for-index 可以指定数组当前下标的变量名

wx:for 可以嵌套

```html
<view wx:for="{{lesson}}" wx:key="*this" wx:for-item="{{s}}"></view>
```

### wx:key

wx:key的意义是为列表中遍历的每一个元素指定一个唯一的标识。当数据改变触发渲染层重新渲染的时候，确保使组件保持自身状态并提高列表渲染时的效率。类似react

1. 如果被遍历的数组中的元素是object，我们可以使用它的某一个的值，这个值必须是不重复的数组或者字符串。

2. 如果被遍历的数组中的元素是数组或者字符串*this，代表在for循环中的item本身

   ```html
   <view wx:for="{{lesson}}" wx:key="*this"></view>
   ```


## 生命周期

onLoad和onReady只会在初始化时执行一次，onShow每次显示页面都会执行

所以一般请求数组时会在onLoad

onShareAppMessage如果不写的话，就不会有分享的按钮

```javascript
onLoad:function(options){
    console.log(options)
},
    onShareAppMessage(){
        return{
            title:'标题',
            path:'/page/uesr?id=123',
            imageUrl:'/images/vue.png', //展示的图片路径，如果不写就是页面的截图
        }
    }    
```

## 向后台发送数据请求

向后台发送请求必须满足两个条件

1. 必须是https请求，因为小程序只允许发送https请求

2. 必须在管理后台中配置合法域名 登录管理后台->开发->开发设置

   如果在开发过程中，我们不想让开发者工具检测我们的接口，只想做一些demo测试等，我们可以在开发者工具的工具栏->详情->在最下方选不校验合法域名选项

wx.request 专门用于向服务器发送请求，函数接收一个Object对象

```javascript
//const app=getApp();
import {HTTP} from "../../utils/http-promise.js"
let http=new HTTP();

Page({
    data:{
        swiper:[],
        lesson:[],
    },
    onLoad:function(options){
		this.getSwiper();
        this.getLesson();
    },
    getSwiper(){
        // 不应该写死
        wx.request({
            url: app.base_url+'/aip/getSwiper', // 仅为示例，并非真实的接口地址
            success:(res)=>{ //一般写成箭头函数，解决this指向问题
                console.log(res.data)
                const data=res.data;
                if(data.status !=undefined && data.status=='ok'){
                    this.setDada({
                        swiper:data.data
                    })
                }else{
                    wx.showToast({
                        title:'接口出错了',
                        icon:'none',
                        duration:3000
                    })
                }
            },
            fail:(res)=>{},// 返回了500 502 404等也是走的成功，所以要对成功后进行处理
            complete:(res)=>{}
        })
    },
    // 把request封装成promise后，上面用法是没修改的
    getLesson(){
        http.request({
            url:'/getSliders'
        }).then((data)=>{
            this.setData({
                swiper:data
            })
        })
    },   
}) 
```

app.js

```javascript
App({
    base_url:"http:www.easy-mock.com/mock/"
})
```

## http-promise

utils->http-promise.js

```javascript
import {base_url} form "./config.js";

class HTTP{
    request({url,data={}, header={}, method="GET", success=()=>{},fail=()=>{}}){
        return new Promise((resolve,reject)=>{
            this._request(url, data, header, method, resolve,reject)
        })
    }
    _request(url,data,header,method, resolve, reject){
        wx.request({
            url: app.base_url+url,
            data:data,
            header:header,
            method:method,
            success:(res)=>{
                console.log(res.data)
                const data=res.data;
                if(data.status !=undefined && data.status=='ok'){
					resolve(data.data);// 调用成功的方法
                }else{
                    reject();
                    wx.showToast({
                        title:'接口出错了',
                        icon:'none',
                        duration:3000
                    })
                }
            },
            fail:(res)=>{// 返回了500 502 404等也是走的成功，所以要对成功后进行处理
                reject();
                wx.showToast({
                    title:'接口出错了',
                    icon:'none',
                    duration:3000
                })
            },
            complete:(res)=>{}
        })
    }
}

export {HTTP}
```

utils->http.js

```javascript
import {base_url} form "./config.js";

function http(){}
export {http}
```

## web-view

home.wxml

```html
<swiper indicator-dots="{{indicatorDots}}" autoplay="true">
  <block wx:for="{{imgUrls}}" wx:key="id">
    <swiper-item>
      <image src="{{item}}" catchtap="onToH5" data-url="{{item.url}}"/>
    </swiper-item>
  </block>
</swiper>
```

home.js

```javascript
Page({
    onToH5(e){
        console.log(e)
        let url=e.target.dataset.url;
        wx.navigateTo({
            url:`/pages/webView/webView?url=${encodeURIComponent(url)}`,
        })
    }
})
```

web-view 组件是一个可以用来承载网页的容器，会自动铺满整个小程序页面。**个人类型与海外类型的小程序暂不支持使用。**

webView.wxml

```html
<web-view src="{{h5Url}}"></web-view>
```

webView.js

```javascript
Page({
    data:{
        htUrl:''
    },
    onLoad:function(options){
        let url=decodeURIComponent(options.url);
        this.setData({
            h5Url:url
        })
    }
})
```

如果想在H5页面调用小程序提供的一些接口就必须在H5页面中引入如下JS

```html
<script type="text/javascript" src="https://res.wx.qq.com/open/js/jweixin-1.3.2.js"
></script>
```

## 获取收货地址和地理位置

```html
<button bindtap="onChooseAddr">
    获取收货地址
</button>
<button bindtap="onChooseLocation">获取用户选择的地理位置</button>
<button bindtap="onGetLocation">显示用户位置</button>
```

```javascript
Page({
    onChooseAddr(){
        wx.chooseAddress({
            success(res){
                console.log(res)
            }
        })
    },
    onChooseLocation(){
        wx.chooseLoaction({
            success:function(res){
                console.log(res)
            }
        })
    },
    onGetLocation(){
        wx.getLocation({
            success:function(res){
                console.log(res);// 可以获取到位置坐标，再在地图打开
                wx.openLocation({
                    latitude:res.latitude,
                    longitude:res.longitude
                })
            }
        })
    },
})
```

## 自定义组件

新建components文件夹里放组件，比如components->lesson->index.js...

要引入自定义组件的话，在需要引入的组件的json里

```json
{
    "usingComponents":{
        "lesson-cmp":"/components/lesson/index"
    }
}
```

使用

info="{{info}}"可以在properties获取到。data-info="{{info}}"可以this.dataset获取到，再放到页面

常用还是属性的方式

```html
<lesson-cmp info="{{info}}" data-info="{{info}}" bind:myEvent="onMyEvent" ></lesson-cmp>
```

```javascript
Page({
    onMyEvent(e){
        console.log(e); //e.detail里就有自定义组件传过来的信息
    }
})
```

###组件和页面传递数据

components->lesson->index.wxml

```html
<text bindTap="onTap">自定义组件 hello {{info.name}}</text>
```

components->lesson->index.js

```javascript
Component({
    // 传进来的属性需要设置一下
    properties:{
        info:{
            type:Object,
            value:{}
        },
        info:Object //写成这样也可以
    },
    // 组件的方法都在写在这里
    methods:{
        onTap(){
            console.log(this.dataset)
            this.setData({
                info:this.dataset.info
            })
        },
        onTap2(){
            let detail={
                name:"react"
            }
			this.triggerEvent('myEvent',detail,{})
        },
    }
})
```

