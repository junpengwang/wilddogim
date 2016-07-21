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
####5 发起聊天