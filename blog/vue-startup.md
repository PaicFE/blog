# 【技术分享】Vue的选型和Webpack的入门

撰稿人：项伟平

## Vue的选型

![Vue全家桶](https://raw.githubusercontent.com/PaicFE/keynote/master/img/architech.png)

现在面试前端者都会在自己简历上写上精通这三个框架，但是仔细一问就三不知。在纵观几个大型前端框架后，我想得出一结论，问什么选择Vue框架呢？

下面我们对这三个框架进行对比。

### Vue与Angular

一切要从Vue1.0讲起，回顾一下历史。Vue在项目初期命名为Angular Lite，说明了我们Vue1.0和Angular之间的关系，它的初始目标是一个精简版的Angular。它们的共同点在于它们都拥有**双向绑定**机制和指令。Angular（后面简称为ng）和Vue的异同点如下：

- ng是一个完整的mvvm框架，vue主要是view层展示
- ng双向绑定基于模版编译规则（“脏”检查), vue是采用es5的get和set机制
- vue可以el对象进行实例化，组件化
- ng框架重，整个设计模式具有依赖注入的思想，学习曲线比较陡峭
- ng2断层式升级，但是ng2更吸引人

总而言之，Angular的“大而整”的结构让它在构建项目的过程当中得心应手，但是Vue的“小而美”组件化结构却是符合我轻量级应用项目的需求。因为现在很多的页面内容都集中在移动端。说到小而美的“组件化思想”，我们会提到Vue和React。

### Vue与React

![React VS Vue](https://raw.githubusercontent.com/PaicFE/keynote/master/img/switch_react_vue.jpg)

在听了尤雨溪的知乎live后，我更加明白vue2大量地借鉴了很多React的特性。React也算是“js大一统”思想的鼻祖，运用了css in html(jsx)和css in js的特点，实现组件化的高度复用。而尤大大则是另辟蹊径制作了[vue-loader](https://github.com/vuejs/vue-loader)和[vueify](https://github.com/vuejs/vueify),通过[webpack](https://github.com/webpack/webpack)和[browserify](http://browserify.org)打包项目工程内容。运用`<template>`和`<style>`去包装html（或者模版语言）和css（或者各种预编译器）。我个人认为vue的这种写法更加聪明和轻量化。

它们两之间的区别：

- React的es5与es6写法
- React的JSX和CSS IN JS的写法
- this相关的行为
- React的生态链，学习成本
- Vue的双向绑定和React的单向数据流

综上所述，Vue2.0也引入了单项数据流的思想，主要是用在Vuex和props的方面。双向绑定使用在表现层，方便开发者使用，相对来说性能开销会比较大。单向数据流的优势在于效率，双向绑定的优势在于开发便利性，混合式的推拉数据都是为了实现更好的开发体验。相对来说，React则是完整而庞大，就如flux思想的数据流，就有Redux和Mobx，生态化的组件库就更多了，一定程度上提高了学习成本。

之所以选择Vue，还是考虑两点，第一，移动端Vue的包相对小，加载效率高。第二，外包员工多，较低的学习成本比较适合。


### Vue1.0与2.0的区别

一年前，我翻译了一篇Vue1.0的教程[Vue笔记二：进阶[译]用Webpack构建Vue](http://www.jianshu.com/p/a5361bff1cd8)，现在已经有点不适用了。

Vue2.0的升级可以说是革命性的，如果很多人把Vue1.0当作“玩具”，那么Vue2.0可以说是“工具”，可以很好地使用于生产环境。得益于以下的特性：

- virtual dom
- 强调单向数据流，推荐vuex
- api区别，生命周期调整

如果说Vue1.0师从Angular，而Vue2.0师从React。Vue2.0把组件化的实现展现得淋漓尽致，主要表现在组件的高度复用和可维护性，得益于较低的学习成本。2.0的两个升级特点应该放在单向数据流和Virtual-dom上。

![Vuex单向数据流](https://raw.githubusercontent.com/PaicFE/keynote/master/img/vuex.png)

单向数据流正如我上述描述的，是出于效率的“推”的“数据流”设计。Mutations是针对state的数据进行修改，state会通过`mapGetters`和`mapStates`影响者Vue组件。在组件内可以通过`mapActions`（或者`mapMutations`）。Actions更多是异步请求操作。所以，整个数据流是一个闭环，相互影响，相互传递。

![Virtual-dom](https://raw.githubusercontent.com/PaicFE/keynote/master/img/virtual-dom.jpg)

虚拟dom是最近挺火的一个概念，但是不见得一定需要它。Virtual-dom涉及到一个概念，那就是重绘重排。在大量的dom操作的情况下，Virtual-dom才会起到它的价值。它的实现原理便是在dom之间做了一个虚拟层，在状态改变了后，做一次diff，把变化的dom一次性以补丁的方式给dom进行修改，减少了dom的变化。

### webpack的入门

#### 横向对比

作为Vue的好“基友”，[webpack](https://github.com/webpack/webpack)应该是这几年的最重要的打包工具。有别于gulp和grunt这种自动化工具，它更关注于模块化的思想，让js的开发变得更加方便。相较于[browserify](http://browserify.org)，webpack的自由度会更好。相比于[rollup](https://github.com/rollup/rollup)，webapck更适合使用于项目当中。

- ~~gulp~~
- browserify
- rollup


#### 基本概念

要掌握好webpack，需要了解好四个以下概念：

- entry
- output
- loader
- plugin

入口是打包的起始位置，分为js和html入口。出口是输出文件内容，可以使用hash命名进行缓存。loader则是抽离各种其他的文件，例如css，图片等等。plugins是更多的批量化操作。

#### loader

各式各样的loader非常多，这里不一一举例。

- less-loader
- sass-loader
- url-loader, file-loader
- babel-loader
- vue-loader, vux-loader

#### plugin

通过plugins对你打包项目内部的文件进行整理。例如js压缩，html压缩，公共包的提取等。

- UglifyJsPlugin
- CommonsChunkPlugin
- HtmlWebpackPlugin
- OccurrenceOrderPlugin

...


#### 多页面打包

官方给出的Vue项目构建模版是单页面项目。但是在国内可能不是所有项目都那么完整的出来所有的需求。这时，我们需要的是多页面的打包情况。我们会考虑在utils里面加一个`getEntries`的函数，这个函数会返回一个对应路径的数组，里面包括所有对应的js文件。

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

概括一下，就是调整一下，整个打包的项目，改为以下几点，详情可以参考[PaicFE/keynote](https://github.com/PaicFE/keynote)。

- 多个js入口（将entries改为数组）
- 多个html入口（基于HtmlWebpackPlugin）
- 单元测试调整

## 未来方向

未来的方向主要专注于性能优化上，主要是对h5页面在移动端的细小性能优化。

- SSR（server side rendering）
- [Nuxt.js](https://nuxtjs.org/)
- 异步请求（promise，async/await）
- 动态路由 (vue-router)

## 参考

- [vue与angular的区别](http://blog.csdn.net/qq_35844177/article/details/54915615)
- [对 virtual-dom 的一些理解](https://zhuanlan.zhihu.com/p/25630842)

欢迎关注团队GITHUB[PaicFE](https://github.com/PaicFE)