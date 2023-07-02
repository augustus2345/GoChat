## GoChat

### 项目介绍

使用 Golang 基于 WebSocket 的通讯聊天的后端项目。

### 项目技术和框架

* web 框架:			 Gin
* 长连接:			WebSocket
* 日志框架:			Zap
* 配置管理:			Viper
* ORM框架:		Gorm
* 通讯协议:			proto buffer
* 数据库:			MySQL
* 图片文件二进制操作

### 功能列表：

* 登录注册
* 修改头像
* 群聊天
* 群好友列表
* 单人聊天
* 添加好友
* 添加群组
* 文本消息
* 剪切板图片
* 图片消息
* 文件发送
* 语音消息
* 视频消息

### protocol buffer协议

```go
syntax = "proto3";
package protocol;

message Message {
    string avatar = 1;       //头像
    string fromUsername = 2; // 发送消息用户的用户名
    string from = 3;         // 发送消息用户uuid
    string to = 4;           // 发送给对端用户的uuid
    string content = 5;      // 文本消息内容
    int32 contentType = 6;   // 消息内容类型：1.文字 2.普通文件 3.图片 4.音频 5.视频 6.语音聊天 7.视频聊天
    string type = 7;         // 如果是心跳消息，该内容为heatbeat
    int32 messageType = 8;   // 消息类型，1.单聊 2.群聊
    string url = 9;          // 图片，视频，语音的路径
    string fileSuffix = 10;  // 文件后缀，如果通过二进制头不能解析文件后缀，使用该后缀
    bytes file = 11;         // 如果是图片，文件，视频等的二进制
}
```

### 选择 protocol buffer 原因

通过消息体能看出，消息大部分都是字符串或者整型类型。通过 json 就可以进行传输。那为什么要选择 google 的 protocol buffer 进行传输呢？

1. 因为 protobuf 序列化后的大小是 json 的10分之一，是 xml 格式的 20分之一，但是性能却是它们的 5~100 倍.
2. 支持二进制
   当我们看到消息体最后一个字段，是定义的 bytes，二进制类型。
   我们在传输图片，文件，视频等内容的时候，可以将文件直接通过 socket 消息进行传输。
   当然我们也可以将文件先通过 http 接口上传后，然后返回路径，再通过 socket 消息进行传输。但是这样只能实现固定大小文件的传输，如果我们是语音电话，或者视频电话的时候，就不能传输流。
