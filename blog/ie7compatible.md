# ie 7兼容想法

nodejs express Nunjucks less bootstrap2 jquery1 require.js gulp

正因为是兼容ie7或者ie8以上的pc端的页面，我在整个项目的计划中，因为ie7不支持了es5的语法。直接和react和vue等框架说“byebye”。虽然网上有很多的兼容的方法，但是怕一旦使用了整个过程就变得很尴尬。

这时，我第一反应就是用express和模版语言，实现动态的数据单项绑定，技术有点旧，但是保证了兼容性以及组件化的拆分。而js的模块化则是用require.js和gulp的自动化操作。