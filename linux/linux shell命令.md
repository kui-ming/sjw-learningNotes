

## 石景文

202108140133



### 一、Shell脚本基础

当Linux系统启动时，Shell便已经启动；在终端输入Linux命令并执行，便是Shell的一次工作过程



通过`cat /etc/shell`命令查看安装的shell

![image-20220316150705206](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220316150705206.png)



通过`/bin/bash --version`命令查看系统中安装的Shell版本号

![image-20220316152027260](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220316152027260.png)



通过查看`/etc/passwd`文件可以查看用户使用的shell类型

![image-20220316151047714](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220316151047714.png)



###### type

使用`type`指令来查看一个命令是否是内置命令：

这里的`cd`就是内置命令，`ifconfig`是外部指令，位置在`/usr/sbin/ficonfig`

![image-20220316151437484](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220316151437484.png)



通过`echo $PATH`来查看所有的环境变量

![image-20220316152248691](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220316152248691.png)



### 二、输入输出重定向

​		Shell默认将指令输出结果与错误打印到终端，但我们可以通过输出重定向修改输出位置

Linux系统中输入输出分为三类

- 标准输入(STDIN)：标准输入文件的编号为0，默认输入设备为键盘，命令在执行时从标准输入文件中读取数据（键盘）
- 标准输出(STDOUT)：标准输出文件的编号为1，默认输出设备为显示器，命令执行的结果会被发送到标准输出文件（屏幕）
- 标准错误(STDERR)：标准错误文件编号为2，默认设备是显示器，命令执行时产生的错误信息会被发送到标准错误文件（屏幕）

Linux允许对以上三种资源进行重定向，所谓重定向就是使默认输入、输出设备发生改变，不再是键盘、显示器来获取或接收数据



#### **1. 输入重定向(不常用)**

输入重定向运算符为	**`<`**	，可以看做是一个指向左边的箭头或一个小于号

作用：`<`可以指定它右侧的值为左值的输入源

​			`<<` 可以从标准输入文件中读取内容到左侧的命令，直到遇到右侧的分界符后结束

格式 ：

```shell
命令 < 文件名
命令 << 分界符
```

举例：

```shell
wall < file
```

`wall`为广播，当执行上述命令时，系统会将file文件中的内容作为命令`wall`的输入，广播给所有用户

- file文件

![image-20220316231753556](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220316231753556.png)

- 执行指令

  ![image-20220316231847214](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220316231847214.png)

> 由于大多数文件都是以参数的形式在命令行上指定输入文件的文件名，所以**输入重定向不经常使用**
>
> 例如：`cat < file` 和 `cat file` 是同样的效果



#### 2. 输出重定向

输出重定向运算符为	**`>`**	，可以看做是一个指向右边的箭头或一个大于号

输出重定向运算符还可以写成 **`1>`**，可以看做是标准输出文件的编号加上输出重定向符号

**`>>`** 两个向右的箭头表示将左侧的输出的结果**追加**到右侧的文件中，而不是覆盖，也可以写成  `1>>`

作用：`>` 可以指定它右侧的值为左值的输出源，左值产生的结果将发送到右侧的输出源

格式：

```shell
命令 > 文件名
```

举例：

```shell
ls > file
```

`ls`指令为查看当前路径下的文件和目录，此命令的输出的结果将从屏幕改为输出到file文件

![image-20220316232613181](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220316232613181.png)



#### 3. 错误重定向

错误重定向运算符为 **`2>`** ，可以看做是标准错误文件编号2与输出符相结合

**`2>>`** 同样表示将输出结果追加到文件

作用：`2>`可将左侧命令执行的错误信息输入到右侧的指定file文件(输出源)中

格式：

```shell
命令 2> 文件名
```

举例：尝试执行一个不存在的指令，并将它的错误信息保存在file文件中，同时尝试使用重定向的追加方式

```sh
aaa 2> file
bbb 2>> file
```

`aaa`指令并不存在，所以执行它一定会产生错误信息。本来错误信息应该输出在屏幕上的，但我们将错误信息重定向到`file`文件中

![image-20220317083701647](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220317083701647.png)



