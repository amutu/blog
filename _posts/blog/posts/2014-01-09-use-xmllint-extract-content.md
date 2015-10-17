---
layout: post
status: publish
published: true
title: "使用xmllint抽取xml内容"
author:
  display_name: amutu
  login: amutu
  email: zhao6014@gmail.com
  url: ''
author_login: amutu
author_email: zhao6014@gmail.com
wordpress_id: 405
wordpress_url: http://amutu.com/blog/?p=405
date: '2014-01-09 18:00:20 +0800'
date_gmt: '2014-01-09 10:00:20 +0800'
categories:
- linux
tags: []
comments: []
---
`
$ cat foo.xml
<emplist>
<emp no="1">
<ename>John</ename>
</emp>
<emp no="2" />
<ename>Jack</ename>
</emp>
</emplist>
$ echo 'cat emplistemp[@no="1"]enametext()'| xmllint --shell foo.xml |sed -n 3p  
John
`
