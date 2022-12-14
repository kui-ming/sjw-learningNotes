# 表示设计模式层次结构的UML类图

> 统一建模语言（Unified Modeling Language，UML）是用来设计软件的可视化建模语言。它的特点是简单、统一、图形化、能表达软件设计中的动态与静态信息。 UML从目标系统的不同角度出发，可分为用例图、类图、对象图、状态图、活动图、时序图、协作图、构件图、部署图等 9 种图

#### 类图的概述

​		**类图是描述系统中的类，以及各个类之间的关系的静态视图**。能够让我们在正确编写代码以前对系统有一个全面的认识。类图是一种模型类型，确切地说，是一种**静态模型类型**。类图表示类、接口和它们之间的协作关系。



#### 类图的表示方法

​	1. 类图使用包含类名、属性和方法的且三者间存在分割线的矩形表示

​	2. 在属性和方法前通过不同符号来表示可见性，`+` 表示public、`-` 表示private、`#` 表示protected

 	3. 接口顶部有<< interface >>显示
 	4. 属性的完整表达方式为：**可见性符号 属性名 : 类型 [ = 默认值]**
 	5. 方法的完整表达方式为：**可见性符号 方法名(参数列表) [ : 返回类型]**

> 中括号`[]` 间的内容时可选的，也有将类型放在属性名前，返回类型放在方法名前的说法



![image-20221116202624351](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211162243746.png)



#### 类与类之间的六种表达关系

|    继承关系     |    实现关系     |                 关联关系                 |     聚合关系      |     组合关系      | 依赖关系 |
| :-------------: | :-------------: | :--------------------------------------: | :---------------: | :---------------: | :------: |
| 空心三角形+实线 | 空心三角形+虚线 | 实线箭头(双向关联可以没有箭头或两个箭头) | 空心菱形+实线箭头 | 实心菱形+实线箭头 | 虚线箭头 |

> 聚合是一种弱“拥有”关系，体现在对象A中可以包含有对象B，但对象B不是对象A的一部分，其可以单独存在。
>
> 合成是一种强“拥有”关系。体现了严格的整体(对象A)和部分(对象B)的关系，只有整体(对象A)存在时，部分(对象B)才存在。



##### 泛化(继承)关系

​		继承关系表示一般与特殊的关系，它指定了子类如何特性化父类的所有非私有的特征和行为。

​		例如：老虎和麻雀都继承了动物体重特性和移动行为。

​		画法：使用带空心三角形箭头的实线连接，三角形指向父类

![image-20221116204254404](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211162243748.png)

##### 实现关系

​		实现关系就是实现类与接口的关系，实现类将必须实现接口中定义的所有抽象方法。

​		画法：使用带空心三角形箭头的虚线连接，三角形指向父类

![image-20221116205614250](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211162243749.png)

##### 关联关系

​		关联关系指的是在A类中，可以知道B类的存在(可以在A类中调用B类的属性或方法)，那么A类就关联了B类，如果在B类中也知道A类的存在，那么他们就是**双向关联关系**，如果B类不知道A类的存在，那么他们就是**单向关联关系**。

​		例如：一个老师通过学生列表属性知道学生的存在，而学生通过班主任属性知道老师的存在，那么他们就存在**双向关联**的关系。学生通过课程列表知道课程类的存在，但课程只是一个抽象的类，并不需要关联学生，所以只有学生关联课程，属于**单向关联**。

​		画法：双向关联可以使用双箭头加实线指向两个关联类，也可以只用实线。单向关联使用实线连接，箭头指向被关联的类。

![image-20221116213533533](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211162243750.png)

​	

还存在一种自关联关系，例如节点中有next属性指向下一个节点，这就是节点关联了自己。

![image-20221116214835385](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211162243751.png)

##### 聚合关系

​		聚合关系是一种较强的关联关系，是一种整体和部分的关联关系，整体没有了部分无法正确运行，但部分却可以离开整体单独存在。

​		例如：学校没有老师则无法正常运行，老师离开学校依旧可以教书，这就是一个整体和部分的聚合关系，有部分聚合成为一个整体。

​		画法：使用带空心菱形箭头的实线连接，菱形指向整体，表示聚合为整体

![image-20221116220153682](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211162243752.png)

##### 组合关系

​		组合关系是比聚合关系更强的关联关系，也是整体与部分的关系。但不同与聚合的部分可以单独存在，组合关系中由整体调度多个部分，整体不需要时，部分也无法正常运行。

​		例如：公司由多个部门组成，多个部门需要通力合作维持公司的正常运转，当公司不存在时，部门也就没必要存在了。

​		画法：使用带实心菱形箭头的实线连接，菱形指向整体，表示组合为整体。

![image-20221116222011414](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211162243753.png)

##### 依赖关系

​		依赖是耦合性很弱的关联方式，是一种临时的关联使用关系。指的是在A类中的方法中，通过形参局部变量的方式使用B类的某些方法，形成一种临时的关联关系，当局部变量所属的方法结束后，这种关联关系就不存在了。

​		例如：人玩游戏，那就需要依赖电脑游戏类来玩游戏，于是在人的玩游戏方法中传入电脑游戏类的对象来产生依赖关系。但电脑游戏类与人的关联关系只存在于玩游戏这个方法中，当这个方法结束后二者的关联关系就不存在了。

​		画法：使用带箭头的虚线连接，箭头从使用类指向被使用的类。

​		

![image-20221116223443346](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211162243754.png)



#### 各种关系的强弱顺序

**泛化= 实现> 组合> 聚合> 关联> 依赖**

下面这张UML图，比较形象地展示了各种类图关系：

![0_1330497855hqk2](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211162340851.jpg)


这张图的原文链接：https://blog.csdn.net/tianhai110/article/details/6339565

