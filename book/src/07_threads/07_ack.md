# 双向通信 (Two-way communication)

我们当前的 client-server 实现里，通信只朝一个方向流动：从客户端到服务端。\
客户端无从得知服务端是否收到消息、是否成功执行、还是失败了。
这并不理想。

要解决这个问题，我们可以引入双向通信系统。

## 响应通道 (Response channel)

我们需要一种方式让服务端把响应送回客户端。\
有多种方式可以做到，但最简单的选项是把一个 `Sender` 通道也包含在客户端发给服务端的消息里。处理完消息后，服务端可以用这个通道把响应送回客户端。

这是建立在消息传递原语 (message-passing primitives) 之上的 Rust 应用中相当常见的模式。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/07_ack.md)
