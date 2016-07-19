 

##1 消息发送
###1.1 会话获取
会话是指面向一个人或者一个群组的对话，发消息时首先需要先获取会话，会话的获取通过 wilddogClient.newConversation() 实现：
#####原型：
	Conversation newConversation(List members)
#####参数说明
参数|说明
---|---
members|用户 id 列表
返回值|Conversation 对象

#####示例

```
Conversation conversation;
try {
   // 创建一对一聊天
   conversation = wilddogClient.newConversation(Arrays.asList("USER-IDENTIFIER"));
} catch (WilddogConversationException e) {
   // 如果已经存在，会返回已有的 conversation
   conversation = e.getConversation();
}
```

###1.2 发送文本消息
Conversation 创建成功后，可以通过 conversation.send(message) 来发送 Message 消息。
 
Message 对象代表 Conversation 中的一条消息。但是一个 Message 对象中可能含有混合消息体，比如一个 Message 对象可以同时含有文本消息和 Emoji 消息。它们都用 MessagePart 对象表示。
####a. 创建 MessagePart 对象：
#####原型：
	MessagePart newMessagePart(String plainText)
#####参数说明：
参数	|说明
----|----
plainText|	文本消息

#####示例：

```
String messageText = "Hi! How are you";
MessagePart messagePart = wilddogClient.newMessagePart(messageText);
```
####b. 创建 Message 对象：
#####原型：
	Message newMessage(List messageParts)
#####参数说明：
参数	| 说明
----|----
messageParts|	messagePart 集合

#####示例：
	Message message = layerClient.newMessage(Arrays.asList(messagePart))
####c. 发送消息：
#####原型：
	void send(Message message, WilddogProgressListener listener)
	
#####参数说明：
参数	| 说明
----|----
message  |	消息体
listener |	消息发送状态回调

#####示例：

``` 
// 发送一条消息
conversation.send(message);

conversation.send(message, new WilddogProgressListener() {
    public void onProgressStart(MessagePart messagePart, Operation operation) {
    }
    public void onProgressUpdate(MessagePart messagePart, Operation operation, long l) {
    }
    public void onProgressComplete(MessagePart messagePart, Operation operation) {
    }
    public void onProgressError(MessagePart messagePart, Operation operation, Throwable throwable) {
    }
});
```

##2 接收消息
野狗 IM 服务为开发者提供一种灵活的消息回调系统，当本地对象（Conversation，Message）和服务器数据不一致时，就会回调此方法，在此方法中可以知道返回的数据变化属于某一个对象的哪一个属性，并且知道这变化是新增、更新和删除。
###2.1 注册监听
接收消息首先需要注册监听事件：
#####原型：
 
	public  WilddogClient registerEventListener(WilddogChangeEventListener listener);
	
#####示例：

```
WilddogClient client = WilddogClient.newInstance(this, "APP ID");
client.registerEventListener(this);
```

###2.2 消息解析
实现 WilddogChangeEventListener 的 onChangeEvent(WilddogChangeEvent event) 方法，从 WilddogChangeEvent 对象中调用 getChanges() 能获取到所有接收到的数据，这些数据以 WilddogChange 的方式保存在数组中：
#####示例：
```
public void onChangeEvent(WilddogChangeEvent event) {
    List<WilddogChange> changes = event.getChanges();
}
```

当本地 Conversation 和 Message 对象和服务器数据不一致时，这个回调方法就会被触发，通过 getObjectType() 能知道是哪个类型对象变化：
#####示例：
```
switch (change.getObjectType()) {
     case CONVERSATION:
     // 对象是 Conversation 类型
     break;
     case MESSAGE:
     // 对象是 Message 类型
     break;
}
break;
```

对象变化存在三层意思：新增、更新、删除。我们可以调用 getChangeType() 来获取是哪一种：
#####示例：
```
switch (change.getChangeType()) {
    case INSERT:
    // 新增对象
    break;
    case UPDATE:
    // 对象被更新
    break;
    case DELETE:
    // 对象被删除
    break;
}
break;
```
当要获取确定的变更对象时，可以调用 change.getObject() 方法.
###示例：

	Object changeObject = change.getObject();
	
##3 消息属性

###3.1 消息状态
通过 Message 的 status 属性可以获取当前消息的状态，如发送中、发送成功、发送失败和删除，对于删除的消息，需要 UI 判断状态并隐藏。

#####原型：

```
public enum MessageStatus {
	// 发送中
	SENDING,
	// 发送成功
	SUCCESS,
	// 发送失败
	FAILED,
	// 删除
	DELETED
}
```

###3.2 消息时间
通过 getSendAt() 方法可以获取到消息的发送时间。该时间是服务器时间，而非本地时间，在创建消息时，此时间为根据服务器时间校准过的时间，发送成功后会改为准确的服务器时间。
#####原型：
```
public long getSendAt()
```

###3.3 消息 ID
消息 ID 是由服务器统一生成的有序 ID。
#####原型：
	public String getId()
	
###3.4 消息发送者
#####原型：
	public Identity getSender()
	
###3.5 消息删除
删除消息只支持本地消息删除，调用 void delete(DeletionMode deletionMode) 方法。DeletionMode 为消息删除选项，为保留字段，可以为空。

##4 会话操作
###4.1 获取所有会话
调用 getConversations 可以获取本地所有会话列表：
#####原型：
	public List<Conversation> getConversations()
#####示例：
	List<Conversation> conversations = wilddogClient.getConversations();
	
###4.2 获取会话本地消息
通过 Conversation 中的 getMessage 方法可以获取本地历史消息，可以实现分页拉取。此方法为同步方法:
#####原型：
	List<Message> getMessage(int limit, Message endAt);
#####参数说明：

参数	| 说明
----|----
limit |	从最后一条消息向前的消息条数
endAt |	获取到的消息时间小于等于 endAt 消息。当传 null 时，默认从最新的消息开始读取

#####示例：
	List<Message> messages = conversation.getMessage(20,null);
###4.3 删除会话
删除会话默认会将会话本地相关的消息也删除掉。
#####原型：
	void delete(DeletionMode deletionMode)
#####参数说明：
参数	| 说明
----|----
deletionMode  |	目前为保留字段，无实际意义，可以为 null

#####示例：
	conversation.delete(null);
 
 
 