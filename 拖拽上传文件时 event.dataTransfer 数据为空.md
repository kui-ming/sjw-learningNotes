# 拖拽上传文件时 `event.dataTransfer` 数据为空

:date:2022年5月7日16:46:51

##### 原因

​	据说是一个浏览器bug，在`drop` 的回调函数中加上`var files = e.dataTransfer.files;` 即可用变量`files` 获取到文件数据