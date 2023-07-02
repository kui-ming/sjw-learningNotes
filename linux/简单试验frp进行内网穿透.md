# 简单使用frp进行内网穿透

环境：

客户端：Windows 10

服务器：【轻量级云服务器】CentOS 8

frp: [v0.50.0](https://github.com/fatedier/frp/releases/tag/v0.50.0)

客户端下载：[frp_0.50.0_windows_amd64.zip](https://github.com/fatedier/frp/releases/download/v0.50.0/frp_0.50.0_windows_amd64.zip)

服务端下载：[frp_0.50.0_linux_amd64.tar.gz](https://github.com/fatedier/frp/releases/download/v0.50.0/frp_0.50.0_linux_amd64.tar.gz)



## [frp介绍](https://gofrp.org/docs/overview/)

### frp 是什么？

frp 是一个专注于内网穿透的高性能的反向代理应用，支持 TCP、UDP、HTTP、HTTPS 等多种协议。可以将内网服务以安全、便捷的方式通过具有公网 IP 节点的中转暴露到公网。



### 为什么使用 frp？

通过在具有公网 IP 的节点上部署 frp 服务端，可以轻松地将内网服务穿透到公网，同时提供诸多专业的功能特性，这包括：

- 客户端服务端通信支持 TCP、KCP 以及 Websocket 等多种协议。
- 采用 TCP 连接流式复用，在单个连接间承载更多请求，节省连接建立时间。
- 代理组间的负载均衡。
- 端口复用，多个服务通过同一个服务端端口暴露。
- 多个原生支持的客户端插件（静态文件查看，HTTP、SOCK5 代理等），便于独立使用 frp 客户端完成某些工作。
- 高度扩展性的服务端插件系统，方便结合自身需求进行功能扩展。
- 服务端和客户端 UI 页面。



## frp使用



### 服务端部署与使用



#### 解压

```shell
tar -zxvf frp_0.50.0_linux_amd64.tar.gz 
```



#### 进入目录编辑`frps.ini`配置文件

```shell
cd frp_0.50.0_linux_amd64/
vim frps.ini
--------------
[common]
# frp监听端口
bind_port = 7000
# 面板端口
dashboard_port = 7001
# 登录面板账号设置，默默user:admin pwd:admin
dashboard_user = admin
dashboard_pwd = *******

# http服务映射端口
vhost_http_port = 7005

# 服务器域名，可不设置
subdomain_host = ****.xyz
# 令牌验证，可不设置
token = *******
```

![image-20230701160740979](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200212.png)



#### 防火墙放行7000-7005端口

```shell
firewall-cmd --zone=public --add-port=7000-7005/tcp --permanent
```



#### 启动frp服务

```shell
./frps -c frps.ini
```

![image-20230701162211727](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200214.png)

#### 访问frp面板

![image-20230701193117409](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200215.png)

![image-20230701162437465](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200216.png)

#### 使用systemd便捷启动frp服务

如果服务器中没有systemd可使用`yum`安装



##### 在systemd中创建并编辑frps.service文件

```shell
vim /etc/systemd/system/frps.service
```

##### 填写内容

```shell
[Unit]
#服务名称，可自定义
Description = frp server
# 在哪些服务之后启动
After = network.target syslog.target
# 启动该服务后还会启动什么服务
Wants = network.target

[Service]
# 启动类型设置为默认
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /usr/local/frp_0.50.0_linux_amd64/frps -c /usr/local/frp_0.50.0_linux_amd64/frps.ini

[Install]
# 所属服务组
# multi-user.target为默认服务组，会开机自启
WantedBy = multi-user.target
```

##### 运行启动

```shell
# 启动frps服务，这个frps执行的就是frps.service
systemctl start frps
#停止frp
systemctl stop frps
#重启frp
systemctl restart frps
```

##### 查询frp状态

```shell
systemctl status frps
```

![image-20230701163817704](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200217.png)



也可以不使用`systemd`启动，而是使用`nohup`指令后台运行



## 客户端部署与使用

#### 准备文件

![image-20230701183601815](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200218.png)



#### 编辑`frpc.ini`文件

```shell
[common]
# 服务器ip
server_addr = ***.***.**.**
# 与服务器的bind_port一致
server_port = 7000
# 与服务器一致的令牌
token = *********

# 代理名称可自定义，这里设置一个简单的文件访问代理
[parking_lot_static_file]
type = tcp
local_port = 22
remote_port = 7002
# 插件
# 静态文件浏览服务，通过暴露一个简单的 HTTP 服务查看指定的目录下的文件
plugin = static_file
# 映射的本地文件目录
plugin_local_path = D:\test\parking_lot\resources\user

# 访问URL时需添加的前缀路由
# 用户请求的 URL 路径会被映射到本地文件，如果希望去除用户访问文件的前缀，需要配置此参数
plugin_strip_prefix = static

[lan]
type = http
local_ip = 127.0.0.1
# 本地项目端口，访问服务器的vhost_http_port端口即可映射到此端口
local_port = 8081
# 配置自定义域名
custom_domains = ****.xyz

# 其实不一定要是http类型，用tcp类型映射本地端口，然后通过tomcat或其他web应用监听那个端口也能实现外网访问
[web]
type = tcp
local_port = 8080
remote_port = 7003
```



#### 启动服务

打开cmd，进入frp当前目录,输入`frpc -c frpc.ini`

![image-20230701184358836](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200219.png)



#### 通过frp面板查看对应代理

![image-20230701184457906](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200220.png)

![image-20230701184513415](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200221.png)

#### 访问7002端口

http://服务器ip地址:7002/static/，这个页面是通过`static_file`自动生成的。

![image-20230701184743267](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200222.png)

![image-20230701184806341](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200223.png)

本地文件

![image-20230701220648862](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012206191.png)



#### 访问7003端口

7003代理的本地端口8080必须要存着web应用监听，这里我使用Python的`flask`框架来实现一个简单的web应用，并监听本地的8080端口。

![image-20230701185124305](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200224.png)

随后再通过http://服务器ip地址:7003/访问

![image-20230701185237673](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200225.png)

#### 访问7005端口

首先我将[lan]监听的本地地址改成了8080，[web]的本地地址改成了8081，然后重启客户端的服务，这时我在上一步运行的python程序还启动着。

![image-20230701185425707](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200226.png)

随后再通过http://设置的域名(已解析的):7005/访问

![image-20230701192917616](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200227.png)

如果使用的是http://服务器IP:7005/

![image-20230701193023951](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202307012200228.png)
