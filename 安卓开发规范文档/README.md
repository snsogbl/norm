# 安卓开发规范文档


## 命名规范

#### 类名

驼峰，首字母大写，单词或者单词简写，见名知意，长度小于20个字符；
```
例子：
UserInfoManager
```


##### 变量名

首字母小写，驼峰，单词或者单词简写，见名知意，长度小于20个字符；禁止使用添加无意义字符或数字的方式区分变量， 全局变量使用m开头
```
例子：
ProductDataBean mProductDatabean;
int pickTypeJD =0;
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
PickOrderAdapter
```

##### Fragment

模块+功能业务+Fragment 
```
例子：
MainPickFragment
```



#### 资源名
??????????????????????????

下划线，英文或者英文简写，见名知意，长度小于20个字符；


##### Layout 

类型+模块+功能

```
Activity 的layout 以activity_ 开头
Fragment 的layout 以fragment_ 开头
Dialog 的layout 以dialog_ 开头
include 的layout 以include_ 开头
ListView 的行layout 以adapter_ 开头
RecyclerView 的item layout 以adapter_ 开头
GridView 的item layout 以adapter_ 开头
```

##### Iamge 

类型+模块+功能+状态

```
ic_member_userinfo
bg_meber_userinfo
btn_menmber_userinfos
btn_common_bag_select
btn_common_bag_unselect


例子：2.2 image_login_pwd_icon.png
```
   
##### color 



资源使用#AARRGGBB 格式，写入module_colors.xml 文件中，命名格式采用以下规则：

```
color_逻辑名称_颜色值

```
如：
```
<color name="color_gary_cccccc">#33b5e5e5</color>
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
login_tips,homepage_notice_desc
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

驼峰，首字母小写，单词或者单词简写，动词开头，见名知意，长度小于25个字符；
如：
```
doLogin(); 
getProductData();
```


## 日志规范

#### 类注释
使用文档注释 ,包含功能描述，作者，时间
```
/**
*作者 liyinchu
*时间 2020-07-17
*描述 用户信息管理器，用于全局存储和获取用户信息
*/
public class UserInfoManager(){}
```


#### 方法注释
使用文档注释，包含方法描述，返回值类型，参数描述
```
/**
*用户登录
*@Params userName 用户名
*@Params passWord 密码
*@return void
*/
public void doLogin(String userName,String Password){}
```


#### 逻辑注释
使用//注释，主要描述清楚逻辑，特殊业务和目的
```
...
```

## 方法规范
原则上一个方法只做一个功能业务，一个方法内不能封装超过2个以上的业务逻辑
能抽象公用的逻辑尽量封装成工具方法，减少代码岑余


```
public String getDate(long timestamp ,String format) throws Exception{}

public void doLogin(){
	try{
       String timestampFormat = DateUtils.getDate(timeStamp);
       ...
	}catch(Exception e){
      //do someing
	}
	
}

```



## 强制规范
总结开发过程中容易引起严重后果和性能的代码，严格遵守该约定可减少很多日后埋下的“坑”

#### Method
```
所有的公用方法和逻辑处理方法，要求全部内部抛出异常，集中在顶层捕获
```


#### Activity 

```
1.
Activity 间的数据通信，对于数据量比较大的，避免使用Intent + Parcelable
的方式，可以考虑EventBus 等替代方案，以免造成TransactionTooLargeException。
```

```
2.
Activity 间通过隐式Intent 的跳转，在发出Intent 之前必须通过resolveActivity
检查，避免找不到合适的调用组件，造成ActivityNotFoundException 的异常。
```


#### Service 

```
1.
避免在Service#onStartCommand()/onBind()方法中执行耗时操作，如果确
实有需求，应改用IntentService 或采用其他异步机制完成。
正例：
public class MainActivity extends Activity {
@Override
public void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
}
public void startIntentService(View source) {
Intent intent = new Intent(this, MyIntentService.class);
startService(intent);
}
}
public class MyIntentService extends IntentService {
public MyIntentService() {
super("MyIntentService");
}
@Override
protected void onHandleIntent(Intent intent) {
synchronized (this) {
try {

......
} catch (Exception e) {
}
}
}
}
```

```
2.
在Activity#onPause()或Activity#onStop()回调中，关闭当前activity 正在执
行的的动画。
正例：
public class MyActivity extends Activity {
ImageView mImageView;
Animation mAnimation;
Button mBtn;
@Override
public void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
mImageView = (ImageView)findViewById(R.id.ImageView01);
mAnimation = AnimationUtils.loadAnimation(this, R.anim.anim);
mBtn= (Button)findViewById(R.id.Button01);
mBtn.setOnClickListener(new View.OnClickListener() {
@Override
public void onClick(View v) {
mImageView.startAnimation(mAnimation);
}
});
}

