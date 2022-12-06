# [Hadoop2.7.1】在云服务器上部署伪分布式集群

[TOC]

**我用到的资源**

- 华为云轻量服务器一台

  ![image-20221130123635014](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211301236853.png)

- CentOS8

- JDK1.8

- [Haddop-2.7.1.tar.gz](https://pan.baidu.com/s/1UwhNkyT0eKVSoXV_ivwpOA?pwd=22ef)

- SecureCRT（用来远程连接)



## 一、准备Hadoop压缩包并安装

#### 1、安装Hadoop

##### （1）准备好hadoop压缩包

![image-20221129193925158](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355602.png)



##### （2）安装hadoop

```shell
tar -zxvf hadoop-2.7.1.tar.gz -C /usr/local # 将hadoop安装到/usr/local目录下
```

![image-20221129194246510](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355604.png)



##### （3）查看是否安装成功

```shell
/usr/local/hadoop-2.7.1/bin/hadoop version # 因为没有设置软链接，所以只能通过bin来查看
```

```shell
Hadoop 2.7.1
Subversion https://git-wip-us.apache.org/repos/asf/hadoop.git -r 15ecc87ccf4a0228f35af08fc56de536e6ce657a
Compiled by jenkins on 2015-06-29T06:04Z
Compiled with protoc 2.5.0
From source with checksum fc0a1a23fc1868e4d5ee7fa2b28a58a
This command was run using /usr/local/hadoop-2.7.1/share/hadoop/common/hadoop-common-2.7.1.jar
```

![image-20221129200341812](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355605.png)



#### 2、将hadoop添加到环境变量

```shell
vim /etc/profile
```

##### （1）在文件末尾添加以下内容

```shell
export HADOOP_HOME=/usr/local/hadoop-2.7.1
# PATH在安装jdk时已经设置,这里需要添加上HADOOP的路径
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

![image-20221129210103494](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355606.png)



##### （2）保存文件，刷新配置

```shell
# 刷新配置文件
source /etc/profile
# 测试是否生效
hadoop version
```

![image-20221129210243717](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355607.png)



## 二、伪分布式配置文件设置

先进入haoop的配置文件目录

```shell
 cd /usr/local/hadoop-2.7.1/etc/hadoop/ #/usr/local/hadoop-2.7.1为我的Hadoop的安装路径
