# 【Vue】Vue2基本使用



[TOC]



## 一、模板语法

### 1、插值语法

#### 写法

**表达式**为任意**js表达式**，可直接读取到data中的所有属性

```vue
<div>{{表达式}}</div>
```



#### 介绍

插值语法用于**解析标签体中的内容**。数据绑定最常见的形式就是使用`Mustache`语法 (双大括号) 进行**文本插值**，被`Mustache`标签包裹的对象的值将会插入`Mustache`标签的位置，并且，无论何时，**绑定的数据对象上发生了改变，插值处的内容都会更新**。

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132561.jpg)

但是通过使用 **[v-once 指令](https://cn.vuejs.org/v2/api/#v-once)**，你也能执行一次性地插值，当数据改变时，**插值处的内容不会更新**。但请留心这会影响到该节点上的其它数据绑定。

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132562.jpg)



### 2、指令语法

#### 写法

Vue中所有指令格式都是以**`v-`**开头，例如`v-bind`。表达式为任意`js`表达式

```vue
v-bind:href="表达式"
```



#### 介绍

指令语法用于**解析标签**（包括标签属性、标签体内容、绑定事件……）。**指令 (Directives)** 是带有 **`v-` 前缀**的特殊 attribute。指令 attribute 的值预期是**单个** `JavaScript 表达式` (v-for 是例外情况)。指令的职责是，**当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM**。

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132563.jpg)

上面例子中，`v-if` 指令将根据表达式` seen` 的值的真假来插入或移除 `<p> `元素。从 **2.6.0** 开始，可以用**方括号**括起来的` JavaScript` 表达式作为一个**指令的参数**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132564.jpg)

这里的 **`attributeName`** 会被作为一个**` JavaScript `表达式**进行**动态求值**，求得的值将会作为最终的参数来使用。例如，如果你的 Vue 实例有一个`data`的属性attributeName，其值为 **"href"**，那么这个绑定将等价于 **v-bind:href**。



### 模板语法示例

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>vue模板语法</title>
    <script src="../js/vue.js"></script>
</head>
<!--
    Vue模板语法两大类
    插值语法
      功能：用于解析标签体内容
      写法：<div>{{表达式}}</div>，表达式为任意js表达式，可直接读取到data中的所有属性
    指令语法
      功能：用于解析标签（包括标签属性、标签体内容、绑定事件……）
      写法：v-bind:href="表达式" 表达式为任意js表达式
      备注：Vue中所有指令格式都是以`v-`开头。如v-bind
-->
<body>
    <div id="root">
        <h1>插值语法</h1>
        <h3>你好，{{name}}</h3>
        <hr/>
        <h1>指令语法</h1>
        <a v-bind:href="url">点击跳转链接</a>
        <!--v-bind可以简写为 `:` -->
        <a :href="url">点击跳转链接2</a>
        <!-- 2.6.0开始，等价于:href="url" -->
        <a :[attr]="url">点击跳转链接3</a>
    </div>
</body>
<script>
    new Vue({
        el:"#root",
        data:{
            name:"张三",
            url:'https://www.baidu.com',
            attr:'href'
        }
    })
</script>
</html>
```



## 二、数据绑定

### 介绍

Vue中有两种数据绑定的方式

- **单向绑定(`v-bind`)：**数据只能从data流向页面
- **双向绑定(`v-model`)：**数据可以从data流向页面，也可以从页面流向data

其中，**双向绑定只能用于表单元素上**，对value属性进行绑定(如:input、select等)， **`v-model:value `可以简写为`v-model`**，因为`v-model`默认收集的就是**`value`属性**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132565.jpg)



### 数据绑定示例

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>vue数据绑定</title>
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        单项数据绑定(v-bind)：<input type="text" v-bind:value="name"/><br/>
        双向数据绑定(v-model)：<input type="text" v-model:value="name"/>
        <!--简写-->
        <br/>
        单项数据绑定(v-bind)：<input type="text" :value="name"/><br/>
        双向数据绑定(v-model)：<input type="text" v-model="name"/>
    </div>
</body>
<script>
    new Vue({
        el:'#root',
        data:{
            name:'张三'
        }
    })
</script>
</html>
```

#### 效果

![GIF 2022-12-12 16-47-44](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132567.gif)







## 三、初始化Vue时，`data`属性与`el`属性的两种写法

### `el`的两种写法

- 第一种，new Vue时配置el属性
- 第二种，先创建Vue实例，再通过`实例.$mount('绑定对象')`指定el的值



### `data`的两种写法

- 第一种，对象式写法（new Vue时配置data属性）
- 第二种，函数式写法（配置data属性时使用的是函数的返回值）
  - **由Vue管理的函数不要写箭头函数，否则this不再指向Vue实例，而是window实例**



### 演示案例

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vueel与data的两种写法</title>
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <h1>你好,{{name}}</h1>
    </div>
</body>
<script>
    /*
    const vm = new Vue({
                    el:'#root',//el的第一种写法
                    data:{ //data的第一种写法
                        name:'张三'
                    }
                })
    vm.$mount('#root') //el的第二种写法
    */
   new Vue({
       el:'#root',
       data:function(){ //data的第二种写法
           console.log("@@@",this)
           return {name:'李四'}
       }
   })
