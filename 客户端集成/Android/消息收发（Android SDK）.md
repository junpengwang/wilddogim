
### 消息收发 

####1 消息发送
#####1.1 会话获取
会话是指面向一个人或者一个群组的对话，发消息时首先需要先获取会话，会话的获取通过 WilddogClient.newConversation() 实现：
######原型：
	Conversation newConversation(List<String> members)
######参数说明
参数|说明
---|---
members|用户 id 列表
返回值|Conversation 对象

######示例

```
Conversation conversation;
try {
   // 创建一对一聊天
   conversation = WilddogIMClient.newConversation(Arrays.asList("USER-IDENTIFIER"));
} catch (WilddogConversationException e) {
   // 如果已经存在，会返回已有的 conversation
   conversation = e.getConversation();
}
```

#####1.2 创建文本消息
Conversation 创建成功后，可以通过 conversation.send(message) 来发送 Message 消息。
 
######原型：
	public static TextMessage newMessage(String text)
######参数说明：
参数	|说明
----|----
text|	文本消息

######示例：

```
String messageText = "Hi! How are you";
TextMessage message = Message.newMessage(messageText);
```

#####1.3 创建图片消息
图片消息由 ImageMessage 定义，它是 Message 的一个子类。
 
######原型：
	public static Message newMessage(MessageType type, byte[] data)
######参数说明：
参数	|说明
----|----
text|	文本消息

######示例：

```
// 创建图片消息
Bitmap imageBitmap = BitmapFactory.decodeResource(getResources(), R.drawable.back_icon);
ByteArrayOutputStream stream = new ByteArrayOutputStream();
imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, stream);
byte[] imageData = stream.toByteArray();

ImageMessage message = Message.newMessage(Message.Type.IMAGE, imageData);
```

#####1.4 创建语音消息
语音消息由 VoiceMessage 定义，它是 Message 的一个子类。
 
######示例：

```
// 创建语音消息
ImageMessage message = Message.newMessage(Message.Type.VOICE, voiceData);
```

#####1.5. 发送消息：

######示例：

``` 
// 发送一条消息，没有回调，可以根据 message.getStatus() 来获取消息的状态。
conversation.send(message);

// 带回调方法的消息发送接口，可以知道消息发送的详细进度。
conversation.send(message, new WilddogIMProgressListener() {
	@Override
    public void onProgressStart(MessagePart messagePart, Operation operation) {
    }
    
    @Override
    public void onProgressUpdate(MessagePart messagePart, Operation operation, long l) {
    }
    
    @Override
    public void onProgressComplete(MessagePart messagePart, Operation operation) {
    } 
      
	@Override
    public void onProgressError(MessagePart messagePart, Operation operation, Throwable throwable) {
    }    
});
```


####2 接收消息
接收消息需要注册新消息通知回调 WilddogIMMessageListener，如果用户是登录状态，SDK 收到新消息会通过此回调方法。
#####2.1 注册监听
######原型：
 
	public  WilddogIMClient registerMessageListener(WilddogIMMessageListener listener);
	
######示例：

```
WilddogIMClient client = WilddogIMClient.newInstance(this, "APP ID");
client.registerMessageListener(this);
```

#####2.2 消息解析
实现 WilddogIMMessageListener 的 onNewMessage(List<Message> messages) 方法，从 messages 集合中能获取所有的新消息通知。
######示例：
```
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

####3 消息属性

#####3.1 消息状态
通过 Message 的 status 属性可以获取当前消息的状态，如发送中、发送成功、发送失败和删除，对于删除的消息，需要 UI 判断状态并隐藏。

######原型：

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

######3.2 消息时间
通过 getSendAt() 方法可以获取到消息的发送时间。该时间是服务器时间，而非本地时间，在创建消息时，此时间为根据服务器时间校准过的时间，发送成功后会改为准确的服务器时间。
######原型：
```
public long getSendAt()
```

#####3.3 消息 ID
消息 ID 是由服务器统一生成的有序 ID。
######原型：
	public String getId()
	
#####3.4 消息发送者
######原型：
	public Identity getSender()
	
#####3.5 消息删除
删除消息只支持本地消息删除，调用 void delete(DeletionMode deletionMode) 方法。DeletionMode 为消息删除选项，为保留字段，可以为空。

####4 会话操作
#####4.1 获取所有会话
调用 getConversations 可以获取本地所有会话列表：
######原型：
	public List<Conversation> getConversations()
######示例：
	List<Conversation> conversations = WilddogClient.getConversations();
	
#####4.2 获取会话本地消息
通过 Conversation 中的 getMessage 方法可以获取本地历史消息，可以实现分页拉取。此方法为同步方法:
######原型：
	List<Message> getMessage(int limit, Message endAt);
######参数说明：

参数	| 说明
----|----
limit |	从最后一条消息向前的消息条数
endAt |	获取到的消息时间小于等于 endAt 消息。当传 null 时，默认从最新的消息开始读取

######示例：
	List<Message> messages = conversation.getMessage(20,null);
#####4.3 删除会话
删除会话默认会将会话本地相关的消息也删除掉。
######原型：
	void delete(DeletionMode deletionMode)
######参数说明：
参数	| 说明
----|----
deletionMode  |	目前为保留字段，无实际意义，可以为 null

######示例：
	conversation.delete(null);
 
 
 