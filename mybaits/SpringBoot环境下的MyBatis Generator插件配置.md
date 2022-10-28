# SpringBoot环境下的MyBatis Generator插件配置



> 官方网站：[MyBatis Generator](http://mybatis.org/generator/index.html)



### 一、在Maven中添加MyBatis Generator Plugin



**简单配置**：

```xml
......
           <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.4.0</version>   
                <configuration>
                    <verbose>true</verbose>
                    <!-- 许覆盖生成的文件 -->
                    <overwrite>true</overwrite>
                </configuration>
            </plugin>
......
```

**进一步配置配置文件路径**：配置 MyBatis Generator config 文件路径

```xml
......
        <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.4.0</version>
                <configuration>
                    <!-- 输出详细信息 -->
                    <verbose>true</verbose>
                    <!-- 覆盖生成文件 -->
                    <overwrite>true</overwrite>
                    <!-- 声明配置文件的路径 -->
                    <configurationFile>src/main/resources/generatorConfiguration.xml</configurationFile>
                </configuration>
            </plugin>
......
```

> 注意：覆盖文件只会覆盖实体类和Mapper接口类，对于mapper.xml文件只会进行追加操作。主要是防止覆盖掉我们辛辛苦苦写的SQL语句	



**添加数据库依赖**

```xml
......
<plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.4.0</version>
                <configuration>
                    <!-- 输出详细信息 -->
                    <verbose>true</verbose>
                    <!-- 覆盖生成文件 -->
                    <overwrite>true</overwrite>
                    <!-- 定义配置文件 -->
                    <configurationFile>src/main/resources/generator-configuration.xml</configurationFile>
                </configuration>
                <dependencies>
                    <!-- mysql的JDBC驱动 -->
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>5.1.49</version>
                    </dependency>
                </dependencies>
            </plugin>
......
```

> Mybatis Generator 需要使用数据库，我们可以在配置文件中添加本地驱动路径，也可以在pom.xml中直接配置数据库的依赖。另外，我用的是mysql版本是5，所以驱动也是选的5，如果是8需要改成8的驱动



**在此插件中使用项目的依赖**

​	数据库驱动我曾在项目依赖中就配置了，现在在插件中再配置一次就略显冗余。这时可以使用Maven提供的**includeCompileDependencies** 属性，直接让插件使用dependencies的依赖。

```xml
......
		<plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.4.0</version>
                <configuration>
                    <!-- 输出详细信息 -->
                    <verbose>true</verbose>
                    <!-- 覆盖生成文件 -->
                    <overwrite>true</overwrite>
                    <!-- 定义配置文件 -->
                    <configurationFile>src/main/resources/generator-configuration.xml</configurationFile>
                    <!-- 将当前pom的依赖项添加到该插件的类路径中 -->
                    <includeCompileDependencies>true</includeCompileDependencies>
                </configuration>

            </plugin>
......
```



### 二、配置generatorConfiguration.xml文件

​	MyBatis Generator 插件启动后，会根据 pom 中配置的路径来查找配置文件。

><configurationFile>src/main/resources/generator-configuration.xml</configurationFile>

​	

此配置文件详细的声明了 MyBatis Generator 生成代码的各种细节，其中 `context` 标签必须存在，一个数据库一个`context`。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>

    <!-- 使用已有的配置文件的属性 -->
    <properties resource="application.properties"></properties>
    <!-- 配置数据库驱动的本地位置，如果在插件中声明了依赖则不需要配置 -->
    <classPathEntry location="E:\md\repository\mysql\mysql-connector-java\5.1.49\mysql-connector-java-5.1.49.jar"/>

    <!-- 一个数据库一个context, context子元素必须按照如下顺序
        property(0个或多个)、plugin(0个或多个)、commentGenerator(0个或1个)、jdbcConnection、javaTypeResolver(0个或1个)
        javaModelGenerator(至少1个)、sqlMapGenerator(0个或1个)、javaClientGenerator(0个或1个)、table(1个或多个)
    -->
    <!--
        id：随便填，保证多个contextId不重复
        targetRuntime：
            MyBatis3Simple：生成简单数据库操作接口方法，只包含增删改查方法
            MyBatis3：按不同规则生成不同的操作接口方法，方法数多
    -->
    <context id="DB2Tables" targetRuntime="MyBatis3">

        <!-- 该插件给生成的Java模型对象增加了equals和hashCode方法 -->
        <plugin type="org.mybatis.generator.plugins.EqualsHashCodePlugin"/>

        <!--配置生成类中的注释，默认生成包含时间戳的注释-->
        <commentGenerator>
            <!-- 不希望生成的注释中包含时间戳 -->
            <property name="suppressDate" value="true"/>
            <!-- 添加数据表中字段的注释 -->
            <property name="addRemarkComments" value="true"/>
            <!-- 是否不生成注释 -->
            <property name="suppressAllComments" value="false"/>
        </commentGenerator>

        <!-- 数据库连接，具体数据借用上方配置文件application.properties中的配置 -->
        <jdbcConnection driverClass="${spring.datasource.driver-class-name}"
                        connectionURL="${spring.datasource.url}"
                        userId="${spring.datasource.username}"
                        password="${spring.datasource.password}">
        </jdbcConnection>

        <!-- 非必须，类型处理器，在数据库类型和java类型之间的转换控制 -->
        <javaTypeResolver >
            <!-- 默认为false，把数据库的decimal和numeric类型解析为Integer -->
            <!-- 为true时把数据库的decimal和numeric类型解析为java.math.BigDecimal -->
            <property name="forceBigDecimals" value="false" />
            <!--默认为false，将数据库的所有时间类型解析为java.util.Date-->
            <!--
                为true时，按不同类型转换为不同java类
                date转为java.time.LocalDate
                time转为java.time.LocalTime
                timestamp转为java.time.LocalDateTime
                time_with_timezone转为java.time.OffsetTime
                timestamp_with_timezone转为java.time.OffsetDateTime
            -->
            <property name="useJSR310Types" value="false"/>
        </javaTypeResolver>

        <!-- 实体类生成的位置 -->
        <javaModelGenerator targetPackage="xyz.kuim.app_home.entity" targetProject="./src/main/java">
            <!--是否启动子包，开启将再生成一级以table的schema属性或数据库名命名的目录-->
            <property name="enableSubPackages" value="false" />
            <!--是否对字符串进行修剪，调用trim()方法移除字符串首尾空格-->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>

        <!-- Mapper映射文件XML的地址 -->
        <sqlMapGenerator targetPackage="mapper"  targetProject="./src/main/resources">
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>

        <!-- Mapper接口类生成的地址 -->
        <!--
            type="XMLMAPPER" 将SQL语句写在XML文件
            type="ANNOTATEDMAPPER" 将SQL语句写成注解放在接口中
        -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="xyz.kuim.app_home.mapper"  targetProject="./src/main/java">
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>

        <!-- 需要生成的数据库表，一个表一个配置项 -->
        <table schema="shop" tableName="user_info" domainObjectName="UserInfo" enableCountByExample="false" enableDeleteByExample="false"
               enableSelectByExample="false" enableUpdateByExample="false">
            <!-- 是否使用实际的列名作为属性名，默认为False-->
            <property name="useActualColumnNames" value="false"/>
        </table>

    </context>
</generatorConfiguration>

```



**properties**

配置后可以使用 `${ }` 表达式来获取其他配置文件中的属性

```xml
<!-- 使用已有的配置文件的属性 -->
    <properties resource="application.properties"></properties>
```



**context标签下的所有子标签**

​	1 property (0个或多个)
​	2 plugin (0个或多个)
​	3 commentGenerator (0个或1个)
​	4 jdbcConnection (需要connectionFactory 或 jdbcConnection)
​	5 javaTypeResolver (0个或1个)
​	6 javaModelGenerator (至少1个)
​	7 sqlMapGenerator (0个或1个)
​	8 javaClientGenerator(0个或1个)
​	9 table (1个或多个)

1. **property**

   编码类型进行设置

   ```xml
   <!--配置生成文件的编码为utf8-->
           <property name="javaFileEncoding" value="UTF-8"/>
   ```

   

2. **plugin** 

   可以添加多个插件

   ```xml
   <!-- 该插件给生成的Java模型对象增加了equals和hashCode方法 -->
           <plugin type="org.mybatis.generator.plugins.EqualsHashCodePlugin"/>
   ```

   

3. **commentGenerator** 

   配置注释

   ```xml
     <!--配置生成类中的注释，默认生成包含时间戳的注释-->
           <commentGenerator>
               <!-- 不希望生成的注释中包含时间戳 -->
               <property name="suppressDate" value="true"/>
               <!-- 添加数据表中字段的注释 -->
               <property name="addRemarkComments" value="true"/>
               <!-- 是否不生成注释 -->
               <property name="suppressAllComments" value="false"/>
           </commentGenerator>
   ```

   

4. **jdbcConnection** 

   数据库链接，可以写死。如果配置了`properties` 则可以读取其他配置文件中的属性

   ```xml
    <!-- 数据库连接，具体数据借用上方配置文件application.properties中的配置 -->
           <jdbcConnection driverClass="${spring.datasource.driver-class-name}"
                           connectionURL="${spring.datasource.url}"
                           userId="${spring.datasource.username}"
                           password="${spring.datasource.password}">
           </jdbcConnection>
   ```

   

5. **javaTypeResolver**

   配置数据表转为类时，字段类型与类属性的转换规则

   ```xml
   <!-- 非必须，类型处理器，在数据库类型和java类型之间的转换控制 -->
           <javaTypeResolver >
               <!-- 默认为false，把数据库的decimal和numeric类型解析为Integer -->
               <!-- 为true时把数据库的decimal和numeric类型解析为java.math.BigDecimal -->
               <property name="forceBigDecimals" value="false" />
               <!--默认为false，将数据库的所有时间类型解析为java.util.Date-->
               <!--
                   为true时，按不同类型转换为不同java类
                   date转为java.time.LocalDate
                   time转为java.time.LocalTime
                   timestamp转为java.time.LocalDateTime
                   time_with_timezone转为java.time.OffsetTime
                   timestamp_with_timezone转为java.time.OffsetDateTime
               -->
               <property name="useJSR310Types" value="false"/>
           </javaTypeResolver>
   ```

   

6. **javaModelGenerator**

   配置生成实体类

   ```xml
    <!-- 实体类生成的位置 -->
           <javaModelGenerator targetPackage="xyz.kuim.app_home.entity" targetProject="./src/main/java">
               <!--是否启动子包，开启将再生成一级以table的schema属性或数据库名命名的目录-->
               <property name="enableSubPackages" value="false" />
               <!--是否对字符串进行修剪，调用trim()方法移除字符串首尾空格-->
               <property name="trimStrings" value="true" />
           </javaModelGenerator>
   ```

   

7. **sqlMapGenerator** 

   配置映射文件

   ```xml
   <!-- Mapper映射文件XML的地址 -->
           <sqlMapGenerator targetPackage="mapper"  targetProject="./src/main/resources">
               <property name="enableSubPackages" value="false" />
           </sqlMapGenerator>
   ```

   

8. **javaClientGenerator**

   配置Mapper接口

   ```xml
   <!-- Mapper接口类生成的地址 -->
           <!--
               type="XMLMAPPER" 将SQL语句写在XML文件
               type="ANNOTATEDMAPPER" 将SQL语句写成注解放在接口中
           -->
           <javaClientGenerator type="XMLMAPPER" targetPackage="xyz.kuim.app_home.mapper"  targetProject="./src/main/java">
               <property name="enableSubPackages" value="false" />
           </javaClientGenerator>
   ```

   

9. **table** 

   配置要导入的数据表

   ```xml
    <!-- 需要生成的数据库表，一个表一个配置项 -->
           <!--
               schema：数据库名     tableName：表名        domainObjectName：生成的实体名，不指定默认和表名相同
               enableXXXByExample：生成一个对应的Example帮助类，只在MyBatis3生效
           -->
           <table schema="shop" tableName="user_info" domainObjectName="UserInfo" enableCountByExample="false" enableDeleteByExample="false"
                  enableSelectByExample="false" enableUpdateByExample="false">
               <!-- 是否使用实际的列名作为属性名，默认为False-->
               <property name="useActualColumnNames" value="false"/>
           </table>
   ```

   



### 三、运行MyBatis Generator

![image-20220422112323613](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202210250903286.png)
