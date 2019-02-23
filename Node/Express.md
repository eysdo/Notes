# Server 框架

## Express

下载:`npm install express --save`

### 正则表达式

#### 创建方式

```javascript
var reg = /pattern/flags
// 字面量创建方式
var reg = new RegExp(pattern,flags);
//实例创建方式
// pattern:正则表达式  
// flags:标识(修饰符)
// 1. i 忽略大小写匹配
// 2. m 多行匹配，即在到达一行文本末尾时还会继续寻常下一行中是否与正则匹配的项
// 3. g 全局匹配 模式应用于所有字符串，而非在找到第一个匹配项时停止
```

> 1. 字面量创建方式不能进行字符串拼接，实例创建方式可以 
> 2. 字面量创建方式特殊含义的字符不需要转义，实例创建方式需要转义

#### 元字符

+ 特殊含义

  ```javascript
  // \d : 0-9之间的任意一个数字  \d只占一个位置
  // \w : 数字，字母 ，下划线 0-9 a-z A-Z _
  // \s : 空格或者空白等
  //  . : 除了\n之外的任意一个字符
  //  \ : 转义字符
  //  | : 或者
  // () : 分组
  // \n : 匹配换行符
  // \b : 匹配边界 字符串的开头和结尾 空格的两边都是边界 => 不占用字符串位数
  // ^ : 限定开始位置 => 本身不占位置
  // $ : 限定结束位置 => 本身不占位置
  // [a-z] : 任意字母 []中的表示任意一个都可以
  // [^a-z] : 非字母 []中^代表除了
  // [abc] : abc三个字母中的任何一个 [^abc]除了这三个字母中的任何一个字符
  ```

+ 量词

  ```javascript
  // * : 0到多个
  // + : 1到多个
  // ? : 0次或1次 可有可无
  // {n} : 正好n次；
  // {n,} : n到多次
  // {n,m} : n次到m次
  ```

### 中间件

Express使用中间件Web请求一个一个处理，并通过其中一个中间件返回



### 路由

下面是一个简单的路由页面代码。

```javascript
const express = require("express")
// 调用其中的Router函数，这个函数返回一个路由对象
const router = express.Router()
// 拿到数据模型
const User = require("../../models/users/User")
// get方法测试接口 
router.get('/test', (req, res) => {
  res.json({msg: "api works"})
})
module.exports = router;
```

这里没有什么复杂的代码，这个时候我们需要在服务器上使用这个被暴露的路由对象，使他在服务器上生效。

```javascript
// ...
// 引入users接口配置好的路由对象
const users = require("./routes/api/users");
const bodyParser = require("body-parser");
// ...
// 在服务器上使用这两个中间件来解析请求。
app.use(bodyParser.urlencoded({extended: false}));
app.use(bodyParser.json());
// 使用这个接口对象，其对应的url是localhost:5000/api/users
// 这个时候我们访问localhost:5000/api/users/test就可以接受到返回的数据
app.use("/api/users", users);
// ...
```

上部分代码中，我们省略了包引入，服务器生成，数据库链接，以及服务器监听的代码。

### 静态文件

```javascript
// __dirname 表示当前文件所在的目录的绝对路径
// __filename 表示当前文件的绝对路径
```

`Express` 提供了内置的中间件 `express.static` 来设置静态文件如：图片， `CSS, JavaScript` 等，你可以使用 `express.static` 中间件来设置静态文件路径。

`app.use(express.static('public'));`

###  源码分析

```javascript
const http = require('http')
// 合并对象的第三方模块
const mixin = require('merge-descriptors')
module.exports = function createServer() {
  const app = function (req, res) {
    res.end('Response From Server')
  }
  mixin(app, proto, false)
  // 调用过后会得到返回的对象 
  return app
}
const proto = Object.create(null)
proto.listen = function (port) {
  const server = http.createServer(this)
  return server.listen.apply(server, arguments)
}
```



## 后端工程注意事项

1. 数据库里的数据不要直接删掉，如果有此业务，添加一个标记字段，对其进行筛选返回，也就是我们常说的假删