@Override
public void onPause() {
//页面退出，及时清理动画资源
mImageView.clearAnimation()
}
}
反例：
页面退出时，不关闭该页面相关的动画。
```


```
2.
禁止在非UI 线程进行View 相关操作。
```


#### BroadcastReceiver 

```
1.
】避免在BroadcastReceiver#onReceive()中执行耗时操作，如果有耗时工作，
应该创建IntentService 完成，而不应该在BroadcastReceiver 内创建子线程去做。
说明：
由于该方法是在主线程执行，如果执行耗时操作会导致UI 不流畅。可以使用
IntentService 、创建HandlerThread 或者调用Context#registerReceiver
(BroadcastReceiver, IntentFilter, String, Handler)方法等方式，在其他Wroker 线程
执行onReceive 方法。BroadcastReceiver#onReceive()方法耗时超过10 秒钟，可
能会被系统杀死。
正例：
IntentFilter filter = new IntentFilter();
filter.addAction(LOGIN_SUCCESS);
this.registerReceiver(mBroadcastReceiver, filter);
mBroadcastReceiver = new BroadcastReceiver() {
@Override
public void onReceive(Context context, Intent intent) {
Intent userHomeIntent = new Intent();
userHomeIntent.setClass(this, UserHomeService.class);
this.startService(userHomeIntent);
}
};
反例：
mBroadcastReceiver = new BroadcastReceiver() {
三、Android 基本组件
- 10 -
@Override
public void onReceive(Context context, Intent intent) {
MyDatabaseHelper myDB = new MyDatabaseHelper(context);
myDB.initData();
// have more database operation here
}
};
```


```
2.
避免使用隐式Intent 广播敏感信息，信息可能被其他注册了对应
BroadcastReceiver 的App 接收。
说明：
通过Context#sendBroadcast()发送的隐式广播会被所有感兴趣的receiver 接收，恶
意应用注册监听该广播的receiver 可能会获取到Intent 中传递的敏感信息，并进行
其他危险操作。如果发送的广播为使用Context#sendOrderedBroadcast()方法发送
的有序广播，优先级较高的恶意receiver 可能直接丢弃该广播，造成服务不可用，
或者向广播结果塞入恶意数据。
如果广播仅限于应用内，则可以使用LocalBroadcastManager#sendBroadcast()实
现，避免敏感信息外泄和Intent 拦截的风险。
正例：
Intent intent = new Intent("my-sensitive-event");
intent.putExtra("event", "this is a test event");
LocalBroadcastManager.getInstance(this).sendBroadcast(intent);
反例：
Intent intent = new Intent();
v1.setAction("com.sample.action.server_running");
v1.putExtra("local_ip", v0.h);
v1.putExtra("port", v0.i);
v1.putExtra("code", v0.g);
v1.putExtra("connected", v0.s);
v1.putExtra("pwd_predefined", v0.r);
if (!TextUtils.isEmpty(v0.t)) {
v1.putExtra("connected_usr", v0.t);
}
context.sendBroadcast(v1);
以上广播可能被其他应用的如下receiver 接收导致敏感信息泄漏
final class MyReceiver extends BroadcastReceiver {
public final void onReceive(Context context, Intent intent) {
if (intent != null && intent.getAction() != null) {
String s = intent.getAction();
if (s.equals("com.sample.action.server_running") {
String ip = intent.getStringExtra("local_ip");
String pwd = intent.getStringExtra("code");
String port = intent.getIntExtra("port", 8888);
boolean status = intent.getBooleanExtra("connected", false);
}
}
}
}
```


```
3.
Activity 或者Fragment 中动态注册BroadCastReceiver 时，registerReceiver()
和unregisterReceiver()要成对出现。
说明：
如果registerReceiver()和unregisterReceiver()不成对出现，则可能导致已经注册的
receiver 没有在合适的时机注销，导致内存泄漏，占用内存空间，加重SystemService
负担。
部分华为的机型会对receiver 进行资源管控，单个应用注册过多receiver 会触发管
控模块抛出异常，应用直接崩溃。


正例：
public class MainActivity extends AppCompatActivity {
private static MyReceiver myReceiver = new MyReceiver();
...
@Override
protected void onResume() {
super.onResume();
IntentFilter filter = new IntentFilter("com.example.myservice");
registerReceiver(myReceiver, filter);
}
@Override
protected void onPause() {
super.onPause();
unregisterReceiver(myReceiver);
}
...
}
反例：
public class MainActivity extends AppCompatActivity {
private static MyReceiver myReceiver;
@Override
protected void onResume() {
super.onResume();
myReceiver = new MyReceiver();
IntentFilter filter = new IntentFilter("com.example.myservice");
registerReceiver(myReceiver, filter);

}
@Override
protected void onDestroy() {
super.onDestroy();
unregisterReceiver(myReceiver);
}
}
```

#### AndroidManifest

```
1.
Android 基础组件如果使用隐式调用，应在 AndroidManifest.xml 中使用
<intent-filter> 或在代码中使用 IntentFilter 增加过滤。
说明：
如果浏览器支持Intent Scheme Uri 语法，如果过滤不当，那么恶意用户可能通过浏
览器js 代码进行一些恶意行为，比如盗取cookie等。如果使用了Intent.parseUri
函数，获取的intent 必须严格过滤。
正例：
// 将intent scheme URL 转换为intent 对象
Intent intent = Intent.parseUri(uri);
// 禁止没有BROWSABLE category 的情况下启动activity
intent.addCategory("android.intent.category.BROWSABLE");
intent.setComponent(null);
intent.setSelector(null);
// 使用intent 启动activity
context.startActivityIfNeeded(intent, -1)
反例：
Intent intent = Intent.parseUri(uri.toString().trim().substring(15), 0);
intent.addCategory("android.intent.category.BROWSABLE");
context.startActivity(intent);
```


```
2.
将android:allowbackup 属性必须设置为false，阻止应用数据被导出。
说明：
android:allowBackup 原本是 Android 提供的 adb 调试功能，如果设置为 true，
可以导出应用数据备份并在任意设备上恢复。这对应用安全性和用户数据隐私构成
极大威胁，所以必须设置为 false，防止数据泄露。
正例：
<application
android:allowBackup="false"
android:largeHeap="true"
android:icon="@drawable/test_launcher"
android:label="@string/app_name"
android:theme="@style/AppTheme" >
```



#### Layout布局文件

```
1.
布局中不得不使用ViewGroup 多重嵌套时，不要使用LinearLayout 嵌套，
改用RelativeLayout，可以有效降低嵌套数。
说明：
Android 应用页面上任何一个View 都需要经过 measure、layout、draw 三个步骤
才能被正确的渲染。从xml layout 的顶部节点开始进行measure，每个子节点都需
要向自己的父节点提供自己的尺寸来决定展示的位置，在此过程中可能还会重新
measure（由此可能导致measure 的时间消耗为原来的2-3 倍）。节点所处位置越深，
嵌套带来的measure 越多，计算就会越费时。这就是为什么扁平的View 结构会性
能更好。
同时，页面拥上的View 越多，measure、layout、draw 所花费的时间就越久。要缩
短这个时间，关键是保持View 的树形结构尽量扁平，而且要移除所有不需要渲染的
View。理想情况下，总共的measure，layout，draw 时间应该被很好的控制在16ms
以内，以保证滑动屏幕时UI 的流畅。
要找到那些多余的View（增加渲染延迟的view），可以用Android Studio Monitor
里的Hierarchy Viewer 工具，可视化的查看所有的view。
```


```
2.
禁止在设计布局时多次为子View 和父View 设置同样背景进而造成页面过
度绘制，推荐将不需要显示的布局进行及时隐藏。
正例：
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:orientation="vertical" >
<TextView
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:text="@string/hello" />
<Button
android:id="@+id/btn_mybuttom"

android:layout_width="match_parent"
android:layout_height="wrap_content"
android:text="click it !" />
<ImageView
android:id="@+id/img"
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:visibility="gone"
android:src="@drawable/youtube" />
<TextView
android:text="it is an example!"
android:layout_width="match_parent"
android:layout_height="wrap_content" />
</LinearLayout>
反例：
@Override
protected void onDraw(Canvas canvas) {
super.onDraw(canvas);
int width = getWidth();
int height = getHeight();
mPaint.setColor(Color.GRAY);
canvas.drawRect(0, 0, width, height, mPaint);
mPaint.setColor(Color.CYAN);
canvas.drawRect(0, height/4, width, height, mPaint);
mPaint.setColor(Color.DKGRAY);
canvas.drawRect(0, height/3, width, height, mPaint);
mPaint.setColor(Color.LTGRAY);
canvas.drawRect(0, height/2, width, height, mPaint);
四、UI 与布局

}
```

#### ListView

```
1.
不能使用ScrollView 包裹ListView/GridView/ExpandableListVIew;因为这
样会把ListView 的所有Item 都加载到内存中，要消耗巨大的内存和cpu 去绘制图
面。
说明：
ScrollView 中嵌套List 或RecyclerView 的做法官方明确禁止。除了开发过程中遇到
的各种视觉和交互问题，这种做法对性能也有较大损耗。ListView 等UI 组件自身有
垂直滚动功能，也没有必要在嵌套一层ScrollView。目前为了较好的UI 体验，更贴
近Material Design 的设计，推荐使用NestedScrollView。
正例：
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout>
<android.support.v4.widget.NestedScrollView>
<LinearLayout>
<ImageView/>
...
<android.support.v7.widget.RecyclerView/>
</LinearLayout>
</android.support.v4.widget.NestedScrollView>
</LinearLayout>
反例：
<ScrollView>
<LinearLayout>
<TextView/>
...
<ListView/>
<TextView />
</LinearLayout>
</ScrollView>
```

#### Application

```
1.
不要在Android 的Application 对象中缓存数据。基础组件之间的数据共享
请使用Intent 等机制，也可使用SharedPreferences 等数据持久化机制。
反例：
class MyApplication extends Application {
String username;
String getUsername() {
return username;
}

void setUsername(String username) {
this.username = username;
}
}
class SetUsernameActivity extends Activity {
void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.set_username);
MyApplication app = (MyApplication) getApplication();
app.setUsername("tester1");
startActivity(new Intent(this, GetUsernameActivity.class));
}
}
class GetUsernameActivity extends Activity {
TextView tv;
@Override
void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.get_username);
tv = (TextView)findViewById(R.id.username);
}
@Override
void onResume() {
super.onResume();

MyApplication app = (MyApplication) getApplication();
tv.setText("Welcome back ! " + app.getUsername().toUpperCase());
}
}
```


```
2.
在Application 的业务初始化代码加入进程判断，确保只在自己需要的进程
初始化。特别是后台进程减少不必要的业务初始化。
正例：
public class MyApplication extends Application {
@Override
public void onCreate() {
//在所有进程中初始化
....
//仅在主进程中初始化
if (mainProcess) {
...
}}

//仅在后台进程中初始化
if (bgProcess) {
...
}
}
```




#### Adapter

```
1.
使用Adapter 的时候，如果你使用了ViewHolder 做缓存，在getView()的
方法中无论这项convertView 的每个子控件是否需要设置属性(比如某个TextView
设置的文本可能为null，某个按钮的背景色为透明，某控件的颜色为透明等)，都需
要为其显式设置属性(Textview 的文本为空也需要设置setText("")，背景透明也需要
设置)，否则在滑动的过程中，因为adapter item 复用的原因，会出现内容的显示错
乱。
正例：
@Override
public View getView(int position, View convertView, ViewGroup parent) {
ViewHolder myViews;
if (convertView == null) {
myViews = new ViewHolder();
convertView = mInflater.inflate(R.layout.list_item, null);
myViews.mUsername = (TextView)convertView.findViewById(R.id.username);
convertView.setTag(myViews);
} else {
myViews = (ViewHolder)convertView.getTag();
}
Info p = infoList.get(position);
String dn = p.getDisplayName;
myViews.mUsername.setText(StringUtils.isEmpty(dn) ? "" : dn);
return convertView;


}
static class ViewHolder {
private TextView mUsername;
}
```

#### Intent

```
1.
不要通过Intent 在Android 基础组件之间传递大数据（binder transaction
缓存为1MB），可能导致OOM。
```



#### Thread

```
1.
新建线程时，必须通过线程池提供（AsyncTask 或者ThreadPoolExecutor
或者其他形式自定义的线程池），不允许在应用中自行显式创建线程。
说明：
使用线程池的好处是减少在创建和销毁线程上所花的时间以及系统资源的开销，解
决资源不足的问题。如果不使用线程池，有可能造成系统创建大量同类线程而导致
五、进程、线程与消息通信
- 32 -
消耗完内存或者“过度切换”的问题。另外创建匿名线程不便于后续的资源使用分析，
对性能分析等会造成困扰。
正例：
int NUMBER_OF_CORES = Runtime.getRuntime().availableProcessors();
int KEEP_ALIVE_TIME = 1;
TimeUnit KEEP_ALIVE_TIME_UNIT = TimeUnit.SECONDS;
BlockingQueue<Runnable> taskQueue = new LinkedBlockingQueue<Runnable>();
ExecutorService executorService = new ThreadPoolExecutor(NUMBER_OF_CORES,
NUMBER_OF_CORES*2, KEEP_ALIVE_TIME, KEEP_ALIVE_TIME_UNIT, taskQueue,
new BackgroundThreadFactory(), new DefaultRejectedExecutionHandler());
//执行任务
executorService.execute(new Runnnable() {
...
});
反例：
new Thread(new Runnable() {
@Override
public void run() {
//操作语句
...
}
}).start();
```


```
2.
线程池不允许使用Executors 去创建，而是通过ThreadPoolExecutor 的方
式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。
说明：
Executors 返回的线程池对象的弊端如下：
1) FixedThreadPool 和SingleThreadPool ： 允许的请求队列长度为

Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM；
2) CachedThreadPool 和ScheduledThreadPool ： 允许的创建线程数量为
Integer.MAX_VALUE，可能会创建大量的线程，从而导致OOM。
正例：
int NUMBER_OF_CORES = Runtime.getRuntime().availableProcessors();
int KEEP_ALIVE_TIME = 1;
TimeUnit KEEP_ALIVE_TIME_UNIT = TimeUnit.SECONDS;
BlockingQueue<Runnable> taskQueue = new LinkedBlockingQueue<Runnable>();
ExecutorService executorService = new ThreadPoolExecutor(NUMBER_OF_CORES,
NUMBER_OF_CORES*2, KEEP_ALIVE_TIME, KEEP_ALIVE_TIME_UNIT,
taskQueue, new BackgroundThreadFactory(), new DefaultRejectedExecutionHandler());
反例：
ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
```


```
子线程中不能更新界面，更新界面必须在主线程中进行，网络操作不能在
主线程中调用。
```


```
任何时候不要硬编码文件路径，请使用Android 文件系统API 访问。
说明：
Android 应用提供内部和外部存储，分别用于存放应用自身数据以及应用产生的用
户数据。可以通过相关API 接口获取对应的目录，进行文件操作。
android.os.Environment#getExternalStorageDirectory()
android.os.Environment#getExternalStoragePublicDirectory()
android.content.Context#getFilesDir()
android.content.Context#getCacheDir
正例：
public File getDir(String alName) {
File file = new File(Environment.getExternalStoragePublicDirectory(Environment.
DIRECTORY_PICTURES), alName);
if (!file.mkdirs()) {
Log.e(LOG_TAG, "Directory not created");
}
return file;
}
反例：
public File getDir(String alName) {
// 任何时候都不要硬编码文件路径，这不仅存在安全隐患，也让app 更容易出现适配问题
File file = new File("/mnt/sdcard/Download/Album", alName);
if (!file.mkdirs()) {
Log.e(LOG_TAG, "Directory not created");
}
return file;

}
```



#### Storage

```
1.
当使用外部存储时，必须检查外部存储的可用性。
正例：
// 读/写检查
public boolean isExternalStorageWritable() {
String state = Environment.getExternalStorageState();
if (Environment.MEDIA_MOUNTED.equals(state)) {
return true;
}
return false;
}
// 只读检查
public boolean isExternalStorageReadable() {
String state = Environment.getExternalStorageState();
if (Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)) {
return true;
}
return false;
}
```


```
2.
应用间共享文件时，不要通过放宽文件系统权限的方式去实现，而应使用
FileProvider。
正例：
<!-- AndroidManifest.xml -->
<manifest>
...
<application>
...

