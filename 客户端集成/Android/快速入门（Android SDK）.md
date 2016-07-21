###快速入门
####1 支持版本
* JDK 版本 1.7 或者更高版本。
* Android Studio 1.4 或更高版本。
* Android SDK Version 23 或者更高版本。

####2 前置准备
######2.1 创建 Wilddog 应用
野狗 IM 解决方案聊天数据都存在野狗云上，所以在集成 IM 解决方案前，需要先创建 Wilddog 应用来获取 Wilddog AppID。

进入野狗官网[首页](https://www.wilddog.com)，登录野狗帐号，如果没有野狗帐号请先注册，进入控制面板，输入应用名称和应用 ID，点击添加应用。就能生成野狗应用。关于更多创建野狗帐号的信息请参考 [创建应用](https://z.wilddog.com/overview/app)

####3 添加 SDK
######3.1 引入 SDK
Android Studio 使用 Gradle 添加 Wilddog IM 的依赖。在你的 build.gradle 添加：
```
dependencies {
    compile 'com.wilddog:wilddog-client-android:0.6.4+'
    compile 'com.wilddog:wilddogim-client-android:0.0.1+'
    //others ...
}
```

######3.2 工程配置
野狗 IM 解决方案在 Android 上需要 android.permission.INTERNET 权限。你需要在 AndroidMainfest.xml 文件添加：
```
<uses-permission android:name="android.permission.INTERNET" />
```
####4 初始化
######4.1 初始化 SDK
在一切操作之前，必须先进行一次初始化，设置 Wilddog AppID 和 AndroidContext。可以在 onCreate 方法中设置。
```
WilddogIMClient wilddogIMClient = WilddogIMClient.newInstance(context, "APP ID");

```
######4.2 建立连接
野狗 IM 解决方案会和野狗服务器建立一个长连接，以达到能实时接收消息的目的。你可以通过 registerConnectionListener 方法来监听连接状态。调用 connect() 方法来建立连接。
```
// 监听连接状态
wilddogIMClient.registerConnectionListener(this)
// 和野狗服务器建立连接
layerClient.connect();

```
######4.3 用户登录
一般 APP 都会有自己的用户系统，野狗通过 JWT Token 的方式来集成 APP 已有用户。更多信息请参考 [用户集成章节]()
```
 @Override
 // 当连接创建成功后将会被调用
 public void onConnectionConnected(WilddogIMClient WilddogIMClient) {
     WilddogIMClient.authenticate(token);
 }
 ```
####5 发起聊天
聊天分为单聊和讨论组，野狗 IM 解决方案不严格区分它们，当聊天人数超过两个人时则自动升级为讨论组，但是不可逆。
```
Conversation conversation = wilddogIMClient.newConversation(Array.asList("user id"));
String messageText = "Hi! How are you";
TextMessage message = Message.newMessage(messageText);
conversation.send(message);
```
####6 接收消息
```
// 注册接收监听
client.registerMessageListener(this);

//实现监听方法
public void onNewMessage(List<Message> messages) {
    for (Message message : messages){

        switch (message.getMessageType()) {

            case TEXT:
            //文本消息
            TextMessage textMessage = (TextMessage)message;

            case IMAGE:
            //图片消息
            ImageMessage imageMessage = (ImageMessage)message;

            case VOICE:
            //语音消息
            VoiceMessage voiceMessage = (VoiceMessage)message;

        }
    }
}
```