# HTTPS

### ssl建立连接过程

1. 客户端请求https地址，发送客户端支持的加密算法
2. 服务端收到消息后，响应匹配好的加密算法和数字证书（公钥）
3. 客户端解析证书，并生成一个随机值（会话密钥）
4. 客户端通过公钥加密会话密钥
5. 客户端向服务端发送4的加密信息
6. 服务端解密获得会话密钥并响应成功
7. 客户端通过会话密钥加密一条消息并发送给服务端，验证服务端是否正常接收加密消息
8. 服务端回传一条通过会话密钥加密的消息，客户端接收的话表明ssl连接建立完成了

