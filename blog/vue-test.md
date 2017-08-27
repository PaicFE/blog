# 【技术分享】 Vue的自动化测试

撰稿人：项伟平

Vue官方脚手架会自带测试配置，但是我们知道国内的前端友人大多不写测试，也不会写测试。

**为什么我们需要测试？**

> 让产品可以快速迭代，同时还能保持高质量 -- [阮一峰 持续集成是什么？](http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html?utm_source=tuicool&utm_medium=referral)

**什么是持续集成？**

**它和持续部署有什么区别？**

代码集成到主分支的时候，需要经过一系列的自动化测试，当测试都通过之后，可以完成自动化部署。只要一个测试用例不通过，就不能集成。这说明了自动化测试的重要性，有时候我们不能等测试工程师去发现问题。

## Karma

Karma是一个专门的测试运行器（runner），它不是一个测试框架框架，也不是以一个断言库。

Karma可以选择用，


## NightWatch

NightWatch是一个专门的端对端测试运行器（runner）。

它依赖于浏览器控制器selenium，而selenium是一个`.jar`后缀的文件，需要java的运行环境。所以你需要安装java并配置好环境变量。然而，selenium需要对应的driver配合来操控浏览器。









