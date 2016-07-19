 

####1 未读消息
未读消息是指用户没有读过的消息，而非对方是否已经阅读，要想显示正确的未读计数，需要开发者显示调用已读上报，告诉 app 某条消息是否已读，例如，当用户进入一个会话，可以设置整个会话的消息已读。目前消息的已读状态是和用户绑定的，和设备无关。

####2 获取当前未读消息数量
通过 Conversation 的 getTotalUnreadMessageCount 方法可以获取一个会话的未读消息数量：
######原型：
	Integer getTotalUnreadMessageCount()
######示例：
	Integer num = conversation.getTotalUnreadMessageCount()
 
####3 已读上报
当用户阅读某个会话的数据后，需要进行会话消息的已读上报，SDK 根据会话中最后一条阅读的消息，设置会话中之前所有消息为已读。
######原型:
	void markAllMessagesAsRead()

 

 
 