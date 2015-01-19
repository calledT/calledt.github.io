---
layout: post
title: sass 中 @include 与 @extend 的区别
description: "sass 中 @include 与 @extend 的区别"
modified: 2015-01-19
tags: [sass]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

### @include
在sass中，@include 代表这调用定义好的 @mixin。在调用的时候如果`mixin`没有参数，可以省略`mixin`之后的括号，如下：

<!-- more -->

{% highlight css %}

@mixin size () {
    width: 100px;
    height: 100px;
}

.foo {
    @include size();
}

.bar {
    @include size;
}

/******** 输出 ********/

.foo {
    width: 100px;
    height: 100px;
}

.bar {
    width: 100px;
    height: 100px;
}
{% endhighlight %}

### @extend 
@extend 代表着继承，可以继承一个选择器中的样式，也可以继承一个定义好的placeholder
{% highlight css %}
.foo {
    width: 100px;
    height: 100px;
}

.bar {
    @extend .foo;
}

/******** 输出 ********/

.foo, .bar {
    width: 100px;
    height: 100px;
}
{% endhighlight %}

有同学可能会有疑问了，既然可以继承选择器，那placeholder是用来干嘛的呀，我们可以再看下面的例子：

{% highlight css %}
.foo {
    .qux {
        width: 100px;
        height: 100px;
    }
}

.bar {
    .baz {
        @extend .qux;
    }
}

/******** 实际输出 ********/

.foo .qux, .foo .bar .baz, .bar .foo .baz {
    height: 100px;
    width: 100px; 
}

/******** 期望输出 ********/

.foo .qux, .bar .baz{
    width: 100px;
    height: 100px; 
}

{% endhighlight %}

&emsp;&emsp;从上面看到，实际的输出和期望的输出是不一样的，有同学可能就有想法了，用`@extend .foo .qux`呢？不好意思，这样会报错。又有同学想了，那我预定义一个没有嵌套的选择器，然后让这两个都继承它不就行了。行是行，可是这样做会有副作用，就是那个预定义的选择器也有跟着输出，这个预定义的选择器在最终的网页中可能就是没有意义的。要解决以上问题，就要用到`placeholder`了，来看一下`placeholder`的语法和用法：

{% highlight css %}
%size {
    width: 100px;
    height: 100px;
}

.foo {
    .qux {
        @extend %size;
    }
}

.bar {
    .baz {
        @extend .foo;
    }
}

/******** 输出 ********/

.foo .qux, .bar .baz {
    width: 100px;
    height: 100px;
}
{% endhighlight %}

### 异同分析
从上面例子中我们可以看到`@include`代表着的mixin和`@extend`代表着的继承中的一些异同。

* **相同的是**：两者都可以用来给选择器添加一系列的属性而不用重复手写这些属性。
* **不同的是**：使用`@extend`会合并选择器，共享这些相同的属性，这样做可以节省字符；而使用`@mixin`的实现的效果则是每个选择器独立的处在。

### 最后
以上只是`@inlcude`和`@extend`实现类似效果当中的一些差异，`mixin`的真正作用并不仅此，要赋予它参数才能显示它的强大之处。
