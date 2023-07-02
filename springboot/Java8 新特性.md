

#### Lambda表达式

lambda表达式可以用来**替换匿名内部类**，使代码更简洁易读。Lambda表达式允许传递一个代码块作为参数给方法或函数。



**Lambda语法：**

- **()->{}**：小括号中填形参(无参时需要保留小括号)，中括号中用来写代码。二者用两个符号(`-`加`>`)当做箭头连接，也叫箭头函数
- **x->{}**：只有一个形参时可以省略小括号
- **x->x*2**：只需要返回参数时可以省略中括号，`x * 2` 相当于 `return x * 2;`



```java
	interface MyInterface {
        int fun(int x);
    }
	
    interface MyInterface2 {
        int fun(int x, float y);
    }
	
	// 接收一个接口的实现类
    static int method(MyInterface2 myInterface2){
        return myInterface2.fun(2,4.5f);
    }

    public static void main(String[] args) {

        // 用lambda表达式替换掉匿名类部类
        MyInterface myInterface = (x)->{return x*x;};
        System.out.println(myInterface.fun(4)); // 16
        
        // 省略掉小括号与中括号
        myInterface = x -> x+x;
        System.out.println(myInterface.fun(4)); // 8

        // 将lambda表达式当做参数传递给方法
        int num = method((x, y) -> (int) (x*y));
        System.out.println(num); // 9
        
        // 在lambda表达式中进行复杂运算
        num = method((x,y)->{
            float temp = x * y; // 2 * 4.5 = 9
            temp = temp / x; // 9 / 2 = 4.5
            temp = temp + x; // 4.5 + 2 = 6.5
            temp = temp - y; // 6.5 - 4.5 = 2
            return (int) temp;
        });
        System.out.println(num); // 2

    }
```





#### 方法引用(Method Reference)

方法引用是一种特殊形式的Lambda表达式，它能够直接引用已有方法和构造方法。



方法引用的语法：

使用两个冒号 `::` 来进行方法引用，左边是类名或对象名，右边是方法名或`new`(构造方法)

- **对象名::实例方法名**：表示引用对象的实例方法
- **类名::静态方法名**：表示引用类的静态方法
- **类名::实例方法名**：表示引用类的任意对象的实例方法(函数式接口的第一个参数类型需与此类一致)
- **类名::new**：表示引用类的构造函数

```java

import java.util.Arrays;
import java.util.List;
import java.util.Random;
import java.util.function.Function;
import java.util.stream.Collectors;

public class MethodReferenceTest {

    interface MyInterface {
        void say(String msg);
    }

    interface PersonInterface {
        Person create(String name, Integer age);
    }

    static class Person {
        String name;
        Integer age;

        public Person(String name, Integer age) {
            this.name = name;
            this.age = age;
        }

        public Person(String name) {
            this.name = name;
            this.age = new Random().nextInt(24) + 16;
        }

        @Override
        public String toString() {
            return "Person{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    '}';
        }
    }

    static Person factory(PersonInterface personInterface){
        return personInterface.create("张三", 18);
    }

    public static void main(String[] args) {
        // 相当于特殊的Lambda表达式，方法体里只有一个输出语句，输出的值取形参的值
        // 返回值和形参长度及类型要与引用的方法一样，println是空返回值say方法必须也是空返回值
        MyInterface myInterface = System.out::println;
        myInterface.say("你好世界！" + 3.1415926);

        // 在工程方法中调用了接口，而接口方法只有一条语句就是创建一个新的Person对象
        // 接口方法的返回值和形参列表要与引用方法或构造方法一样
        Person p = factory(Person::new);
        System.out.println(p);
		
        //类名::实例方法名 函数式接口的第一个参数类型需与此类一致
        Function<Person,String> fun = Person::toString;

        List<String> names = Arrays.asList("李四", "王五", "赵六", "钱七");
        // 引用构造函数 Person(String name)
        List<Person> personList = names.stream().map(Person::new).collect(Collectors.toList());
        personList.forEach(x-> System.out.println(fun.apply(x)));
    }
}

```

输出：

```java
你好世界！3.1415926
Person{name='张三', age=18}
Person{name='李四', age=16}
Person{name='王五', age=38}
Person{name='赵六', age=29}
Person{name='钱七', age=31}
```





#### Stream(流)API



Stream流可以看做是对集合的一个高级抽象，它不是集合，而是由多个数据组成的数据流。Stream API提供了一种函数式、流式处理集合元素的方法，可以对集合进行过滤、映射、排序、聚合等操作，这些操作可以通过链式调用来实现非常灵活的数据处理流程。与集合类似，Stream可以包含任意对象，包括基本类型和对象类型。



##### 怎么创建Stream？

- 从集合中创建流 `集合对象.stream()`

- 从数组中创建流 `Arrays.stream(数组对象)`

- 直接创建流 `Stream.of("a","b","c")`

- 从文件中创建流 `Files.lines(Paths.get("文件路径"))`

  

**注意**：

- 为了避免基本数据频繁拆箱封箱，Stream额外提供了三种数据流类：`IntStream`、`LongStream`、`DoubleStream`。

