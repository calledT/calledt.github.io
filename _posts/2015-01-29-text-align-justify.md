---
layout: post
title: 中文,中英字符两端对齐的方法
description: "在表单布局中经常需要处理字符对齐的情况，本文探索如何实现中文字符，中英混合支付两端对齐的方法"
modified: 2015-01-29
tags: [css, text-align]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---
### 情景
很多时候设计师给我们的稿子如左图所示，表单中的字段描述文字呈两端对齐状态。而大多时候还原设计稿的时候，会偷懒把字段描述整成左对齐，如右图所示。这样做的原因是因为省时省力，而且表单的排版还算整齐。但有在某些非常需要排版美观的界面，如注册登陆页，设计师就一定要文字两端最齐，这就是这篇文章出现的缘故了，探索中文，中文混合文字两端对齐的方法。
<!-- more -->
<p>
<img src="http://ww4.sinaimg.cn/large/68250c36gw1eoofvwbw1ej20ho086jrh.jpg" alt="设计稿" style="width: 49.5%;">
<img src="http://ww3.sinaimg.cn/large/68250c36gw1eoofvrnx0qj20ia08a74d.jpg" alt="还原设计稿" style="width: 49.5%;">
</p>

### 空格法
相信大多数开发者最常用的空格就是**`&nbsp;`**这个字符实体了，它等于按下space键产生的空格，但是这个空格受字体的影响强烈，所以不适合用来辅助排版。所幸我们还有另外两个字符实体**`&ensp;`**和**`&emsp;`**，第一个所占的宽度等于半个中文字符的宽度，第二个所占的宽度等于一个中文字符宽度，这两个字符实体用来解决上面所描述的情景就可以的了。但是这两个字符实体只能解决全是中文字符的情形，遇到中英文字符混合的情形就没辙了。

<pre class="codepen" data-height="200" data-type="result" data-href="GgvNXV" data-user="calledT" data-safe="true">
    <code></code>
    <a href="http://codepen.io/calledT/embed/GgvNXV/">Check out this Pen!</a> 
</pre>
<script async src="http://codepen.io/assets/embed/ei.js"> </script>

而且这种办法只适合小规模的表单，如果是大规模的表单，这样的手动插入字符实体也挺不易的。有些表单的描述甚至是从后端读取的变量，要手动插入字符实体的机会都没有。接下来介绍的CSS大就可以解决这两个问题。

### 纯CSS法

<pre class="codepen" data-height="200" data-type="result" data-href="vEJgBo" data-user="calledT" data-safe="true">
    <code></code>
    <a href="http://codepen.io/calledT/embed/vEJgBo/">Check out this Pen!</a> 
</pre>
<script async src="http://codepen.io/assets/embed/ei.js"> </script>

先看看效果，这个方法有一点不完美的地方就是要在每个中文字符之间插入空格而且要固定容器的宽度，因为大部分浏览器两端对齐的实现，都是通过调整字之间的空格大小来达成，而英文单词本来就是以空格为间隔的，所以就不用做处理。因为这样的空格比较有规律可寻，相信如果是从后端读取变量的话，是有办法批量处理的。

处理两端对齐的CSS属性是`text-align: justify`,但是这个属性是不处理段落中的最后一行的，像上面表单字段的描述中，就相当于一段只有一行的段落，也既是最后一行，如果只是添加这个属性，是不会起效果的。所以，我们要让它变成不是最后一行，思路就是利用伪元素添加多一行作为最后一行,再让这一行变得不可见就好。

{% highlight css %}
.text-justify {
    text-align: justify;
}

.text-justify:after {
    content: '';
    overflow: hidden;
    width: 100%; /*百分百的宽度，独占一行*/
    height: 0;
}
{% endhighlight %}

其实是有一个属性`text-align-last: justify`用来处理最后一行对齐的，但是因为这是CSS3中的一个实验性属性，有些浏览器要加前缀使用，有些就根本不支持，所以上面那种实现方式是兼容性比较好的。

去看看[哪些浏览器支持text-align-last属性](http://caniuse.com/#search=text-align-last){:target="_blank"}

对于不支持伪元素的旧IE（ie6 ~ ie8）,可以通过IE浏览器特有的属性`text-justify: distribute-all-lines;`来让字符实现两端对齐。想了解更多`text-justify`的属性值，请看[这篇文章](http://www.xmlas.com/css-text-justify.html){:target="_blank"}

#### 参考链接
* [小tips: 使用&#x3000;等空格实现最小成本中文对齐](http://www.zhangxinxu.com/wordpress/2015/01/tips-blank-character-chinese-align/){:target="_blank"}
* [css 文本两端对齐](http://www.cnblogs.com/rubylouvre/archive/2012/11/28/2792504.html){:target="_blank"}
* [CSS3 justify](http://demo.doyoe.com/css3/justify/){:target="_blank"}




