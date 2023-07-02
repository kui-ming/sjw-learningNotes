- 在`synchronized`代码段下，如果两个线程的**同步代码段持有相同锁**，那么总是一线程中的代码段执行完后才执行二线程中的持有相同锁的代码段,存在明确先后同步顺序

- 如果`synchronized`代码段没有持相同锁，则两个线程没有前后顺序，始终串行，如下面的“盘库”线程与上两个线程不持有相同锁
- `synchronized`持有的相同锁应当是**不变换引用地址的对象**，如果在方法上添加`synchronized`，那么他的锁就是这个方法所属的对象

```java
public class Test {

    static class Lock{
        public Integer good = 0;
        Boolean inFlag = true;
    }

    static Lock lock = new Lock();
    public static void main(String[] args) {
        // 进货进程
        new Thread(new Runnable(){
            @Override
            public void run() {
                synchronized(lock) { // 同步代码段，持有一个对象lock当锁，该变量的引用地址不能变
                    System.out.println("进货");
                    for (int i = 0; i < 10; i++) {
                        lock.good++;
                        System.out.println("进货一件，当前存量："+lock.good);
                        try {
                            Thread.sleep(100);
                        }catch(Exception e){
                            e.printStackTrace();
                        }
                    }
                }
            }
        }).start();
		// 出货进程
        new Thread(new Runnable(){
            @Override
            public void run() {
                synchronized(lock) { // 同步代码段，持有一个对象lock当锁，该变量的引用地址不能变
                    System.out.println("出货");
                    for (int i = 0; i < 8; i++) {
                        lock.good--;
                        System.out.println("出货一件，当前存量："+lock.good);
                        try {
                            Thread.sleep(100);
                        }catch(Exception e){
                            e.printStackTrace();
                        }
                    }
                }
            }
        }).start();
        // 在synchronized代码段下，如果两个线程的同步代码段持有相同锁，那么总是一线程中的代码段执行完后才执行二线程中的持有相同锁的代码段,存在明确先后同步顺序
        // 如果synchronized代码段没有持相同锁，则两个线程没有前后顺序，始终串行，如下面的“盘库”线程与上两个线程不持有相同锁
        // synchronized持有的相同锁应当是不变换引用地址的对象，如果在方法上添加synchronized，那么他的锁就是这个方法所属的对象
        // 盘库线程
        new Thread(new Runnable(){

            @Override
            public void run() {
                synchronized(this){ // 该同步锁持有的是这个Runnable对象
                    for (int i = 0; i < 10; i++) {
                        System.out.println("盘库，当前存量："+lock.good);
                        try {
                            Thread.sleep(100);
                        }catch(Exception e){
                            e.printStackTrace();
                        }
                    }
                }
            }
        }).start();
    }
}

```



测试结果：

```
进货
进货一件，当前存量：1
盘库，当前存量：1
进货一件，当前存量：2
盘库，当前存量：2
进货一件，当前存量：3
盘库，当前存量：3
进货一件，当前存量：4
盘库，当前存量：4
进货一件，当前存量：5
盘库，当前存量：5
进货一件，当前存量：6
盘库，当前存量：6
进货一件，当前存量：7
盘库，当前存量：7
进货一件，当前存量：8
盘库，当前存量：8
进货一件，当前存量：9
盘库，当前存量：9
进货一件，当前存量：10
盘库，当前存量：10
出货
出货一件，当前存量：9
出货一件，当前存量：8
出货一件，当前存量：7
出货一件，当前存量：6
出货一件，当前存量：5
出货一件，当前存量：4
出货一件，当前存量：3
出货一件，当前存量：2
```

![image-20230221213939525](../../MDimages/Untitled_images/image-20230221213939525.png)
