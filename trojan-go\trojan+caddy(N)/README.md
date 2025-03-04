介绍：

本示例配置为 trojan-go 或 trojan 与 naiveproxy 应用。trojan-go 或 trojan 服务端前置（监听 443 端口）处理来自墙内的 HTTPS 请求，如果是合法的 trojan-go 或 trojan 客户端请求，那么为该请求提供服务（科学上网）；否则将已解除 TLS 的流量请求回落（转发）给 caddy，由 caddy 为其提供 WEB 服务，若 caddy 发现是 naiveproxy 流量就进行正向代理。其应用如下：

1、trojan-go或trojan（回落配置，TLS由自己提供及处理。）

2、naiveproxy（基于caddy的改进版forwardproxy插件实现，TLS由trojan-go或trojan提供及处理，不需配置。）

原理：

默认流程：trojan-go/trojan client <------ HTTP/2或HTTPS ------> trojan-go/trojan server  
回落流程：WEB client <------------- HTTP/2或HTTPS ------------> trojan-go/trojan server <-- H2C或HTTP/1.1 --> caddy（WEB server）

注意：

1、因 trojan-go 或 trojan 不支持 UDS，故回落仅使用 Local Loopback 连接。

2、因 trojan-go 或 trojan 不支持 PROXY protocol，故回落不启用 PROXY protocol。

3、trojan-go 完全兼容 trojan，服务端还有自己的特色：支持 trojan 应用与自己的 WebSocket 应用共存；支持 CDN 流量中转(基于 WebSocket over TLS)；支持使用 AEAD 对 trojan 协议流量进行二次加密(基于 Shadowsocks AEAD)。

4、caddy 支持 HTTP/1.1 server 与 H2C server 共用一个端口或一个进程。

5、使用本人 Releases 中编译好的 caddy 文件，可支持 H2C server、naiveproxy 等应用。

6、本示例的 naiveproxy 仅支持 HTTP/2 代理应用，即 HTTPS 协议传输。

7、不要使用第三方 ACME 客户端在当前服务器上以 HTTP-01 或 TLS-ALPN-01 验证方式申请与更新 SSL/TLS 证书，因 HTTP-01 或 TLS-ALPN-01 验证方式申请与更新 SSL/TLS 证书需监听 80 或 443 端口，从而与当前应用端口冲突。
