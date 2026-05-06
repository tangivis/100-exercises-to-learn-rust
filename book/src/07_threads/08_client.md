# 专用的 `Client` 类型 (A dedicated `Client` type)

到目前为止从客户端侧的所有交互都相当低层：你必须手动创建响应通道、构建命令、把它发给服务端，再对响应通道调用 `recv` 取响应。

这有大量样板代码可以抽象出去，本练习要做的正是这件事。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/08_client.md)