- 普通的对象流Stream可以通过mapToInt()、mapToLong()、mapToDouble()方法转为基本数据流。

- 基本数据流可以通过boxed()方法转为对象流。

- 一般流不用关闭，但自定义流如文件流、网络流需要调用close()方法关闭底层资源，防止资源泄露。



```java
package jdk8新特性;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.Arrays;
import java.util.List;
import java.util.stream.IntStream;
import java.util.stream.Stream;

public class StreamTest {

    public static void main(String[] args) {
        // 从集合中获取流
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
        Stream<Integer> stream = list.stream();

        // 将数组转为流
        int[] arr = {6,7,8,9,10};
        // 为了避免基本数据频繁拆箱封箱，Stream额外提供了三种数据流类：IntStream、LongStream、DoubleStream
        // 普通的对象流Stream可以通过mapToInt()、mapToLong()、mapToDouble()方法转为基本数据流
        // 基本数据流可以通过boxed()方法转为对象流
        IntStream stream1 = Arrays.stream(arr);

        // 直接创建流
        Stream<String> stream2 = Stream.of("a", "b", "c");

        try {
            // 从文件中获取流
            Stream<String> stream3 = Files.lines(Paths.get("test.txt")); // 文件的一行为一个数据
            stream3.forEach(System.out::println); // 你好，世界！ \n hello,world!
            // 普通流操作完会自动关闭，但自定义流入文件流、网络流需要显性调用close()方法关闭底层资源
            stream3.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```



test.txt

```tex
你好，世界！
hello,world!
```



##### 通过函数创建流

除了上面几种方法创建流以外，Stream类还提供**`iterate()`**、**`generate()`**两个静态方法创建无限流。

```java
	@Test
    void iterateAndGenerate(){
        // iterate(send,UnaryOperator)接收两个参数，send为初始值，UnaryOperator为每次循环执行的函数
        // generate(supplier)接收一个参数，Supplier为供给型函数，没有接收值，需要返回一个值
        // iterate和generate都会创建一个无限流，必须使用limit方法加以限制

        // 循环5次，每次的值为x+1，x初始为0
        Stream.iterate(0,x->x+1).limit(5).forEach(System.out::print);

        System.out.println("\n--------");
        
        Random random = new Random();
        // 传入一个供给类函数，不接收值，有返回值
        Stream.generate(()->random.nextInt(10)+1).limit(5).forEach(x->{System.out.print(x + " ");});
        /*
        01234
        --------
        1 8 3 6 10 
         */
    }
```



##### 中间操作和终端操作的区别

流的操作分为两个部分：**中间操作**和**终端操作**

流的操作是不可逆的，到达终端操作时就不能再进行中间操作了。



中间操作：

创建流后可以进行零次或多次中间操作，其目的是对数据进行过滤、转换、映射等操作，每次操作后会返回一个新的流，随后可以方便的使用链式调用方法调用新的中间操作。

注意：这类操作是惰性的，也就是调用它们时并没有进行真正的数据遍历，真正的操作需要等待终端操作进行收尾时才执行。



终端操作：

一个流只能进行一次终端操作，当操作执行后，流会被永久的关闭，因此一个流只能被遍历一遍。

注意：只有在调用终端操作时，流才会真正的流动（被遍历）。



##### Stream API的使用



自己定义的、之后代码中会出现的打印方法

```java
/* StreamTest.class*/

static void print(Object msg){
    System.out.print(msg + " ");
}	
```



中间操作（常见的）：

- **filter()**：过滤`Stream`中不符合条件的元素。
- **distinct()**：去重，去掉`Stream`中重复的元素。
- **limit()**：截取`Stream`中的前 N 个元素。
- **skip()**：跳过`Stream`中的前 N 个元素。
- **map()**：对`Stream`中的每个元素进行转换。
- **flatMap()**：将`Stream`中的每个元素转换成一个新的`Stream`，然后将所有新的`Stream`合并成一个`Stream`。
- **sorted()**：对`Stream`中的元素进行排序。
- **parallel()**：用于将串行的`Stream`转换为并行的`Stream`。并行的`Stream`会自动将其内部的数据分成多个部分，并行处理，可以更快地完成处理任务。
- **peek()**：用来查看每个元素在中间阶段处理的结果，同时不改变流本身，主要用来调试和分析流处理的过程，同时也可以用来记录流中元素的操作结果。

