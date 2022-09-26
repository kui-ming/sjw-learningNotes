# Linux进程管理

##### 前后台进程

> Linux进程有前台、后台之分
>
> **前台进程**控制键盘和显示器，在进程执行时，shell暂时挂起，进程结束后回到shell。
>
> **后台进程**一般运行时间较长，运行时不调用键盘和显示器，而是在系统后台默默运行(后台进程的优先级低于前台进程)。



启动进程

​	启动进程本质上就是运行程序，通过在shell中或桌面环境中运行某程序称之为启动进程。启动进程有两个主要途径：用户手动执行和系统调度。



`cd/etc` 切换目录不创建进程

![image-20220414143503494](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220414143503494.png)

`ls` 创建进程

![image-20220414143536414](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220414143536414.png)

`echo` 创建进程

![image-20220414143847516](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220414143847516.png)

启动前台进程

​	在终端中输入命令，例如`ls -l`

![image-20220414143136715](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220414143136715.png)

启动后台进程

​	在命令末尾加上一个 `&` 符号，键入命令后会返回一个进程编号。之后用户可以继续输入其他命令。注意需要用户交互的命令不要放在后台执行，因为后台进程无法获取到用户的输入。

​	启动方法

​		`命令&` ：命令后台运行，关闭终端后停止运行

​		`nohup 命令&` ：命令后台运行，关闭终端后任然运行

​	**举例**

  		1. 运行 `top&` 命令， 将性能监控工具放在后台运行

![image-20220414150145268](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220414150145268.png)

> 使用 `命令&` 方式运行在后台的命令在终端关闭后自动停止运行
>
> `top` 命令需要与用户进行交换，所以在后台会处于挂起状态。



2.  运行`nohup ls&` 

![image-20220414154840766](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220414154840766.png)

> 使用 `nohup` 指令挂入后台的进程不会因终端断开而结束



注意：在后台运行的进程如果有输出项，需要将输出结果重定向到文件中，否则该命令的输出结果还是会出现在终端中，干扰用户的正常工作。

举例 `ls&`

![image-20220414155419681](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220414155419681.png)



查看当前环境下的后台进程

​	命令：`jobs [ -l | -n | -p ] JobID`

`jobs`  命令用于显示当前 shell 环境中已启动的作业状态。如果 JobID 参数没有指定特定作业，就显示所有的活动的作业的状态信息。如果报告了一个作业的终止，shell 将从当前的 shell 环境的已知的列表中删除作业的进程标识。

​	举例：先使用 `top&` 开启一个后台进程，然后使用 `jobs` 查看

![image-20220414160349814](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220414160349814.png)