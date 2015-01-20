---
layout: post
title: event.target 和 event.currentTarget 的区别
description: "js中event.target 和 event.currentTarget 的区别"
modified: 2015-01-20
tags: [js]
image:
  feature: abstract-8.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

### event.target
先来看看`event.target`的定义

> This property of event objects is the object the event was dispatched on. It is different than event.currentTarget when the event handler is called in bubbling or capturing phase of the event.

意思就是`event.target`这个事件对象是通过`event`这个对象分发出来的。它和`event.currentTarget`在事件冒泡和捕获阶段的处理函数中是不同的。

### event.currentTarget
再看看`event.currentTarget`的定义

> Identifies the current target for the event, as the event traverses the DOM. It always refers to the element the event handler has been attached to as opposed to event.target which identifies the element on which the event occurred.

大概是说在事件穿透DOM的过程中，`event.currentTarget`在事件处理函数中始终代表当前的对象,而`event.target`代表的是目标事件发生时的对象。

### 冒泡和捕获

读定义总是很绕，要彻底了解这两者的区别，我们要先了解浏览器中事件传递的机制**冒泡**和**捕获**。
在页面中点击一个元素，事件是从这个元素的祖先元素中逐层传递下来的，这个阶段为事件的捕获阶段。当事件传递到这个元素之后，又会把事件逐成传递回去，直到根元素为止，这个阶段是事件的冒泡阶段。

<img src="http://ww4.sinaimg.cn/large/68250c36gw1eofufrk2kxj20cj0aht8w.jpg" alt="capturing" style="width: 49%;">
<img src="http://ww4.sinaimg.cn/large/68250c36gw1eofufqrs15j20ck0abjrj.jpg" alt="bubbling" style="width: 49%;">

我们为一个元素绑定一个点击事件的时候，可以指定是要在捕获阶段绑定或者换在冒泡阶段绑定。
当`addEventListener`的第三个参数为`true`的时候，代表是在捕获阶段绑定，当第三个参数为`false`或者为空的时候，代表在冒泡阶段绑定。

知道事件有这么一个穿透的过程之后，结合下面的例子，就可以很好来理解`event.target`和`event.currentTarget`：
{% highlight html %}
<div id="a">
    <div id="b">
      <div id="c">
        <div id="d"></div>
      </div>
    </div>
</div>

<script>
    document.getElementById('a').addEventListener('click', function(e) {
      console.log('target:' + e.target.id + '&currentTarget:' + e.currentTarget.id);
    });    
    document.getElementById('a').addEventListener('click', function(e) {
      console.log('target:' + e.target.id + '&currentTarget:' + e.currentTarget.id);
    });    
    document.getElementById('a').addEventListener('click', function(e) {
      console.log('target:' + e.target.id + '&currentTarget:' + e.currentTarget.id);
    });    
    document.getElementById('a').addEventListener('click', function(e) {
      console.log('target:' + e.target.id + '&currentTarget:' + e.currentTarget.id);
    });
</script>
{% endhighlight %}

上面事件的绑定都是在冒泡阶段的，当我们点击最里层的**元素d**的时候，会依次输出:

{% highlight js %}
target:d&currentTarget:d
target:d&currentTarget:c
target:d&currentTarget:b
target:d&currentTarget:a
{% endhighlight %}


从输出中我们可以看到，`event.target`代表着的是引起事件触发的那个元素，而`event.currentTarget`是事件在穿透过程中来到的当前那个元素，只有被点击的那个目标元素的`event.target`才会等于`event.currentTarget`。

如果我们把事件都绑定在捕获阶段，然后还是点击最里层的**元素d**，这时`event.target`还依旧会指向d，因为那是引起事件触发的元素，因为`event.currentTaget`是指向事件穿透过程中来到的当前元素，在捕获阶段，最先来到的元素是a,然后是b,接着是c,最后是d。所以输出的内容如下：
{% highlight js %}
target:d&currentTarget:a
target:d&currentTarget:b
target:d&currentTarget:c
target:d&currentTarget:d
{% endhighlight %}