```java
@Test
void operation(){
    // filter()：过滤 Stream 中不符合条件的元素，反正true的留下,返回false的过滤
    // 将第一个字符为A的元素存入新数组
    List<String> arrayList = Arrays.asList("A1","B2","A3","C4","A5","b6");
    arrayList = arrayList.stream().filter(s->s.startsWith("A")).collect(Collectors.toList());
    arrayList.forEach(StreamTest::print); // A1 A3 A5
    System.out.println("\n---------");



    // distinct()：去重，去掉 Stream 中重复的元素
    // limit()：截取 Stream 中的前 N 个元素
    // skip()：跳过 Stream 中的前 N 个元素
    int[] arr = {1,1,2,3,3,4,5,6,7,7,8,9};
    // 去掉数组中的重复数后，取出第4个到第6个元素
    // 先去重，此时流为{1,2,3,4,5,6,7,8}
    // 再跳过前3个元素，流为{4,5,6,7,8}
    // 再截取当前流的前三个元素{4,5,6}
    // 最后进行终端操作，转为int数组
    arr = Arrays.stream(arr).distinct().skip(3).limit(3).toArray();
    // 此时生成的流为IntStream流，进行手动封箱后成功Stream流，int变为Integer。其实可以自动封箱，我多此一举罢了
    Arrays.stream(arr).boxed().forEach(StreamTest::print); // 4 5 6
    System.out.println("\n---------");



    // map()：对 Stream 中的每个元素进行转换，循环每个元素，将当前元素替换为另外一个元素
    Stream<String> stream = Stream.of("a", "b", "c", "d");
    // 将每个元素都替换为Integer类型的1，随后转为IntStream流，每个1变为int型的2
    int[] arr2 = stream.map(x -> 1).mapToInt(x->2).toArray();
    Arrays.stream(arr2).forEach(StreamTest::print); // 2 2 2 2
    try {
        // 这个流已经被使用过一次，无法再使用了
        stream.forEach(StreamTest::print); //抛出：IllegalStateException 异常
    }catch (Exception e){
        System.out.println("\n不合法状态异常：流已经被操作或者关闭！");;
    }
    System.out.println("\n---------");



    // flatMap()：将 Stream 中的每个元素转换成一个新的 Stream，然后将所有新的 Stream 合并成一个 Stream
    // flatMap具有压平数组的功能
    // 如果流中的每个元素都是一个数组，那么flatMap将每个元素数组中的元素转为流然后合并成一个集合流
    List<String> list = Arrays.asList("hello world", "你好 世界");
    // 将两个短句分解成四个单词
    list.stream().map(s->s.split(" ")).flatMap(Arrays::stream).forEach(StreamTest::print); 
    // hello world 你好 世界
    System.out.println();
    int[][] arr3 = {{1,2,3},{4,5,6},{7,8,9}};
    // 将3x3的数组压平成为一个一维数组
    Arrays.stream(arr3).flatMapToInt(Arrays::stream).forEach(StreamTest::print); // 1 2 3 4 5 6 7 8 9



    // sorted()：对 Stream 中的元素进行排序
    Object[] arr4 =  Stream.of(5, 3, 1, 4, 2, 6).sorted().toArray();
    Arrays.stream(arr4).forEach(StreamTest::print); // 1 2 3 4 5 6
    System.out.println("\n---------");
    
    // parallel()：用于将串行的`Stream`转换为并行的`Stream`。并行的`Stream`会自动将其内部的数据分成多个部分，并行处理，可以更快地完成处理任务
    // peek()：用来查看每个元素在中间阶段处理的结果，同时不改变流本身，主要用来调试和分析流处理的过程，同时也可以用来记录流中元素的操作结果
    // peek中传入一个Consumer消费者函数
    Integer reduce = Stream.of(1, 2, 3, 4, 5).parallel()
        .peek(x -> {
            System.out.println("线程[" + Thread.currentThread().getName() + "]处理前的元素：" + x);
        })
        .map(x -> x * 10)
        .peek(x -> {
            System.out.println("线程[" + Thread.currentThread().getName() + "]处理后的元素：" + x);
        }).reduce(0, Integer::sum);
    System.out.println("求和："+ reduce);
    
}
```

输出结果

```java
A1 A3 A5 
---------
4 5 6 
---------
2 2 2 2 
不合法状态异常：流已经被操作或者关闭！

---------
hello world 你好 世界 
1 2 3 4 5 6 7 8 9 
---------
1 2 3 4 5 6 
---------
线程[main]处理前的元素：3
线程[main]处理后的元素：30
线程[main]处理前的元素：4
线程[ForkJoinPool.commonPool-worker-9]处理前的元素：2
线程[ForkJoinPool.commonPool-worker-9]处理后的元素：20
线程[main]处理后的元素：40
线程[ForkJoinPool.commonPool-worker-2]处理前的元素：5
线程[ForkJoinPool.commonPool-worker-2]处理后的元素：50
线程[ForkJoinPool.commonPool-worker-11]处理前的元素：1
线程[ForkJoinPool.commonPool-worker-11]处理后的元素：10
求和：150
```





终端操作（常见的）：

- **forEach()**：对`Stream`中的每个元素执行指定操作,无返回值， 在并行流中不能保证循环的顺序，普通流中可以保证。
- **forEachOrdered()**：和`forEach()`基本一致，但在并行流中能够保证循环顺序。
- **count()**：计算`Stream`中的元素个数。
- **collect()**：将`Stream`中的元素收集到一个集合中。
- **reduce()**：对`Stream`中的元素进行归约操作。
- **findFirst()**：查找`Stream`中的第一个元素。
- **findAny()**：查找`Stream`中的任意一个元素，在普通流下和`findFirst()`一致，在并行流下获得任意元素。
- **anyMatch()**：判断`Stream`中是否有任意一个元素符合指定条件。
- **allMatch()**：判断`Stream`中是否所有元素都符合指定条件。
- **noneMatch()**：判断`Stream`中是否所有元素都不符合指定条件。
- **min()**：查找`Stream`中的最小元素。
- **max()**：查找`Stream`中的最大元素。
- **toArray()**：用于将`Stream`中的元素转换为一个数组。它可以接受一个`IntFunction`接口类型的参数，用于创建数组对象。
- **iterator()**：不同于`iterate()`方法实现的无限流，`iterator`方法用于返回一个迭代器，可以用于遍历`Stream`中的元素。