</script>
</html>
```



## 四、MVVM模型



### 介绍

- M：**模型(model)**，data中的数据
- V：**视图(View)**，模板代码
- **VM：视图模型(ViewModel)**，Vue实例

data中的所有属性，最后都会出现在`vm`(**Vue实例**)上，`vm`(**Vue实例**)上的**所有属性**及**Vue原型的所有属性**都可**在Vue模板上直接使用**。



## 五、数据代理



### JS数据代理

通过一个对象代理另外一个对象中的属性的操作叫做数据代理，在JS中使用`Object.defineProperty`方法实现数据代理。

#### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>回顾Object.defineProperty方法</title>
</head>
<body>
<script>
    let num = 10
    let person = {
        name:'张三',
        sex:'男',
    }
    //为对象追加属性
    Object.defineProperty(person,'age',{
        value:18,//此属性的值
        enumerable:true,//此属性是否可以枚举，默认为false
        writable:true,//此属性是否可修改，默认为false
        configurable:true,//此属性是否可删除，默认为false
    })
    //数据代理，将num属性交于person对象代理
    Object.defineProperty(person,'n',{
        //当调用person的n属性时将返回num的值
        get(){
            console.log('有人读取n属性，值为'+num)
            return num
        },
        //当修改person的n属性改变num的值
        set(value){
            console.log('有人修改n的值,修改值为'+value)
            num = value
        }
    })
    console.log(person)
</script>
</body>
</html>
```

#### 实现效果

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132568.jpg)



### Vue数据代理

#### 介绍

通过`vm对象`来**代理**`data对象`中属性的操作，好处是更加方便的操作data中的数据

#### 原理

通过**`Object.defineProperty()`**把`data对象`中的所有属性添加到`vm对象`上，为每个添加到`vm`上的属性都指定`getter/setter`方法，在`getter/setter`内部去**操作（读/写）**data中对应的属性





## 六、事件处理



### 事件的基本使用

#### 介绍

- 使用`v-on:xxx `或简写为 `@xxx` 绑定事件，**其中xxx为事件名**
- 事件的回调需配置在`methods`对象中，最终由`vm`代理
- `methods`属性中配置的函数**不能是箭头函数**！否则`this`不再执行`vm`对象
- `methods`属性中配置的函数都是被Vue管理的函数，`this`指向`vm`或组件实例对象
- `@click="demo"`和`"@click="demo($event)"`效果是一样的，不过后者可以传其他参数

#### 演示案例

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>事件的基本使用</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <h2>你好，{{name}}</h2>
        <button v-on:click="showInfo">点我提示信息</button>
        <!--v-on简写为@click-->
        <button @click="showInfo2('你好，世界')">点我提示mes</button>
        <!--没有写参数时默认传递事件信息，也可以写成showInfo3($event)-->
        <button @click="showInfo3">点我显示event信息</button>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            name:'张三'
        },
        methods:{
            showInfo(){
                alert("同学你好")
            },
            showInfo2(msg){
                alert(msg)
            },
            showInfo3(event){
                console.log(event)
            }
        }
    })
</script>
</html>
```

**实现效果**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132569.jpg)



### 事件修饰符



#### 六种修饰符

- **prevent:阻止默认事件(常用)**
- **stop:阻止事件冒泡(常用)**
- **once:事件只触发一次(常用)**
- **capture**:使用事件的捕捉模式
- **self**:只有event.target是当前操作的元素时才会触发事件
- **passive**:事件的默认行为会立即触发，无需等待事件回调完毕

另外，修饰符可以连续书写，例如：`@click.prevent.stop="回调函数"`

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132570.jpg)



#### 演示案例

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>事件修饰符</title>
    <style>
        *{margin-top: 20px;}
        li{height: 100px;}
    </style>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <h2>欢迎来到{{name}}学习</h2>
        <!--阻止默认行为，在这里是阻止跳转到指定网址（常用）-->
        <a href="http://www.baidu.com" @click.prevent="showInfo">点我提示信息</a>
        <!--阻止冒泡事件（常用）-->
        <div @click="showInfo" style="height:50px;background-color:skyblue;">
            <button @click.stop="showInfo">点我提示信息</button>
        </div>
        <!--阻止默认行为的同时阻止冒泡事件-->
        <div @click="showInfo" style="height:50px;background-color:skyblue;">
            <a href="http://www.baidu.com" @click.prevent.stop="showInfo">点我提示信息</a>
        </div>
        <!--事件只触发一次(常用)-->
        <button @click.once="showInfo">点我提示信息</button>
        <!--使用事件的捕捉模式(先捕获事件，然后在从内到外冒泡事件，
            如果不加的话执行事件的顺序为2、1，使用捕获模式是在捕获阶段执行事件，顺序为1、2-->
        <div @click.capture="showInfo(1)" style="height: 70px;background-color: tomato;padding: 2px;">
            div1
            <div @click="showInfo(2)" style="height: 30px;background-color: wheat;">
                div2
            </div>
        </div>
        <!--只有event.target是当前操作的元素时才会触发事件-->
        <div @click.self="showInfo" style="height:50px;background-color:skyblue;">
            <button @click="showInfo">点我提示信息</button>
        </div>
        <!--事件的默认行为会立即触发，无需等待事件回调完毕-->
        <!--wheel:鼠标滚轮滚动事件 scroll:滚动条移动事件-->
        <ul @wheel.passive="demo" style="width: 200px;height: 200px;background-color: peru;overflow: auto;">
            <li>1</li>
            <li>2</li>
            <li>3</li>
            <li>4</li>
        </ul>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            name:"长沙理工"
        },
        methods:{
            showInfo:function(event){
                alert('欢迎你的到来')
                console.log(event.target)
            },
            demo(){
                //模拟事件体延时
                for(let i=0;i<10000;i++){
                    console.log('@')
                }
                console.log("延迟完成")
            }
        }
    })
</script>
</html>
```



