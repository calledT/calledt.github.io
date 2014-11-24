---
layout: post
title: 自动登陆那点事
description: "也说说自动登陆的实现及存在问题"
modified: 2014-11-24
tags: [登陆,安全,cookie]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

### cookie 和 session

[http][http]是无状态的，无状态是怎么样一种情况呢？可以简单解释一下：

> 比如用户张三依次发起两次请求，仅通过 [http][http]
> 是无法知道第一次请求和第二次请求是由同一个用户张三发起的。
<!-- more -->

所以无法仅通过 [http][http] 来识别用户，我们就要寻找另外途径，这就需要用到 [cookie][cookie] 和 [session][session] 了。

[session][session] 在服务器端生成，通过 `Response Headers` 的 `Set-Cookie` 把 `sessionid`返回给客户端，这个包含 `sessionid` 的 [cookie][cookie] 通常是带有 **`HttpOnly`** 标识的，这个标识代表着这个 [cookie][cookie] 不能被客户端读写，也就是说我们不能通过脚本读取或者设置这个 [cookie][cookie]。这样用户成功登陆后，再发起请求的时候，都会在 `Request Headers` 里携带这个带有 `sessionid`
的 [cookie][cookie] 发送到服务器端，服务器端对比发送过来的 `sessionid`和服务器上保存的 [session][session]， 就是识别对应的用户。

### 自动登陆最佳实践

上面讲了利用 [cookie][cookie] 和 [session][session] 来保存用户的登录状态，一般情况下含着`sessionid` 的 [cookie][cookie] 是保存在内存里的，即 [cookie][cookie] 的 `expires` 值小于0，随着浏览器的关闭，这个 [cookie][cookie] 就会被清空。当用户打开浏览器再次访问这个网站的时候，由于没有标识用户的 `sessionid`，这时候就要再次重复以上登录过程。

网站为了用户体验，会设置一个记住我的选项，这个选项背后的工作就是自动登录，我们会利用 [cookie][cookie] 来实现。

> 注意: 利用 [cookie][cookie] 来实现自动登录会增加网站的不完全因素。但是我们会通过多种方法来使这种不安全因素降到最少。

#### 以下是实现自动登录的大概步骤：

1. 在数据库里增加一张表用来保存用户的登录信息，暂且叫他 `auto_login` 表，这个表里有以下字段 `id`， `user_id`， `auto_login_hash`， `create_time`；

2. 当用户勾选记住我并且成功登录我们的网站之后， 除了返回带有 `sessionid` 的 [cookie][cookie] 之外， 还有 `auto_login_hash` 和 `username` 的 [cookie][cookie]， 这两个 [cookie][cookie] 是要被保存在硬盘里的， `expires` 的值可能会被设置会一周或者两周，由开发者决定；

3. 用户关闭浏览器之后再次访问我们的网站，我们检测如果 [cookie][cookie] 里存在 `auto_login_hash` 和 `username`，但是不存在 `sessionid`，就发起一个 `Ajax` 请求去登录；

4. 后端程序根据传过来的 `auto_login_hash` 和 `username`检测到 `auto_login` 表里有对应的记录，代表的这个用户可以登录.同时给这条记录的`auto_login_hash`设置一个新的值， `create_time`保持不变，再把新的 `auto_login_hash` 和 `sessionid` 通过`Response Headers` 的 `Set-Cookie` 返回给客户端就完成了一次自动登录的过程；

以上自动登录过程存在的风险就是 `auto_login_hash` 可能会被攻击者捕获。
攻击者可以利用这个 `auto_login_hash` 去登录用户的账号，成功之后会有新的 `auto_login_hash`生成并返回。
这时如果真正的用户重新打开浏览器访问我们网站的时候，我们对比发送过来的 `auto_login_hash`和数据库里的不一样，就判定用户账号有安全问题，清除这个用户的`auto_login_hash`信息，让用户使用密码重新登录。
但是攻击者任然可以利用捕获 `auto_login_hash` 到用户再次访问我们网站的时间差。

为了加大[cookie][cookie] 被抓包泄露的难度， 提倡网站使用 [https][https]。同时在进行以下操作的时候，我们可以让用户再一次输入密码确认的设计来提高安全性。

* 修改注册邮箱(特别是涉及到密码重置邮箱)
* 修改密码
* 涉及金钱的操作
* 一些第三方应用获取权限
* .......


[cookie]: http://en.wikipedia.org/wiki/HTTP_cooki "cookie"
[session]: http://en.wikipedia.org/wiki/Session_(computer_science) "session"
[http]: http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol "http"
[https]: http://en.wikipedia.org/wiki/HTTP_Secure "https"
