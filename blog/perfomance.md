# 【技术研究】vue项目的性能优化之路

撰稿人：项伟平

我最近也经常面试外包同事。面试的时候，总会有个问题，“你说一下性能优化的手段”。百分之八十的人都会说，压缩js和css之类的。显然这些都是必须做的，而且已经根本不是主要的性能优化的关键点。如果你只会说这些，只能说明你是个过时的前端工程师。

性能优化过程中，我们需要面对的更多是DMS解析过程，服务器缓存和浏览器缓存机制。

## gzip压缩

在所有的web前端项目，静态资源基本都放在cdn上，gzip的压缩是非常必要的，它直接改变了js文件的大小，减少两到三倍。

参考[加速nginx: 开启gzip和缓存](https://www.darrenfang.com/2015/01/setting-up-http-cache-and-gzip-with-nginx/)，nginx的gzip配置非常简单，在你对应的域名底下，添加下面的配置，重启服务即可。`gzip_comp_level`的值大于2的时候并不明显，建议设置在1或者2之间。

```bash
# 开启gzip
gzip on;
# 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
gzip_min_length 1k;
# gzip 压缩级别，1-10，数字越大压缩的越好，也越占用CPU时间，后面会有详细说明
gzip_comp_level 2;
# 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
# 是否在http header中添加Vary: Accept-Encoding，建议开启
gzip_vary on;
# 禁用IE 6 gzip
gzip_disable "MSIE [1-6]\.";
```

## 服务器缓存

为了提高服务器获取数据的速度，nginx缓存着静态资源是非常必要的。如果是测试服务器对html应该不设置缓存，而js等静态资源环境因为文件尾部会加上一个hash值，这可以有效实现缓存的控制。

```bash
location ~* ^.+\.(ico|gif|jpg|jpeg|png)$ { 
  access_log   off; 
  expires      30d;
}
location ~* ^.+\.(css|js|txt|xml|swf|wav)$ {
  access_log   off;
  expires      24h;
}
location ~* ^.+\.(html|htm)$ {
  expires      1h;
}
```

## 浏览器缓存

浏览器缓存是通过html的头文件中的meta来控制。`http-equiv`是一个专门针对http的头文件，可以向浏览器传回一些有用的信息。与之对应的content，是各个参数的变量值。

### HTTP 1.0

在HTTP1.0中通过**Pragma**控制页面缓存，可以设置为**Pragma**或**no-cache**。在不让浏览器或中间缓存服务器缓存页面的情况下，通常设置的值为no-cache，不过这个值不这么保险，通常还加上**Expires**置为0来达到目的。**Expires**可以用于设定网页的到期时间。一旦网页过期，必须到服务器上重新传输获取新的页面信息。PS：内容必须使用GMT的时间格式。

```html
<meta http-equiv="Pragma" content="no-cache">
<meta http-equiv="Expires" content="0">
```

### HTTP 1.1

在HTTP1.1中通过**Cache-Control**控制页面缓存，可以设置为**no-cache**、**private**、**no-store**、**max-age**或**must-revalidate**等，默认为private。

```html
<meta http-equiv="Cache-Control" content="no-cache">
```

- public 浏览器和缓存服务器都可以缓存页面信息
- private 对于单个用户的整个或部分响应消息，不能被共享缓存处理。这允许服务器仅仅描述当用户的部分响应消息，此响应消息对于其他用户的请求无效
- no-cache 浏览器和缓存服务器都不应该缓存页面信息
- no-store 请求和响应的信息都不应该被存储在对方的磁盘系统中，不使用缓存
- must-revalidate 对于客户机的每次请求，代理服务器必须想服务器验证缓存是否过时
- max-age 客户机可以接收生存期不大于指定时间（以秒为单位）的响应
- min-fresh 客户机可以接收响应时间小于当前时间加上指定时间的响应

### Last-Modified和Etags

Last-Modified服务器端文件响应头，描述最后修改时间。当浏览器再次进行请求时，会向服务器传送If-Modified-Since报头，询问时间点之后资源是否被修改过，从而区分200和304的请求状态码，304则选择浏览器缓存。

Etags不同的是，ETag是根据实体内容生成一段hash字符串，是标识资源的状态。它由服务端产生来判断文件是否有更新。

参考资料：

- [HTTP缓存深入实践](http://www.jianshu.com/p/32733a356acf)
- [掌握缓存，不再让你蓝瘦香菇](http://www.jianshu.com/p/13505e90c686)


## JS分包

前面说的两部分都可以说是偏后端的活，如果真的从前端方面考虑，我们可能会分包入手。正因为vue的脚手架搭建的项目，webpack的配置当中就包含了压缩js，css和html的压缩。所以，当我们的单页面越做越大的情况下，首要的一步就是分包。

vue官方称gzip压缩后只有20kb，但是你普通的打包方式也有100kb，再加上你自己的逻辑代码，整体包的体积也挺大的。直接影响首屏页面加载的效率。下面介绍一下两种分包的方法：

- external 把包排除，使用cdn资源
- dll 打包

#### vue，vuex和vue-router

在webpack配置文件中external设置，把这三个场用包排除这个操作，主要是把这三个包从vendor.js分开。

最后当然需要在html标签上添加上额外cdn的link或者script。

#### DLL打包

这种打包方式专门引用webpack官方的**DllPlugin**和**DllReferencePlugin**。DllPlugin会生成一个dll包的代码指纹manifest，管理额外的打包。而在项目生成的过程中，DllReferencePlugin会参考manifest的内容去打包。额外生成的js文件应该被放置在vue项目的文件当中的static文件夹底下，以便于代码部署。

参考[PaicFE／vue-multi](https://github.com/PaicFE/vue-multi)中的配置文件`webpack.dll.config.js`的写法。

## 预加载

预加载技术（prefetch）是在用户需要前我们就将所需的资源加载完毕，不是所有浏览器都支持，主要是Chrome浏览器。

> DNS prefetch 分析这个页面需要的资源所在的域名，浏览器空闲时提前将这些域名转化为 IP 地址，真正请求资源时就避免了上述这个过程的时间。----[HTML5 prefetch](http://www.jianshu.com/p/7f58ddfc1392)

由于域名转换成为IP的过程是非常耗时的一个过程，DNS prefetch可以减少这部分的时间。

```html
<meta http-equiv='x-dns-prefetch-control' content='on'>
<link rel='dns-prefetch' href='http://g-ecx.images-amazon.com'>
<link rel='dns-prefetch' href='http://z-ecx.images-amazon.com'>
<link rel='dns-prefetch' href='http://ecx.images-amazon.com'>
<link rel='dns-prefetch' href='http://completion.amazon.com'>
<link rel='dns-prefetch' href='http://fls-na.amazon.com'>
```

预加载也可以对某个静态资源起到专门的作用。

```html
<link rel='subresource' href='libs.js'>
```

预渲染（pre-rendering）是这个页面会提前加载好用户即将访问的下一个页面。

```html
<link rel='prerender' href='http://www.pagetoprerender.com'>
```

## vue组件keep-alive

如果你做用一个大型web的spa的时候，你有很多router，对应的是很多个页面。在页面的快速切换中，为了保证页面加载的效率，除了缓存机制之外，vue的keep-alive组件可以帮的上忙。

它会把组件保存在浏览器内存当中，方便你快速切换。

百度的[lavas](https://github.com/lavas-project/lavas)项目中就在vue-router当中使用keep-alive的组件，用它包裹着router-view。使用了keep-alive的组件内的数据将会保留，“是否需要重新同步数据”可以在vue-router的钩子中路由所带的参数执行判断。

## Promise请求

es6的其中一个特性就是原生支持**promise**。在这里，我先不说异步编程里的**generator**和**aync/await**的属性。它们功能的实现都是基于promise。

Promise的特点在于：

- 减少回调函数
- 串并行处理
- 代码的优雅

这里特别讲一下，ES6在性能优化上可以使用promise或者async／await去压缩请求时间。在过去，很多jquery的页面在调用接口请求都是一个接口等另一个接口，串行执行所有请求，最后在完成最后的回调函数，如此类推。这样的写法会直接导致“回调地狱”。即使你用`vue-resource`，我也review到非常多的“回调地狱”的情况。为了从根本上解决这个问题并提高开发效率，我建议优先使用promise。（async／await不急着投入使用），考虑到还有很多同事还在高效地开发业务代码。

现在的`vue-resource`已经支持promise的写法，为了更好地让技术向后发展，我建议将[pagekit/vue-resource](https://github.com/pagekit/vue-resource)替换称为[mzabriskie/axios](https://github.com/mzabriskie/axios)和[webmodules/jsonp](https://github.com/webmodules/jsonp)。`axios`是可以同时满足服务端和浏览器端，同构的写法有助于以后将技术栈往SSR（服务端渲染）发展。`jsonp`这个库则是为了兼容jsonp的请求需要，需要对它进行了promise的封装。

```javascript
export function getJsonp(urlHost, key, data, _params) {
  return new Promise((resolve, reject) => {
    let url = urlHost + key;
    if (data) url += `?${querystring.stringify({ ...data, temp: new Date().getTime() })}`;
    const params = _params || { timeout: 15000 };
    if (!params.timeout) params.timeout = 15000;
    jsonp(url, params, (err, res) => {
      if (err) {
        reject(err);
      } else {
        resolve(res);
      }
    });
  });
}
```

Promise的使用需要避免以下的写法，

```javascript
promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```

尽量使用链式写法，

```javascript
promise.then(function(value) {
  // step1
}).then(function(value){
  // step2
}).catch(function(value){
  // failure
})
```

并行的操作主要是`Promise.all()`，它可以将Promise操作的数组并行执行完成然后在进行串行的操作。`Promise.race()`则是返回并行请求中最先返回的请求的那个结果。它们的使用可以有效地压缩数据获取的时间。


## 扩展阅读

- [html的meta总结，html标签中meta属性使用介绍](http://www.haorooms.com/post/html_meta_ds)
- [使用 webpack 3 构建高性能的应用程序](http://www.css88.com/archives/7661#more-7661)
