###用户集成 
* 1 获取 Token
* 2 登录
* 3 登出
* 4 获取当前用户
* 5 设置登录监听


####1 获取 Token
野狗 IM 解决方案不开发独立的用户系统，为了集成 APP 已有的用户，我们通过自定义 Token 的方式。开发者需要将 APP 已有的用户 ID，用户名称，用户头像等信息结合野狗的超级密钥生成 [JWT Token](https://jwt.io/)，在登录野狗 IM 解决方案前，需要请求 APP Server 来获取这个 JWT Token 才能进入下一步登录操作。关于更多自定义 Token 和野狗超级密钥等信息请参考 [自定义 Token](https://z.WilddogIM.com/rule/guide#5-zi-ding-yi-token0)。
####2 登录
获取 Token 后，调用登录接口就可以正常收发消息了。登录为异步过程，通过回调函数返回是否成功，成功后方能进行后续操作。
#####原型：
	WilddogIMClient authenticate(String token)
######参数说明
参数	| 说明
----|----
token | 由 App 服务器生产的 jwt token

######示例
```
 @Override
 // 当连接创建成功后将会被调用
 public void onConnectionConnected(WilddogIMClient WilddogIMClient) {
     WilddogIMClient.authenticate(token);
 }
 ```
 
####3 登出
当用户需要主动注销或进行用户切换的时候，需要调用注销操作：
######原型：
	WilddogIMClient deauthenticate()
	
####4 获取当前用户
通过 WilddogIMClient 成员方法 getAuthenticatedUserId 能获取当前登录用户：
######原型：
	String getAuthenticatedUserId()
######返回值：
返回值为登录用户的用户 id。

####5 设置登录监听
通过WilddogIMClient的成员方法registerAuthenticationListener（）和unregisterAuthenticationListener（），可以对登录状态进行监听。
######原型：
```
WilddogIMClient  unregisterAuthenticationListener(WilddogIMAuthenticationListener listener)
WilddogIMClient  registerAuthenticationListener(WilddogIMAuthenticationListener listener)
```
 
 
 
 