+++
title = "elastalert 的微信报警 "
description = ""
date = "2018-02-01"
tags = ["elastalert"]
+++
### elastalert 的微信报警
* 前几天搞了,elasticsearch 日志监控系统,很标准的方案,没有什么大的问题,对于elasticsearch 我的优化很粗暴就是大内存,索引只保留一天的,毕竟实时报警系统,不需要历史数据.

* elastalert 是一个好东西,实现的很简单,但是有几个问题.
  
  1. 所有的rule 是顺序执行的,这个就对于你的elasticsearch 性能有很高的要求.

  2. python2  实现, 不是说不好,但是都9012 了 再用python2的话就....

  3. 没有微信和短信接口.
  
* 所以我就实现了短信和微信接口.短信和运营商绑定的,就不public了.微信接口的代码地址在 https://github.com/lostz/wechat_qiye_alert.

  核心地方就是 token cache 化,减少对于微信接口的访问.由于是runtime 的导致了无法把token cache ,所有就使用了sqlite3 数据库来解决这个问题.

  觉得设计还是很巧妙的,核心思想就是实现 mysql 的 INSERT ON DUPLICATE KEY UPDAE. 剩下就没有什么了. 欢迎提issue.
