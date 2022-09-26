# SpringBoot的一些东西

:clock2: 2022年5月22日14:04:27

Controller层的**@ResquesBody**注释，如果接收参数为表单格式的话只能用`Map`或`string`形参接收，使用对象形式接收会报错。想使用对象形式接收可以删除**@ResquesBody**注解，或前端传递`json`数据。



:clock3: 2022年5月23日15:11:13

1. SpringBoot默认使用 `jackjson` 解析数据。
2. 在将实体类数据解析传递给前端时，想要忽视掉某些实体属性可以加上 `@JsonIgnore` 注解。
3. 前端数据传递给实体时，如果前端属性名与实体属性名不同，可以在实体属性上加上 `@JsonProperty("前端属性名")` 来对接。