### 键盘事件



#### Vue中常用的按键别名

- **enter**(回车)
- **delete**(删除和退格键)
- **esc**(退出)
- **space**(空格)
- **tab**(换行，必须要配合keydown事件使用)
- **up**(上)
-  **down**(下)
- **left**(左)
- **right**(右)

Vue没有提供别名的**特殊按键**（如`CapsLock`）可以使用按键名称的去绑定，但注意要转为"**小写单词-小写单词**"的格式(如`caps-lock`)

按键修饰符也能连续使用，例如：`@keyup.ctrl.a="方法名"`![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132571.jpg)



#### 系统修饰键

- ctrl、alt、shift、meta(win键)
- 配合**keyup事件**使用时，按下修饰键的同时还需按下其他键，随后释放其他键后事件才被触发
- 配合**keydown**事件可以正常触发



#### 自动义按键别名

可以使用keyCode去指定具体按键(不推荐)

写法：`Vue.config.keyCodes.自定义键名 = 键码`



#### 演示案例

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>键盘事件</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <h2>欢迎{{name}}来此学习</h2>
        <!--
            enter是回车键的别名，代码keyCode值13
            1.Vue中常用的按键别名
                enter(回车)
                delete(删除和退格键)
                esc(退出)
                space(空格)
                tab(换行，必须要配合keydown事件使用)
                up(上)
                down(下)
                left(左)
                right(右)
            2.Vue未提供别名的特殊按键（如CapsLock）可以使用按键名称的去绑定，但注意要转为"小写单词-小写单词"的格式(如caps-lock)
            3.系统修饰键：ctrl、alt、shift、meta(win键)
                配合keyup事件使用时，按下修饰键的同时还需按下其他键，随后释放其他键后事件才被触发
                配合keydown事件可以正常触发
            4.可以使用keyCode去指定具体按键(不推荐)
            5.Vue.config.keyCodes.自定义键名 = 键码，可以自定义按键别名
        -->
        <input type="text" placeholder="按下回车键提示输入" @keyup.enter="showInfo"/>
        <input type="text" placeholder="按下回车键提示输入" @keyup.huiche="showInfo"/>
        <input type="text" placeholder="按下ctrl+A提示输入" @keyup.ctrl.a="showInfo"/>
    </div>
</body>
<script>
    Vue.config.keyCodes.huiche = 13 //自定义回车键的别名
    const vm = new Vue({
        el:'#root',
        data:{
            name:'张三'
        },
        methods:{
            showInfo(e){
                console.log(e.key)//输出按键名称
                console.log(e.keyCode)//输出按键编码
                console.log(e.target.value)//拿到元素中的值
            }
        }
    })
</script>
</html>
```



## 七、计算属性与监听属性



### 计算属性(computed)

#### 介绍

##### 1)定义

本身没有具体值，需要通过已有的属性计算得出

##### 2)原理

底层借助**`Objiect.defineProperty()`**方法提供的**gettter**和**setter**

##### 3)get函数何时执行

- **初次读取**时执行一次
- 当依赖的**数据发生改变**时会再次调用

##### 4)优势

与methods实现相比，内部有缓存机制（复用），效率更高，调用方便

##### 5)备注

- 计算属性最终出现在**`vm`（Vue实例）**上，**直接读取使用**即可
- 如果计算属性**需要修改**，**必须写set函数**响应，且set中要编写修改依赖数据的语句



#### 简写方式

计算属性在`computed`属性下声明，如果只读不写的情况可以直接使用一个函数完成声明，如下图：

```vue
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            xing:'张',
            ming:'三'
        },
        computed:{//计算属性
            /*当此属性只读不改时可以简写成如下格式，相当于
                fullname:{
                    get:function(){
                        return this.xing + '-' + this.ming
                    }
                    // set属性不用写，只用写get
                }
            */
            fullname(){ // 属性名：fullname
                return this.xing + '-' + this.ming
            }
        }
    })
</script>
```



#### 演示案例

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>姓名案例_计算属性实现</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        姓：<input type="text" v-model:value="xing" /> <br/><br/>
        <!--v-model:value的简写模式v-model-->
        名：<input type="text" v-model="ming" /> <br/><br/>
        全名：<span>{{fullname}}</span>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            xing:'张',
            ming:'三'
        },
        computed:{//计算属性，计算属性没有实质的值，他的值只能通过getter/setter函数计算得出
            fullname:{
                //当有人读取fullname属性时，get函数会被调用，且返回值作为fullname的值
                //get何时调用？1.初次读取fullName属性时 2.所依赖的数据发送改变时(这里的依赖数据为xing和ming)
                get:function(){
                    return this.xing + '-' + this.ming
                },
                //如果要修改此属性，则调用set函数
                set(value){
                    arr = value.split('-')
                    this.xing = arr[0]
                    this.ming = arr[1]
                }
            }
        }
    })
</script>
</html>
```