#### 4. 避免文件重写

​	Shell提供一种称为 `noclobber` 的功能，该功能可防止重定向时不经意的重写了已经存在的文件

```
set -o noclobber //开启
set +o noclobber //关闭
```

![image-20220317102214722](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220317102214722.png)



#### 5. 补充

- 标准编号和追加

​			错误重定向必须在箭头左侧添加标准错误文件的编号2，而输入输出重定向则可以**省略**其左侧的编号0和1

```sh
命令 0< 文件
命令 1> 文件
命令 2> 文件
```

​			错误重定向和输出重定向可以用两个箭头`>>`表示将输出结果追加到文件

```sh
命令 1>> 文件
命令 2>> 文件
```



- 运算符 `&`

​		`&` 代表 “等同于” 的意思，例如 `2>&1` 表示将标准错误重定向等同于标准输出，也就是将标准错误输出结果指向默认的标准输出文件，若此语句的标准输出文件已被修改，则输出到当前指定的标准输出文件，注意`&`两侧不要有空格

举例：将错误信息和标准输入信息一起输入到file文件中

```shell
aaa>file 2>&1 //将一个不存在的语句的输出结果输出到file文件，同时错误信息也输入file文件
bbb>>file 2>&1 //继续追加输入
```

![image-20220317105016601](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220317105016601.png)



​		**`&>`** 表示将左侧命令的输出结果和错误信息一起输入到右侧的输出源中

举例：

```sh
ccc &> file
ls &>> file
```

![image-20220317105311345](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220317105311345.png)





### 三、管道

​	管道的符号为 `|` ；管道可将多个简单的命令连接起来，使一个命令的输出作为另一个命令的输入使用。第一个命令的输入作为第二个命令的输入，第二个命令的输出作为第三个命令的输入，如此反复，形成管道流。

格式：

```sh
命令1 | 命令2 | 命令3 | …… | 命令n
```

举例：查找 `/etc` 目录下的所有文件，将这些文件作为第二条命令的输入源，让第二条语句从这些文件中找到包含 `init` 字符的项

```sh
ls -l /etc | grep init
```

![image-20220317110659657](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220317110659657.png)

若不使用管道，上述指令实现的效果需要两条命令来实现：

```sh
ls -l /etc > tmp.txt
grep init tmp.txt
```



### 四、命令连接符



1. `;` 连接符

​	使用 `;` 连接符间隔的命令会按照先后次序依次执行，假如一组命令需要同步执行，它们直接存在先后次序时可以使用 `;` 连接符

格式：

```sh
命令1;命令2;……;命令n
```

2. `&&` 连接符

​	使用 `&&` 连接的命令，其前后命令的执行遵循**逻辑与**的关系，只要该连接符前面的命令**执行成功**后才会接着执行后面的命令

3. `||` 连接符

   使用 `||` 连接的命令，其前后命令的执行遵循**逻辑或**的关系，只有该连接符前面的命令**执行失败**后才会执行后面的命令



### 五、特殊符号

 1. 通配符

    通配符用于模式匹配，如文件名匹配、路径名搜索、字符串查找等。常用的通配符有：

     - `*` 星号，代表任何字符串（包括0个）。例如：`"f*"`  匹配以f打头的任意字符串

     - `?` 问号，代表任意**单个**字符。例如：`"f?"` 匹配以f开头，后面跟随一个任意字符的长度为2的字符串

     - `[]` 中括号，代表指定的一个字符范围，只要文件名中 `[]` 位置处的字符在 `[]` 中指定的范围之内，则匹配此文件名

       

 2. 引号

	- 单引号 `'字符串'`
	
	  - 由单引号括起来的字符都作为普通字符出现，**特殊字符**用单引号括起来会**失去特殊意义**，只能当普通字符解析
	
	    举例：![image-20220318084052525](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318084052525.png)
	
	  - 双引号 `"字符串"`
	
	    由双引号包括的字符，除 `$` 、`\` 、`'`  和 `"` 这几个字符任作为特殊字符使用，其余字符作为普通字符对待
	
	    举例：![image-20220318084433289](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318084433289.png)
	
	  - 反引号 `` ` 
	
	    反引号(`)包括的字符被Shell解释为**命令**，在执行时，shell首先执行该命令，并以它的标准输出结果取代整个反引号部分
	
	    举例：![image-20220318085104550](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318085104550.png)
	
	
	
	3. 注释符 `#`
	
	   在shell编程中经常要对某些正文进行注释，以增加程序的可读性，在Shell中以 `#` 开头的**正文行**表示**注释行**



