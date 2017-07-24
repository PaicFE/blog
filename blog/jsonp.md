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

axios因为在node.js端和browser端同时都支持，而且长期支持。vue-resource相对来说，曾经被官方放弃。axios的维护者并不打算支持jsonp，正因为它的安全性隐患。

### jsonp的安全问题



## CORS是什么？

### CORS的实现原理

### CORS较jsonp的优点在哪里

