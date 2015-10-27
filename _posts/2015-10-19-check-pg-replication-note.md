---
layout: post
status: publish
published: true
title: "监控PG流复制需要注意的地方"
author:
  display_name: amutu
  login: amutu
  email: zhao6014@gmail.com
  url: ''
author_login: amutu
author_email: zhao6014@gmail.com
categories: pgsql
tags: [pgsql,replication,operation]
---
在PostgreSQL中，如果我们想看流复制是否正常，可以通过在master中查看pg_stat_replication视图来达到目的。为了监控流复制是否异常，不能简单的通过count这个视图来达到，必须关注state这个字段。 

上周遇到一个问题，发现主备已经不同步了，但是pg_stat_replication中还能看到备机。只不过备机的state状态不是streaming，而是startup。最后定位，是因为这个master是之前cascade复制时，promote的，但是它的下游slave的recovery_target_timeline设置是默认的，并不是latest，导致这个slave一直在像新的master获取promote之前timeline的WAL，所以一直在startup状态。  
所以在检查流复制状态时，一定要关注state这个字段，正常情况下，这个字段应该是streaming状态，如下所示

<pre>
postgres=# select * from pg_stat_replication ;
-[ RECORD 1 ]----+------------------------------
pid              | 33746
usesysid         | 16387
usename          | repuser
application_name | walreceiver
client_addr      | 104.0.0.0
client_hostname  | 
client_port      | 14365
backend_start    | 2015-10-27 19:03:44.895699+08
state            | streaming
sent_location    | D8/24026000
write_location   | D8/24020000
flush_location   | D8/2401E000
replay_location  | D8/2401DF10
sync_priority    | 0
sync_state       | async
</pre>
