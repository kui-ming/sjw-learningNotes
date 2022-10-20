# MyBatis-Plus使用联合组件时报错：@TableId can't more than one in Class

> 当我的数据库表中存在联合组件时，我依旧选择使用MyBatisPlus作用链接中间件，并使用MyBatisXGenerator自动生成了相关层的代码，可当我运行时就出现了错误，因为MyBatisPlus是不支持双组件的，我发现它在实体类中给我自动生成了两个@TableId注解

#### 错误信息：

```
……
Caused by: com.baomidou.mybatisplus.core.exceptions.MybatisPlusException: @TableId can't more than one in Class: "com.example.csust_hot_wall.entity.Follow".
	at com.baomidou.mybatisplus.core.toolkit.ExceptionUtils.mpe(ExceptionUtils.java:49) ~[mybatis-plus-core-3.4.2.jar:3.4.2]
	at com.baomidou.mybatisplus.core.metadata.TableInfoHelper.initTableFields(TableInfoHelper.java:297) ~[mybatis-plus-core-3.4.2.jar:3.4.2]
	at com.baomidou.mybatisplus.core.metadata.TableInfoHelper.initTableInfo(TableInfoHelper.java:168) ~[mybatis-plus-core-3.4.2.jar:3.4.2]
	at com.baomidou.mybatisplus.core.metadata.TableInfoHelper.initTableInfo(TableInfoHelper.java:144) ~[mybatis-plus-core-3.4.2.jar:3.4.2]
	at com.baomidou.mybatisplus.core.injector.AbstractSqlInjector.inspectInject(AbstractSqlInjector.java:53) ~[mybatis-plus-core-3.4.2.jar:3.4.2]
	at com.baomidou.mybatisplus.core.MybatisMapperAnnotationBuilder.parserInjector(MybatisMapperAnnotationBuilder.java:133) ~[mybatis-plus-core-3.4.2.jar:3.4.2]
	at com.baomidou.mybatisplus.core.MybatisMapperAnnotationBuilder.parse(MybatisMapperAnnotationBuilder.java:123) ~[mybatis-plus-core-3.4.2.jar:3.4.2]
	at com.baomidou.mybatisplus.core.MybatisMapperRegistry.addMapper(MybatisMapperRegistry.java:83) ~[mybatis-plus-core-3.4.2.jar:3.4.2]
	at com.baomidou.mybatisplus.core.MybatisConfiguration.addMapper(MybatisConfiguration.java:152) ~[mybatis-plus-core-3.4.2.jar:3.4.2]
	at org.apache.ibatis.builder.xml.XMLMapperBuilder.bindMapperForNamespace(XMLMapperBuilder.java:432) ~[mybatis-3.5.6.jar:3.5.6]
	at org.apache.ibatis.builder.xml.XMLMapperBuilder.parse(XMLMapperBuilder.java:97) ~[mybatis-3.5.6.jar:3.5.6]
	at com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean.buildSqlSessionFactory(MybatisSqlSessionFactoryBean.java:593) ~[mybatis-plus-extension-3.4.2.jar:3.4.2]
	... 71 common frames omitted
```



### 解决办法：
​	使用MyBatisPlus-Plus，它可以和MyBatisPlus相互兼容，并且，它是支持联合主键的。

##### 第一步：引入依赖包

```xml
<dependency>
            <groupId>com.github.jeffreyning</groupId>
            <artifactId>mybatisplus-plus</artifactId>
            <version>1.7.0-RELEASE</version>
</dependency>
```



##### 第二步：修改实体类

​	将@TableId去掉，换成@TableField，并加上@MppMultiId注解。@TableField声明表字段名，@MappMultiId声明联合主键

```java
@TableName(value ="likes")
@Data
public class Likes implements Serializable {
    /**
     * 用户id
     */
    @MppMultiId
    @TableField("user_id")
    private Integer userId;

    /**
     * 文章id
     */
    @MppMultiId
    @TableField("article_id")
    private Integer articleId;

    /**
     * 时间
     */
    private Date creationTime;

    @TableField(exist = false)
    private static final long serialVersionUID = 1L;

}
```



##### 第三步：找到实体类对应Mapper层，继承MppBaseMapper

```java
public interface LikesMapper extends MppBaseMapper<Likes> {

}
```



##### 第四步：对应Service与ServiceImpl分别继承 IMppService与MppServiceImpl

Service

```java
public interface LikesService extends IMppService<Likes> {

}
```

ServiceImpl

```java
@Service
public class LikesServiceImpl extends MppServiceImpl<LikesMapper, Likes>
    implements LikesService{

}
```



#### 第五步：在启动类上添加注解@EnableMPP

​	之后可以使用`selectByMultiId`、`updateBatchByMultiId`、`deleteByMultiId`、`saveOrUpdateByMultiId`等方法操作此数据库表