### 六、别名

​	不加参数的 `alias` 命令执行结果将显示当前系统中定义的所有命令别名，如 `ls`、`cp`等

​	举例：![image-20220318085514499](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318085514499.png)



### 七、命令历史



- Linux系统的bash提供了命令历史的功能，通过 `history` 命令可以对当前系统中执行过的所有shell命令进行显示

![image-20220318090121527](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318090121527.png)



- 若项重复执行命令历史中的指定命令，可使用 `!历史指令编号` 方式

![image-20220318090342475](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318090342475.png)



- 使用环境变量 `HISTSIZE` 来查看可以保存历史命令的总行数，当从shell中退出时，最近执行的命令将保存在HSITFIEL变量指定的文件中

![image-20220318091025912](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318091025912.png)



- 环境变量 `HISTFILE` 指定存放历史命令的文件名称
- ![image-20220318091050264](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318091050264.png)





## 八、shell脚本



#### 1. 什么是Shell脚本

 - Shell脚本是使用shell命令编写的文件，也称作 `shell script`
 - 与结构化程序不同，shell脚本**不需要进行编译**，也**不需要链接**成可执行的目标码。shell脚本是按行一条接一条地**解释执行**脚本中的命令



#### 2. shell脚本执行方式

	- 有三种方式可以执行一个bash脚本

​		先编写一个简单的脚本，名字叫`1+1` ：

​		![image-20220318092011335](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318092011335.png)



​		(1) 为脚本文件加上执行权限，然后在命令行直接输入shell脚本文件名执行

​				![image-20220318092317866](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318092317866.png)

​		(2) `sh shell脚本名` 执行

​				![image-20220318092453563](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318092453563.png)

​		(3) ` . shell脚本名` 执行

![image-20220318092519816](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318092519816.png)





###  九、shell变量

- 在shell脚本中也可以使用变量，一个变量就是内存中被命名的一块存储空间
- 一个Shell变量的名字可以包含数字、字母和下划线，变量名的开头只能是字母或下划线，变量区分大小写
- 变量名理论上是无限制的
- 在shell编程中可以使用四种变量：用户自定义变量、环境变量、位置变量和特殊变量



#### 1. 用户自定义变量

​	用户自定义变量也称为局部变量或本地变量。这些变量由用户自己定义，一般定义在脚本中，只在该脚本中生效。

- 用户自定义变量的格式

​		`Variable-name=value`

​		注意：如果字符串里包含空格，就必须用引号把他们括起来。还需要注意在等号两边不能有空格

​		获取变量内容：需要获取变量中的内容时，必须通过 `$变量名` 来获取

![image-20220318093926463](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318093926463.png)

- 清除变量

设置的变量不需要时可以清除，清除格式为：`unset variable-name`

​		![image-20220318101725208](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318101725208.png)



- 设置只读变量

  Shell中可以使用readonly将某个变量设置为只读变量，使用方式如下

  ```sh
  readonly 变量名=值
  ```

  还可以将一个已经存在的变量

  ```sh
  readonly 变量名
  ```

  举例：

  ![image-20220320173019403](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220320173019403.png)



#### 2.  环境变量

环境变量又称为永久变量，有效区域为创建该变量的Shell进程和从该Shell中派生的子Shell进程。为区别于局部变量，环境变量的字母全部**大写**。因系统启动时Shell自动登录，所以Shell中执行的用户进程均为子进程，环境变量可以用于所以用户进程。

   - Linux是一个多用户的操作系统，每个用户登录系统后，都会有一个专用的运行环境。
   - 通常每个用户默认的环境都是相同的，这个默认环境实际上就是一组环境变量的定义。
   - 用户可以在系统的配置文件中进行修改相应的系统环境变量。

