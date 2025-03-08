# frp 内网穿透

## 环境

> frp GitHub 地址：https://github.com/fatedier/frp

```
设备：Windows，Linux2，Linux3

Windows--攻击机（通过Linux2访问Linux3的网络）
Linux2--frps服务（公网主机, 负责转发流量）
Linux3--frpc服务（内网主机, 将内网服务暴露到Linux2）
```

## 步骤

### 在 Linux2 上配置 frps

1. 下载合适的 frp 版本，进行解压

   ```
   tar -zxvf frp_0.xx.0_linux_amd64.tar.gz
   cd frp_0.xx.0_linux_amd64
   ```

2. 配置 frps：

   - 编辑`frps.ini`文件
     ```
     [common]
     bind_port = 7000  # FRP 服务端监听的端口
     token = your_token  # 可选，用于客户端和服务端之间的认证
     ```

3. 启动 frps：

   - 临时启动 `./frps -c frps.ini`

   - 后台启动 `nohup ./frps -c frps.ini &`

### 在 Linux3 上配置 frpc

1. 下载合适的 frp 版本，进行解压

   ```
   tar -zxvf frp_0.xx.0_linux_amd64.tar.gz
   cd frp_0.xx.0_linux_amd64
   ```

2. 配置 frpc：

   - 编辑`frpc.ini`文件

     ```
     [common]
     server_addr = linux2的公网IP  # Linux 2 的公网 IP
     server_port = 7000            # FRP 服务端的端口
     token = your_token            # 与服务端一致

     [socks5_proxy]                # 定义一个 SOCKS5 代理
     type = tcp
     remote_port = 1080            # 在 Linux 2 上暴露的端口
     plugin = socks5

     [http_proxy]
     type = tcp
     remote_port = 8080            # 在 Linux 2 上暴露的端口
     plugin = http_proxy
     ```

3. 启动 frpc：

   - 临时启动 `./frpc -c frpc.ini`

   - 后台启动 `nohup ./frpc -c frpc.ini &`

### 在 Windows 上配置 SOCKS5 代理

设置代理：

地址：Linux 2 的公网 IP

端口：与 frpc 配置的 remote_port 一致

```
socks5://liunx2_ip:1080

http://linux2_ip:8080
```