<provider
android:name="android.support.v4.content.FileProvider"
android:authorities="com.example.fileprovider"
android:exported="false"
android:grantUriPermissions="true">
<meta-data
android:name="android.support.FILE_PROVIDER_PATHS"
android:resource="@xml/provider_paths" />
</provider>
...
</application>
</manifest>
<!-- res/xml/provider_paths.xml -->
<paths>
<files-path path="album/" name="myimages" />
</paths>
void getAlbumImage(String imagePath) {
File image = new File(imagePath);
Intent getAlbumImageIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
Uri imageUri = FileProvider.getUriForFile(
this,
"com.example.provider",
image);
getAlbumImageIntent.putExtra(MediaStore.EXTRA_OUTPUT, imageUri);
startActivityForResult(takePhotoIntent, REQUEST_GET_ALBUMIMAGE);
}
反例：
六、文件与数据库
- 38 -
void getAlbumImage(String imagePath) {
File image = new File(imagePath);
Intent getAlbumImageIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
//不要使用file://的URI 分享文件给别的应用，包括但不限于Intent
getAlbumImageIntent.putExtra(MediaStore.EXTRA_OUTPUT, Uri.fromFile(image));
startActivityForResult(takePhotoIntent, REQUEST_GET_ALBUMIMAGE);
}
```


```
3.
数据库Cursor 必须确保使用完后关闭，以免内存泄漏。
说明：
Cursor 是对数据库查询结果集管理的一个类，当查询的结果集较小时，消耗内存不
易察觉。但是当结果集较大，长时间重复操作会导致内存消耗过大，需要开发者在
操作完成后手动关闭Cursor。
数据库Cursor 在创建及使用时，可能发生各种异常，无论程序是否正常结束，必须
在最后确保Cursor 正确关闭，以避免内存泄漏。同时，如果Cursor 的使用还牵涉
多线程场景，那么需要自行保证操作同步。
正例：
public void handlePhotos(SQLiteDatabase db, String userId) {
Cursor cursor;
try {
cursor = db.query(TUserPhoto, new String[] { "userId", "content" }, "userId=?", new
String[] { userId }, null, null, null);
while (cursor.moveToNext()) {
// TODO
}
} catch (Exception e) {
// TODO
} finally {
if (cursor != null) {
cursor.close();
}
}
}
反例：
public void handlePhotos(SQLiteDatabase db, String userId) {
Cursor cursor = db.query(TUserPhoto, new String[] { "userId", "content" }, "userId=?", new
String[] { userId }, null, null, null);
while (cursor.moveToNext()) {
// TODO
}
// 不能放任cursor 不关闭
}
```


```
4.
多线程操作写入数据库时，需要使用事务，以免出现同步问题。
说明：
通过SQLiteOpenHelper 获取数据库SQLiteDatabase 实例，Helper 中会自动缓存

