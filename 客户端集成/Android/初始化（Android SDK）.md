
###初始化
* 1 初始化 SDK
* 2 应用状态
* 3 连接状态监听
* 4 用户登录状态监听
* 5 和服务器建立连接

####1 初始化 SDK
野狗 IM 解决方案的主要入口为 WilddogIMClient，一切操作都从此入口开始。SDK 操作第一步需要创建 WilddogIMClient：

原型：

	public static WilddogIMClient newInstance(Context context, String WilddogAppId)
  
获取 WilddogIMClient 实例:

######参数：

参数 | 说明
-----|------
context | 应用的 context
WilddogAppId | 野狗通讯云 

######示例：

	WilddogIMClient client = WilddogIMClient.newInstance(context, "APP ID");
	
####2 应用状态
为了 WilddogIMClient 能管理网络连接和获取通知状态，我们需要在 Application.onCreate() 方法中调用 WilddogIMClient.applicationCreated(app)。

######原型：

	void applicationCreated(android.app.Application application)
	
######示例：

```
public class MyApp extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        // Lets the WilddogIMClient track Application state for connection and notification management.
        WilddogIMClient.applicationCreated(this);
    }
}
```
####3 连接状态监听

当 WilddogIMClient 已经连接到服务器时，此回调会被调用。此方法能知道 WilddogIMClient 的连接和断开事件。需要注意的是，WilddogIMClient 因某种原因导致的连接错误或者连接中断，SDK 内部都有相应的容错机制和重连机制，用户无需关心。

######原型：

	client registerConnectionListener(WilddogIMConnectionListener listener)
	
######示例：
	client.registerConnectionListener(this)
	
####4 用户登录状态监听

用户登录登出的时候，SDK 会有相应的回调方法，通过 WilddogIMClient 中 registerAuthenticationListener 方法可以设置用户 auth 成功状态进行监听。目前有三个回调方法，登录成功，退出登录成功，出错回调。

######原型：

	client registerAuthenticationListener(WilddogIMAuthenticationListener listener)
	
######示例：

	client.registerAuthenticationListener(this);
	
####5 和服务器建立连接
设置监听后，我们可以通过 WilddogIMClient 中的 connect() 方法来和服务器建立连接。

	// 野狗 SDK 和野狗服务器简历连接
	client.connect();