**forEach()与forEachOrdered() 循环元素**

```java
// forEach()：对 Stream 中的每个元素执行指定操作,无返回值， 在并行流中不能保证循环的顺序。
Stream.of("A","B","C","D").forEach(x->{
    x = x.toLowerCase(); // 将每个元素转为小写
    System.out.print(x + " "); // a b c d
});

// 将普通流通过parallel()方法转为并行流后的forEach()循环
System.out.println();
Stream.of("A","B","C","D").parallel().forEach(x->{
    x = x.toLowerCase(); // 将每个元素转为小写
    System.out.print(x + " "); // c d a b
});

// forEachOrdered()：和forEach()基本一致，但在并行流中能够保证循环顺序。
System.out.println();
Stream.of("A","B","C","D").parallel().forEachOrdered(x->{
    x = x.toLowerCase(); // 将每个元素转为小写
    System.out.print(x + " "); // a b c d
});
/*输出：
a b c d
c d a b
a b c d 
*/
```



**count() 统计元素**

```java
// count()：计算 Stream 中的元素个数。
long count = IntStream.of(1,2,3,4,5).count();
System.out.println(count);
/*输出：
5
*/
```



**collect() 收集元素**

```java
// collect()：将 Stream 中的元素收集到一个集合中。
// collect()中需要传入 Collector接口的实现类，如果自定义的话要实现四个收集方法

// 这里使用JDK8已经定义好的Collectors工具类来完成收集

// joining()：将 Stream 中的字符串元素连接成一个字符串
String s = Stream.of("a", "b", "c").collect(Collectors.joining());
System.out.println(s);
s = Stream.of("a", "b", "c").collect(Collectors.joining(","));
System.out.println(s);

// toList()：将 Stream 中的元素收集到一个 List 容器中
List<String> list = Stream.of("a", "b", "c").collect(Collectors.toList());

// toMap()：将 Stream 中的元素收集到一个 Map 容器中，可以指定键值对的映射关系，接收两个函数,一记录key，一个记录value
Map<String, String> map = Stream.of("a", "b", "c").collect(Collectors.toMap(x -> x, x -> x));
System.out.println(map);

// toMap()：当键重复时，需要使用 BinaryOperator<U> mergeFunction 函数选出一个值，否则抛异常
Map<String, String> map1 = Stream.of("a", "b", "c", "b", "d").collect(Collectors.toMap(
    key -> key,
    value -> value,
    (o, n) -> o // 键冲突的情况下，直接返回原来值，舍弃新值，如b与b冲突，不控制会抛异常
));
System.out.println(map1);

// toSet()：将 Stream 中的元素收集到一个 Set 容器中
Set<Integer> set = Arrays.asList(1, 3, 5, 5, 2, 1, 1).stream().collect(Collectors.toSet());
System.out.println(set);

/*输出：
abc
a,b,c
{a=a, b=b, c=c}
{a=a, b=b, c=c, d=d}
[1, 2, 3, 5]
*/
```



**reduce() 合并元素**

```java
// reduce()：对 Stream 中的元素进行归约操作。
// 初始值设置为0，随后与每个元素值累加求和
Integer sum = Stream.of(1, 3, 5).reduce(0, Integer::sum);
System.out.println(sum);
/*输出：
9
*/
```



**findFirst()与findAny() 查找第一个元素和任意元素** 

```java
// findFirst()：查找 Stream 中的第一个元素。
// 查找的值装载在容器类Optional中
Optional<Integer> first = Stream.of(1, 3, 5, 7, 9, 11).parallel().findFirst();
System.out.println(first.get());

// findAny()：查找 Stream 中的任意一个元素。
// 因为流内部的优化，查找到第一个元素就会结束，所以该方法的结果和findFirst()的结果一致
// 其主要的差别在并行流中，findAny会得到一个元素后马上返回，findFirst会等待所有线程，然后返回流的第一个
Optional<Integer> any = Stream.of(1, 3, 5, 7, 9, 11).parallel().findAny();
System.out.println(any.get());
/*输出：
1
7
*/
```



**anyMatch()、allMatch()与noneMatch() 匹配元素**

anyMatch、allMatch和noneMatch方法分别用于判断Stream中是否存在元素满足某个条件、所有元素都满足某个条件、所有元素都不满足某个条件。它们接受一个Predicate接口类型的参数，用于描述条件。

