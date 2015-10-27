---
layout: post
status: publish
published: true
author:
  display_name: amutu
  login: amutu
  email: zhao6014@gmail.com
  url: ''
author_login: amutu
author_email: zhao6014@gmail.com
categories:
- pgsql
tags: []
comments: []
---
在PostgreSQL中，如果我们想看流复制是否正常，可以通过在master中查看pg_stat_replication视图来达到目的。为了监控流复制是否异常，不能简单的通过count这个视图来达到，必须关注state这个字段。  
上周遇到一个问题，发现主备已经不同步了，但是pg_stat_replication中还能看到备机。只不过备机的state状态不是streaming，而是startup。最后定位，是因为这个master是之前cascade复制时，promote的，但是它的下游slave的recovery_target_timeline设置是默认的，并不是latest，导致这个slave一直在像新的master获取promote之前timeline的WAL，所以一直在startup状态。  
所以在检查流复制状态时，一定要关注state这个字段，正常情况下，这个字段应该是streaming状态。
