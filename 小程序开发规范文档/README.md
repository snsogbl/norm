# 微信小程序开发规范文档


## 目录规范

#### 页面目录

一个页面由wxml，wxss，js，json四个文件组成。为了方便开发者减少配置项，描述页面的四个文件必须具有相同的路径与文件名

```
├─ pages/
	├─ home/
	    ├─ home.wxml
	    ├─ home.wxss
	    ├─ home.js
	    └─ home.json
```


#### 组件文件

所有组件相关文件统一放在 components 目录下

```
├─ components/
	├─ bbg-tab-bar/
	    ├─ bbg-tab-bar.wxml
	    ├─ bbg-tab-bar.wxss
	    ├─ bbg-tab-bar.js
	    └─ bbg-tab-bar.json
```

#### 图片文件

项目图片文件放置于根目录的 images 文件夹下

尽量减少使用大图片资源、增加图片资源要检查大小进行相应压缩

只有共用图片资源、主包页面图片资源允许放主包image目录，分包页面图片资源只能放分包下image目录

#### 公用类文件
项目公用类放在utils文件夹下

```
1.通用函数放在util.js文件内
2.请求地址放在apiUrl.js文件内
```

## 命名规范
所有命名尽量用英文

页面、类命名参考微信小程序规范小写开头，驼峰命名法

组件名参考微信小程序规范，标签名有多个词时，词之间以连接符-连接

### js命名规范
使用let代替var声明变量

```
	let index = 1
```

变量名以及函数名统一采用驼峰命名法，正常情况下函数名前缀需加上清晰的动词表示函数功能。常量需用 const 声明。

```
//定义常量
const indexRow = 1

//定义变量
let imageContent = res.data

//函数命名
getInfo:function(){
	return '';
}

//点击事件
phoneClick:function(){
	return '';
}
```

### wxss命名规范
统一采用小写中划线分割

```
.header-title{

}

.list-item{
}

```


## WXML 规范

### 1.WXML 规范

---


控制每行 HTML 的代码数量在 50 个字符以内，方便阅读浏览，多余的代码进行换行处理，标签所带属性每个属性间进行换行。


合理展现分离内容，少使用内联样式。

```
//推荐使用
<image class="tag"></image>
```


## CSS 规范
---

在开发过程中 rpx 和 px 均可能用到，如通常情况下间距使用 rpx，开发者根据实际情况而定。

```
width: 100rpx;
font-size: 14rpx;
```

CSS 代码需有明显的代码缩进。

```
.tag{
	width: 100%;
}
```

尽量使用简写属性，并且同一属性放置在一起，避免散乱。

```
/**使用简写属性**/
.goods-image{
	margin: 0 auto;
}
/**同一属性放在一块**/
.tag{
	margin-left: 10rpx;
	margin-right: 10rpx
}
```
建议多采用 flex 进行布局。

```
.container{
	disaplay: flex;
	flex-direction: row
}
```

### 2.注释规范

---

成组的 wxss 规则之间用块状注释。请勿在代码后面直接注释。

```
/* 修改button默认的点击态样式类 */
.button-hover {
	background-color: red;
}
```

###3.统一语义理解和命名
####布局

|语义|命名|简写|
|----|----|----|
|文档	|doc	|doc
|头部	|head	|hd
|主体	|body	|bd
|尾部	|foot	|ft
|主栏	|main	|mn
|主栏子容器	|mainc	|mnc
|侧栏	|side	|sd
|侧栏子容器	|sidec	|sdc
|盒容器	|wrap/box	|wrap/box
|导航	|nav	|nav
|子导航	|subnav	|snav
|面包屑	|crumb	|crm
|菜单	|menu	|menu
|选项卡	|tab	|tab
|标题区	|head/title	|hd/tt
|内容区	|body/content	 |bd/ct
|列表	|list	|lst
|表格	|table	|tb
|表单	|form	|fm
|热点	|hot	|hot
|排行	|top	|top
|登录	|login	|log
|标志	|logo	|logo
|广告	|advertise	|ad
|搜索	|search	|sch
|幻灯	|slide	|sld
|提示	|tips	|tips
|帮助	|help	|help
|新闻	|news	|news
|下载	|download	|dld
|注册	|regist	|reg
|结果	|result	|rst
|标题	|title	|title
|按钮	|button	|btn
|输入	|input	|ipt

####状态

|语义|命名|简写|
|----|----|----|
|选中	|selected	|sel
|当前	|current	|crt
|显示	|show	|show
|隐藏	|hide	|hide
|打开	|open	|open
|关闭	|close |close
|出错	|error	|err
|不可用	|disabled	|dis


## JS 规范

### 数据绑定变量定义规范

所有涉及到数据绑定的变量均需在 data 中初始化。禁止在不定义的情况下直接 setData。

```

Pages({
	data:{
		id : null
	},
	onLoad:function(options){
		let id = options.id
		this.setData({
			id:id
		})
	}

})
```

### 分享

页面不需要分享的要去掉onShareAppMessage

页面要用分享功能时统一采用页面路由方式(pageroute.js)

## 组件规范

### 组件名命名规范

组件在使用时命名以 “bbg-”为开头的组件名，若组件名称为多个单词名拼接而成，采用 ' - ' 连接。

```
<bbg-tab-bar />
```

### 触发事件规范

组件点击触发事件建议用冒号分隔开

```
<bbg-emporium-home bind:backHomePage="backHomePage"/>
```

## 分包规范

### 主包规范

原则上tabbar的页面放主包、其它页面按功能模块分多个分包

### 分包插件规范

使用到的插件如果只在分包中使用、插件必须只放分包中注册

## 交互体验
交互体验不能有卡顿感

页面可能为空的情况要做空页面展示

重要操作比如保存、提现、支付等要做节流处理、或者做页面loding禁止页面其它操作，使用技术方案视情况而定

导航栏标题按设计稿修改


# 考核规范
1.是否遵守文档规范

2.代码可读性

3.代码注释

4.交互体验

5.业务理解

