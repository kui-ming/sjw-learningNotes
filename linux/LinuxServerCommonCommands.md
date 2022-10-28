# Linux服务器常用命令



##### ln (软链接)

**命令：**

```sh
ln -s 链接的地址 新的链接名
```

> 不加-s的链接是硬链接，直接拷贝源文件到新的地址，占用空间。加-s是软链接，只拷贝镜像，不占用空间。链接都是同步的，源文件改变，链接也会改变。

**举例**

为/usr/bin/python3创建软链接/usr/bin/python

```sh
ln -s /usr/bin/python3 /usr/bin/python
```



##### service(查看服务)

**命令：**

```sh
service 服务名 status	# 查看服务状态
service --status-all  # 查看所有服务
```



##### service(开启服务与关闭服务)

**命令：**

```sh
service 服务名 start   # 启动服务
service 服务名 restart	# 重启服务

```



#### 防火墙端口管理

> 要暴露端口，在云厂商的安全组里添加还不够，还得在Linux服务器上的firewall中放行，以下命令仅适用于CentOS7以上



##### 防火墙设置

```sh
systemctl status firewalld	# 查看防火墙状态
```

![image-20221027213118812](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210272131093.png)

```sh
systemctl start firewalld # 开启防火墙
systemctl stop firewalld  # 关闭防火墙 
service firewalld start   # 重启防火墙
# 若遇到无法开启 先用：
systemctl unmask firewalld.service 
# 然后：
systemctl start firewalld.service
```



##### 放开端口

```sh
firewall-cmd --add-port=3306/tcp --permanent	# 开放3306端口
```

![image-20221027213648145](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210272136067.png)



##### 重载防火墙

```sh
firewall-cmd --reload	# 重新加载防火墙，使得添加的端口生效
```

![image-20221027213907803](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210272139310.png)



##### 查询指定端口是否开放

```sh
firewall-cmd --query-port=3306/tcp	# 查询指定端口(这里是3306)是否开放
```

![image-20221027214029840](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210272140527.png)



##### 关闭指定端口

```sh
firewall-cmd --permanent --remove-port=3306/tcp	# 关闭3306端口
```

![image-20221027214525530](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210272145026.png)



##### 查询所有开放的端口

```sh
firewall-cmd --zone=public --list-ports	#查询所有开放的端口
```

![image-20221027214648077](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210272146723.png)
