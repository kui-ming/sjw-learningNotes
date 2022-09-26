# 项目正常但打包后启动报错：ConfigServletWebServerApplicationContext……

报错信息：

```java
 ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'articleController': Unsatisfied dependency expressed through field 'articleService'; nested exception is org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'articleServiceImpl': Unsatisfied dependency expressed through field 'articleMapper'……
```

截图

![image-20220526125327478](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261316391.png)



#### 项目可正常启动

![image-20220526125407044](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261316393.png)



#### 原因

##### mysql驱动包丢失！

因为使用到了mybatis-generator自动生成器，在其中配置了指定版本的mysql驱动包![image-20220526125724107](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261316394.png)



在项目中配置的mysql也使用的同版本驱动包![image-20220526125809054](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261316395.png)



这样就使得他们使用的是同一个驱动包，但在打包时会自动剔除掉mybatis-generator插件及它使用的包，最后导致了mysql驱动包的丢失。![image-20220526130021370](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261316397.png)



#### 解决办法

将mysql依赖版本设置为`runtime`，会自动导入8.0的驱动。或将mysql-generator插件依赖的mysql包删除。

![image-20220526130237491](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261316398.png)



#### 结果

正常运行！

![image-20220526130448609](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261316399.png)