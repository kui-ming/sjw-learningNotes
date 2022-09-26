# JWT

[JWT官方网站](https://jwt.io/)

<br>

> ##### JWT由三部分组成

- Header：头部，用于描述该JWT的基本信息，例如类型和加密算法
- Payload(Cliaims)：有效载荷，是JWT的主体部分，包含一些列关于此JWT的信息和用户自定义的信息
- Signature：签名，由上面两部分经过Base64编码后加上签名秘钥后经过指定的不可逆算法计算得出

基本格式

`XXX.XXX.XXX`



> ##### 有效载荷(Payload)

```js
iss: jwt签发者
sub: jwt所面向，使用jwt的用户
aud: 接收jwt的一方
exp: jwt的过期时间，这个过期时间必须大于签发时间
nbf: 定义在指定时间之前，该jwt都是不可用的
iat: jwt的签发时间
jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击
```

