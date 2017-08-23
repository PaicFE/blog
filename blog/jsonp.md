# 【技术分享】 JSONP和跨域

撰稿人：项伟平

跨域是一位前端工程师经常面对的问题。

面试的时候，面试官会问你一个问题，“解释一下什么是跨域？”，其实百分之八十的面试者都会回答jsonp。如果你回答了jsonp，又会出现很多问题，例如“jsonp的实现原理”，“不用jquery怎么实现jsonp”，“jsonp的安全问题”。如果你回答CORS，也会出现很多问题，例如“CORS的实现原理”，“CORS较jsonp的优点在哪里”，“为什么CORS不用jsonp”。

## jsonp是什么？

### jsonp的实现原理

jsonp是一个很老的技术，为啥现在还在使用？很大一部分原因是因为相对来说配置相对简单，后端工程师偷了懒。实现原理用自己的话说就是使用HTML的动态脚本的漏洞。在js端构建一个`<script></script>`，通过一个callback参数构建一种关系，你想得到的参数通过函数回调完成。

为了生动形象地展示一下jsonp，我用koa写一个简单的实现。

```javascript
const Koa = require('koa');
const querystring = require('querystring');
const app = new Koa();

const main = ctx => {
   var data = {
    "name": "Monkey"
    };
    var qs = querystring.parse(ctx.request.url.split('?')[1]);
    data = JSON.stringify(data);
    var callback = qs.callback+'('+data+');';
    ctx.response.body = callback;
};

app.use(main);
app.listen(3000);
```

### 不用jquery怎么实现jsonp

[jquery](https://github.com/jquery/jquery)或者[zepto](https://github.com/madrobby/zepto)实现jsonp跨域操作非常简单，在设置`dataType`字段时设置为jsonp即可。但是，如果没有了jquery，怎么去实现jsonp，你可能会选择去创建一个动态脚本。把参数和callback拼在url上。

如果你使用的是Vue的项目，你应该不会使用jquery，那你怎么使用jsonp？

- [vue-resource](https://github.com/vuejs/vue-resource) 是支持jsonp
- [axios](https://github.com/mzabriskie/axios) 不支持jsonp

axios因为在node.js端和browser端同时都支持，而且长期支持。axios的维护者并不打算支持jsonp，正因为它存在着安全性隐患，如果你希望兼容jsonp的接口，可以使用这个库[webmodules/jsonp](https://github.com/webmodules/jsonp)替代部分的功能。vue-resource相对来说，曾经被官方放弃，从设计方面vue-resource相对来说考虑得比较全面，全面兼容jsonp。

### jsonp的安全问题

从[webmodules/jsonp](https://github.com/webmodules/jsonp)这个库，我们可以看到它的实现方式。jsonp的漏洞在于callback函数，它可以植入脚本影响后端代码。[JSONP 安全攻防技术](http://blog.knownsec.com/2015/03/jsonp_security_technic/)里面介绍了在使用jsonp存在的安全性问题，一方面是json挟持，另一方面是callback植入脚本的漏洞。解决漏洞的方法可以在头文件中加上`Content-Type: application/json`，但是在老版本的ie还是会出现编码安全问题，还需要限制编码`charset=utf-8`。

> 1. 严格安全的实现 CSRF 方式调用 JSON 文件：限制 Referer 、部署一次性 Token 等。
> 2. 严格安装 JSON 格式标准输出 Content-Type 及编码（ Content-Type : application/json; charset=utf-8 ）。
> 3. 严格过滤 callback 函数名及 JSON 里数据的输出。
> 4. 严格限制对 JSONP 输出 callback 函数名的长度(如防御上面 flash 输出的方法)。
> 5. 其他一些比较“猥琐”的方法：如在 Callback 输出之前加入其他字符(如：/**/、回车换行)这样不影响 JSON 文件加载，又能一定程度预防其他文件格式的输出。还比如 Gmail 早起使用 AJAX 的方式获取 JSON ，听过在输出 JSON 之前加入 while(1) ;这样的代码来防止 JS 远程调用

## CORS是什么？

CORS即是跨域资源共享（全称为Cross-origin resource sharing）。它的实现需要后端配合，可以针对域名，端口和请求方式等作出限制。这样保证了跨域请求的安全性。阮一峰老师的[跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)详细地介绍了两类请求，简单请求（simple request）和非简单请求（not-so-simple request）。

### CORS的实现原理

CORS的实现需要浏览器和服务器同时支持，IE浏览器不能低于IE10。所以对于老版本的IE需要jsonp接口的兼容。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样，主要是服务器配置的附加头文件。

下面是非简单请求CORS在express上的具体实现方式：

```javascript
app.all('*', function(req, res, next) {
    res.header("Access-Control-Allow-Origin", "http://localhost:3333");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
    res.header("X-Powered-By",' 3.2.1')
    res.header("Content-Type", "application/json;charset=utf-8");
    next();
});
```

- Access-Control-Allow-Origin 必需，是对域名的限制
- Access-Control-Allow-Methods 必需，是支持跨域请求的方法
- Access-Control-Allow-Credentials 是否允许发送coookie
- Access-Control-Allow-Headers 服务器支持的所有头信息字段
- Access-Control-Max-Age 预检的有效时间

### CORS比jsonp的优点在哪里

JSONP只支持GET请求，CORS支持所有类型的HTTP请求。相比之下，CORS的兼容性稍逊一筹，但是它在安全性上更有保障，如果是新版接口尽可能使用CORS方式跨域。

