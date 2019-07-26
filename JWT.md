## JWT是一种认证协议
JSON Web Token (JWT)
它是 JSON 结构的 token，是访问资源的凭证。



## OAuth2是一种授权框架
是一种安全的授权框架，提供了一套详细的授权机制。它详细描述了系统中不同角色、用户、服务前端应用（比如API），以及客户端（比如网站或移动App）之间怎么实现相互认证。



时间投入  
OAuth2是一个安全框架，描述了在各种不同场景下，多个应用之间的授权问题。有海量的资料需要学习，要完全理解需要花费大量时间。甚至对于一些有经验的开发工程师来说，也会需要大概一个月的时间来深入理解OAuth2。 这是个很大的时间投入。  
相反，JWT是一个相对轻量级的概念。可能花一天时间深入学习一下标准规范，就可以很容易地开始具体实施。


### JWT简介
* 无状态的认证方案



### JWT结构

![](/images/jwt.png)

由三部分组成：
1. header  
描述 元信息
```js
{    
    "typ": "JWT", // token类型
    "alg": "HS256" // 使用哪一种 哈希算法 来创建 signature
}
```
2. payload  
携带你希望 向服务端传递 的信息
可以包含特殊属性iss,sub,aud,exp,iat,jti等
```js
{ 
  “sub”：“90129920”，
  “uuid”：“sfgdsrfg434fdt535fg”，
  “iat”：1516239022，// 指定创建令牌的时间
  “exp”：1545926973，// 指定令牌变为无效的时间
}
// 标准注册声明
iss: jwt签发者
sub: jwt所面向的用户
aud: 接收jwt的一方
exp: jwt的过期时间，这个过期时间必须要大于签发时间
nbf: 定义在什么时间之前，该jwt都是不可用的.
iat: jwt的签发时间
jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击
```

3. signature  
对前两部分的签名，防止数据篡改签名，由从接口服务端拿到密钥secret，header，payload几者，用哈希算法计算得到  
private key保存在服务端，验证Signature时要用  
symmetrical algorithm  
asymmetrical algorithm:the private key is used to sign the token and a public key is used for verifying the tokens
```js
signature=sha256(base64URL(head)+'.'+base64URL(body),'MY_PRIVATE_KEY')
```

最后使用 Base64URL 算法将header，payload转成字符串，三者用 . 进行拼接

### JWT认证步骤
![](/images/jwtAuth.png)
1. 客户端post用户名、密码
2. 服务器认证后，生成JWT，发回给客户端
3. 客户端将JWT存储在本地（local storage or session storage），包括姓名、到期时间等信息
4. 客户端每次请求时带上JWT
5. 服务端验证JWT的合法性（根据Signature）







### jwt如何log out （注销、修改密码）
有以下几个方法可以做到失效 JWT token：

1. 维护一个 token 黑名单，失效则加入黑名单中。
2. 在 JWT 中增加一个版本号字段，失效则改变该版本号。DB中userId和版本号对应存储。
3. 将 token 存入 DB（如 Redis）中，失效则删除；但增加了一个每次校验时候都要先从 DB 中查询 token 是否存在的步骤，而且违背了 JWT 的无状态原则（这不就和 session 一样了么？）。
4. 在服务端设置加密的 key 时，为每个用户生成唯一的 key，失效则改变该 key。（每个用户一个密钥）

### jwt如何续签
1. 每次请求刷新 jwt
2. 只要快要过期的时候刷新 jwt
3. 完善 refreshToken
借鉴 oauth2 的设计，返回给客户端一个 refreshToken，允许客户端主动刷新 jwt。一般而言，jwt 的过期时间可以设置为数小时，而 refreshToken 的过期时间设置为数天。  
（但是在并发的时候也会出现问题，如果前一个请求刷新了Token，后面的请求使用了一个旧的Token像服务请求数据，这个时候请求会被拒绝。不过可以通过设置一个宽限时间，在Token刷新后，如果旧Token仍处于刷新宽限时间内，就放行。）
4. 使用 redis 记录独立的过期时间
为了解决续签问题，我们在 redis 中单独会每个 jwt 设置了过期时间，每次访问时刷新 jwt 的过期时间，若 jwt 不存在与 redis 中则认为过期。


### jwt特点：
1. 不需要在服务端保留用户的会话信息，它易于应用的扩展（可替代Session）
2. 可防护CSRF攻击(Cross-site request forgery)
3. payload不能保存敏感信息，要用https传输
4. 跨语言支持


### jwt使用场景
1. 一次性验证（密码修改、邮箱验证）  
比如用户注册后需要发一封邮件让其激活账户，通常邮件中需要有一个链接，这个链接需要具备以下的特性：能够标识用户，该链接具有时效性（通常只允许几小时之内激活），不能被篡改以激活其他可能的账户。

### 关于password
Passwords are never saved in the database.  
uer login: password+salt ---(hash algorithm)---> a hash  
created hash === stored hash  ====> password matched  


<br>

#### 参考：
jwt库：https://jwt.io/#libraries  
http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html  
https://www.jianshu.com/p/3c3de39ff8f0  
https://medium.com/swlh/a-practical-guide-for-jwt-authentication-using-nodejs-and-express-d48369e7e6d4  
jwt失效：https://medium.com/devgorilla/how-to-log-out-when-using-jwt-a8c7823e8a6  
jwt失效：https://segmentfault.com/q/1010000010043871  
