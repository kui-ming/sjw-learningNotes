# SpringBoot多环境配置

#####  使用环境

 1. IntelliJ IDEA

 2. jdk 1.8

 3. 在IntelliJ IDEA中用Spring  Initializr方式新建的SpringBoot项目

 4. 配置在IDEA中的Maven3.52

###### 第一步，创建配置文件application.yaml 或 application.properties

- **项目目录**

![image-20220315104650603](https://cdn.staticaly.com/gh/kui-ming/tuchuang@main/images202209261136377.png)



- **application.properties内容：**

![image-20220315104939526](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261136378.png)



> 主配置文件时必须的，所有的通用配置项都配置在这里面
> 通过spring.profiles.active配置不同环境下的配置环境
>
> @profileActive@映射pom文件中的配置项




###### 第二步，配置多个附加的配置文件(application-自定义名称)，这些附加的配置文件配置不同环境下的配置

- **项目目录**

![image-20220315105242083](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261136380.png)

> 这里我配置了两种环境，包括dev(开发)环境和demo(演示)环境



- **application-demo.properties内容**

![image-20220315105440387](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261136381.png)



- **application-dev.properties内容**

  ![image-20220315105606516](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261136382.png)



###### 第三步，在pom文件中配置Profiles进行环境约束



- **pom截图**

![image-20220315110309099](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261136383.png)

> 在pom中配置两个环境的约束配置项，并将dev环境设置为默认环境



- **相关配置项代码**

```xml
<project ……>
	……
	<profiles>
        <!-- 开发环境 -->
        <profile>
            <id>dev</id>
            <properties>
                <profileActive>dev</profileActive>
            </properties>
            <!-- 设置此环境为默认环境 -->
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
        </profile>
        <!-- 演示环境 -->
        <profile>
            <id>demo</id>
            <properties>
                <profileActive>demo</profileActive>
            </properties>
        </profile>
    </profiles>
	……
</project>
```

> 这里面的<profileActive>标签中字符串就代表application文件中的@profileActive@

###### 第四步，在pom.xml中配置< resource >，使得项目找到指定资源

![image-20220523170853431](https://github.com/kui-ming/sjw-learningNotes/blob/main/SpringBoot%E5%A4%9A%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE.assets/image-20220523170853431.png?raw=true)

**相关配置项代码**

```xaml
<project>
    ……
	 <build>
        <plugins>
           ……
        </plugins>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>application-${profileActive}.properties</include>
                    <include>application.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
    ……
</project>
```





###### 第五步，此时就点开IDEA右侧的Maven选项卡，找到profiles选项，选择自己当前的环境了

![image-20220315111456635](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261136384.png)

> 直接通过点击复选框来选择自己当前的环境，如果没有自动修改，记得刷新一下



###### 第六步，打包

​	在Profiles选项中选择需要的环境，然后选择clean和install并点击运行

![image-20220315111940157](https://cdn.jsdelivr.net/gh/kui-ming/tuchuang/images202209261136385.png)
