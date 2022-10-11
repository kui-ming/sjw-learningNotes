# IDEA+Tomcat——前端输入数据乱码问题

> 给别人远程部署项目的时候，发现比较老的项目会出现接收前端数据是乱码的问题，但这个项目在我自己的电脑上却是正常的，通过对比发现，IDEA版本或Tomcat版本不同及过低是造成此问题的主要原因，解决如下：



### 1. 在IDEA Settings中配置File Encodings

​		打开IDEA的settings框，找到Editor->File Encodings，然后修改成正确的编码，是UTF-8就UTF-8，是GBK就GBK。

![image-20221011171010005](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202210111710285.png)







### 2. 修改Tomcat配置

​		修改当前项目的tomcat Server虚拟机，在VM option中添加"-Dfile.encoding=指定编码"，修改完记得重启Tomcat。

![image-20221011171331683](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202210111713855.png)







### 3. 修改idea64.exe.vmoptions文件

​		点击Help->Edit Custom Vm Options打开文件后在末尾添加-Dfile.encoding=指定编码。

![image-20221011172354297](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202210111723920.png)







### 4. 在jsp文件头部进行编码设置

​	在jsp文件的头部加上指定编码，例如：`<%@ page language="java" import="java.util.*" contentType="text/html;charset=utf-8" pageEncoding="utf-8"%>`

![image-20221011172910626](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202210111729360.png)

 





### 5. 在设置拦截器中配置编码格式

​		在一个能够拦截全部路由的拦截器中配置Request和Response的编码格式及类型，这样前端传递的数据经过拦截器到后端就是正确的编码格式了。

![image-20221011173913166](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202210111739982.png)



##### 结尾

>以上五步依次尝试，我就是靠这五个方法解决的问题，如果还不行，那就再探寻新的方法吧！
