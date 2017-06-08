# 【技术研究】如何动态获取跨域iframe高度

撰稿人：项伟平

## 引言

iframe是一个“好东西”，但是又会带给你很多头疼的“问题”，特别是在ios的兼容性问题。在ios当中，iframe里的页面不会随着外层的网页大小自适应弹性缩放，相比PC浏览器浏览器和安卓的浏览器则是可以实现缩放。这时候，第一时刻想到的是兼容的写法。专门针对ios专门设置iframe的scrolling属性为“no”，其他浏览器为“yes”，如下方源码。但是如果iframe子页面中存在tab，高度进行变化，则会引起重绘重排，导致页面突然回调到顶部。

```html
<div id="url-wrapper"></div>
```

```css
html, body{
    height: 100%;
}

#url-wrapper{
    margin-top: 51px;
    height: 100%;
}

#url-wrapper iframe{
    height: 100%;
    width: 100%;
}

#url-wrapper.ios{
    overflow-y: auto;
    -webkit-overflow-scrolling:touch !important;
    height: 100%;
}

#url-wrapper.ios iframe{
    height: 100%;
    min-width: 100%;
    width: 100px;
    *width: 100%;
}
```

```javascript
function create_iframe(url){

    var wrapper = jQuery('#url-wrapper');

    if(navigator.userAgent.match(/(iPod|iPhone|iPad)/)){
        wrapper.addClass('ios');
        var scrolling = 'no';
    }else{
        var scrolling = 'yes';
    }

    jQuery('<iframe>', {
        src: url,
        id:  'url',
        frameborder: 0,
        scrolling: scrolling
    }).appendTo(wrapper);
}
```

上述的方法能解决部分网站的问题，但是如果是响应网页，一样会出现问题。

随着技术的发展，iframe一般满足的是跨域的页面。受限于浏览器的同源政策，是没法跨域获取子网页的高度或者跨度。这时候，我们可能考虑将iframe的高和宽定死。跨域交换iframe内外的数据的方法有两种，一种是中间代理页面，一种是h5的API--postMessage。

## 中间代理页面

参考[iframe高度自适应的6个方法](http://caibaojian.com/iframe-adjust-content-height.html)的最后一种方法，这种方法基于了一个中间代理层。原理很简单，用网页地址的hash值传高度和宽度。假设www.a.com域名下的一个页面a.html要包含www.b.com下的一个页面b.html。这时，我们需要在a域名下添加一个agent.html，代理层代码如下，放置在自己的服务器。

```html
<script type="text/javascript">
    var other = window.parent.parent.document.getElementById("other");
    var hash_url = window.location.hash;
    if (hash_url.indexOf("#") >= 0) {
        var hash_width = hash_url.split("#")[1].split("|")[0] + "px";
        var hash_height = hash_url.split("#")[1].split("|")[1] + "px";
        other.style.width = hash_width;
        other.style.height = hash_height;
    }
</script>
```

**而它是被iframe目标页面所引用，iframe把高度和宽度值组织好并传递到链接上。由于链接的调用不受跨域的限制，也算是走了个“后门”，把你想要的值“偷偷”传到代理页面上。而代理页面和主页面同源，不构成跨域，所以避免了浏览器的跨域限制。**我们还需要在iframe目标页面添加一段代码，就是把添加一个iframe把数据往链接上拼接。在b.html的尾部加上这段js。

```javascript
(function autoHeight() {
    var b_width = document.body.clientWidth;
    var b_height = document.body.clientHeight;
    var agent = document.getElementById("agent");
    agent.src = agent.src + "#" + b_width + "|" + b_height;  // 这里通过hash传递b.htm的宽高
})();
```

而在a.html还是原封不动的那个iframe就可以了。

```html
<iframe src="./othersite.html" id="other" frameborder="0" scrolling="no" style="border:0px;"></iframe>
```

源码参考[brandonxiang/iframe-height](https://github.com/brandonxiang/iframe-height/tree/master/agent)。

## postMessage

有些人觉得上面的方法非常难理解，因为中间代理层的缘故，增加了请求量。

随着HTML5 API的发展，postMessage是不同的html页面之间进行数据通信的方法，大大简化了上述方法的步骤。

在b.html中添加一段代码，在它加载完成后，往父页面跨域发送自己的高和宽。并且可以限制你发送的父网站的ip地址，大大保证安全性。

```javascript
document.addEventListener('DOMContentLoaded', function () {
    var tbody = document.body
    var width = tbody.clientWidth
    var height = tbody.clientHeight
    window.parent.postMessage({ height: height, width: width }, '*')
}, false)
```

还需要在a.html网站添加一个事件监听，获取iframe内的b.html发送的高与宽。

```javascript
var frame = document.getElementById('other')
window.addEventListener('message', function(e){
    frame.style.height = e.data.height+'px'
    frame.style.width = e.data.width+'px'
})
```

源码参考[brandonxiang/iframe-height](https://github.com/brandonxiang/iframe-height/tree/master/postmessage)。

## 总结

相比之下，第二种方法会比较简单，而且有效。但是由于跨域限制，你不得不要求对方添加一段代码去“消除”跨域限制。

总的来说，iframe在移动端受非常多的限制，尽可能地慎用。

欢迎订阅我们的博客，点击上方的watch按钮噢。

## 参考
[跨域iframe高度自适应的多种方法](http://blog.csdn.net/aaronpan21/article/details/51245685)