```java
// anyMatch()：判断 Stream 中是否有任意一个元素符合指定条件。
// 流中如果有任意一个元素大于5则返回true，否则返回false
boolean match = Arrays.asList(1, 2, 3, 4, 5).stream().anyMatch(x -> x > 5);
System.out.println(match);

// allMatch()：判断 Stream 中是否所有元素都符合指定条件。
// 流中所有元素小于等于5返回true，否则返回false
match = Arrays.asList(1, 2, 3, 4, 5).stream().allMatch(x -> x <= 5);
System.out.println(match);

// noneMatch()：判断 Stream 中是否所有元素都不符合指定条件。
// 流中所有元素都不大于5则返回true，否则返回false
match = Arrays.asList(1, 2, 3, 4, 5).stream().noneMatch(x -> x > 5);
System.out.println(match);

/*输出：
false
true
true
*/
```



**min()与max() 比较元素**

```java
 List<Integer> asList = Arrays.asList(1, 2, 3, 4, 5);
// min()：查找 Stream 中的最小元素。
Optional<Integer> min = asList.stream().min(Integer::compareTo);
// 使用基本数据流可以省略比较方法
asList.stream().mapToInt(Integer::valueOf).min();
asList.stream().mapToLong(Long::valueOf).min();
asList.stream().mapToDouble(Double::valueOf).min();
System.out.println(min.get());

// max()：查找 Stream 中的最大元素。
// 通过比较方法找到最大或最小的数
Optional<Integer> max = asList.stream().max((a, b) -> a - b);
System.out.println(max.get());

/*输出：
1
5
*/
```



**toArray() 转为数组**

```java
// toArray()：用于将Stream中的元素转换为一个数组。它接受一个IntFunction接口类型的参数，用于创建数组对象。
// IntFunction： x 传入流的长度，return 一个大小为5的integer数组，用于存储流中的数据
Integer[] array = Stream.of(1, 2, 3, 4, 5).toArray(x->new Integer[5]);
Arrays.stream(array).forEach(StreamTest::print);
System.out.println();

// 数组列表转为数组
List<String> stringList = Arrays.asList("a", "b", "c");
// 不传递IntFunction 普通流默认返回对象数组，基本数据流返回基本数据类型的数组
Object[] objects = stringList.stream().toArray();
String[] strings = stringList.stream().toArray(String[]::new);
Arrays.stream(strings).forEach(StreamTest::print);
System.out.println();

// 基本数据流会直接返回基本数据类型的数组
int[] array1 = IntStream.of(1, 2, 3, 4).toArray();
Arrays.stream(array1).forEach(StreamTest::print);
System.out.println();

/*输出：
1 2 3 4 5 
a b c 
1 2 3 4 
*/
```



**iterator() 迭代器**

```java
// iterator()：不同于iterate()方法实现的无限流，iterator方法用于返回一个迭代器，可以用于遍历Stream中的元素
Iterator<Integer> iterator = Stream.of(1, 2, 3, 4, 5).iterator();
while (iterator.hasNext()) {
    System.out.print(iterator.next() + "-");
}

/*输出：
1-2-3-4-5-
*/
```



##### IntStream、LongStream和DoubleStream流

`IntStream`、`LongStream`以及`DoubleStream`是`Stream`接口的三个特化版本，它们分别对应处理`int`、`long`和`double`型的基本类型数据，它们比`Stream`多提供了一些特定于此类数据的方法，使得操作这些基本数据更加简单、快捷。



常用方法：

- **range()**：用于生成一段范围内的**整数**/**长整数**流(`DoubleStream`没有此方法)，**不包含**结束值。
- **rangeClosed()**：用于生成一段范围内的**整数**/**长整数**流(`DoubleStream`没有此方法)，**包含**结束值。
- **sum()**：用于对流元素进行求和。
- **min()**：用于取流元素之间的最小值。
- **max()**：用于取流元素之间的最大值。
- **average()**：用于取流元素之间的平均值。
- **mapToInt()**：将当前流的元素映射为`IntStream`流。
- **mapToLong()**：将当前流的元素映射为`LongStream`流。
- **mapToDouble()**：将当前流的元素映射为`DoubleStream`流。
- **mapToObj()**：将当前流的元素映射为`Stream`流。
- **boxed()**：`boxed`方法用于将`IntStream`、`LongStream`、`DoubleStream`中的元素装箱成对应的包装类型（`Integer`、`Long`、`Double`），返回一个`Stream`对象。



`IntStream`、`LongStream` 和 `DoubleStream` 除了继承 `Stream` 接口中的所有中间操作方法和终端操作方法外，同时提供了自己特定类型的操作方法，但这三个基本数据流的操作方法是类似的，所以直接一起介绍就可以了。



**range和rangeClosed**

```java
 // range(): 用于生成一段范围内的整数/长整数流(DoubleStream没有此方法)，不包含结束值。
IntStream.range(1,5).forEach(StreamTest::print);
System.out.println();
LongStream.range(10000,10005).forEach(StreamTest::print);
System.out.println();

// rangeClosed(): 用于生成一段范围内的整数/长整数流(DoubleStream没有此方法)，包含结束值。
IntStream.rangeClosed(1,5).forEach(StreamTest::print);
System.out.println();
LongStream.rangeClosed(10000,10005).forEach(StreamTest::print);
System.out.println();
```

输出

```java
1 2 3 4 
10000 10001 10002 10003 10004 
1 2 3 4 5 
10000 10001 10002 10003 10004 10005
```



