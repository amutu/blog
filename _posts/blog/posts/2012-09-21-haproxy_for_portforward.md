---
layout: post
status: publish
published: true
title: "使用haproxy做端口转发"
author:
  display_name: amutu
  login: amutu
  email: zhao6014@gmail.com
  url: ''
author_login: amutu
author_email: zhao6014@gmail.com
wordpress_id: 6
wordpress_url: http://amutu.com/blog/?p=6
date: '2012-09-21 12:13:17 +0800'
date_gmt: '2012-09-21 04:13:17 +0800'
categories:
- "未分类"
tags: []
comments:
- id: 73
  author: Talia
  author_email: info@ashdown-ingram.com.au
  author_url: http://www.facebook.com/profile.php?id=100003419318732
  date: '2013-03-23 05:46:28 +0800'
  date_gmt: '2013-03-22 21:46:28 +0800'
  content: That addersess several of my concerns actually.
---
<p>因为公司限制在开发机只能访问IDC内特定端口，比如80端口，为了能在开发机访问，之前用ssh端口转发。但是公司的安全策略又不允许sshd启动在80这样的端口上，因此在使用了一段事件后，就不停的被安全中心骚扰，要求停掉80端口上的sshd。</p>
<p>无奈只能找其他的软件做端口转发，之前尝试过haproxy，它有tcp的模式，可以用来做转发。配了以后，发现很容易客户端段和服务端断掉，往往交互一两个命令后，就断掉了。</p>
<p>今天又尝试了一下，终于成功了。原来之前的timeout值设置的太小，改大后，功能稳定。</p>
<p>下面是配置：<br />
global<br />
maxconn 40000<br />
# debug<br />
#quiet<br />
user allison<br />
group users<br />
nbproc 1<br />
log 127.0.0.1 local3<br />
spread-checks 2<br />
defaults<br />
option srvtcpka<br />
option clitcpka<br />
option tcpka</p>
<p>#以下几个timeout值注意不能设置太短<br />
timeout server 300s<br />
timeout connect 300s<br />
timeout client 300s<br />
timeout http-request 300s<br />
timeout queue 300s<br />
listen pg_for_ide pg_host:80<br />
mode tcp<br />
option persist<br />
server pg pg_ip:5432 weight 256<br />
</p>

haproxy配置项很多，功能也很强大，有时间再细细研究一下。
<p>参考信息：
haproxy官网：<a href="http://haproxy.1wt.eu/">http://haproxy.1wt.eu/</p>
