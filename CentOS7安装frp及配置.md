# CentOS7安装frp及配置

1.下载frp

下载地址：[https://github.com/fatedier/frp/releases](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Ffatedier%2Ffrp%2Freleases)

中文文档：[https://github.com/fatedier/frp/blob/master/doc/quick_start_zh.md](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Ffatedier%2Ffrp%2Fblob%2Fmaster%2Fdoc%2Fquick_start_zh.md)

2.解压

```bash
tar -zxvf frp_x.x.x_linux_amd64.tar.gz
cd frp_x.x.x_linux_amd64
```

文件说明

> `frps.ini`: 服务端配置文件
> `frps_full.ini`:服务端完整配置文件
> `frps`: 服务端可执行文件
> `frpc.ini`: 客户端配置文件
> `frpc_full.ini`:服务端完整配置文件
> `frpc`: 客户端可执行文件

3.修改服务端配置
```bash
vi frps.ini
```
采用最简配置即可
```bash
[common]
bind_port = 7000
vhost_http_port = 80
```

4.开启服务端
```bash
./frps -c frps.ini
```

5.将服务端设为开机启动
```bash
#创建frps.service文件
vim /usr/lib/systemd/system/frps.service
```
填写如下内容：
```bash
[Unit]
Description=frps
After=network.target

[Service]
TimeoutStartSec=30
ExecStart=/usr/local/bin/frps -c /etc/frp/frps.ini
ExecStop=/bin/kill $MAINPID

[Install]
WantedBy=multi-user.target
```
保存退出，启动frp并设置为开机启动
```bash
systemctl enable frps
systemctl start frps
systemctl status frps
```

6.配置客户端

```bash
vi frpc.ini
```
修改为如下内容
```bash
[common]
server_addr = x.x.x.x  #更改为代理服务器的公网地址
server_port = 7000     #更改为服务端配置文件中的端口 默认为7000 此为frp通信端口

[web]				   #新建一个端口映射命名为[web]
type = http			   #转发类型：http,tcp,udp...根据具体需要转发的类型而定
local_port = 80		   #本地端口

[RDP]
type = tcp			
local_ip = 0.0.0.0	   #本地IP
local_port = 3389	   #本地端口
remote_port = 3389	   #远程端口
```

7.启动客户端即可
```bash
./frpc -c frpc.ini
```