已经打开的SQLiteDatabase 实例，单个App 中应使用SQLiteOpenHelper 的单例
模式确保数据库连接唯一。由于SQLite 自身是数据库级锁，单个数据库操作是保证
线程安全的（不能同时写入），transaction 是一次原子操作，因此处于事务中的操作
是线程安全的。
若同时打开多个数据库连接，并通过多线程写入数据库，会导致数据库异常，提示
数据库已被锁住。
正例：
public void insertUserPhoto(SQLiteDatabase db, String userId, String content) {
ContentValues cv = new ContentValues();
cv.put("userId", userId);
cv.put("content", content);
db.beginTransaction();
try {
db.insert(TUserPhoto, null, cv);
// 其他操作
db.setTransactionSuccessful();
} catch (Exception e) {
// TODO
} finally {
db.endTransaction();
}
}
反例：
public void insertUserPhoto(SQLiteDatabase db, String userId, String content) {
ContentValues cv = new ContentValues();
cv.put("userId", userId);
cv.put("content", content);
db.insert(TUserPhoto, null, cv);
}
```


```
反例：
public void updateUserPhoto(SQLiteDatabase db, String userId, String content) {
String sqlStmt = String.format("UPDATE %s SET content=%s WHERE userId=%s",
TUserPhoto, userId, content);
//请提高安全意识，不要直接执行字符串作为SQL 语句
db.execSQL(sqlStmt);
}
```


```
5.
如果ContentProvider 管理的数据存储在SQL 数据库中，应该避免将不受
信任的外部数据直接拼接在原始SQL 语句中。
正例：
// 使用一个可替换参数
String mSelectionClause = "var = ?";
String[] selectionArgs = {""};
selectionArgs[0] = mUserInput;
反例：
// 拼接用户输入内容和列名
String mSelectionClause = "var = " + mUserInput;
```

#### Image

```
1.
加载大图片或者一次性加载多张图片，应该在异步线程中进行。图片的加
载，涉及到IO 操作，以及CPU 密集操作，很可能引起卡顿。
正例：
class BitmapWorkerTask extends AsyncTask<Integer, Void, Bitmap> {
...
// 在后台进行图片解码
@Override
protected Bitmap doInBackground(Integer... params) {
final Bitmap bitmap = BitmapFactory.decodeFile("some path");
return bitmap;
}
...
}
反例：
Button btnLoadImage = (Button) findViewById(R.id.btn);
btnLoadImage.setOnClickListener(new OnClickListener(){
public void onClick(View v) {
Bitmap bitmap = BitmapFactory.decodeFile("some path");
}
});
```


```
2.
在ListView，ViewPager，RecyclerView，GirdView 等组件中使用图片时，
应做好图片的缓存，避免始终持有图片导致内存溢出，也避免重复创建图片，引起
性能问题。建议使用Fresco （ https://github.com/facebook/fresco ）、Glide
（https://github.com/bumptech/glide）等图片库。
正例：

