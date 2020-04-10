# frp 内网穿透


```
# 配置 ubuntu ssh 服务（默认关闭）
# ubuntu 下重新安装 openssh-server
sudo apt-get remove openssh-server openssh-client --purge && sudo apt-get autoremove && sudo apt-get autoclean && sudo apt-get update

# 重新安装 ssh-server
sudo apt-get install openssh-server openssh-client

# 启动 ssh 服务
sudo service ssh start

# 查看 ssh 是否启动，看到 Active: active(running) 即表示成功
sudo systemctl status ssh

# 开机自动启动配置
sudo systemctl enable ssh

# 测试 ssh 连接
ssh ahtcm@127.0.0.1

# 本地 scp 上传到远程服务器，在退出登录模式下运行
scp ./frp_0.21.0_linux_amd64.tar.gz root@47.110.144.17:/home/admin/


# 设置完毕后的登录方法，lan_user 是内网的用户名
ssh -p 6000 lan_user@pulic_ip 

```

[supervisor 进程守护配置请查看 supervisor 官方文档](http://www.supervisord.org/configuration.html)

```
# 安装 supervisor 开启启动插件
sudo apt install supervisor

# 新建配置文件
sudo vim /etc/supervisor/conf.d/frpc.conf

# 添加以下内容

[program:frpc]
command=/home/ahtcm/frp/frpc -c /home/ahtcm/frp/frpc.ini
autostart=true
autorestart=true
startsecs=1
startretries=100

stdout_logfile=/tmp/frpc-log.log
stdout_logfile_maxbytes=50MB
stdout_logfile_backups=0
stdout_capture_maxbytes=0
stdout_events_enabled=false

stderr_logfile=/tmp/frpc-err.log
stderr_logfile_maxbytes=50MB
stderr_logfile_backups=0
stderr_capture_maxbytes=0
stderr_events_enabled=false
```

```
# 重启 supervisor
sudo systemctl restart supervisor

# 查看 supervisor 运行状态，显示 RUNNING 则正常（不行的话关机重启再看看）
sudo supervisorctl status

# 查看日志
tail -f /tmp/frpc-log.log
```

**注意：关闭防火墙（默认关闭）， 还有阿里云服务器实例安全组添加安全组规则。**

### ssh 保持连接配置

```
sudo vim /etc/ssh/sshd_config

# G 到最后一行
# 添加以下内容
ClientAliveInterval 60
ClientAliveCountMax 10

# 重启 sshd 服务
service sshd reload
```