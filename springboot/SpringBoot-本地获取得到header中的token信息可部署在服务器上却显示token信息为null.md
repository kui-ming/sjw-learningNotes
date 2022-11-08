# SpringBoot-本地获取得到header中的token信息可部署在服务器上却显示token信息为null

>今天发现部署在服务器上的项目一直提示我token验证失败，看日志发现token值是空的。我马上在本地进行了测试，可本地却是正常的，心态顿时不好了。

#### 原因

**nginx会默认把header中带`_`下划线的参数删除**。而我刚好使用了nginx进行代理，更刚好设置了一个`user_token`，这就麻烦了，它直接把我的token给删掉了，于是后台一直提示验证失败。

#### 解决版本

直接把`user_token`换成了`user-token`