---
layout: post
title: SublimeLinter实践
description: "sublimelinter在工程中的实际应用"
modified: 2014-12-29
tags: [sublime text,lint]
image:
  feature: abstract-8.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

### 何为 Lint

**Lint** 在编程里意味着一种代码验证工具，不同的**Lint** 会根据语言本身的语法，再加上一些最佳实践形成的代码写法和风格，去验证你的代码是否符合规定,这是我的个人见解，大家也可以去看 Wiki 上的解释 [Lint(software)](Lint)。

在前端开发里，我们熟知的**Lint**就有 [jshint](jshint)、[jslint](jslint)、[csslint](csslint)、[scsslint](scsslint)。其它语言也有相应的 `lint`,这里就不一一列出了，感兴趣的可以去看这份[list](http://en.wikipedia.org/wiki/List_of_tools_for_static_code_analysis)。
<!-- more -->

这里要注意的一下就是 `jshint` 和 `jslint`, `jslint`检测的严格程度是无法被改变的，倘若要修改默认规则，只能通过修改[jslint](jslint) 的源码去实现。相比之下，[jshint](jshint)就显得友好得多，可以通过配置文件的方式去更改默认规则。
在实际开发中，我们并没有那么理想的状况，有时甚至会应用一些黑魔法，所以选择[jshint](jshint)会好过点。想具体了解两者的差别，这里有篇文章值得一看[jslint-vs-jshint](http://www.scottlogic.com/blog/2011/03/28/jslint-vs-jshint.html)。

### 为什么要用 SublimeLinter

首先要提的就是 `code review`, `code review`其实就是一种代码检测手段了，我提过一次，但是我们的团队不崇尚这个，认为开发任务都来不及更没有时间花在`code review`上了。但我还是要表明一下我的态度：不肯在 code review 上花时间的，一定会在其它地方加倍还回来，出来‘码’的，迟早要还。

既然没有 `code review`,那就得用其它办法规范一下代码的语法和格式，因为像我们很多后端工程师写`js`是没有章法可言的，能达到效果就行，从不考虑维护性。而且我们有的前端经验也比较少，所以引入[SublimeLinter](sublimelinter) 就能在自己写代码的时候发现问题及时解决。

因为现在都是写`scss`了，我在项目里用到的两个[SublimeLinter](sublimelinter) 的plugin 是[SublimeLinter-jshint](SublimeLinter-jshint) 和 [SublimeLinter-contrib-scss-lint](SublimeLinter-contrib-scss-lint)。

要想用[SublimeLinter-jshint](SublimeLinter-jshint)，除了要在 [sublime text](sublimetext) 中装上这个包，还需要你的机器上有[nodejs](nodejs)环境，并全局安装了`jshint`

{% highlight js %}
npm install -g jshint
{% endhighlight %}

如果你是想我一样的使用`NVM`和`zsh`的话，要记得把`NVM`的加载,下面这句

{% highlight js %}
[ -s "/Users/Kimi/.nvm/nvm.sh" ] && . "/Users/Kimi/.nvm/nvm.sh"
{% endhighlight %}

放到`.zshenv`中，而不是`.zshrc`。因为放到`.zshrc`中，`nvm`只有在终端打开的时候才会去加载，而放到`.zshenv`中，会在开机的时候加载。

做完以上步骤，把你的`.jshintrc`配置扔在项目的某个目录下，[SublimeLinter](sublimelinter)会自动去寻找这些配置文件，找不到就用默认的。

贴出我的`.jshintrc`配置
{% highlight json %}
{
  "es5": true,
  "bitwise": true,
  "curly": true,
  "eqeqeq": true,
  "forin": false,
  "immed": true,
  "latedef": true,
  "newcap": true,
  "noarg": true,
  "noempty": true,
  "nonew": true,
  "plusplus": false,
  "undef": true,
  "strict": false,
  "trailing": false,
  "globalstrict": true,
  "nonstandard": true,
  "white": true,
  "indent": 2,
  "expr": true,
  "onevar": false,
  // 开发环境
  "browser": true,
  "jquery": true,
  "node" : false,
  // 自定义全局变量
  "globals" : {}
}
{% endhighlight %}

[SublimeLinter-contrib-scss-lint](SublimeLinter-contrib-scss-lint)的使用和上面的类似，只不过要求你的机器上要有[ruby](ruby)而且通过`gem install scss-lint`装了[scss-lint](scsslint)。

[sublimetext]: http://www.sublimetext.com/3 "sublimetext"
[Lint]: http://en.wikipedia.org/wiki/Lint_(software) "Lint"
[jshint]: http://jshint.com/ "jshint"
[jslint]: http://www.jslint.com/ "jslint"
[scsslint]: https://github.com/causes/scss-lint "scsslint"
[csslint]: http://csslint.net/ "css-lint"
[sublimelinter]: http://www.sublimelinter.com/ "sublimelinter"
[nodejs]: http://nodejs.org/ "nodejs"
[ruby]: https://www.ruby-lang.org/ "ruby"
[SublimeLinter-jshint]: https://packagecontrol.io/packages/SublimeLinter-jshint "SublimeLinter-jshint"
[SublimeLinter-contrib-scss-lint]: https://packagecontrol.io/packages/SublimeLinter-contrib-scss-lint "SublimeLinter-contrib-scss-lint"

