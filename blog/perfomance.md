#【技术研究】vue项目的性能优化之路

我最近也经常面试外包同事。面试的时候，总会有个问题，“你说一下性能优化的手段”。百分之八十的人都会说，压缩js和css之类的。其实这些都是必须做的，而且已经根本不是主要的性能优化的关键点。

性能优化过程中，我们需要面对的更多是请求的DMS过程，服务器缓存，浏览器缓存机制。

## gzip压缩

在所有的web前端项目，静态资源基本都放在cdn上，gzip的压缩是非常必要的，它直接改变了js文件的大小，减少两到三倍。

参考[加速nginx: 开启gzip和缓存](https://www.darrenfang.com/2015/01/setting-up-http-cache-and-gzip-with-nginx/),nginx的gzip配置非常简单，在你对应的域名底下，添加下面的配置，重启服务即可。`gzip_comp_level`的值大于2的时候并不明显，建议设置在1或者2之间。

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

浏览器缓存是通过html的头文件中的meta。


## 分包

前面说的两部分都可以说是偏后端的活，如果真的从前端方面考虑，我们可能会分包入手。正因为vue的脚手架搭建的项目，webpack的配置当中就包含了压缩js，css和html的压缩。所以，当我们的单页面越做越大的情况下，首要的一步就是分包。

vue官方称gzip压缩后只有20kb，但是你普通的打包方式也有100kb，再加上你自己的逻辑代码，整体包的体积也挺大的。直接影响首屏页面加载的效率。下面介绍一下两种分包的方法：

- external 把包排除，使用cdn资源
- dll 打包

#### vue，vuex和vue-router

这个操作主要是把这三个包从vendor.js分开，在webpack配置文件中external设置，把这三个场用包排除。

最后当然需要在html标签上添加上link或者script。

#### DLL打包

参考[PaicFE／vue-multi](https://github.com/PaicFE/vue-multi)中的dll的写法，专门把配置文件写在`webpack.dll.config.js`。

## 预加载



## keep-alive

## Promise请求

## 避免重绘重排




[使用 webpack 3 构建高性能的应用程序](http://www.css88.com/archives/7661#more-7661)