1). 环境变量的定义

​	环境变量使用 `export` 设置或定义。若需将一个已存在的局部变量修改为环境变量可以使用如下格式：

```sh
export 局部变量名
```

​	**注意：**直接使用 `export` 命令在终端中创建的环境变量只在当前的Shell会话中的当前用户上生效，下次链接需要重新定义



​	定义一个环境变量的格式：

```sh
export 变量名=值
```

2). 环境变量的删除

​	可使用 `unset` 命令删除已经存在的环境变量

​	

3). 系统定义的常用环境变量

| 环境变量 | 含义说明                                 |
| -------- | ---------------------------------------- |
| HOME     | 当前用户的主目录，即用户登录时默认的目录 |
| PATH|以冒号分隔用来搜索命令的路径列表|
|PS1|命令提示符，root用户为 `#` ，bash中普通用户通常是 `$` 字符，在cshell中通常是 `%` |
|PS2|二级提示符，用来提示后续的输入，通常是 `>` 字符|
|IFS|输入域分隔符，用来分割单词的一组字符，通常是空格、制表符和换行符|
|TERM|用来设置用户的终端类型，系统主控制台不用设置|



​	4). 查看环境变量指令 `env`

![image-20220318103205374](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318103205374.png)



#### 3. 位置变量

​	位置变量指执行脚本时传入脚本中对应脚本位置的变量，相当于函数的参数。

- 执行脚本时，可以在脚本后面添加任意个命令参数，这些参数在脚本中通过 `$1` 至 `$9` 来获取，`$0` 始终指向脚本的名称

 - 当命令行上的命令参数超过9个时，shell提供了shift命令可以吧所有的参数变量左移一位（移除首位命令参数），使得第二叉树个命令参数变成$1，第十个参数变成$9

 - 使用格式：`shift [n]`

   ​	其中 `[n]` 表示向左移动参数的个数，默认值为1

- `$@`表示所有的的命令参数

​	举例：

​		shell脚本 `demo.sh`

![image-20220318105408442](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318105408442.png)

​	运行

![image-20220318105354852](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318105354852.png)

​	添加 `$@` 参数：输出所有的命令参数

![image-20220321141902036](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220321141902036.png)

![image-20220321142003028](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220321142003028.png)



#### 4.  特殊变量

​	特殊变量也是环境变量。shell中有一些变量的变量值由系统指定，被称之为特殊变量。

		- $# ：表示传递给脚本或函数的实际参数个数
		- $$ ：当前shell脚本的进程号
		- $* ：表示传递给脚本或函数的全部参数。各个参数之间用环境变量IFS中定义的字符分隔开
		- $@ ：也表示传递给脚本或函数的全部参数。它不使用IFS环境变量，所有当IFS为空时，参数值不会结合在一起
		- $? ：前一条命令的退出状态，如果执行成功退出显示0，其他值表示失败
		- $! ：运行脚本最后一条命令



通过 `$?` 来判断连接状态

![image-20220318110758605](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318110758605.png)

`$@` 和 `$*` 的区别

![image-20220318111230444](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220318111230444.png)



#### 5. 变量的引用

​	Shell中使用 `$` 符号来引用变量，若要输出上文定义的变量，可以使用如下方式

```sh
echo $变量名
```

​	`echo` 命令类似于其他语言(如C语言、Python)中的print()输出函数，用于打印变量或字符串。以上语句中输出的结果为变量中存储的值

- 单引号和双引号的区别

​		在定义时，使用双引号或单引号来标注变量都可以，但是在引用时，其实现效果略有差异。

​		1). 使用单引号

​			`变量名='值'`

​			使用单引号包括的的值将保持最初的字符串状态，字符串中的拥有特殊意义的符号不会被转义

​		2). 使用双引号

​			`变量名="值"`

​			使用双引号包括的值中如果存在特殊意义的符号，输出时将会输出此特殊符号的特殊意义

​		举例：

![image-20220320233119269](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220320233119269.png)

