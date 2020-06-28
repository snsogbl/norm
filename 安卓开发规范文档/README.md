# 安卓开发规范文档


## 命名规范

#### 类名

驼峰，首字母大写，单词或者单词简写，见名知意，长度小于20个字符；
```
例子：
UserInfoManager
```

##### Activity

模块名+功能业务+Activity 
```
例子：
ProductSearchActivity
```

##### Adapter

模块+功能业务+Adapter
```
例子：
OrderAdapter
```

##### Fragment

模块+功能业务+Fragment 
```
例子：
MainPickFragment
```



#### 资源名

下划线，英文或者英文简写，见名知意，长度小于20个字符；

##### Layout 

类型+模块+功能

```
Activity 的layout 以activity_ 开头
Fragment 的layout 以fragment_ 开头
Dialog 的layout 以dialog_ 开头
include 的layout 以include_ 开头
ListView 的行layout 以list_item_ 开头
RecyclerView 的item layout 以recycle_item_ 开头
GridView 的item layout 以grid_item_ 开头
```

##### Iamge 

类型+模块+功能+用途

```
例子：2.2 image_login_pwd_icon.png
```
   
##### color 
资源使用#AARRGGBB 格式，写入module_colors.xml 文件中，命名格式采用以下规则：

```
模块名_逻辑名称_颜色
```
如：
```
<color name="module_btn_bg_color">#33b5e5e5</color>
```


##### dimen
资源以小写单词+下划线方式命名，写入module_dimens.xml 文件中，采用以下规则：

```
模块名_描述信息
```
如
```
<dimen name="module_horizontal_line_height">1dp</dimen>
```

##### string
资源文件或者文本用到字符需要全部写入module_strings.xml 文件中，字符串以小写单词+下划线的方式命名，采用以下规则：

```
模块名_逻辑名称
```
如：
```
moudule_login_tips,module_homepage_notice_desc
```


##### Id
资源原则上以驼峰法命名，View 组件的资源id 建议以View 的缩写作为前缀。常用缩写表如下：

```
LinearLayout ll
RelativeLayout rl
ConstraintLayout cl
ListView lv
ScollView sv
TextView tv
Button btn
ImageView iv
CheckBox cb
RadioButton rb
EditText et
```

其它控件的缩写推荐使用小写字母并用下划线进行分割，例如：ProgressBar 对应
的缩写为progress_bar；DatePicker 对应的缩写为date_picker。


#### 方法名

驼峰，首字母小写，单词或者单词简写，动词开头，见名知意，长度小于20个字符；



## 日志规范

#### 类注释
使用文档注释 ,包含功能描述，作者，时间

#### 方法注释
使用文档注释，包含方法描述，返回值类型，参数描述

#### 逻辑注释
使用//注释，主要描述清楚逻辑，特殊业务和目的



## 方法规范
原则上一个方法只做一个功能业务，一个方法内不能封装超过2个以上的业务逻辑
能抽象公用的逻辑尽量封装成工具方法，减少代码岑余
所有的公用方法和逻辑处理方法，要求全部内部抛出异常，集中在顶层捕获