**实现效果**

![GIF 2022-12-12 19-34-50](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132572.gif)





### 监视属性(watch)

#### 说明

- 当被监视的属性发生变化时，回调函数自动被调用

- 监视的属性必须存在

#### 监视的两种写法

##### 第一种，new Vue时传入watch配置

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132573.jpg)

##### 第二种， 通过vm.$watch监视

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132574.jpg)



#### 深度监视

Vue中的watch默认**不监视**对象**内部值的改变**（**一层**），配置**`deep:true`**后**可以监视**对象内部值的改变（**多层**）

##### 演示案例

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>天气案例-深度监视</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <h3>a的值是:{{numbers.a}}</h3>
        <button @click="numbers.a++">点我让a+1</button>
        <h3>b的值是:{{numbers.b}}</h3>
        <button @click="numbers.b++">点我让b+1</button>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            numbers:{
                a:0,
                b:1,
            },
        },
        watch: {
            //监测多级结构中某个属性的变化
            'numbers.a':{
                handler(){
                    console.log('a被改变了')
                }
            },
            //监视多级结构中所有属性的变化
            numbers:{
                deep:true,//开启深度监视
                handler(){
                    console.log('numbers中有值发生了改变')
                }
            }
        },
    })
</script>
</html>
```

**演示效果**

![GIF 2022-12-12 19-45-43](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132575.gif)



#### 监视属性的简写方式

```vue
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            isHot:true
        },
        computed: ……,
        methods: ……,
        watch: {
            //正常写法
            /* isHot:{
                //immediate:true,//初始化时就调用handler函数
                //监测isHot属性，当isHot属性被修改时触发
                handler(newValue,oldValue){
                    console.log('isHot被修改了',newValue,oldValue)   
                }
            } */
            //简写
            isHot(newValue,oldValue){
                console.log('isHot被修改了',newValue,oldValue)  
            }
        },
    })
    // 另外一种监听方法
    vm.$watch('isHot',function(newValue,oldValue){
        console.log('isHot被修改了',newValue,oldValue)  
    })

</script>
```



#### 备注

- Vue自身**可以监视**对象**内部值**的**改变**，但Vue提供的`watch`默认**不可以**
- 使用watch时根据数据的具体结构，决定是否采用深度监视

##### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>天气案例-监听属性</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <h2>今天天气很{{isHot?'炎热':'凉爽'}}</h2>
        <h2>今天天气很{{info}}</h2>
        <button @click="changeWeather">切换天气</button><br/>
        <!--绑定事件的时候：@xxx="yyy" yyy可以写一些简单的语句，指向vm实例-->
        <button @click="isHot = !isHot">切换天气</button>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            isHot:true
        },
        computed: {
            info:function(){
                /*相当于
                info:{
                    get:function(){}
                }
                */
                return this.isHot?'炎热':'凉爽'
            }
        },
        methods: {
            changeWeather(){
                this.isHot = !this.isHot
            }
        },
        watch: {//监视属性
            isHot:{//监视isHot属性
                immediate:true,//初始化时就调用handler函数
                //监测isHot属性，当isHot属性被修改时触发
                handler(newValue,oldValue){
                    console.log('isHot被修改了',newValue,oldValue)   
                }
            }
        },
    })
    //在初始化外写监视属性,监视isHot属性
    vm.$watch('isHot',{
                immediate:true,//初始化时就调用handler函数
                //监测isHot属性，当isHot属性被修改时触发
                handler(newValue,oldValue){
                    console.log('isHot被修改了',newValue,oldValue)   
                }
            })
</script>
</html>
```

**演示效果**

![GIF 2022-12-12 19-52-52](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132576.gif)

### `computed`和`watch`之间的区别

#### 区别

- `computed`能完成的功能，`watch`都可以完成
- `watch`能完成的功能，`computed`不一定能完成，例如`watch`才能进行异步操作



#### 两个重要的小原则

- **所有Vue管理的函数，最好写成普通函数，这样this指向vm或组件实例对象**

- **所有不被Vue管理的（如定时器回调函数、ajax回调函数）最好写成箭头函数，这样this才指向vm或组件实例对象**



#### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>姓名案例_watch写法</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        姓：<input type="text" v-model:value="xing" /> <br/><br/>
        <!--v-model:value的简写模式v-model-->
        名：<input type="text" v-model="ming" /> <br/><br/>
        全名：<span>{{fullName}}</span>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            xing:'张',
            ming:'三',
            fullName:'张-三'
        },
        watch: {
            xing:{//监视属性的完全写法
                handler:function(newValue,oldValue){
                    //计算属性中不能开启异步数据，但是watch可以开启异步数据
                    setTimeout(() => {
                        this.fullName = newValue + '-' + this.ming
                    }, 1000);
                }
            },
            ming(val){//监视属性的简写方式
                this.fullName = this.xing + '-' + val
            }
        },
    })