例如使用系统LruCache 缓存，参考：
https://developer.android.com/topic/performance/graphics/cache-bitmap.html
private LruCache<String, Bitmap> mMemoryCache;
@Override
protected void onCreate(Bundle savedInstanceState) {
...
// 获取可用内存的最大值，使用内存超出这个值将抛出OutOfMemory 异常。LruCache 通
过构造函数传入缓存值，以KB 为单位。
final int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);
// 把最大可用内存的1/8 作为缓存空间
final int cacheSize = maxMemory / 8;
mMemoryCache = new LruCache<String, Bitmap>(cacheSize) {
@Override
protected int sizeOf(String key, Bitmap bitmap) {
return bitmap.getByteCount() / 1024;
}
};
...
}
public void addBitmapToMemoryCache(String key, Bitmap bitmap) {
if (getBitmapFromMemCache(key) == null) {
mMemoryCache.put(key, bitmap);
}
}
public Bitmap getBitmapFromMemCache(String key) {
return mMemoryCache.get(key);
}

public void loadBitmap(int resId, ImageView imageView) {
final String imageKey = String.valueOf(resId);
final Bitmap bitmap = getBitmapFromMemCache(imageKey);
if (bitmap != null) {
mImageView.setImageBitmap(bitmap);
} else {
mImageView.setImageResource(R.drawable.image_placeholder);
BitmapWorkerTask task = new BitmapWorkerTask(mImageView);
task.execute(resId);
}
}
class BitmapWorkerTask extends AsyncTask<Integer, Void, Bitmap> {
...
// 在后台进行图片解码
@Override
protected Bitmap doInBackground(Integer... params) {
final Bitmap bitmap = decodeSampledBitmapFromResource(getResources(),
params[0], 100, 100));
addBitmapToMemoryCache(String.valueOf(params[0]), bitmap);
return bitmap;
}
...
}
反例：
没有存储，每次都需要解码，或者有缓存但是没有合适的淘汰机制，导致缓存效果
很差，依然经常需要重新解码
```

```
3.
png 图片使用TinyPNG 或者类似工具压缩处理，减少包体积。
```


```
4.
使用完毕的图片，应该及时回收，释放宝贵的内存。
正例：
Bitmap bitmap = null;
loadBitmapAsync(new OnResult(result){
bitmap = result;
});
...使用该bitmap...
// 使用结束，在2.3.3 及以下需要调用recycle()函数，在2.3.3 以上GC 会自动管理，除非你明
确不需要再用。
七、Bitmap、Drawable 与动画
- 48 -
if (Build.VERSION.SDK_INT <= Build.VERSION_CODES.GINGERBREAD_MR1) {
bitmap.recycle();
}
bitmap = null;
反例：
使用完成图片，始终不释放资源。
```




#### Security

```
1.
在SDK 支持的情况下，Android 应用必须使用V2 签名，这将对APK 文
件的修改做更多的保护。
```

```
2.
所有的 Android 基本组件（Activity、Service、BroadcastReceiver、

