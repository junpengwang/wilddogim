###离线推送 


用户断开与野狗服务器的连接的时候，收到的消息将通过 push 通知的形式展示出来，用户可以自定义点击通知之后的操作。
####1.首先需要在 build.gradle 中配置下载 push 的 aar 包
	compile 'com.wilddog.push:wilddog-push-android:0.0.2-SNAPSHOT'
####2.配置 PushServer 去开启推送
#####示例：
```
<service
 android:name=".push.PushService"
 android:exported="false"
 android:process=":push" />
 ```
####3.首先需要配置一个 BroadcastReceiver,它的 intent-filter 中需要配置 action 为 com.wilddog.push.{YOUR_APP_ID}，然后就可以在离线的时候收到推送过来的消息。
######示例：
```
<receiver
 android:name=".receiver.MyMessageReceiver"
 android:exported="true" >
    <intent-filter android:priority="1000">
        <action android:name="com.wilddog.push.imdemo" />
    </intent-filter>
</receiver>
```
####4.在 onReceive 方法中处理结果 data 中的数据为推送过来数据
######示例：
```
String data = intent.getStringExtra("data");
if (!TextUtils.isEmpty(data)) {
   
Intent chatIntent = new Intent(context, LoginActivity.class);
                  
chatIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
context.startActivity(chatIntent);
    
```
 



 