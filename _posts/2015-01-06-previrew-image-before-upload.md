---
layout: post
title: 兼容IE、Chrome、FF的图片上传前预览
description: "图片上传前预览，兼容所有浏览器"
modified: 2015-01-06
tags: [IE,图片预览]
image:
  feature: abstract-4.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

### 原由 

&emsp;&emsp;最近在帮同事解决一个图片显示的问题，情形是这样的。图片通过异步上传，成功后返回一个图片的链接，再把这个链接赋给`img`标签的`src`属性，但这个过程会间歇性的出现`404`错误。
<!-- more -->

&emsp;&emsp;`404`我们知道是访问一个不存在的链接，但在浏览器中直接访问这个链接，又是可以的。反复排查了前端代码和后端代码，也没发现有错误的地方。再经过一番深入了解，知道上传图片的功能用的是内网的一个文件系统[fasetdfs][fastdfs]{:target="_blank"}，于是就想会不会是磁盘快满了才会出现这个问题，以前遇到过[mysql][mysql]{:target="_blank"}所在服务器因磁盘空间不够出现过各种诡异问题。

&emsp;&emsp;从负责这个文件系统架设的工程师那里拿到说是文件系统所在服务器的`IP`后就上去查磁盘空间大小（当时自己也不了解这个文件系统，以为这个 IP 所在的服务器就是存放文件的地方），看着磁盘空间还剩很多，就带着疑惑上网查找，发现那个工程师给我的并不是最终存放文件的服务器，只是[nginx][nginx]{:target="_blank"}所在的机器，在打开`nginx.conf`看到配置后，就一切明了了。

&emsp;&emsp;原来最终存放文件的是两台机器，[nginx][nginx]{:target="_blank"}担任负载均衡作用，而负载策略是随机的。随机的负载策略加上文件系统同步文件的时间差，就造成了上传响应成功，设置`img`标签的`src`属性出现`404`想象。

解决的办法就是[nginx][nginx]{:target="_blank"}根据`IP`去负载或者设置文件系统为主从，根据我们的业务需求选择了主从。

### 图片上传前预览代码

&emsp;&emsp;回过头想了想，发现这地方其实存在交互不合理的地方，正确的做法应该是图片在上传前可以预览，而不是一选择图片就上传然后返回图片的链接回来。所以写了以下兼容所有浏览器的图片上传前预览的代码。

<pre class="codepen" data-height="400" data-type="result" data-href="ZYBZze" data-user="calledT" data-safe="true">
    <code></code>
    <a href="http://codepen.io/calledT/embed/ZYBZze/">Check out this Pen!</a> 
</pre>
<script async src="http://codepen.io/assets/embed/ei.js"> </script>


#### 参考链接：
* [How to Preview image before Upload](http://forums.asp.net/t/1320559.aspx){:target="_blank"}
* [Preview an image before it is uploaded](http://stackoverflow.com/questions/4459379/preview-an-image-before-it-is-uploaded?rq=1){:target="_blank"}

[fastdfs]: https://code.google.com/p/fastdfs "fastdfs" 
[nginx]: http://nginx.org "nginx"
[mysql]: http://www.mysql.com "mysql"
