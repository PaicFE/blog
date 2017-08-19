# JSONP和跨域

跨域是一位前端工程师经常面对的问题。

面试的时候，面试官会问你一个问题，“解释一下什么是跨域？”，其实百分之八十的面试者都会回答jsonp。如果你回答了jsonp，又会出现很多问题，例如“jsonp的实现原理”，“不用jquery怎么实现jsonp”，“jsonp的安全问题”。如果你回答CORS，也会出现很多问题，例如“CORS的实现原理”，“CORS较jsonp的优点在哪里”。

## jsonp是什么？

### jsonp的实现原理

jsonp是一个很老的技术，为啥现在还在使用？很大一部分原因是因为相对来说配置相对简单，后端工程师偷了懒。实现原理用自己的话说就是使用HTML的动态脚本的漏洞。在js端构建一个`<script></script>`，通过一个callback参数构建一种关系，你想得到的参数通过函数回调完成。

### 不用jquery怎么实现jsonp

[jquery](https://github.com/jquery/jquery)或者[zepto](https://github.com/madrobby/zepto)实现jsonp跨域操作非常简单，在datatype中设置为jsonp即可。但是，如果没有了jquery，怎么去实现jsonp，你可能会选择去创建一个动态脚本。把参数和callback拼在url上。

如果你使用的是Vue的项目，你应该不会使用jquery，那你怎么使用jsonp？

- [vue-resource](https://github.com/vuejs/vue-resource)是支持jsonp
- [axios](https://github.com/mzabriskie/axios)不支持jsonp

axios因为在node.js端和browser端同时都支持，而且长期支持。axios的维护者并不打算支持jsonp，正因为它存在着安全性隐患，如果你希望兼容jsonp的接口，可以使用这个库[webmodules/jsonp](https://github.com/webmodules/jsonp)替代部分的功能。vue-resource相对来说，曾经被官方放弃，从设计方面vue-resource相对来说考虑得比较全面，全面兼容jsonp。

### jsonp的安全问题

从[webmodules/jsonp](https://github.com/webmodules/jsonp)这个库，我们可以看到它的实现方式。jsonp的漏洞在于callback函数，它可以植入脚本影响后端代码。

## CORS是什么？

CORS即是跨域资源共享（全称为Cross-origin resource sharing）。它的实现需要后端配合，可以针对域名，端口和请求方式等作出限制。这样保证了跨域请求的安全性。阮一峰老师的[跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)很好地介绍了两种

### CORS的实现原理

### CORS较jsonp的优点在哪里