</script>
</html>
```

**演示效果**

![GIF 2022-12-12 19-59-15](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132577.gif)





## 八、绑定样式([Class与 Style 绑定](https://cn.vuejs.org/v2/guide/class-and-style.html))



### 绑定`class`样式

- 写法  :**`class="xxx"`**
- **xxx**可以是字符串、对象、数组
- 数组写法适用于**要绑定多个样式，个数和名称确定，但不确定用不用**
- 对象写法适用于**要绑定多个样式，个数和名称不确定**
- 字符串写法适用于**类名不确定，需动态获取**



### 绑定`style`样式

**对象写法**

- **`:style="{fontSize:xxx}"`**

- 其中xxx是动态值

**数组写法**

- **`:tyle="[a,b]"`**
- 其中**a,b**是样式对象



### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>绑定样式</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
    <style>
        .base{/*基础样式*/
            width: 400px;
            height: 100px;
            padding: 10px;
            border: black 1px solid;
        }
        .happy{/*高兴的背景样式*/
            background-color:tomato;
            border: springgreen 5px solid;
        }
        .sad{/*伤心的背景样式*/
            background-color: blue;
            border: brown 5px solid;
        }
        .normal{/*普通的背景样式*/
            background-color: gray;
            border: blue 5px solid;
        }
        .sjw1{/*字体加粗样式*/
            font-size: 24px;
            font-weight: bold;
        }
        .sjw2{/*字体阴影样式*/
            text-shadow: 0 0 10px black;
        }
        .sjw3{/*边框圆角样式*/
            border-radius: 20px;
        }
    </style>
</head>
<body>
    <div id='root'>
        <!--绑定class样式--字符串写法，适用于样式的类名不确定，需要动态指定-->
        <div class="base" v-bind:class='mood' @click='changeMood'>{{text}}</div><br/><br/>
        <!--`:class`为`v-bind:class`的简写-->
        <!--绑定class样式--数组写法，适用于样式的类名不确定，需动态指定-->
        <div class="base" :class="classArr">{{text}}</div><br/><br/>
        <!--绑定class样式--对象写法，适用于样式个数和名称确定，需动态决定用不用-->
        <div class="base" :class="classObj">{{text}}</div><br/><br/>
        <!--style样式--对象写法-->
        <div class="base" :style="styleObj">{{text}}</div><br/><br/>
        <!--style样式--数组写法(不常用)-->
        <div class="base" :style="styleArr">{{text}}</div><br/><br/>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            text:'你好，世界！',
            mood:'happy',
            classArr:['sjw1'],//数组写法
            classObj:{//对象写法
                sjw1:false,
                sjw2:false,
                sjw3:true,
            },
            styleObj:{ //style样式--对象写法
                fontSize:'40px',//font-size的键形式
                color:'red',
                backgroundColor:'#0ff',//background-color的键形式
            },
            styleArr:[ //style样式--数组写法
                {backgroundColor:'red',fontSize:'40px'},//对象1
                {borderRadius:'20px',textShadow:'0 0 10px blue'},//对象2
            ],
        },
        methods: {
            changeMood(){
                const arr = ['happy','sad','normal']
                index = Math.floor(Math.random()*3)
                this.mood = arr[index]
            }
        },
    })
    //模拟添加样式
    vm.classArr.shift() //移除最后一个元素 这里移除的是'sjw1'绑定样式.html
    vm.classArr.push('sjw1') //往arr数组中添加一个元素
    vm.classArr.push('sjw2')
    vm.classArr.push('sjw3')
</script>
</html>
```

**演示效果**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132578.jpg)



## 九、条件渲染(v-if、v-show)



### `v-if`

#### 写法

- **`v-if="表达式"`**

- **`v-else-if="表达式"`**

- **`v-else=""`**(简写为v-else)

#### 介绍

**适用于：元素切换频率较低的场景**

**特点：不展示的DOM元素之间被移除**

**注意：v-if可以和:v-else-if、v-else一起使用，但要求结构不能被“打断”**



### `v-show`

#### 写法

- **`v-show="表达式"`**

#### 介绍

**适用于：元素切换频率较高的场景**

**特点：不展示的DOM元素不被移除，仅使用样式隐藏掉**



### 备注

使用v-if时，该DOM元素可能因被移除而无法获取，但使用v-show一定可以获取到。



### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>条件渲染</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <!-- 使用v-show做条件渲染（结构存在，只是加了display: none） -->
        <h2 v-show="false">1.欢迎光临，{{name}}</h2>
        <h2 v-show="true">2.欢迎光临，{{name}}</h2>
        <h2 v-show="isShow">3.欢迎光临，{{name}}</h2>
        <!-- 使用v-if做条件渲染 -->
        <h2>当前n的值为:{{n}}</h2>
        <button @click="n++">点我n+1</button>
        <!-- template不影响结构层次，只配合v-if使用，如果使用div的话，<h4>标签外会套一层<div>标签 -->
        <template v-if="n==1"><h4>条件成立，第一条语句得到显示</h4></template>
        <template v-else-if="n==2"><h4>条件成立，第二条语句得到显示</h4></template>
        <template v-else>
            <template v-if="n>=2&&n<=5"><h4>当前n值大于等于2，小于等于5</h4></template>
            <template v-else-if="n>5&&n<11"><h4>当前n值大于5，小于11</h4></template>
            <template v-else><h4>当前的n值等于0或大于10</h4></template>
        </template>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            name:'张三',
            isShow:true,
            n:0,
        }
    })
