# 【Hadoop 2.7.1】HDFS Shell操作的简单试验

[TOC]

#### HDFS Shell命令的使用

```shell
hadoop fs <args>
hadoop dfs <args>
hdfs dfs <args>
```

如果使用的Hadoop3版本，需要使用

```shell
hdfs dfs
```



#### 上传文件(put)

用法：

```shell
#上传JDK文件到HDFS的根路径
hadoop fs -put ./jdk-8u341-linux-x64.tar.gz /
# 也可以一次上传多个文件
hadoop fs -put file1 file2 …… /
# hadoop3必须用此命令上传
hdfs dfs -put
```

![image-20221130152746101](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041764.png)

在HDFS的WEB管理系统中点击`Utilities -> Browse the file system`可以看到刚刚上传的文件

![image-20221130152854094](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041766.png)

#### 查看文件列表(ls)

```shell
# 查看上传到HDFS根路径中的文件
hadoop fs -ls / 
# 递归查询根目录及根目录下所有目录下的文件
hadoop fs -R /
# 另外一种命令形式
hdfs dfs -ls /
# 通过设置的NameNode名称也可以查看根路径的文件
hadoop fs -ls hdfs://192.168.0.109:9000/ 
```

![image-20221130153930676](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041767.png)



#### 统计文件大小(du)

```shell
# 统计根目录下所有文件和目录的大小
hadoop fs -du /
# 统计多个文件的大小
hadoop fs -du /a /b /c /test /app/hadoop-2.7.1.tar.gz
# 也可以统计单各文件的大小
hadoop fs -du /app/hadoop-2.7.1.tar.gz
```

![image-20221205221450180](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212052215924.png)



#### 创建文件夹(mkdir)

```shell
# 创建文件夹 test
hadoop fs -mkdir /test
```

![image-20221130162351135](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041768.png)



#### 移动文件(mv)

```shell
# 移动jdk文件到test文件夹中
hadoop fs -mv /jdk-8u341-linux-x64.tar.gz /test
```

![image-20221130162554313](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041769.png)



#### 递归创建文件夹(mkdir -p)

```shell
# 递归创建temp目录和temp目录下的temp1目录
hadoop fs -mkdir -p /temp/temp1
```

![image-20221130182510347](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041770.png)



#### 创建新文件(touchz)

```shell
# 在/temp路径下创建新的空文件文件 `C`
hadoop fs -touchz /temp/c
```

![image-20221204154427613](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041771.png)



#### 复制文件到指定目录(cp)

```shell
# 复制/temp/c文件到/app目录下
hadoop fs -cp /temp/c /app
```

![image-20221204163302356](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041772.png)



#### 复制文件到本地(get)

```shell
#移动/app目录下的新文件`c`到本地的用户根目录
hadoop fs -get /app/c ~
```

![image-20221204164032852](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041773.png)



#### 递归删除目录(rm)

```shell
# 递归删除temp目录，rm删除 -R递归
hadoop fs -rm -R /temp
```

![image-20221204165440588](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041774.png)



#### 查看文件内容(cat)

```shell
# 查看a b c三个文件的内容
hadoop fs -cat /a /b /c
```

![image-20221204170224370](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041775.png)



#### 将文件以文本形式输出(text)

```shell
#使用方法：hadoop fs -text <src>
#允许的格式是zip和TextRecordInputStream
hadoop fs -text /a
```

![image-20221205221856064](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212052218123.png)



#### 合并HDFS多个文件并传至本地（ getmerge）

```shell
# 合并a b c三个文件，并上传到本地当前目录，本地新文件命名为testfile
hadoop fs -getmerge /a /b /c testfile 
```

![image-20221204171104619](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212042041776.png)