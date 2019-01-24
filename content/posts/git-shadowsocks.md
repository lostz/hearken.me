+++
title = "用shadowsocks加速git clone"
description = ""
date = "2018-08-09"
tags = ["git"]
+++
### 用shadowsocks加速git clone
git 支持socks5，直接配置代理，就ok了。

```
git config --global http.proxy 'socks5://127.0.0.1:1080' 
git config --global https.proxy 'socks5://127.0.0.1:1080'

```