</script>
</html>
```

**演示效果**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132579.jpg)



## 十、列表渲染(v-for)

### `v-for`指令

#### 写法

- **`v-for="(item, index) in xxx" :key="yyy"`**

#### 介绍

`v-for`指令通过循环遍历来展示指定列表的数据，它可以遍历：**数组、对象、字符串(不常使用)、指定对象(很少使用)**



#### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>基本列表</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <!--遍历数组-->
        <h2>人员列表(遍历数组)</h2>
        <ul>
            <li v-for="(item,index) in persons" :key="item.id">
                {{index}}:{{item.name}}-{{item.age}}
            </li>
        </ul>
        <!--遍历对象-->
        <h2>汽车信息(遍历对象)</h2>
        <ul>
            <li v-for="(value,key) of cars" :key="key">
                {{key}}:{{value}}
            </li>
        </ul>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            persons:[
                {id:'001',name:'张三',age:18},
                {id:'002',name:'李四',age:19},
                {id:'003',name:'王五',age:20},
            ],
            cars:{
                name:'奥迪A8',
                price:'70万',
                color:'黑色'
            },
        }
    })
</script>
</html>
```

**演示效果**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132580.jpg)



### `key`的原理

#### 虚拟DOM中key的作用

`key`是`虚拟DOM对象`的标识，当状态中的数据发生**改变**时，Vue会根据**【新数据】**生成**【新的虚拟DOM】**,随后Vue进行**【新虚拟DOM】**与**【旧虚拟DOM】**的**差异比较**

#### 比较规则

**旧虚拟DOM中找到与新虚拟DOM相同的key时**

- 第一种情况：**若虚拟DOM中内容没有改变，直接使用之前的真实DOM**
- 第二种情况：**若虚拟DOM中内容变了，则生成新的真实DOM，随后替换掉页面中之前的真实DOM**

**旧虚拟DOM中未找到与新虚拟DOM相同的key时，创建新的真实DOM，随后渲染到页面**



#### 用`index`作为`key`可能会引发的问题

- 第一种情况，若对数据进行**逆序添加、逆序删除**等**破坏顺序的操作**会产生**没有必要**的`真实DOM`更新，界面**效果没问题**，但效率低

- 第二种情况，如果结构中**包含输入类DOM**，产生错误的DOM更新，**界面出现问题**



#### 开发中如何选择`key`？

最好使用每条数据的**唯一标识**作为`key`，比如id、手机号、身份证号、学号等唯一标识字段。如**不存在**对数据的**逆序添加、逆序删除**等破坏顺序的操作，**仅用于渲染列表进行展示**，用`index`作为`key`**没有问题**



#### 遍历列表时key的作用

##### index作为key

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132581.jpg)



##### id作为key

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132582.jpg)



> 如果v-for是未写key值，Vue会将索引值index自动设置为key值



#### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>key的原理</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <button @click.once="add">添加一个老刘</button>
        <h2>人员列表(1)</h2>
        <ul>
            <!-- :key指向主键 -->
            <li v-for="(item,index) in persons" :key="index">
                {{item.name}}-{{item.age}}
                <input type="text"/>
            </li>
        </ul>
        <h2>人员列表(2)</h2>
        <ul>
            <!-- :key指向主键 -->
            <li v-for="(item,index) in persons" :key="item.id">
                {{item.name}}-{{item.age}}
                <input type="text"/>
            </li>
        </ul>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            persons:[
                {id:'001',name:'张三',age:18},
                {id:'002',name:'李四',age:19},
                {id:'003',name:'王五',age:20},
            ],
        },
        methods: {
            add(){
                const p = {id:'004',name:'老刘',age:45}
                this.persons.unshift(p) // 添加到数组第一个
            }
        },})
</script>
</html>
```

**演示效果**

![GIF 2022-12-12 20-37-33](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132583.gif)





### 列表过滤

通过监视属性或计算属性来实现列表的过滤

#### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>列表过滤</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <h2>人员列表</h2>
        <input type="text" placeholder='请输入名字' v-model="keyWord"/>
        <ul>
            <li v-for="item in filPersons" :key="item.id">
                {{item.name}}-{{item.age}}-{{item.sex}}
            </li>
        </ul>

    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            persons:[
                {id:'001',name:'马冬梅',age:18,sex:'女'},
                {id:'002',name:'周杰伦',age:19,sex:'男'},
                {id:'003',name:'周冬雨',age:20,sex:'女'},
                {id:'004',name:'温兆伦',age:21,sex:'男'},
            ],
            //filPersons:[],//收集过滤后的列表信息(监视模式使用)
            keyWord:"",//收集用户的输入 
        },
        watch: { //监视模式过滤列表
            /* keyWord:{
                immediate:true,//初始化时执行一次
                handler(val){
                    this.filPersons =  this.persons.filter((p)=>{ //过滤
                        return p.name.indexOf(val) != -1
                    })
                }
            } */
        },
        computed: { //计算属性过滤列表
            filPersons(){//获得经过过滤后的数组
                return this.persons.filter((p)=>{
                    return p.name.indexOf(this.keyWord) != -1
                })
            }
        },
    })
</script>
</html>
```

