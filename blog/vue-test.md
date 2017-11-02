# Vue的自动化测试

Vue官方脚手架会自带测试配置，但是我们知道国内的前端友人大多不写测试，也不会写测试。

**为什么我们需要测试？**

> 让产品可以快速迭代，同时还能保持高质量 -- [阮一峰 持续集成是什么？](http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html?utm_source=tuicool&utm_medium=referral)

**什么是持续集成？**

**它和持续部署有什么区别？**

代码集成到主分支的时候，需要经过一系列的自动化测试，当测试都通过之后，可以完成自动化部署。只要一个测试用例不通过，就不能集成。这说明了自动化测试的重要性，有时候我们不能等测试工程师去发现问题。

在Vue脚手架当中，Karma和NightWatch分别对应着单元测试和e2e测试。单元测试更多是面向JS功能逻辑的检验，而NightWatch更多是面对业务逻辑的检验。

## Karma

[Karma](https://github.com/karma-runner/karma)是一个专门的测试运行器（runner），它不是一个测试框架框架，也不是以一个断言库。

Karma兼容[Jasmine](https://github.com/karma-runner/karma-jasmine)，[Mocha](https://github.com/karma-runner/karma-mocha)和[QUnit](https://github.com/karma-runner/karma-qunit)，可以集成mocha，webpack等功能，成为以Karma为平台的单元测试。它拥有一些测试插件：

- [karma-webpack](https://github.com/webpack-contrib/karma-webpack) 用webpack预处理文件
- [karma-coverage](https://github.com/karma-runner/karma-coverage) 测试覆盖率
- [karma-mocha](https://github.com/karma-runner/karma-mocha) 接入mocha测试框架
- [karma-spec-reporter](https://github.com/mlex/karma-spec-reporter) 输出报告
- [karma-phantomjs-launcher](https://github.com/karma-runner/karma-phantomjs-launcher) 控制PhantomJS
- [karma-phantomjs-shim](https://github.com/tschaub/karma-phantomjs-shim) 给PhantomJS兼容的控制

karma-coverage是基于[istanbul](https://github.com/gotwarlost/istanbul)。这些插件可以集成为一个测试平台，把webpack打包的vue项目，在测试里面的组件实现的功能。包括独立的组件库，业务逻辑和请求范围。


## NightWatch

NightWatch是一个专门的端对端测试运行器（runner）。

它依赖于浏览器控制器selenium，而selenium是一个`.jar`后缀的文件，需要java的运行环境。所以你需要安装java并配置好环境变量。然而，selenium需要对应的driver配合来操控浏览器。









