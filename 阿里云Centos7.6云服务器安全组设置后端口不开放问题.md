问题：当我在阿里云服务器控制台设置安全组后，端口依旧不开放

解决：还需要配置防火墙，centos默认防火墙firewalld
```
# 查看开放端口
firewall-cmd --list-all
# 开放端口
firewall-cmd --zone=public --add-port=7000/tcp --permanent
# 永久开发端口
firewall-cmd --reload

重启服务器
```