```

![image-20221129195039476](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355608.png)



#### 1、修改 `hadoop-env.sh`

修改文件中的`export JAVA_HOME=${JAVA_HOME}`，将JAVA_HOME设置为你JDK的路径

```shell
vim  hadoop-env.sh
```

```shell
export JAVA_HOME=/usr/local/java/jdk1.8.0_341
```

![image-20221129201246376](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355609.png)



#### 2、修改`core-site.xml`

```shell
vim core-site.xml
```

在文件末尾的`<configuration></configuration>`之间添加以下内容

**注意：这里的地址千万别用外网地址，因为云服务器中只有一块内网网卡，外网地址是服务商分配的**

```xml
<!--指定hadoop所使用的文件系统schema(URI),hdfs的老大(NameNode)的地址-->
<property>
<name>fs.defaultFS</name>
<value>hdfs://192.168.0.109:9000</value>
</property>
<!--指定hadoop运行时产生的文件的存储目录 -->
<property>
<name>hadoop.tmp.dir</name>
<value>/usr/local/hadoop-2.7.1/tmp</value>
</property>
```

![image-20221130114913980](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211301232225.png)



#### 3、修改`hdfs-site.xml`

```shell
vim hdfs-site.xml
```

在文件末尾的`<configuration></configuration>`之间添加以下内容

```xml
<!--指定hdfs副本的数量-->
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
```

![image-20221129204022498](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355612.png)



#### 4、拷贝`mapred-site.xml.template`文件内容并命名为`mapred-site.xml `

```shell
# 拷贝
cp mapred-site.xml.template mapred-site.xml
```

![image-20221129204611336](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355613.png)



#### 5、修改`mapred-site.xml`

```shell
vim mapred-site.xml
```

在文件末尾的`<configuration></configuration>`之间添加以下内容

```xml
<!-- 指定mr运行在yarn上 -->
<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>
```

![image-20221129205020300](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355614.png)



#### 6、修改 `yarn-site.xml`

```shell
vim yarn-site.xml
```

在文件末尾的`<configuration></configuration>`之间添加以下内容

```xml
<!--指定yarn的老大(ResouceManager)的地址	-->
<property>
<name>yarn.resourcemanager.hostname</name>
<value>192.168.0.109</value>
</property>
<!--指定reduce获取数据的方式是mapreduce_shuffle	-->
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
```

![image-20221130105418781](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211301232227.png)



## 三、启动Hadoop集群

#### 1、关闭防火墙

```shell
# Hadoop启动需要使用很多端口，如果不关闭防火墙会出现无法连接的问题
systemctl stop firewalld
```

**注意:需要root权限才能关闭**

![image-20221130113150541](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211301232228.png)



#### 2、格式化HDFS（namenode）第一次使用时要格式化

```shell
hadoop namenode -format
```

![image-20221129210901762](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355616.png)



#### 3、启动HDFS

```shell
# 启动hdfs
start-dfs.sh
#注意在启动过程要多次输入yes和root的密码
```

![image-20221130105703707](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211301232229.png)

```shell
# 查看当前进程
jps
```

![image-20221129225507696](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355618.png)



#### 4、启动YARN

```shell
# 启动YARN
start-yarn.sh
```

![image-20221129230433932](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355619.png)

```shell
# 查看当前进程
jps
```



![image-20221129230344998](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355620.png)

**当DFS和YARN的进程都启动时，应存在5个进程**

```shell
ResourceManager
SecondaryNameNode
DataNode
NodeManager
NameNode
```



#### 5、访问HDFS的WEB管理页面

启动Hadoop后，通过访问`50070`端口可以进入HDFS的管理页面

![image-20221130105752157](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211301232230.png)





#### 6、访问YARN的WEB管理页面

启动Hadoop后，通过访问`8088`端口可以进入YARN的管理页面

![image-20221130105905378](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211301232231.png)

#### 7、停止HDFS和YARN服务

![image-20221129235220382](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211292355623.png)



## 四、遇到问题



#### NameNode启动报错：Cannot assign requested address

当我启动HDFS服务后发现少了一个`NameNode`进程，于是查看日志文件发现报`BindException` ，通过百度发现，原来是因为我在`core-site.xml`文件中设置的`defaultFS`值是外网IP，而云服务器只有一块内网网卡，外网IP是设置在云服务提供商的公网网关的，通过NAT技术映射到内网网卡上，所以NameNode无法访问该地址。

![image-20221130104815103](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211301232232.png)

**解决办法：**

将`defaultFS`值设置为服务器的内网IP



#### 重新格式化HDFS

NameNode在第一次格式化失败后，需要删除格式化失败残留文件，才能重新进行格式化

##### （1）删除残留文件

```sh
rm -rf /usr/local/hadoop-2.7.1/tmp          # 删除hdfs缓存文件
rm -rf /usr/local/hadoop-2.7.1/dfs/name		# 删除NameNode缓存目录
rm -rf /usr/local/hadoop-2.7.1/dfs/data		# 删除DataNode婚车目录
rm -rf /usr/local/hadoop-2.7.1/logs			# 删除日志文件
```

##### （2）手动创建配置文件

```shell
mkdir -p /usr/local/hadoop-2.7.1/tmp          	# 创建hdfs缓存文件
mkdir -p /usr/local/hadoop-2.7.1/dfs/name		# 创建NameNode缓存目录
mkdir -p /usr/local/hadoop-2.7.1/dfs/data		# 创建DataNode婚车目录
mkdir -p /usr/local/hadoop-2.7.1/logs			# 创建日志目录
```

##### （3）重新格式化

```shell
hadoop namenode -format
```

