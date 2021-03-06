## Express

下载:`npm install express --save`



### 中间件

app.use()是请求与响应中执行的一件事，按代码顺序来执行，use的第一个参数是虚拟目录。

```javascript
app.use("/用户选择性URL", (req, res, next) => {
    // ...some code 
    next() // 这个函数是放行开关，表示继续执行下一件事，也就是下一个use()函数
})
```

`Express`使用中间件`Web`请求一个一个处理，并通过其中一个中间件返回。

应用级中间件，路由级中间件，内置中间件，第三方中间件...

### 路由中间件

路往哪里走？监视`#/xxx`如果是没有刷新，局部内容改变此时是前端路由，如果点击a标签会刷新页面，响应一个新的页面回来，则是后端路由。既然我们是做服务器，我们来研究的就是后端路由。

```javascript
const express = require('express');
let server = express();
let router = express.Router();

router.get('/login', (req, res) => {
    res.end('login page');
})
server.use(router);
```

#### res扩展函数

1. `res.download('./xxx.txt')`下载文件
2. `res.json({})`响应json对象，由于end()只能响应字符串以及读文件的data(Buffer)
3. `res.send()`发送字符串数据，自动家文本类型
4. `res.sendStatus()`响应状态码

#### 渲染模板(art-template)

1. 下载`express-art-template`以及`art-template`
2. 配置：
   + 注册引擎：`app.engine('.html', express-art-template)`
   + 设置默认引擎：`app.set('view engines', '.html')`
3. 使用`res.render`(文件名, 数据对象)来对页面进行渲染

> express这一套，默认在当前`app.js`同级的`views`目录进行查找

#### 处理post请求

```javascript
const bodyParser = require('body-parser')
// 解析键值对application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }));
// 解析application/json
app.use(bodyParser.json())
```

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
// 
const http = require('http')
const url = require('url')
function createApplication () {
    let app = (req, res) => {
        let m = req.method.toLowerCase()
        let { pathName } = url.parse(req.url, true)
        for (let i = 0; i < app.routes.length; i++) {
            let { method, path, handle } = app.routes[i];
            if(method === m && pathName === path) {
                handle(req, res)
            }
        }
        res.end(`Cannot ${m} ${pathName}`)
    }
    app.routes = [];
    http.METHODS.forEach((method) => {
        method = method.toLowerCase()
        app.[method] = function (path, handler) {
            let layer = {
                method,
                path,
                handler
            }
            app.routes.push(layer);
    	}
    })
    app.listen = function() {
        let server = http.createServer(app);
        server.listen(...arguments)
    }
    return app;
}
moudule.export = createApplication;
```


## 后端工程注意事项

1. 数据库里的数据不要直接删掉，如果有此业务，添加一个标记字段，对其进行筛选返回，也就是我们常说的假删