**sum、min、max、average**

```java
// sum(): 用于对流元素进行求和
// 1 + 2 + 3 + 4
int sum = IntStream.range(1, 5).sum();
// 1.5 + 2.0 + 2.5 + 3.0 + 3.5
double sum1 = DoubleStream.iterate(1.5, x -> x + 0.5).limit(5).sum();
// 5 + 6 + 7 + 8 + 9 + 10
long sum2 = LongStream.rangeClosed(1, 5).sum();
System.out.println(sum + " : " + sum1 + " : " + sum2);

// min(): 用于取流元素之间的最小值
OptionalInt min = IntStream.range(1, 5).min();
OptionalLong min1 = LongStream.rangeClosed(1, 5).min();
OptionalDouble min2 = DoubleStream.iterate(1.5, x -> x + 0.5).limit(5).min();
System.out.println(min.getAsInt() + " : " + min1.getAsLong() + " : " + min2.getAsDouble());

// max(): 用于取流元素之间的最大值
OptionalInt max = IntStream.range(1, 5).max();
OptionalLong max1 = LongStream.rangeClosed(1, 5).max();
OptionalDouble max2 = DoubleStream.iterate(1.5, x -> x + 0.5).limit(5).max();
System.out.println(max.getAsInt() + " : " + max1.getAsLong() + " : " + max2.getAsDouble());

// average(): 用于取流元素之间的平均值
double avg = IntStream.of(1, 2, 3).average().getAsDouble();
double avg1 = LongStream.of(1, 2, 3).average().getAsDouble();
double avg2 = DoubleStream.of(1, 2, 3).average().getAsDouble();
System.out.println(avg + " : " + avg1 + " : " + avg2);
```

输出

```java
10 : 12.5 : 15
1 : 1 : 1.5
4 : 5 : 3.5
2.0 : 2.0 : 2.0
```



**mapToInt、mapToLong、mapToDouble、mapToObj**

```java
// mapToInt(): 将当前流的元素映射为IntStream流
// mapToLong(): 将当前流的元素映射为LongStream流
// mapToDouble(): 将当前流的元素映射为DoubleStream流
// mapToObject(): 将当前流的元素映射为Stream流

long[] array = IntStream.of(1, 2, 3).mapToLong(x -> x).toArray();
double[] array1 = LongStream.of(1, 2, 3).mapToDouble(x -> x).toArray();
// 向下转型需要强转
int[] array2 = DoubleStream.of(1.5, 2.5, 3.5).mapToInt(x -> (int) x).toArray();
List<Integer> list = Arrays.asList(1, 2, 3, 4);
// 映射时自动解包
int[] array3 = list.stream().mapToInt(x -> x).toArray();
// 将基本类型元素乘10后映射为Object类型，随后用Integer数组存储
Integer[] array4 = IntStream.of(1, 2, 3).mapToObj(x -> x * 10).toArray(x -> new Integer[x]);

Arrays.stream(array).forEach(StreamTest::print);
System.out.println();
Arrays.stream(array1).forEach(StreamTest::print);
System.out.println();
Arrays.stream(array2).forEach(StreamTest::print);
System.out.println();
Arrays.stream(array3).forEach(StreamTest::print);
System.out.println();
Arrays.stream(array4).forEach(StreamTest::print);
System.out.println();
```

输出

```java
1 2 3 
1.0 2.0 3.0 
1 2 3 
1 2 3 4 
10 20 30 
```



**boxed**

```java
// boxed(): boxed方法用于将IntStream、LongStream、DoubleStream中的元素装箱成对应的包装类型（Integer、Long、Double）
// 返回一个Stream对象
Stream<Integer> stream = IntStream.of(1, 2, 3).boxed();
```





#### 函数式接口

函数式接口也叫SAM(Single Abstract Method)接口，它**只包含一个抽象方法**，但可以拥有多个非抽象的方法。因为只有一个需要实现的抽象方法，所以可以直接使用Lambda表达式来实现。



##### @FunctionInterface

Java 8中定义了一个新的注解` @FunctionalInterface`，用于标记和检测函数式接口。该注解只能在定义函数式接口时使用，如果一个接口被 `@FunctionalInterface` 注解标记，但它不符合函数式接口的要求（即有多于一个抽象方法），则编译器会报错。不添加此注解的单个抽象方法的接口还是可以使用`Lambda`表达式实现的。



自定义函数式接口

```java
interface MyFunctionInterface<R,T> {
    /**
     * 不加@FunctionInterface接口也是可以的，但不能标记和检测
     *  自定义的函数式接口，接收一个T类型的参数，返回一个R类型的值
     */
    R apply(T t);
}

@FunctionalInterface
interface MyConsumer {
    /**
     * 接收一个String类型参数，供此函数接口消费
     * @param t
     */
    void accept(String t);

    default void consume(){
        accept("hello,world");
    }

}

public class FunctionInterfaceTest {

    /**
     * 传递一个字符串供接口消费
     * @param consumer
     */
    static void consumer(String t,MyConsumer consumer) {
        // 如果传递的字符串为null，则调用消费者接口的默认方法，消费“hello，world”
        if (t == null) consumer.consume();
        else consumer.accept(t);
    }

    public static void main(String[] args) {
        // 函数作用 将字符串转为整数
        MyFunctionInterface<Integer,String> fun = x->Integer.valueOf(x);
        System.out.println(fun.apply("11") + 1);

        // 消费方式，将字符串转为大写
        consumer("abc",x->{
            x = x.toUpperCase();
            System.out.println(x);
        });
        // 消费 消费者接口中的默认方法传递来的字符串“hello，world”
        consumer(null,x->{
            x = x.toUpperCase();
            System.out.println(x + "！");
        });

    }

}
```