ContentProvider 等）都不应在没有严格权限控制的情况下，将 android:exported 设
置为 true。
```


```
3.
WebView 应设置 WebView#getSettings()#setAllowFileAccess(false)、
WebView#getSettings()#setAllowFileAccessFromFileURLs(false) 、
WebView#getSettings()#setAllowUniversalAccessFromFileURLs(false)，阻止 file
scheme URL 的访问。
```



```
4.
不要把敏感信息打印到log 中。
说明：
在开发过程中，为了方便调试，通常会使用log 函数输出一些关键流程的信息，这
些信息中通常会包含敏感内容，让攻击者更加容易了解APP 内部结构，方便破解和
攻击，甚至直接获取到有价值的敏感信息。
反例：
String username = "log_leak";
String password = "log_leak_pwd";
Log.d("MY_APP", "usesname" + username);
Log.v("MY_APP", "send message to server ");
以上代码使用Log.d Log.v 打印程序的执行过程的username 等调试信息，日志没有
关闭，攻击者可以直接从Logcat 中读取这些敏感信息。所以在产品的线上版本中关
闭调试接口，不要输出敏感信息。
```


```
5.
确保应用发布版本的android:debuggable 属性设置为false。
```


```
6.
本地加密秘钥不能硬编码在代码中，更不能使用 SharedPreferences 等本
地持久化机制存储。应选择Android 自身的秘钥库（KeyStore）机制或者其他安全
性更高的安全解决方案保存。
说明：
应用程序在加解密时，使用硬编码在程序中的密钥，攻击者通过反编译拿到密钥可
以轻易解密APP 通信数据。
```


```
7.
使用Android 的AES/DES/DESede 加密算法时，不要使用ECB 加密模式，
应使用CBC 或CFB 加密模式。
说明：
加密模式有 ECB、CBC、CFB、OFB 等，其中 ECB 的安全性较弱，如果使用固
定的密钥，相同的明文将会生成相同的密文，容易受到字典攻击，建议使用 CBC、
CFB 或OFB 等模式。
1) ECB：Electronic codebook，电子密码本模式
2) CBC：Cipher-block chaining，密码分组链接模式
3) CFB：Cipher feedback，密文反馈模式
4) OFB：Output feedback，输出反馈模式
```


```
8.
Android APP 在HTTPS 通信中，验证策略需要改成严格模式。
说明：
Android APP 在HTTPS 通信中，使用ALLOW_ALL_HOSTNAME_VERIFIER，表
示允许和所有的HOST 建立SSL 通信，这会存在中间人攻击的风险，最终导致敏感
信息可能会被劫持，以及其他形式的攻击。
反例：
SSLSocketFactory sf = new MySSLSocketFactory(trustStore);
sf.setHostnameVerifier(SSLSocketFactory.ALLOW_ALL_HOSTNAME_VERIFIER);
ALLOW_ALL_HOSTNAME_VERIFIER 关闭host 验证，允许和所有的host 建立
SSL 通信，BROWSER_COMPATIBLE_HOSTNAME_VERIFIER 和浏览器兼容的
验证策略，即通配符能够匹配所有子域名 ，STRICT_HOSTNAME_VERIFIER 严
格匹配模式，hostname 必须匹配第一个CN 或者任何一个subject-alts，以上例子
使用了ALLOW_ALL_HOSTNAME_VERIFIER，需要改成STRICT_HOSTNAME_
VERIFIER
```


```
9.
如果使用自定义X509TrustManager 实现类，必须在checkServerTrusted()
方法中校验服务端证书的合法性，否则可能受到中间人攻击。
说明：
常见误区是checkServerTrusted()方法根本没有实现，这将导致 X509TrustManager
形同虚设。该方法中需要实现完备的校验逻辑， 对于证书错误抛出
CertificateException 。
正例：
HostnameVerifier hnv = new HostnameVerifier() {
@Override
public boolean verify(String hostname, SSLSession session) {
八、安全
- 56 -
if("yourhostname".equals(hostname)){
return true;
} else {
HostnameVerifier hv = HttpsURLConnection.getDefaultHostnameVerifier();
return hv.verify(hostname, session);
}
}
};
反例：
TrustManager tm = new X509TrustManager() {
public void checkClientTrusted(X509Certificate[] chain, String authType)
throws CertificateException {
//do nothing，接受任意客户端证书
}
public void checkServerTrusted(X509Certificate[] chain, String authType)
throws CertificateException {
//do nothing，接受任意服务端证书
}
public X509Certificate[] getAcceptedIssuers(){
return null;
}
};
sslContext.init(null, new TrustManager[] { tm }, null);
```


#### Log

```
1.
不能使用System.out.println 打印log。
正例：
Log.d(TAG, "Some Android Debug info ...");
反例：
System.out.println("System out println ...");
```


```
2.
Log 的tag 不能是" "。
说明：
日志的tag 是空字符串没有任何意义，也不利于过滤日志。
正例：
private static String TAG = "LoginActivity";
Log.e(TAG, "Login failed!");
反例：
Log.e("", "Login failed!");
```