![image-20220320233101712](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220320233101712.png)

- Shell变量的常用引用

​		除了可以引用变量获取值，还可以获取变量的长度、子串等信息

| 引用格式                  | 返回值                                                       | 举例                                                         |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| $var                      | 返回变量值                                                   | var="shijingwen",$var即shijingwen                            |
| ${var}                    | 返回变量值                                                   | var="shijingwen",$var即shijingwen                            |
| ${#var}                   | 返回变量长度                                                 | var="shijingwen",${#var}即10                                 |
| ${var:start_index}        | 返回从start_index开始到字符串末尾的子串，字符串中的下标从0开始 | var="shijingwen",${var:3}即jingwen                           |
| ${var:start_index:length} | 返回从start_index开始的length个字符，若start_index为负数，表示从末尾往前数length个字符 | var="shijingwen",${var:3:6}即jingwe;${var:0-4:3}即gwe        |
| ${var#string}             | 从左边开始，删除string及string前面的字符，返回剩余字符       | var="shijingwen",${var#*i}即jingwen,\*表示通配符             |
| ${var##string}            | 从左边开始，删除string字符串，匹配最长的字段，返回剩余字符   | var="shijingwen",${var##*i}即ngwen                           |
| ${var:=newstring}         | 若var为空或未定义，则返回newstring,并把newstring赋给var      | var未定义,${var:=sjw}即sjw,var=sjw;var="sjw",${var:=shi}即sjw |
| ${var:-newstring}         | 若var为空或未定义，则返回newstring，不会赋值给var            | var未定义,${var:-sjw}即sjw,var还是未定义;var=sjw,${var:-shi}即sjw |
| ${var:+newstring}         | 若var为空或未定义，则返回空值，否则返回newstring             | var='',${var:+sjw}为空；var='shi',${var:+sjw}即sjw,var=shi   |
| ${var:?errstring}         | 若var为空或未定义，抛出错误信息errstring                     | var未定义,${var:?sjw}即抛出错误 -bash:var:sjw；var='shi',${var:?sjw}即shi |
| ${var/sub/new}            | 将var中第一个sub字符串替换为new字符串并返回新的var           | var='shijingwen',${var/jing/shi}即shishiwen,var值不变        |
| ${var//sub/new}           | 将var中所有sub字符串替换为new字符串并返回新的var             | var='shijingwen',${var//i/*}即sh\*j\*ngwen                   |



#### 6. 变量的输入

​	除了通过代码为变量赋值外，还可以使用 `read` 命令从标准输入文件(默认输入设备为键盘)中读取用户输入的信息

```sh
read 变量名
```

​	当执行此命令时，终端将等待用户输入信息，按回车键结束

​	举例：

​		创建shell脚本 `input.sh`

![image-20220321143252968](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220321143252968.png)

​	运行shell脚本 `sh input.sh`

![image-20220321143340421](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220321143340421.png)



#### 7. 变量的计算

​	Shell中变量没有明确的类型，值都以字符串形式存储。但Shell中也可进行一些算数运算。

​	1). `let` 命令计算

​		`let` 命令可进行算数运算和数值表达式测试。格式如下：

```sh
let 表达式
```

举例：

![image-20220321161517991](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220321161517991.png)

​	`let` 命令可以使用如下形式代替：

```sh
((算数表达式))
```

​	举例：

![image-20220321161939282](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220321161939282.png)



​	2). `expr` 命令计算

​		`expr` 命令用于对整型变量进行算数计算。使用expr命令可以使两个数值直接进行运算，例如：

![image-20220321162638040](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220321162638040.png)

​		**注意：**运算符与变量或数据之间需要加一个空格，否则内容会原样输出，不会进行计算

​		

​	使用 `expr` 命令对变量的引用进行运算，变量名需要添加 `$` 符，示例如下：

![image-20220321163342107](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220321163342107.png)

**注意：**若想要用一个变量接收另外两个变量的运算结果，需要使用 `` ` 号(一般是TAB键上面的那个键)来将运算等式包括起来



### 十、Shell中的条件语句

​		Shell中常用的条件语句有 `if` 、`select` 和 `case` 语句。

#### 1. 条件判断

​	条件判断是条件语句的核心。Shell中通常通常使用 `test` 命令或 `[` 命令对条件进行判断，判断内容可以是变量或文件。之所以脚本最好加上 `exit` 退出命令，本质上是可能需要条件判断来判断该脚本的退出状态。



- `test` **命令**，格式如下：

```sh
test 选项 参数
```

​	检测某个文件是否存在，可以用如下语句进行判断：

![image-20220322204620462](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220322204620462.png)

在此段代码中，条件语句会根据 `test` 命令的退出码来决定是否执行 `then` 之后的内容。如果 `then` 语句与判断语句处在同一行，则需要再在两者之间添加 `;` 英文分号。

- `[` **命令**

  `[` 命令与 `test` 命令功能相同，因此所有 `test` 命令能实现的功能都可以使用 `[` 命令来实现，格式如下：

  ```sh
  [ 选项 参数 ]
  ```

  使用 `[` 命令检测一个文件是否存在：

  ![image-20220322210554383](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220322210554383.png)

​		**注意：**`[` 命令也属于命令，命令与选项及参数之间应有空格。因此在 `[]` 符号与 `[]` 符号中的检查条件之间需要留出空格，否则将报错。

​		

- `;` **分号**

​		在使用 `[` 命令时，`then` 可以与 `if` 条件放在同一行，但使用这种格式时，需要使用分号 `;` ，将条件语句与 `then` 分隔开来。示例代码如下：

```sh
if [ -f file ] ; then
……
fi
```



#### 2. 判断语句

​		判断语句一般分为三类，分别为字符串比较、算数比较和文件测试判断。

- **字符串比较判断**

| 条件       | 说明                               |
| ---------- | ---------------------------------- |
| str1==str2 | 若字符串str1等于str2，则结果为真   |
| str1!=str2 | 若字符串str1不等于str2，则结果为真 |
| -n str     | 若字符串str不为空，则结果为真      |
| -z str     | 若字符串str为空，则结果为真        |



 - **算数比较判断**

| 条件            | 说明                                                   |
| --------------- | ------------------------------------------------------ |
| expr1 -eq expr2 | 若表达式expr1与expr2的返回值相同，则结果为真           |
| expr1 -ne expr2 | 若表达式expr1与expr2的返回值不相同，则结果为真         |
| expr1 -gt expr2 | 若表达式expr1的返回值大于expr2的返回值，则结果为真     |
| expr1 -ge expr2 | 若表达式expr1的返回值大于等于expr2的返回值，则结果为真 |
| expr1 -lt expr2 | 若表达式expr1的返回值小于expr2的返回值，则结果为真     |
| expr1 -le expr2 | 若表达式expr1的返回值小于等于expr2的返回值，则结果为真 |
| !expr           | 若表达式expr的结果为假，则结果为真                     |



 - **文件测试判断**

| 条件    | 说明                             |
| ------- | -------------------------------- |
| -d file | 若文件file是目录，则结果为真     |
| -f file | 若文件file是普通文件，则结果为真 |
| -r file | 若文件file可读，则结果为真       |
| -w file | 若文件file可写，则结果为真       |
| -x file | 若文件file可以执行，则结果为真   |
| -s file | 若文件file大小不为0，则结果为真  |
| -a file | 若文件file存在，则结果为真       |



#### 3. if条件语句

​	`if` 条件语句分为：单分支 `if` 语句、双分支 `if` 语句和多分支 `if` 语句，结构大体与其他程序设计语句的条件语句相同。

-  **单分支if语句**

​		单分支语句是最简单的条件语句，它对某个条件判断语句的结果进行测试，根据测试的结果选择要执行的语句。格式如下：

```sh
if [ 条件判断语句 ] ; then
	……
fi
```

其中的关键字为：`if` 、`then` 和 `fi` ，`fi` 表示该语句到此结束。

​		举例：输入文件名，判断文件是否为目录，若是，则输出“[文件名]是个目录”

​					先创建demo.sh

![image-20220323185819982](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220323185819982.png)

​					执行

![image-20220323185937516](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220323185937516.png)



- **双分支 if 语句**

​		双分支 `if` 语句相当于C语言中的 `if……else……` 语句，格式如下：

```sh
if [ 条件判断语句 ] ; then
	……
else
	……
fi
```

其中关键字为 `if` 、`then`、`else` 和 `fi`

​		举例：修改demo.sh脚本，判断文件是否是目录，是则输出为是，不是则输出不是

​				修改demo.sh

![image-20220323190539152](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220323190539152.png)

​				执行demo.sh脚本

![image-20220323190709968](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220323190709968.png)



-  **多分支if语句**

​		多分支 `if` 语句中可以出现不止一个的条件判断，格式如下：

```sh
if [ 条件判断语句 ] ; then
	……
elif [ 条件判断语句 ] ; then
	……
else
	……
fi
```

其中关键字为 `if` 、`then`、`elif`、`else` 和 `fi`，其中 `elif` 相当于其他编程语言中的 `else if` 

​		举例：修改demo.sh脚本，判断文件是否是目录，是则输出目录中的所有文件，不是则判断此文件是否可运行，可以则输出是执行文件，不能则输出无法运行

​					修改demo.sh

![image-20220323191605682](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220323191605682.png)

​					运行demo.sh

![image-20220323192012423](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220323192012423.png)



#### 4. select语句

​	Shell中的 `select` 语句可以将选项列表作出类似目录的形式，以交互的形式选择列表中的数据，传入 `select` 语句中的主体部分加以执行。

​	`select` 语句格式：

```sh
select 变量 in 列表
do
	……
	[break]
done
```

其中关键字为 `select`、`break` 和 `done` 。`select` 语句实质上也是一个循环语句，若不添加 `break` 关键字，程序将无法跳出 `select` 结构。

​	举例：编写 `select-test.sh` 脚本，设置一个需要学习的语言列表，脚本根据用户的选择来输出对应内容。

​			`select-test.sh` 脚本

![image-20220324214136721](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220324214136721.png)

​			执行 `select-test` 脚本

![image-20220324214344128](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220324214344128.png)



#### 5. case语句

​	`case` 语句可以将一个变量的内容与多个选项进行匹配，若匹配成功，则执行该条件下对应的语句。类似于C语言或Java中的switch开关语句。

​	`case` 语句格式：

```sh
case var in
	选项1)……;;
	'选项2')……;;
	"选项2")……;;
	……
	*)……
esac
exit 0
```

`case` 语句中的关键字有 `case` 和 `esac` ，`esac` 表示 `case` 语句的结束

其中，选项表示匹配项，用于匹配 `var` 值。匹配项可以使用(单引号/双引号)引起，也可直接列出;

在选项后面需要添加一个 `)` 收括号，之后才是对应匹配条件下执行的内容，每个匹配条件需要以 `;;` 双分号结尾;

最后一个匹配项 `*` 类似于C语言中的 `default` ，是一个通配符，该匹配项的末尾无需匹配符。

​	

​	举例：创建一个 `case-demo.sh` 脚本，实现一个简单的四则运算，要求用户从键盘输入两个数据和一个运算符。脚本程序根据用户的输入，输出计算结果。

​		`case-demo` 脚本

![image-20220326150048250](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326150048250.png)

​	运行 `case-demo` 脚本

![image-20220326150612841](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326150612841.png)



`case` 语句的匹配条件可以有多个，每个匹配项的多个条件使用 `|` 符号连接。举例如下：

![image-20220326151138044](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326151138044.png)

![image-20220326151215790](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326151215790.png)



`case` 语句可以和 `select` 语句搭配使用

![image-20220326152503210](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326152503210.png)

![image-20220326152517095](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326152517095.png)



### 十一、Shell中的循环语句

​		Shell中的循环语句有 `for` 、`while` 和 `until` 语句。

#### 1. for循环

 	`for` 循环格式如下：

```sh
for 变量 in 变量列表
do
	……
done
```

其中变量为当前循环中使用的一个对象，用来接收变量列表中的元素；变量列表是整个循环要操作的对象的集合，可以是字符串集合或文件名、参数等，变量列表的值会被逐个赋给变量。

​	举例：创建 `for-demo.sh` 脚本，使用 `for` 循环输出月份列表中的12个月份。

​				`for-demo` 脚本

![image-20220326154133733](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326154133733.png)

​			运行 `for-demo` 脚本

![image-20220326154236314](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326154236314.png)

需要注意，变量列表中的每个变量都可以使用引号单独引起了，但是不能将整个列表置于一对引号中，因为使用一对引号一起来的值会被视为一个变量。



​	举例：使用通配符 `*` 输出当前目录下所有后缀为 `demo.sh` 的文件

​	![image-20220326162515767](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326162515767.png)

![image-20220326162530115](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326162530115.png)



#### 2. while循环

​		`while` 循环格式如下：

```sh
while [ 表达式 ]
do 
	……
done
```

在 `while` 循环中，表达式的值为假时停止循环，否则循环将一直进行。此处表达式的 `[]` 为 `[` 命令，不可省略。



举例：创建 `while-demo.sh` 脚本，计算整数1~100的和。

​			`while-demo` 脚本

![image-20220326163748190](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326163748190.png)

​			运行 `while-demo` 脚本

![image-20220326163809962](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326163809962.png)



#### 3. until循环

​	`until` 循环格式如下：

```sh
until [ 表达式 ]
do
	……
done
```

`until` 循环与 `while` 循环格式基本相同，不同的是，当 `until` 循环的条件为假时，才能继续执行循环中的命令。



举例：创建 `until-demo.sh` 脚本，使用 `until` 循环输出有限个数据。

​		`until-dmeo` 脚本

![image-20220326164641479](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326164641479.png)

​		执行 `until-demo` 脚本

![image-20220326164706373](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326164706373.png)





### 十二、Shell函数

​		Shell除了可以定义变量外，还可以定义函数。

#### 1. 函数定义

​	Shell中的函数相当于用户自定义的命令，函数名相当于命令名，代码段用来实现该函数的核心功能。Shell中函数的格式如下：

```sh
[function] 函数名 [()]{
	代码段……
	[return int]
}
```

​	定义函数可使用 `function` 关键字，也可以不使用；

​	函数名后的括号可省略，若省略，则函数名与 `{` 之间需要有空格；

​	Shell脚本中的函数不带任何参数；

​	函数可使用 `return` 返回一个值，也可以不返回，若 `return` 不设返回值，则该函数返回最后一条命令的执行结果；



#### 2. 函数的调用

​	Shell脚本是逐行执行的，其中函数需要先定义再使用。

​	举例：创建 `fun-demo.sh` 脚本，定义一个hello函数，并在该脚本中使用该函数。

​				`fun-demo` 脚本

![image-20220326170206183](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326170206183.png)

​				执行 `fun-demo` 脚本

![image-20220326170054673](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326170054673.png)



#### 3. 函数中的参数

​		函数和脚本一样，可以使用位置参数传递数据，其中 `$0` 代表函数名， `$n` 代表传入函数的第n个参数。

​		需要注意，函数中使用的位置变量并不会与脚本使用的位置变量冲突。函数中的位置变量在函数调用时传入，脚本中的位置变量在脚本执行时传入。



举例：创建 `fun-demo2.sh` 脚本，分别为脚本中的位置变量和函数中的位置变量传值。

​			`fun-demo2` 脚本

![image-20220326171321803](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326171321803.png)

​			执行 `fun-demo2` 脚本

![image-20220326171353479](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326171353479.png)



#### 4. 函数中的变量

​	直接通过 `变量名=值` 方式定义的变量为函数变量，想要在函数中定义局部变量需要使用 `local` 关键字。格式如下：

```sh
loacl 变量名=值
```

举例：创建 `fun-demo3.sh` 脚本，使用 `local` 在函数中定义一个局部变量。

​			`fun-demo3.sh` 脚本

![image-20220326172333817](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326172333817.png)

​			执行 `fun-demo3.sh` 脚本

![image-20220326172434526](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220326172434526.png)

