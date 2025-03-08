# ARL 资产侦察灯塔系统

> ARL 安装参考：https://github.com/Aabyss-Team/ARL

## 灯塔增加任务扫描进程

修改文件 `ARL/misc/arl-worker.service` 和 `/etc/systemd/system/arl-worker.service`

```
Description=ARL Worker Service
After=network.target

[Service]
User=root
Group=root
WorkingDirectory=/opt/ARL
ExecStart=/usr/local/bin/celery -A
app.celerytask.celery worker -l info -Q arltask -n arltask -c 10 -O fair -f
arl_worker.log

[Install]
WantedBy=multi-user.target
```

其中 `-c 10` 参数决定任务并发的进程

再重启服务

```
systemctl restart arl-worker.service
```

## 灯塔截图失败问题

灯塔进行扫描首页截图时，显示截图失败

增加 `/usr/bin/phantomjs` 和 `ARL/app/tools/phantomjs` 文件的执行权限

```
chmod +x /usr/bin/phantomjs
```
