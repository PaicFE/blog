# 【技术分享】Vue的选型和Webpack的入门

撰稿人：项伟平

## Vue的选型

![Vue全家桶](https://raw.githubusercontent.com/PaicFE/keynote/master/img/architech.png)

### Vue与Angular

一切要从Vue1.0讲起，回顾一下历史。Vue在项目初期命名为Angular Lite，这说明了我们Vue1.0和Angular之间的关系，它更多是一个精简版的Angular。它们的共同点在于它们都拥有**双向绑定**机制，指令。

- ng是完整mvvm框架，vue主要是view层
- 双向绑定基于模版编译规则，“脏”检查
- vue可以el对象进行实例化，组件化
- ng框架重，整个设计模式具有依赖注入的思想
- ng2断层式升级

### Vue与React

- React的es5与es6写法
- JSX和CSS IN JS的写法
- this 相关的奇怪行为
- React的生态链，学习成本


### Vue1.0与2.0的区别

- virtual dom
- 强调单向数据流，推荐vuex
- api区别，生命周期调整

##Vue的优势

双向绑定
Virtual-dom


 - <img src="./img/virtual-dom.jpg" width ="90%" alt="virtual-dom" align=center />

## 未来方向

未来的方向主要专注于性能优化上。主要是对h5页面在移动端的细小性能优化。


- [Vue SSR]()
- [Nuxt.js](https://nuxtjs.org/)

## 参考

- [vue与angular的区别](http://blog.csdn.net/qq_35844177/article/details/54915615)
- [对 virtual-dom 的一些理解](https://zhuanlan.zhihu.com/p/25630842)



#### vuex的单向数据流

<img src="./img/vuex.png" width ="70%" alt="vuex单向数据流" align=center />

----

#### vue的选型

轻量，易学，适用于移动端，生态

----

### webpack的入门

----

#### 横向对比

- ~~gulp~~
- browserify
- rollup
- **prepack**

----

#### 基本概念

- entry
- output
- loader
- plugin

----

#### loader

- less-loader
- sass-loader
- url-loader, file-loader
- babel-loader
- vue-loader, vux-loader

----

#### 例子





----

#### plugin

- UglifyJsPlugin
- CommonsChunkPlugin
- HtmlWebpackPlugin
- OccurrenceOrderPlugin

...

----

#### 多页面打包

```javascript
exports.getEntries = function (globPath) {
  let entries = {},
    basename,
    tmp,
    pathname;
  glob.sync(globPath).forEach((entry) => {
    basename = path.basename(entry, path.extname(entry));
    tmp = entry.split('/').splice(-3);
    pathname = tmp.slice(0, 2).join('/'); // 正确输出js路径
    entries[pathname] = entry;
  });

  return entries;
};
```

```javascript
utils.getEntries('./src/module/**/*.js')
```

----

#### 多页面打包

- 多个js入口（将entries改为数组）
- 多个html入口（基于HtmlWebpackPlugin）
- 单元测试调整

----

### 项目构建优化


- 异步请求（promise，async/await）
- 动态路由 (vue-router)
- SSR（server side rendering）


----

### 团队GITHUB

[PaicFE](https://github.com/PaicFE)



