# Nginx配置反向代理的路径问题

>新搞了台服务器，开始部署下项目，但是使用域名加端口访问很不给力，决定使用Nginx的反向代理，统一到443端口。但我对Nginx的的了解可以说是完全不了解，那只能通过百度来看看能不能解决问题了



#### 一张网图解决我的配置难题

之前找的网图，我的反向代理基本靠它解决

![20200220222941128](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210282116132.png)



#### 自己的理解

- 在`http`下，可配置多个`server`虚拟主机
- 每个`server`中，可配置多个`location`地址	

##### http块

> Nginx配置中最重要的部分，缓存和日志、反向代理、动态和静态资源分离、负载均衡都在这里配置。	



##### server块

> 一个server相当于一台虚拟的主机，通过监听客户端传来的端口和地址，然后将这条请求定向到自己location中指定地址



##### location块

>server监听到请求后，对server_name后的字符路径进行匹配，如果匹配成功，就可执行重定向、数据缓存和应答控制的等功能



##### 静态资源配置

> 假设请求路径为 https://test.xyz/static/test.jpg

​	**root：会保留匹配的路由路径**

```nginx
location /static { 
    # 设置静态资源路径(相对路径),查询的路径为/{nginx路径}/html/static/test.jpg
	root html/;
    # 设置静态资源路径(绝对路径),查询的路径为/var/www/wwwroot/html/static/test.jpg 
    # root /var/www/wwwroot/html/ 
}
```

​	**alias：会舍去匹配的路由路径**

```nginx
location /static {
    # 设置静态资源路径(相对路径),查询的路径为/{nginx路径}/html/test.jpg
	alias html/;
    # 设置静态资源路径(绝对路径),查询的路径为/var/www/wwwroot/html/test.jpg 
    # alias /var/www/wwwroot/html/ 
}
```



#### 实际配置

```nginx
server
    {
        listen 443 ssl;
    	# 自己的域名
        server_name test.xyz;
    	# 设置SSL证书
        ssl_certificate test.xyz_bundle.crt;
        ssl_certificate_key test.xyz.key;
        ssl_session_timeout 5m;
        #请按照以下协议配置
        ssl_protocols TLSv1.2 TLSv1.3; 
        #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE; 
        ssl_prefer_server_ciphers on;
    	# 如果请求中的路由为/hot_wall
        location /hot_wall{
          # 将/hot_wall路由后面的路径全部定向到8800端口上，并不包含/hot_wall
          proxy_pass http://127.0.0.1:8800/;
        }
    }
    server {
      listen 80;
      #自己的域名
      server_name test.xyz; 
      #把http的域名请求转成https
      return 301 https://$host$request_uri; 
  }
```

