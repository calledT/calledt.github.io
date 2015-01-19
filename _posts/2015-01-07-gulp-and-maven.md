---
layout: post
title: gulp 与 maven
description: "gulp 与 maven 的配合"
modified: 2015-01-07
tags: [gulp,maven]
image:
  feature: abstract-8.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

### 背景

&emsp;&emsp;项目前端使用[gulp][gulp]{:target="_blank"}作为工作流构建系统，一直默默的在背后支撑着文件变动监控，打包压缩，图像优化等一些任务，在开发阶段，它出色的完成自己的使命。可是，项目终归有一天是要上线的，上线之后免不了更新版本，问题就随着而来了。我们项目是用[java][java]{:target="_blank"}作为服务器端开发语言，用[maven][maven]{:target="_blank"}作为包管理工具，在发布版本的时候，打包成[war][war]{:target="_blank"}包。以前常规的开发方式，开发中和线上前端资源的是一样的，直接用`maven package`打成[war][war]{:target="_blank"}包就行。我来之后，当然要改变这种局面，线上的资源一定是要经过优化的，所以就引进了[gulp][gulp]{:target="_blank"}、[sass][sass]{:target="_blank"}等一系列提升开发效率的工具，到上线的时候，运行一下[npm][npm]{:target="_blank"}和[gulp][gulp]{:target="_blank"}的命令，就可以构建线上资源。

&emsp;&emsp;问题就出在运行[npm][npm]{:target="_blank"}和[gulp][gulp]{:target="_blank"}的命令，以前开发完从`svn`拉个版本出来直接运行`maven package`就打包了，现在要在这个步骤之前要先执行一下`npm install`、`gulp build`这两个命令。在有些人看来这也许没什么，不就多了二个步骤嘛，但是当构建频繁的时候，手动输入这两个命令也是烦人的嘛，本着`DRY`的原则，决心要归一。

### 使用 maven plugins

&emsp;&emsp;在经过一番搜寻之后，发现了[exec-maven-plugin][exec-maven-plugin]{:target="_blank"}，这款插件用来执行一些在命令行里执行的命令。你只需去了解[maven][maven]{:target="_blank"}的生命周期，在合适的周期执行恰当的命令就行，以下配置是跟我我们项目情况配的，仅供参考。

> 吐个槽，windows真是让人操进了心。切记`exec-maven-plugin`版本要用最新版的，不然在`windows`下会出问题。别问我为什么，我就是因为在自己的`mac`上整完了以为就行，不顾广大用 windows 而处于水深火热中的同事，等到在他们机器上运行的时候，没反应过来，后来找了好久才发现是版本问题，囧！ 

{% highlight xml %}
<plugin>
<groupId>org.codehaus.mojo</groupId>
<artifactId>exec-maven-plugin</artifactId>
<version>1.3.2</version>
<executions>
  <execution>
    <id>NPM package install</id>
    <phase>prepare-package</phase>
    <goals>
      <goal>exec</goal>
    </goals>
    <configuration>
      <executable>npm</executable>
      <workingDirectory>src/main/webapp/</workingDirectory>
      <arguments>
        <argument>
          install
        </argument>
      </arguments>
    </configuration>
  </execution>
  <execution>
    <id>Gulp task run</id>
    <phase>package</phase>
    <goals>
      <goal>exec</goal>
    </goals>
    <configuration>
      <executable>gulp</executable>
      <workingDirectory>src/main/webapp/</workingDirectory>
      <arguments>
        <argument>
          build
        </argument>
      </arguments>
    </configuration>
  </execution>
</executions>
</plugin>
{% endhighlight %}

### 再奉献一点存货

&emsp;&emsp;因为在执行`npm install`的时候会把`node_moduels`都下载到项目目录里来，打[war][war]{:target="_blank"}包的时候会把`node_modules`这个文件夹也包括进来，有时候这个文件夹大得相当客观，要把`node_modules`排除在外，就要用到另外一款插件[maven-war-plugin][maven-war-plugin]{:target="_blank"}，看以下配置，注意`webappDirectory`的写法。

{% highlight xml %}
<plugin>
<artifactId>maven-war-plugin</artifactId>
<version>2.5</version>
<configuration>
  <webappDirectory>${project.build.directory}/${project.build.finalName}</webappDirectory>
  <warSourceExcludes>node_modules/</warSourceExcludes>
</configuration>
</plugin>
{% endhighlight %}

[gulp]: http://gulpjs.com "gulp"
[sass]: http://sass-lang.com "sass"
[npm]: http://npmjs.com "npm"
[maven]: http://maven.apache.org "maven"
[java]: http://www.java.com "java"
[war]: http://en.wikipedia.org/wiki/War "war"
[exec-maven-plugin]: http://mojo.codehaus.org/exec-maven-plugin "exec-maven-plugin"
[maven-war-plugin]: http://maven.apache.org/plugins/maven-war-plugin "maven-war-plugin"
