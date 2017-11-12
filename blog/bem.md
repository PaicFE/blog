# 【技术分享】入门BEM

## 前言

**为什么不用ID标识符**

主要考虑到样式重用性以及与页面的耦合性。ID本来就是唯一的而且权值那么大，还不如写在行内样式。

```
#button {
  text-decoration: underline;
}
```

**为什么不用大于三层嵌套选择器**

嵌套选择器增加了代码耦合，使重用变得不可能，过多的嵌套会导致显示性能下降。简洁的选择器不仅可以减少css文件大小，提高页面的加载性能，让浏览器解析时也会更加高效。同时也会提高开发人员的开发效率，降低了维护成本。子代选择器不好的地方还在于，如果层次关系过长，逻辑不清晰，非常不利于维护。

css的匹配原理**不是从左到右的，而是从右到左的**，从右边开始匹配是为了尽早过滤掉一些无关的样式规则和元素。

```css
.button_hovered .button__text {
  text-decoration: underline;
}
```

**为什么不用组合选择器**

组合选择器的耦合性更强，而且可维护性更加差。

```css
.button.button_theme_islands {
  text-decoration: underline;
}
```

## 介绍

BEM是**Block，Element，Modifier**的缩写。下面分别来介绍一下这三个概念：

- 块（Block）是一个块级元素，可以理解为组件块，比如头部是个block，内容也是block，一个block可能由几个子block组成。
- 元素（Element）element是block的一部分完成某种功能，element依赖于block，比如在logo中，img是logo的一个element，在菜单中，菜单项是菜单的一个element。
- 修饰符（Modifier）modifier是用来修饰block或者element的，它表示block或者element在外观或行为上的改变，例如actived。

这里引用[BEM的定义](http://www.w3cplus.com/css/bem-definitions.html)一文中的两张图：

![Definitions-BEM-5.jpg](http://upload-images.jianshu.io/upload_images/685800-3b3eebb021d6f352.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Definitions-BEM-6.jpg](http://upload-images.jianshu.io/upload_images/685800-e889f5883e808f98.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们通过BEM命名法写样式如下：
* .block{}
* .block__element{}
* .block--modifier{}
* .block__element--modifier{}

BEM解决了的问题，

- 页面CSS模块化，每个block就是一个模块，模块间相互独立，不会造成污染
- 多级的class命名，避免选择器的嵌套结构
- 减少通配符*或者类似[hidden="true"]这类选择器的使用
- 巧妙运用scss的特性，保证了样式的可维护性

BEM将页面解析为block和element，然后根据不同的状态使用modifier来设置样式，在scss的属性帮助下，其实，写BEM并不麻烦。

## 使用scss写BEM

#### 灵活的&

当嵌套使用`&`时，它会直接抓取父选择器的类名，自动拼接上元素名字，避免CSS文件中组件样式的冲突。这样，我们可以得到一个完整的BEM结构的类名，这样有效利用scss的嵌套特性避免CSS文件中组件样式的冲突。

示例：

```scss
.header {
  &__text {
    text-decoration: underline; 
  }
  &__image {
    background-color: steelblue;
  }
}
```

#### 强大的@extend

`@extend`是scss的继承语法，它可以将父级的属性继承到子级。主要使用在modifier上，因为改变状态的属性和原来的基本保持一致。你可以弄少一个classname。

示例：

```html
<nav class="nav">
  <ul class="nav__container">
    <li class="nav__item"></li>
    <li class="nav__item"></li>
    <li class="nav__item"></li>
    <li class="nav__item--active"></li>
  </ul> 
</nav>
```

```scss
.nav {
  background-color: steelblue;
  &__container {
    display: flex;
    justify-content: space-between;
  }
  &__item {
    color: white;
    &--active {
      @extend .nav__item;
      border-bottom: 1px solid red;
    }
  }
}
```


## 参考

- [BEM的定义](http://www.w3cplus.com/css/bem-definitions.html)
- [如何更好的使用BEM](http://www.w3cplus.com/preprocessor/getting-sass-y-with-bem.html)
- [CSS性能分析，如何优化CSS提高性能](http://www.cnblogs.com/xiaoloulan/p/5801663.html)