**演示效果**

![GIF 2022-12-12 20-43-05](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132584.gif)



### 列表排序

使用计算属性，在过滤列表的基础上进行列表排序

#### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>列表排序</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <h2>人员列表</h2>
        <input type="text" placeholder='请输入名字' v-model="keyWord"/>
        <button @click="sortType=2">年龄升序</button>
        <button @click="sortType=1">年龄降序</button>
        <button @click="sortType=0">原顺序</button>
        <ul>
            <li v-for="item in filPersons" :key="item.id">
                {{item.name}}-{{item.age}}-{{item.sex}}
            </li>
        </ul>

    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            persons:[
                {id:'001',name:'马冬梅',age:38,sex:'女'},
                {id:'002',name:'周杰伦',age:19,sex:'男'},
                {id:'003',name:'周冬雨',age:40,sex:'女'},
                {id:'004',name:'温兆伦',age:21,sex:'男'},
            ],
            keyWord:"",//收集用户的输入 
            sortType:0,// 0 原顺序 1 降序 2升序
        },
        computed: { //计算属性过滤列表
            filPersons(){//获得经过过滤后的数组
                const arr =  this.persons.filter((p)=>{//用来存储待排序的列表
                    return p.name.indexOf(this.keyWord) != -1
                })
                if(this.sortType){//如果排序模式不等于0 开启排序
                    arr.sort((p1,p2)=>{
                        return this.sortType==1 ? p2.age-p1.age : p1.age-p2.age //降序用p2-p1,升序用p1-p2
                    })
                }
                return arr
            }
        },
    })
</script>
</html>
```

**演示效果**

**初始状态**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132586.jpg)

升序

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132587.jpg)

**降序**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132588.jpg)

过滤情况下的**降序**列表

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132589.jpg)





## 十一、Vue怎么监视数据

Vue会监视data中**所有层次**的数据



### 如何监测对象中的数据？

- 通过setter实现监视，且要在new Vue时就传入要监视的数据

- 对象中**后追加**（动态追加）的属性，Vue默认**不做响应式处理**

- 如需要给后添加的属性做响应式，可使用如下API

  - **`Vue.set(目标对象,属性名或下标,值)`**

  - **`vm.$set(目标对象,属性名或下标,值)`**



### 如何监视数组中的数据？

通过**包裹**(重写)**数组更新元素的方法**实现，本质就是做了两件事：

- 第一是**调用原生对应的方法对数组进行更新**

- 第二是**重新解析模板，进而更新页面**



### Vue中修改数组元素使用方法

#### 第一种，使数组的7个[变更方法](https://cn.vuejs.org/v2/guide/list.html#变更方法)

##### 1)push(追加)

向数组的**末尾添加**一个或更多元素，并**返回新的长度**

语法：`array.push(item1, item2, ..., itemX)`

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132590.jpg)



##### 2)pop(删除最后一个元素)

用于**删除**数组的**最后一个元素**并**返回删除的元素**

语法：`array.pop()`

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132591.jpg)



##### 3)shift(删除第一个元素)

用于把数组的**第一个元素**从其中**删除**，并**返回第一个元素的值**

语法：`array.shift()`

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132592.jpg)



##### 4)unshift(开头添加) 

unshift() 方法可向数组的**开头添加**一个或更多元素，并**返回新的长度**

语法：`array.unshift(item1,item2, ..., itemX)`

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132593.jpg)



##### 5)splice(删除与添加指定位置的任意个元素)

从数组中**添加或删除**元素

**语法**：`array.splice(index[,howmany][,item1,.....,itemX])`

- **index**：必需，规定从何处添加/删除元素
  - 该参数是开始插入和（或）删除的数组元素的下标，必须是数字

- howmany：可选，规定应该删除多少元素

  - 必须是数字，但可以是 "0"

  - 如果未规定此参数，则删除从 index 开始到原数组结尾的所有元素

- item：可选，要添加到数组的新元素

**返回值**：如果仅删除一个元素，则返回一个元素的数组。 如果未删除任何元素，则返回空数组

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132594.jpg)

**实例**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132595.jpg)



##### 6)sort(排序)

排序顺序可以是字母或数字，并按升序或降序。默认排序顺序为按字母升序

语法：`array.sort([sortfunction])`

- sortfunction：可选，规定排序顺序。必须是函数

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132596.jpg)



##### 7)reverse(反转数组) 

reverse() 方法用于颠倒数组中元素的顺序

语法：`array.reverse()`

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132597.jpg)



#### 第二种，使用`Vue.set()`或`vm.$set()`

略



#### 注意

**`Vue.set`或`vm.$set`不能给`vm`或`vm`的根数据对象添加属性！**



## 十二、收集表单数据([表单输入绑定](https://cn.vuejs.org/v2/guide/forms.html))

### 根据`input`类型绑定数据

若`<input type="text" />`(文本框)，则`v-model`收集的是`value`值，用户输入的就是`value`值

若`type="radio"`(单选框)，则`v-model`收集的是`value`值，且需要给标签设置`value`值

若 `type="checkbox"`(多选框)

- (1)**没有配置**`input`的`value`属性，那么Vue收集的是**checked值**(勾选or未勾选，是**布尔值**)

- (2)**配置了**`input`的`value`值时

  - 如果`v-model`的值是**非数组**属性，那么收集的还是**checked值(布尔值)**

  - 如果`v-model`的值是**数组**属性，那么收集的是`value`值组成的**数组**



### v-model的三个修饰符

- **lazy**：失去焦点后再收集数据
- **number**：输入字符串转换为有效数字
- **trim**：过滤输入字符串的首尾空格



### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>收集表单数据</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id='root'>
        <form @submit.prevent="demo"><!--prevent阻止默认事件刷新页面-->
            <label for="zh">账号：</label><input id="zh" type="text" v-model.trim="userInfo.account" /><br />
            密码：<input type="password" v-model.trim="userInfo.pswd" /><br/>
            年龄：<input type="number" v-model.number="userInfo.age"><br/>
            性别：
            男<input type="radio" name="sex" value="male" v-model="userInfo.sex"><!--需设置默认的value值用于区分男女-->
            女<input type="radio" name="sex" value="female" v-model="userInfo.sex"><!--需设置默认的value值用于区分男女-->
            <br/>
            爱好：
            学习<input type="checkbox" value="study" v-model="userInfo.hobby">
            锻炼<input type="checkbox" value="duanlian" v-model="userInfo.hobby">
            写代码<input type="checkbox" value="code" v-model="userInfo.hobby">
            <br/>
            所属校区
            <select v-model='userInfo.ctiy'>
                <option value="">请选择校区</option>
                <option value="beijing">北京</option>
                <option value="shanghai">上海</option>
                <option value="shenzhen">深圳</option>
            </select>
            <br/>
            其他信息：<textarea v-model.lazy='userInfo.other'></textarea><!--lazy脱离焦点后才会收集数据-->
            <br/>
            <input type="checkbox" v-model="userInfo.agree">阅读并接受协议<a href="https://www.baidu.com">《用户协议》</a>
            <br/>
            <button >提交</button>
        </form>
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            userInfo:{
                account:"",
                pswd:"",
                age:"",
                sex:"male",//接受单选框的value值：male或female
                hobby:[],
                ctiy:'',
                other:'',
                agree:false,
            },
        },
        methods: {
            demo(){
                console.log(JSON.stringify(this.userInfo))
            }
        },
    })
</script>
</html>
```