输出结果

```java
12
ABC
HELLO,WORLD！
```





内置函数式接口

函数式接口是Lambda表达式和方法引用的基础，它们可以被用于在Java中实现函数式编程。Java 8中提供了很多内置的函数式接口，包括以下常用的函数式接口



Consumer ：消费型接口

```java
@FunctionalInterface
public interface Consumer<T> {
    
    void accept(T t); //接受一个输入参数并且不返回结果的操作
    
    // 默认方法，将当前消费者和另一个消费者组合起来，返回一个“先执行当前消费者，再执行另一个消费者”的新的消费者对象
    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after); // 为空会抛异常
        return (T t) -> { accept(t); after.accept(t); };
    }
}
```

Supplier ：供给型接口

```java
@FunctionalInterface
public interface Supplier<T> {
    T get(); //表示不接受任何输入参数，但返回一个结果（结果类型由泛型T决定）的操作
}
```



Function<T,R> ：函数型接口

```java
@FunctionalInterface
public interface Function<T, R> {
    
    R apply(T t); // 需实现的方法
    
    // 将当前函数和另一个函数组合起来，返回一个“先执行当前函数，再执行另一个函数”的新函数
    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }
    
    // 将当前函数和另一个函数组合起来，返回一个“先执行before函数，再执行当前函数”的新函数
    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }
}
```



Predicate ：断言型接口

```java
@FunctionalInterface
public interface Predicate<T> {
    
    boolean test(T t); // 需实现的方法
    
    // 将当前断言和另一个断言组合起来，返回一个新的断言，当当前断言和另一个断言的结果都为真时，新的断言返回真，否则返回假
    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }
    
    // 将当前断言和另一个断言组合起来，返回一个新的断言，当当前断言和另一个断言有一个结果为真时，新地断言返回真，否则返回假
    default Predicate<T> or(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) || other.test(t);
    }
    
    // 取反，如果当前断言结果为真，那么新的断言结果一定为假
    default Predicate<T> negate() {
        return (t) -> !test(t);
    }
}
```



##### Consumer ：消费型接口

`Consumer<T>` 表示接受一个输入参数（参数类型由泛型T决定），但不返回结果的操作。也就是他只消费(接收参数，执行方法体)，不生产(没有返回值)。

`andThen(Consumer<? super T> after)` ：将当前消费者和另一个消费者组合起来，返回一个“先执行当前消费者，再执行另一个消费者”的新的消费者对象。

```java
 @Test
void consumer() {
    // 消费方式 输出字符串
    Consumer<String> consumer = System.out::println;
    consumer.accept("你好，世界！");
    // 消费方式 输出字符串的长度
    consumer = s->{System.out.println("长度：" + s.length());};
    consumer.accept("你好，世界！");

    // 测试Consumer的默认方法
    // andThen(Consumer<? super T> after)
    // 将当前消费者和另一个消费者组合起来，返回一个“先执行当前消费者，再执行另一个消费者”的新的消费者对象
    consumer = s -> {System.out.print("(长度" + s.length() + ")：");};
    consumer =  consumer.andThen(s -> {
        System.out.println(s.toUpperCase()); // 转为大写
    });
    consumer.accept("hello,world!");
}
```

输出结果

```java
你好，世界！
长度：6
(长度12)：HELLO,WORLD!
```



##### Supplier ：供给型接口

`Supplier<T>` 表示不接受任何输入参数，但返回一个结果（结果类型由泛型T决定）的操作。也就是说它是个纯纯的供给者(不接收值，有返回值)。

```java
@Test
void supplier() {
    // 供给简单的字符串
    Supplier<String> supplier = ()->"你好，世界！";
    System.out.println(supplier.get());

    // 供给1-10之间的随机数
    Random random = new Random();
    Supplier<Integer> supplier2 = ()->random.nextInt(10)+1;
    for (int i = 0; i < 5; i++) {
        System.out.println(supplier2.get());
    }
}
```

输出结果

```java
你好，世界！
8 8 1 3 2 
```



##### Function<T,R> ：函数型接口

`Function<T, R>` 表示接受一个输入参数（类型由泛型T决定）并返回一个结果(类型有泛型R决定)的操作。

`andThen(Function<? super R, ? extends V> after)`：将当前函数和另一个函数组合起来，返回一个“先执行当前函数，再执行另一个函数”的新函数。

`compose(Function<? super V, ? extends T> before)`：将当前函数和另一个函数组合起来，返回一个“先执行before函数，再执行当前函数”的新函数。

