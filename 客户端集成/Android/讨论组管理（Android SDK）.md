 

####1 创建讨论组
野狗 IM 服务不严格区分单聊和讨论组，创建会话时，当会话成员为 2 个时，则为单聊。当大于 2 个时则自动升级为讨论组，可通过 newConvesation 来创建讨论组，目前创建的讨论组是根据成员来确定唯一性：
######原型：
	public Conversation newConversation(List<String> members)
	
######参数说明：
参数	| 说明
----|----
members	| 讨论组成员 id 列表

######示例：
```
Conversation conversation;
try {
   // 以三个成员建立一个讨论组
   conversation = wilddogClient.newConversation(Arrays.asList("USER-IDENTIFIER1","USER-IDENTIFIER2","USER-IDENTIFIER3"));
} catch (WilddogConversationException e) {
   // 如果已经有一个成员列表一样的讨论组，则返回已有会话。
   conversation = e.getConversation();
}
```

####2 邀请用户入讨论组
Conversation 的接口 addMembers 可以拉用户进入讨论组，讨论组内任何人都可以邀请用户，且无需同意：
######原型：
	public void addMembers(List<String> members);
######参数说明
参数	| 说明
----|----
members | 邀请成员 id 列表

######示例：
```
// 邀请用户入讨论组
conversation.addMembers(Arrays.asList("74848949"));
```
 
####3 删除讨论组成员
Conversation 的接口 removeMembers 可以删除讨论组成员，只有群主可以删除讨论组成员，群主为创建会话的用户。另外用户自己可以删除自己，意思就是退出讨论组。
######原型：
	public void removeMembers(List<String> members);
######参数说明：
参数	| 说明
----|----
members	| 删除成员 id 列表

######示例：
```
// 删除讨论组成员
conversation.addMembers(Arrays.asList("74848949"));
```

####4 获取讨论组成员列表
getMembers 方法可以获取讨论组成员列表，任何讨论组成员都有权限调用此方法：
######原型：

	List<String> getMembers()
	
######示例：
	List<String> members = conversation.getMembers();

####5 讨论组事件消息
当讨论组内有资料或成员变更时，需要注册讨论组资料变更监听事件。讨论组的信息变更会通知讨论组内所有成员。
######示例：
```
wilddogIMClient.registerGroupChangeListener(this);
```
####5.1 用户加入讨论组通知
######示例：
```
void memberJoined(String groupId, String operateUser, String operatedUsers) {
	// 新用户加入讨论组通知
}	
```

####5.2 用户退出讨论组通知
######示例：
```
void memberQuit(String groupId) {
	//用户主动退出讨论组通知
}
```

####5.3 用户被踢出讨论组通知
######示例：
```
void memberRemoved(String groupId String operatedUsers) {
	//用户被踢出讨论组通知
}
```
 
 