**演示效果**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132598.jpg)



## 十三、过滤器

### 定义

对要显示的数据进行特定格式化后再显示（适用于一些简单处理逻辑）

### 语法

- 注册过滤器：`Vue.filter(name,callback) `或`new Vue(fulters:{})`
- 使用过滤器: `{{xxx | 过滤器名称}} `或` v-bind:属性="xxx | 过滤器名称"`

### 备注

过滤器也可以接受额外参数，多个**过滤器可串联**；过滤器并**不改变原本的数据**，是产生新的对应数据

### 案例演示

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>过滤器</title>
    <!--引入Vue-->
    <script src="../js/vue.js"></script>
    <!--引入dayjs-->
    <script src="../js/dayjs.min.js"></script>
</head>
<body>
    <div id='root'>
        <h2>显示格式化后的时间</h2>
        <!--计算属性实现-->
        <h3>现在是：{{fmtTime}}</h3>
        <!--methods实现-->
        <h3>现在是：{{getFmtTime()}}</h3>
        <!--过滤器实现-->
        <h3>现在是：{{time | timeFormater}}</h3>
        <!--过滤器实现（传参）-->
        <h3>现在是：{{time | timeFormater('YYYY_MM_DD') }}</h3>
        <!--过滤器实现（传参与连接）-->
        <h3>现在是：{{time | timeFormater('YYYY_MM_DD') | mySlice }}</h3>
    </div>
    <div id="root2">
        <h2>{{msg | mySlice}}</h2>
        <!--指令语法v-bind使用过滤器-->
        <h2 :x="msg | mySlice">你好，世界</h2>
    </div>
</body>
<script>
    //全局过滤器
    Vue.filter('mySlice', function(value){
            return value.slice(0,4) + '年' //截取前4位字符
    })
    //vue实例
    const vm = new Vue({
        el:'#root',
        data:{
            time:1644132679607, //时间戳
        },
        computed: {
            fmtTime(){
                return dayjs(this.time).format("YYYY年MM月DD日 HH:mm:ss")
            }
        },
        methods: {
            getFmtTime(){
                return dayjs(this.time).format("YYYY年MM月DD日 HH点mm分ss秒")
            }
        },
        filters:{ // 局部过滤器
            timeFormater:function(value,format="YYYY年MM月DD日 HH:mm:ss"){
                return dayjs(value).format(format)
            },
        },
    })
    //vue第二个实例
    new Vue({
        el:'#root2',
        data:{
            msg:'hello,world!',
        }
    })
</script>
</html>
```

**演示效果**

![image](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202212122132599.jpg)