# VUE项目使用.env配置多种环境

> 在Vue项目中，可以使用.env文件配置全局环境变量，就如同SpringBoot项目的application文件一样



> #### 第一步，创建多个环境配置文件

- .env文件是默认的全局配置环境，无论是什么环境都会加载

- .env.development 文件是开发环境，使用`npm run server`时默认会加载此配置文件

- .env.production文件是生产环境，使用`npm run build`时默认会加载此配置文件

- .env.自定义名称 根据自身要求，给此配置文件取名，前面一定是.env.

  ![image-20220315152409864](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220315152409864.png)





> #### 第二步，编写内容

- .env.prod中的内容

![image-20220315152827939](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220315152827939.png)

> 在配置文件中定义自定义变量时，一定要以 ‘VUE_APP_'开头，否则Vue无法读取此变量



> #### 第三步，在package.json文件中编写启动命令

除了.env文件会自动被加载外，其他环境需要手动的添加加载指令

![image-20220315153231978](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220315153231978.png)

```json
"scripts": {
    "serve": "vue-cli-service serve", 
    "serve:prod": "vue-cli-service serve --mode prod",
    "build": "vue-cli-service build",
    "build:dev": "vue-cli-service build --mode development",
    "lint": "vue-cli-service lint"
  }
```

> **"serve": "vue-cli-service serve"** 此配置会自动加载.env.development配置文件，所以不用写 --mode
>
> **"serve:prod": "vue-cli-service serve --mode prod"** 使用npm run serve:prod指令运行时，会加载.env.prod配置文件
>
> **"build": "vue-cli-service build"** 使用npm run build指令时，会自动加载.env.production配置文件，但我这个项目中的生产环境名为prod，vue并不会自动加载prod



> #### 第四步，输出环境变量并演示

- 输出环境变量

  ![image-20220315155226059](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220315155226059.png)

- **npm run serve:prod**

  ![image-20220315154636663](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220315154636663.png)

![image-20220315154801807](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220315154801807.png)



- **npm run serve**

  ![image-20220315154856539](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220315154856539.png)

![image-20220315155045279](C:\Users\15675\AppData\Roaming\Typora\typora-user-images\image-20220315155045279.png)