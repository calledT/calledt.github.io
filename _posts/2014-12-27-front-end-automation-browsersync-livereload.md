---
layout: post
title: 前端自动化工程之浏览器同步更新
description: "browsersnyc 和 livereload 在前端自动化工程中的实践"
modified: 2014-12-28
tags: [自动化,浏览器同步]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

## livereload

### sublime text plugin & chrome extends

刚接触 [sublime text](sublime-text)的时候，非常热衷于折腾插件，当时有一款 [livereload](livereload)的[sublime插件](https://sublime.wbond.net/packages/LiveReload)，配合浏览器安装的[livereload扩展](https://chrome.google.com/extensions/detail/jnihajbhpnppcggbcgedagnkighmdlei)，安装完在扩展程序界面允许这个扩展访问文件网址，就可以通过 `file://`文件的形式或者以`server`的形式同步查看页面源文件修改之后在浏览器的效果，不用去手动刷新。这样的方式对于写简单的页面非常有用，你可以省却手动启动一个`server`的步骤，因为[livereload](livereload)的[sublime插件](https://sublime.wbond.net/packages/LiveReload)已经做了这件事。对于有超宽屏或者双显示器的页面重构人员，这相当令人激动的。
<!-- more -->

为了写这篇文章，特意再去体验一把，因为我现在用的是`sublime text 3`，以前那个插件是给`sublime text 2`用的，发现现在有一个[devel version](https://github.com/dz0ny/LiveReload-sublimetext2/tree/devel)，说是给`sublime text 3`的，但我装上，建了一个测试页面，点击浏览器上的 [livereload扩展](https://chrome.google.com/extensions/detail/jnihajbhpnppcggbcgedagnkighmdlei)按钮，却提示

>Could not connect to LiveReload server. Please make sure that LiveReload 2.3 (or later) or another compatible server is running.

一开始以为是浏览器插件的问题，上网搜也没找到多少有用信息，只是看到这个浏览器插件评论页有不少人也在抱怨这个问题，到这里都会认为真的是这个扩展在新版浏览器中的问题了，但是强迫症的我还是把`sublime text 2`装上，验证到底是不是浏览器插件的问题。

最后的结论: **不是 livereload 浏览器扩展的问题**，是`sublime text 3`插件的问题。


### grunt-contrib-watch

我在接触了[grunt](grunt)之后，就放弃了上面那种使用方式。因为我会配置一套工作流来协助开发，[livereload](livereload)会是工作流中的一个`task`。

以前有一个叫[grunt-contrib-livereload](https://github.com/gruntjs/grunt-contrib-livereload)的plugin，不过现在已经合并到[grunt-contrib-watch](https://github.com/gruntjs/grunt-contrib-watch#optionslivereload)中去了.关于怎样配置任务可以去看[enabling-live-reload-in-your-html](https://github.com/gruntjs/grunt-contrib-watch/blob/master/docs/watch-examples.md#enabling-live-reload-in-your-html)。

这种方式的相比上面那种方式的优点是配合[grunt](grunt)的`task`，监控更改的文件类型更多了，毕竟现在好多开发者在使用[sass](sass)，[less](less)， [coffeescript](coffeescript)这些预处理语言或者各式各样的模板引擎。

但这种方式我们还是需要安装浏览器插件或者在页面里嵌入类似下面的`livereload脚本`

{% highlight js %}
<script src="//localhost:35729/livereload.js"></script>
{% endhighlight %}

嵌入脚本的话上线的时候还得移除它，当然有工具可以在上线的时候处理掉这段脚本.但是这样终究显得有些不够全自动，呵呵。


## browsersync

在工具的选择上我算是一个比较喜新厌旧的人了！所以用过[gulp](gulp)之后，我就不再写[grunt](grunt)了。[gulp](gulp)写起任务来比[grunt](grunt)优雅多了，而且现在[gulp](gulp)社区的繁荣，相比年初的时候，可以毫不犹豫的切换过来。

好了，吐槽完毕，进入正题，接下来要介绍的工具叫[browsersync](browsersync)，会用`gulpfile`来做示例，但它是不依赖于[gulp](gulp)或者[grunt](grunt)的。

[browsersync](browsersync)是一款多端同步刷新页面内容的工具，这能大大节省开发测试的时间。比如你在 `chrome` 和 `IE`中打开同一个被 [browsersync](browsersync) 监控的 URL，在 `chrome` 页面表单中输入的内容会被同步到 `IE`浏览器中，用户操作也是能同步的，很酷是吧.当然啦，像[livereload](livereload)的修改源文件刷新浏览器页面的功能也是有的。多端同步在开发响应式网站的时候相当有用，想想你电脑上有好几个浏览器，而且还手机端，pad端 上的浏览器，一个个打开去测多浪费时间呀！

[browsersync](browsersync)通过启用一个`server`的方式，通过访问这个`server`的地址，就可以不用手动在页面上插入脚本或者去安装浏览器扩展了.有人可能会有疑惑，我已经有自己的`server`了，例如`tomcat`，那怎么办？别急，可以，把[browsersync](browsersync)的`server`设置成代理的`server`，这样访问[browsersync](browsersync)的`server`的时候，会把所有请求都转发到你自己的`server`上，棒!下面来看看我的配置吧！


{% highlight js %}

// autoprefixer 浏览器支持版本
var browsersSupport = ['> 5%'， 'last 2 versions'， 'Firefox < 20'];

/**
 * 浏览器同步查看修改
 * http://www.browsersync.io/docs/gulp/
 */
gulp.task('browser-sync'， function() {
  browserSync({
    /**
     * 二选一
     * 或者使用代理，或者启用一个静态资源服务
     */
    proxy: "localhost:8080"
    // server: {baseDir: "./static"}
  });
});

/**
 * 编译sass
 * https://github.com/dlmanning/gulp-sass
 * https://github.com/sindresorhus/gulp-autoprefixer
 * https://github.com/floridoo/gulp-sourcemaps
 */
gulp.task('sass'， function(cb) {
  return gulp.src(path.src.scss)
    .pipe($.sourcemaps.init())
    .pipe($.sass({errLogToConsole: true， outputStyle: 'nested'}))
    .pipe($.sourcemaps.write({includeContent: false})) // 可以通过下面的链接去了解为什么要加上这两句
    .pipe($.sourcemaps.init({loadMaps: true})) // https://github.com/floridoo/gulp-sourcemaps/issues/60
    .pipe($.autoprefixer({browsers: browsersSupport， cascade: false}))
    .pipe($.sourcemaps.write('./maps'))
    .pipe(gulp.dest(path.dest.css))
    .pipe($.filter('**/*.css'))
    .pipe(browserSync.reload({stream:true}));
});


gulp.task('watchsass'， ['sass'， 'browser-sync']， function () {
  gulp.watch(path.src.scss， ['sass']);
});

{% endhighlight %}


更多的配置，可以通过[browsersync](browsersync)官网去了解，配置很简单，却是十分的实用，感谢作者开源这样的工具。

[sublime-text]: http://www.sublimetext.com "sublime-text"
[livereload]:  http://livereload.com "livereload"
[nodejs]: http://nodejs.org "nodejs"
[grunt]:  http://gruntjs.com "grunt"
[gulp]: http://gulpjs.com "gulp"
[sass]: http://sass-lang.com "sass"
[less]: http://lesscss.org "less"
[coffeescript]: http://coffeescript.org "coffeescript"
[browsersync]: http://www.browsersync.io "browsersync"