```java
@Test
void function() {
    // 将字符串转为整数型
    Function<String, Integer> function = Integer::valueOf;
    System.out.println(function.apply("12") + 3);

    // 将整数转为字符串
    Function<Integer, String> function1 = x->"传入数字：" + x;
    System.out.println(function1.apply(12));

    // 将字符串加工后再传出
    Function<String,String> function2 = x->x.toUpperCase() + " (长度" + x.length() + ")";
    System.out.println(function2.apply("hello,world!"));

    // 测试Function的默认方法
    // andThen(Function<? super R, ? extends V> after)
    // 将当前函数和另一个函数组合起来，返回一个“先执行当前函数，再执行另一个函数”的新函数
    function1 = function1.andThen(function2);
    System.out.println(function1.apply(12)); // 输出 传入实在：12 (长度7)
    
    // compose(Function<? super V, ? extends T> before)
    // 将当前函数和另一个函数组合起来，返回一个“先执行before函数，再执行当前函数”的新函数
    function2 = function2.compose(x->"[begin]" + x);
    System.out.println(function2.apply("hello,world!")); // 输出 [BEGIN]HELLO,WORLD! (长度19)
}
```

输出结果

```java
15
传入数字：12
HELLO,WORLD! (长度12)
传入数字：12 (长度7)
[BEGIN]HELLO,WORLD! (长度19)
```



##### Predicate ：断言型接口

`Predicate<T> ` 表示接受一个输入参数（类型有泛型T决定）并返回一个布尔值的操作。

`and(Predicate<? super T> other)`：将当前断言和另一个断言组合起来，返回一个新的断言，当当前断言和另一个断言的结果都为真时，新的断言返回真，否则返回假。

`or(Predicate<? super T> other)`：将当前断言和另一个断言组合起来，返回一个新的断言，当当前断言和另一个断言有一个结果为真时，新地断言返回真，否则返回假。

`negate()`：取反，如果当前断言结果为真，那么新的断言结果一定为假。

```java
@Test
void predicate() {
    // 断言：传入一个整型，如果是偶数返回真，否则返回假
    Predicate<Integer> predicate = x -> x%2 == 0;
    System.out.println(predicate.test(21)); // false
    System.out.println(predicate.test(22)); // true
    System.out.println("------");

    // 测试Predicate的默认方法
    // and(Predicate<? super T> other)
    // 将当前断言和另一个断言组合起来，返回一个新的断言，当当前断言和另一个断言的结果都为真时，新的断言返回真，否则返回假
    Predicate<String> p = x->x.startsWith("h");
    Predicate<String> p2 = x->x.endsWith("!");
    Predicate<String> newPredicate = p.and(p2);
    // 当第一个字符为'h'，最后一个字符为'!'时断言为真
    System.out.println(newPredicate.test("hello,world!")); // true
    System.out.println(newPredicate.test("hello")); // false
    System.out.println("------");

    // or(Predicate<? super T> other)
    // 将当前断言和另一个断言组合起来，返回一个新的断言，当当前断言和另一个断言有一个结果为真时，新地断言返回真，否则返回假
    newPredicate = p.or(p2);
    System.out.println(newPredicate.test("hello")); // true
    System.out.println(newPredicate.test("world!")); // true
    System.out.println(newPredicate.test("你好")); // false
    System.out.println("------");

    // negate()
    // 取反，如果当前断言结果为真，那么新的断言结果一定为假
    newPredicate = p.negate();
    System.out.println(newPredicate.test("hello")); // false
    System.out.println(newPredicate.test("你好")); // true

}
```

输出结果

```java
false
true
------
true
false
------
true
true
false
------
false
true
```





#### 新的日期和时间API

新的日期和时间API解决了旧的java.util.Date和java.util.Calendar类的许多问题，并提供了更多的功能。



#### Optional类

Optional类是一种新的类，它可以解决Java中的空指针异常问题。它可以包含一个非空值，也可以表示一个空值。



#### 接口默认方法和静态方法





#### 更多类型注解

JDK 8支持在代码中使用类型注解，包括：类、接口、方法、构造函数、参数、局部变量等



#### 新的重复注解

Java 8允许使用重复注解来替代原来的多次注解，从而使代码更加简洁和易读。



#### CompletableFuture类

CompletableFuture类是一个带有异步执行和回调机制的Future类，可以简化异步编程的代码





#### Parallel Arrays 并行数组

JDK 8提供了一组支持并行计算的数组操作方法，使得对于大规模数据集的处理更加高效





#### Base64编码器和解码器

JDK 8提供了java.util.Base64类，可以进行Base64编码和解码操作



#### 新的安全特性

JDK 8中增强了Java安全机制，包括更强的密码支持、更安全的加密和更灵活的密钥管理等



#### G1垃圾回收器

JDK 8中引入了一种新的垃圾回收器，称为G1(Garbage First)垃圾回收器，它是一种高效的垃圾回收算法，适合处理大规模的内存数据



#### 新的集合API

Java 8中引入了新的集合API，其中包括新的集合接口和实现，使集合操作更加方便和高效。



#### Nashorn引擎

ashorn是Java 8中新的JavaScript引擎，它可以将JavaScript代码编译为Java字节码并在Java虚拟机中运行。

