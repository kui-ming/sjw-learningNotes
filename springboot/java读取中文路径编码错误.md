##### 读取中文路径出现的编码错误

可能出现的问题

- 路径中存在空格，会被转为 `%20`
- 路径中存在中文，会被URI编码为 `/%e5%81%9c%e8%bd%a6%……`





代码如下：

```java
public class SupperWrapperFactory {

    static WrapperFactory create(Class<?> c){
        return new AccountWrapperFactory();
    }

    public static void main(String[] args) {
        String basePackage = SupperWrapperFactory.class.getPackage().getName();
        URL url = SupperWrapperFactory.class.getClassLoader().getResource(basePackage.replaceAll("\\.","/"));
        assert url != null;
        System.out.println(url.getFile());
        File file = new File(url.getFile());
        System.out.println(file.isDirectory());
        String[] list = file.list();
        System.out.println(list);
    }
}
```



输出结果：在 `/graduationproject` 路径后的中文路径没有正常显示

```java
/E:/Desktop/graduationproject/%e5%81%9c%e8%bd%a6%e5%9c%ba%e7%ae%a1%e7%90%86%e7%b3%bb%e7%bb%9f/parking_lot/target/classes/com/example/parkinglot/service/factory
false
null
```





##### 解决办法

- 使用 `replaceAll("%20"," ")` 解决空格的转码问题
- 使用 `URLDecoder.decode("待转换字符串","UTF-8")` 解决中文转码问题，但路径中存在  `+` 号会被转为空格
- 使用 `getResource("").toURI().getPath()` 完美解决问题



修改后的代码：

```java
public class SupperWrapperFactory {

    static WrapperFactory create(Class<?> c){
        return new AccountWrapperFactory();
    }

    public static void main(String[] args) throws URISyntaxException, MalformedURLException {
        String basePackage = SupperWrapperFactory.class.getPackage().getName();
        // 将URL改为URI
        URI uri = SupperWrapperFactory.class.getClassLoader().getResource(basePackage.replaceAll("\\.","/")).toURI();
        assert uri != null;
        String url = uri.getPath();
        System.out.println(url);
        File file = new File(url);
        System.out.println(file.isDirectory());
        String[] list = file.list();
        System.out.println(list);
    }
}

```



输出结果：

```java
/E:/Desktop/graduationproject/停车场管理系统/parking_lot/target/classes/com/example/parkinglot/service/factory
true
[Ljava.lang.String;@53d8d10a
```

