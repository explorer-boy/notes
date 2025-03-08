# BurpSuite 抓包Android 设备

抓包条件：

1. 手机代理工具修改为本地电脑 IP 代理，端口与 BurpSuite 对应
2. 导入系统证书：
   - 版本 <= Android 5，app 信任用户安装的证书
   - Android 7 <= 版本 <= Android 14，app 默认不信任用户安装的证书，只默认信任系统证书，需要将 BurpSuite 证书导入在系统证书目录内 `/system/etc/security/cacerts`
   - 版本 >= Android 14，从系统证书目录从 `/system/etc/security/cacerts` 迁移到 `com.android.conscrypt.apex`（可通过 Google Play 动态更新）中 apex 挂载后的目录 `/apex/com.android.conscrypt/cacerts`，且 `/apex` 目录下不可挂载读写。无法直接通过root 权限对其进行直接写入操作。

## 1. 修改代理

打开设置-WLAN 长按连接的网络，选择修改网络，在高级选项中设置代理，代理服务器主机名为自己电脑 IP，端口和 BurpSuite 设置的端口一致即可

也可以下载 [super proxy](https://super-proxy.en.softonic.com/android) 等代理工具app，直接进行设置代理

## 2. 导入系统证书

### 导出证书或下载证书

- 手机访问 `http://burpsuite` 或者 `http://bp_ip:bp_port` 右上角进行 CA 证书下载
- BurpSuite 中导出 CA 证书传输到手机的 `/storage/emulated/0/Pictures`（默认下载目录）中

将 cacert.der 证书重命名为 cacert.cer

### 导入用户证书

1. 进入手机中设置-安全-从 SD 卡安装证书，选择证书导入
2. 打开文件管理器（记得开启 Root 权限，手机右上角设置中开启）进入根目录，然后进入 `/data/misc/user/0/cacerts-added`（该目录存储的是用户自己安装的证书文件）

> **注意**：
>
> 配置好后，如果仍然抓不到包，删除系统证书后，重新安装证书，将用户证书 `/data/misc/user/0/cacerts-added` 路径下的证书，使用`MT管理器`修改所属用户和组为：`root`，权限为`644`

#### Android 7

将证书文件移动到系统证书目录 `/etc/security/cacerts`

#### Android 14

使用 magisk 模块 [Magisk-MoveCACerts](https://github.com/wjf0214/Magisk-MoveCACerts)，将证书可以直接移动到 `/data/adb/modules/MoveCACerts/system/etc/security/cacerts/` 目录下，再重启手机即可

## 工具链接

> super proxy：https://super-proxy.en.softonic.com/android

> Magisk-MoveCACerts：https://github.com/wjf0214/Magisk-MoveCACerts