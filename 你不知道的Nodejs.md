# 你不知道的 Nodejs

### node自动化测试：
```
使用 mocha 或 supertest库
```

### Docker快速部署发布Node.js:
```
1. 通过 docker pull node 安装镜像，启动node镜像容器安装pm2管理包
2. node容器运行 node 后端服务，通过 nginx 或 openresty 代理后端服务端口
3. 可使用 Jenkins 持续集成的方式自动从 GitHUb 获取 node 后端提交更新的代码自动部署发布服务
```

### express将session存储到数据库：
```js
const express = require("express")
const session = require("express-session")
const MongoStore  = require("connect-mongo")(session)
const app = express()

app.use(session({
  secret: 'this is a string key',   // 可以随便写。 一个 String 类型的字符串，作为服务器端生成 session 的签名
  name:'session_id',                // 保存在本地cookie的一个名字 默认connect.sid  可以不设置
  resave: false,                    // 强制保存 session 即使它并没有变化,。默认为 true。建议设置成 false。
  saveUninitialized: true,          // 强制将未初始化的 session 存储。  默认值是true  建议设置成true
  cookie: {
      maxAge:1000*30*60             // 过期时间

  },                                // secure:true  https这样的情况才可以访问cookie
  rolling:true,                     //在每次请求时强行设置 cookie，这将重置 cookie 过期时间（默认：false）
  store:new MongoStore({
      url: 'mongodb://127.0.0.1:27017/shop',  // 数据库的地址  shop是数据库名
      touchAfter: 24 * 3600         // 通过这样做，设置touchAfter:24 * 3600，您在24小时内只更新一次会话，不管有多少请求(除了在会话数据上更改某些内容的除外)
  })
}))

// 扩展：如果是服务器集群，则需要将 session 存入可信任第三方仓库，防止存储session服务器挂机
```
### jwt和session架构的区别和优缺点:
```
session的认证流程如下：
  1. 用户输入其登录信息
  2. 服务器验证信息是否正确，并创建一个session，然后将其存储在数据库中
  3. 服务器为用户生成一个sessionId，返回setCookie将具有sesssionId的Cookie将放置在用户浏览器中
  4. 在后续请求中，会根据数据库验证sessionID，如果有效，则接受请求
  5. 一旦用户注销应用程序，会话将在客户端和服务器端都被销毁
优点：持久化，方便续签，使用方便有成熟的框架可开箱即用，配合redis方便前后端分离
缺点：session存储在服务器数据库开销大, 不方便跨域

jwt的认证流程如下：
  1. 用户输入其登录信息
  2. 服务器验证信息是否正确，并返回已签名的token
  3. token储在客户端，例如存在local storage或cookie中
  4. 之后的HTTP请求都将token添加到请求头里
  5. 服务器解码JWT，并且如果令牌有效，则接受请求
  6. 一旦用户注销，令牌将在客户端被销毁，不需要与服务器进行交互一个关键是，令牌是无状态的。后端服务器不需要保存令牌或当前session的记录。

优点：更安全，方便跨域， 方便前后端分离
缺点：一次性，到期前无法废弃，需要自配axois钩子
```